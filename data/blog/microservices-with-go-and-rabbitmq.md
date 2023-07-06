---
title: Implementing Simple Microservices with Go and RabbitMQ in eCommerce
date: '2023-05-23'
tags: ['Go', 'Node.js', 'Docker', 'RabbitMQ', 'Microservices']
draft: false
summary: 'Implementation of a microservice architecture for an using eCommerce platform using Go, RabbitMQ, and Docker.'
---

# Introduction

Microservices architecture has seen a rapid rise in popularity among developers and businesses alike, due to the flexibility and scalability it provides, as compared to traditional monolithic architectures. In the context of an eCommerce platform, microservices can streamline development, facilitate team collaboration, and simplify the process of scaling or updating individual components.

In this tutorial, we'll walk through the process of creating a microservice-oriented eCommerce platform using Go, RabbitMQ, and Docker. Our platform will consist of two primary services: 'product' and 'order'.

RabbitMQ is an open-source message broker that enables loosely coupled communication between microservices. We'll delve deeper into RabbitMQ's role as we progress.

Before diving into the main content, let's take a look at some prerequisites.

# Prerequisites

To follow this tutorial, ensure that you have:

- [Golang](https://golang.org/dl/) installed on your system.
- Basic understanding of the Go language and syntax.
- [RabbitMQ](https://www.rabbitmq.com/download.html) and [Docker](https://www.docker.com/products/docker-desktop/) installed on your system.

# Microservices in Go

**Golang**, or **Go**, has become a go-to language for implementing microservices due to its simple syntax, Golang compiles codes faster, strong standard library, and intrinsic support for concurrent programming. Moreover, its compiled nature leads to fast execution and less resource consumption, making it an optimal choice for microservices.

In our eCommerce platform, we'll use Go to build our 'product' and 'order' services. The product service will handle functionalities related to products, such as creating new products or fetching product details. The order service will manage the ordering process, updating the status and details of orders.

To facilitate communication between these services, we'll use RabbitMQ.

## RabbitMQ and Inter-Service Communication

RabbitMQ will be used as the message broker in our architecture. It will enable our services to communicate and share data without needing to be directly connected, thus adhering to the loose coupling principle of microservices. When a service needs to communicate an event or data, it sends a message to a queue, and RabbitMQ ensures that the message is delivered to all interested services.

# Implementation

Let's begin implementing our microservices.

Firstly, let's structure our project. We will have two main directories, one for each service:

```css
├── order-service
│   ├── main.go
│   ├── order.go
│   ├── dockerfile
└── product-service
    ├── main.go
    ├── product.go
    ├── dockerfile
```

## Product Service

Let's initialize our product service. The product service will handle all operations related to products.

# Defining the Product Struct

In `product.go`, we define our Product struct and methods for creating and retrieving products:

```go
Copy code
package main

import (
	"encoding/json"
	"log"
)

type Product struct {
	ID          string `json:"id"`
	Name        string `json:"name"`
	Description string `json:"description"`
	Price       float64 `json:"price"`
}

// Function to create a new product
func createProduct(id string, name string, desc string, price float64) *Product {
	p := Product{ID: id, Name: name, Description: desc, Price: price}

	// Simulate writing to a database
	data, err := json.Marshal(p)
	if
err != nil {
		log.Fatal(err)
	}

	log.Printf("New product created: %s\n", data)

	return &p
}

// Function to fetch a product by id
func getProduct(id string) *Product {
	// Simulate fetching from a database
	// In a real-world application, you'll want to replace this with actual database calls

	p := Product{ID: id, Name: "Sample Product", Description: "This is a sample product.", Price: 19.99}
	return &p
}
```

the createProduct and getProduct functions represent database calls.

# Implementing RabbitMQ Messaging

In main.go, we define our main function where we establish a connection with RabbitMQ, define our routes, and start the server:

```go
Copy code
package main

import (
	"log"
	"net/http"

	"github.com/gin-gonic/gin"
	"github.com/streadway/amqp"
)

func failOnError(err error, msg string) {
	if err != nil {
		log.Fatalf("%s: %s", msg, err)
	}
}

func main() {
	// Connect to RabbitMQ
	conn, err := amqp.Dial("amqp://guest:guest@localhost:5672/")
	failOnError(err, "Failed to connect to RabbitMQ")
	defer conn.Close()

	ch, err := conn.Channel()
	failOnError(err, "Failed to open a channel")
	defer ch.Close()

	// Declare a queue for our product-service
	q, err := ch.QueueDeclare(
		"product-service-queue", // name
		false,   // durable
		false,   // delete when unused
		false,   // exclusive
		false,   // no-wait
		nil,     // arguments
	)
	failOnError(err, "Failed to declare a queue")

	// Create a new Gin router
	router := gin.Default()

	// Define our routes
	router.POST("/product", func(c *gin.Context) {
		var p Product

		if err := c.ShouldBindJSON(&p); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}

		// Create a new product and print its details
		product := createProduct(p.ID, p.Name, p.Description, p.Price)
		c.JSON(http.StatusOK, product)
	})

	router.GET("/product/:id", func(c *gin.Context) {
		// Retrieve the product by id
		product := getProduct(c.Param("id"))
		c.JSON(http.StatusOK, product)
	})

	// Run the server
	router.Run(":8080")
}
```

In this code, we connect to RabbitMQ, declare a queue, define our routes for creating and fetching products, and then start the server.

# Order Service

Onto our order service, the order service will handle operations related to creating and managing orders.

# Defining the Order Struct

In order.go, we define our Order struct and methods for creating and retrieving orders:

```go
Copy code
package main

import (
	"encoding/json"
	"log"
)

type Order struct {
	ID       string   `json:"id"`
	ProductIDs []string `json:"product_ids"`
	Status   string `json:"status"`
}

// Function to create a new order
func createOrder(id string, productIDs []string, status string) *Order {
	o := Order{ID: id, ProductIDs: productIDs, Status: status}

	// Simulate writing to a database
	data, err := json.Marshal(o)
	if err != nil {
		log.Fatal(err)
	}

		// ...
	log.Printf("New order created: %s\n", data)

	return &o
}

// Function to fetch an order by id
func getOrder(id string) *Order {
	// Simulate fetching from a database
	// In a real-world application, you'll want to replace this with actual database calls

	o := Order{ID: id, ProductIDs: []string{"product1", "product2"}, Status: "processed"}
	return &o
}
```

# Implementing RabbitMQ Messaging in the Order Service

We now need to integrate RabbitMQ into our order service as well, to ensure communication between our microservices. Similar to the product service, we will create a connection to the RabbitMQ server, create a channel, and define a queue for the order service.

In `main.go` of the order service, we first connect to RabbitMQ, declare our queue, and then define a function to consume from the product service queue:

```go
Copy code
package main

import (
	"encoding/json"
	"log"
	"net/http"

	"github.com/gin-gonic/gin"
	"github.com/streadway/amqp"
)

func failOnError(err error, msg string) {
	if err != nil {
		log.Fatalf("%s: %s", msg, err)
	}
}

func main() {
	// Connect to RabbitMQ
	conn, err := amqp.Dial("amqp://guest:guest@localhost:5672/")
	failOnError(err, "Failed to connect to RabbitMQ")
	defer conn.Close()

	ch, err := conn.Channel()
	failOnError(err, "Failed to open a channel")
	defer ch.Close()

	// Declare a queue for our order-service
	q, err := ch.QueueDeclare(
		"order-service-queue", // name
		false,   // durable
		false,   // delete when unused
		false,   // exclusive
		false,   // no-wait
		nil,     // arguments
	)
	failOnError(err, "Failed to declare a queue")

	// Consume from the product-service-queue and create an order
	msgs, err := ch.Consume(
		"product-service-queue", // queue
		"",     // consumer
		true,   // auto-ack
		false,  // exclusive
		false,  // no-local
		false,  // no-wait
		nil,    // args
	)
	failOnError(err, "Failed to register a consumer")

	go func() {
		for d := range msgs {
			log.Printf("Received a message: %s", d.Body)

			// Unmarshal the product IDs from the received message
			var productIDs []string
			err = json.Unmarshal(d.Body, &productIDs)
			failOnError(err, "Failed to unmarshal JSON")

			// Create a new order
			order := createOrder("1", productIDs, "CREATED")
			log.Printf("Created a new order: %s\n", order)

			// Publish the created order to the order-service-queue
			body, err := json.Marshal(order)
			failOnError(err, "Failed to marshal JSON")

			err = ch.Publish(
				"",     // exchange
				q.Name, // routing key
				false,  // mandatory
				false,  // immediate
				amqp.Publishing{
					ContentType: "application/json",
					Body:        body,
				})
			failOnError(err, "Failed to publish a message")

			log.Printf("Published a message: %s\n", body)
		}
	}()

	log.Printf(" [*] Waiting for messages. To exit press CTRL+C")

	// Create a new Gin router
	router := gin.Default()

	// Define our routes
	router.POST("/order", func(c *gin.Context) {
		var o Order

		if err := c.ShouldBindJSON(&o); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}

		// Create a new order and print its details
		order := createOrder(o.ID, o.ProductIDs, o.Status)

		c.JSON(http.StatusOK, gin.H{
			"message": "Order created successfully",
			"order":   order,
		})
	})

	// Run the server on port 8081
	router.Run(":8081")
}
```

In the handler function for the `/order` route, we first bind the JSON body of the request to an `Order` struct. If the body cannot be unmarshaled into an `Order`, we send a `400 Bad Request` response back to the client. If the body can be unmarshaled successfully, we create a new order, print its details, and then send a `200 OK` response back to the client with the details of the created order.

The Order Model
Let's define the Order struct in a separate order.go file:

```go
Copy code
package main

import "time"

// Order represents an order made by a customer
type Order struct {
	ID         string    `json:"id"`
	ProductIDs []string  `json:"product_ids"`
	Status     string    `json:"status"`
	CreatedAt  time.Time `json:"created_at"`
}

// createOrder creates a new order with the given product IDs and status
func createOrder(id string, productIDs []string, status string) Order {
	return Order{
		ID:         id,
		ProductIDs: productIDs,
		Status:     status,
		CreatedAt:  time.Now(),
	}
}
```

The `Order` struct has four fields: `ID`, `ProductIDs`, `Status`, and `CreatedAt`. We also have a function `createOrder` that takes an ID, a slice of product IDs, and a status, and returns a new `Order`.

# Conclusion

In this post, we went over the basics of microservices and their advantages over monolithic applications. We then built two simple microservices in Go, one for managing products and the other for managing orders. We used RabbitMQ to handle communication between the two services, allowing us to create orders consisting of multiple products.

Thanks for reading. Leave a comment,like and share.

If you enjoyed this article, please leave a comment, like it, share it, and follow me on Twitter [@Tonycodh](https://twitter.com/Tonycodh).

```~ Tony 😄

```
