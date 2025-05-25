# Introduction to RabbitMQ
- Queue - A place where items of work are stored and managed. Items of work represent units of task or actions that need to be processed
- First In, First Out (FIFO) - A pattern of behavior for queues where the first item added to the queue is the first one to be processed
- List In, First Out (LIFO) - A pattern of behavior for queues where the last item added to the queue is the first one to be processed
- Producer - The entity responsible for generating and adding units of work to the queue
- Consumer - The entity responsible for processing and removing units of work from the queue
- Broker/Messag broker - A seperate process used to connect Python applications to message queues such as RabbitMQ
- Celery - A middleman package in Python that allows producing items of work in Python and putting them somwhere else, such as RabbitMQ
- AMQP - Advanced Message Queing Protocol, a protocol for messaging middleware use by RabbitMQ
- Task queue - Represents a specific type of message queue designed to handle tasks or units of work
- Concurrency - The ability for multiple operations to execute simultaneously
- Serialization - The process of converting complex data types into simpler formats for storage or transmission
- Image processing - A use case for queues involving resizing images and creating down samples at large scales
- Publish/Subscribe - An architectural pattern used in message queuing systems allowing multiple entities to communicate by publishing messages to a queue and subscribing to recieve those messages
- Request-Reply Pattern - A communication model between two parties where one party sends a request and expects a response from the second party
- Units of work - represent individual tasks or actions that need to be processed - data, sending emails, or any other type of action

## Overview of Queues
- Celery is how we connect Python applications to message brokers such as RabbitMQ
- The queues are responsible for managing units of work as they are getting produced
- queues have different ways of working, but a conventional pattern is FIFO for prioritizing which units of work are consumed
- we will always have somehting that produces items of work, and these producers will produce units of work that will populate a queue
- RabbitMQ is a type of queue 
- the consumer is a seperate process looking into the queue for the next item it needs to work with
- this pattern of working is useful because the producer can continue to adding to the queue while the consumer seperately consumes work
- there is seperation of concern between producer and consumer which scales

## Celery
- a Python package that works with many different frameworks and different brokers / queues such as RabbitMQ
- Celery is the middleman, that allows us to work within Python to produce items of work that can then be sent to RabbitMQ
- AMQP is the default protocol in Celery for messaging RabbitMQ
- We can actually use a database as a queue 
- We can use the Python application to produce items of work that will get connected to Celery and then Celery will put them in RabbitMQ
- Celery allows us to consume these messages and also store back results that are the byproduct of these units of work 
- Celery is necessary when working with Python and trying to utilize RabbitMQ

### What is a Task Queue?
- Celery communicates via messages, using a broker to mediate between clietns and workers
- To initiate a task the client adds a message to the queue, the broker then delivers that message to the worker
- A Celery system can consist of multiple workers and brokers, giving way to high availability and horizontal scaling
- Celery requires a message transport to send and recieve messages
- RabbitMQ broker transports are feature complete
- Celery can run on a single machine, on multiple machines, or even across data centers
- Celery workers and clients will automatically retry in the event of connection loss or failure, and some brokers support HA in way of Primary/Primary or Primary/Replica replication
- A single Celery process can process millions of  tasks a minute with sub-ms round-trip latency
- Every part of Celery can be extended or used on its own, custom pool implementations, serializers, compression schemes, logging, schedulers, consumers, producers, broker transports, etc.
- Flow-er can be used for real-time web based monitoring and administration for Celery
  - allows for tracking task progress and history
  - ability to show task details (arguments, start time, run-time, and more)
  - graphs and statistics
- Remote Control of Celery process
  - View worker status and statistics
  - control worker pool size and autoscale settings
  - view and modify the queues a worker instance consumes from
  - view currently running tasks
  - view scheduled tasks (ETA / countdown)
  - view reserved and revoked tasks
  - apply time and rate limits
  - configuration viewer
  - revoke or terminate tasks
- HTTP API
  - List workers
  - shut down a worker
  - restart/shrink/grow/autoscale worker's pool
  - start consuming from queue
  - stop consuming from queue
  - List tasks
  - List (seen) task types
  - get task info
  - execute task
  - get a task result
  - change soft and hard time limits for task
  - change rate limit for a task
  - revoke a task
- We must be able to manage RabbitMQ through inspecting queues
  - we can list queues, exchanges, bindings, queue lengths, the memory usage of each queue as well as users, virtual hosts,  and their permissions
  - `rabbitmqctl list_queues name messages messages_ready \ messages_unacknowledged`- the number of tasks in a queue
  - `rabbitmqctl list_queues name consumers`
  - `rabbitmqctl list_queues name memory` 
- Task Events from Celery that can be sent by the worker
  - task-sent: when a task message is published and the setting is enabled
  - task-recieved: sent when the worker recieves a task
  - task-started: sent just before the worker executes the task
  - task-succeeded: sent if the task is executed successfully with runtime from when the task is sent to the worker pool
  - task-failed: sent if the execution failed
  - task-rejected: task was rejected by the worker, possibly to be re-queued or moved to a dead letter queue
  - task-revoked: sent if the task has been revoked (likely sent by more than one worker)
- Worker Events
  - worker-online: the worker has connected to the broker and is online
  - worker-heartbeat: sent every minute if the worker hasn't sent a heartbeat in 2 minutes, it is considered to be offline
  - worker-offline: The worker has disconnected from the broker
### Use cases for RabbitMQ
- Work Queues - the producer-consumer model where the producer creates the units of work that get put into the queue for the consumers (workers) taking from the queue
- Publish/Subscribe - send messages to many consumers at once
  - we can subscribe to a queue and get notified when things are happening and what is happening in a queue
  - we consume messages, not necessarily units of work
  - allow many nodes to communicate with each other
- Routing
  - recieving messages selectively
- Request/ Reply pattern
# Using RabbitMQ with Flask and Celery
- Celery: An asynchronous task queue based on distributed message passing. Allows you to execute tasks asynchronously outside of the request-response cycle. Integrates well with Flask for building asyncronous web apps
- RabbitMQ: An open source message broker that implements the Advanced Message Queuing Protocol (AMQP)
- Used by Celery to pass messages between cclients and workers for processing tasks async
- Task: A unit of work in Celery that gets executed async. Typically invoked on a route or endpoint and then handed off to a worker process to execute
- Worker: A process that Celery starts to execute tasks that are sent through the message queue. Pull takss from the queue and executes them. Can run asynchronously in the background
- Queue: A queue that Celery uses to pass messages between clients and workers. Celery uses RabbitMQ and AMPQ to implement the queue. Messages represent tasks to be executed
- Shared task: A convenience Celery decorator to create tasks - automatically connects to a broker and backend based on configuration
- Delay - A celery method to introduce artificial delays when executing tasks. Used to demonstrate asynchronous behavior since tasks return immediately
# MySQL for Data Engineering

# Apache Airflow
- Airflow DAG (Directed Acyclic Graph) : A collection of tasks with dependencies that are arranged in a way that prevents cycles in the graph - This allows Airflow to schedule tasks efficiently
- Airflow Operator : Represents a single idemoptent task in a workflow. Many operators come "out of the box" for common tasks
- Airflow Hook : An interface to connect to external systems and services like databases, S3, etc. Commonly used by operators
- Airflow Backfill : Ability to re-run previous DAG runs over a time period. Useful for testing pipelines with historical data.
- Airflow Trigger DAG: Manually triggering a pipeline run outside of the scheduler. Useful for testing
  
## Introduction
- Apache Airflow allows data engineers to create powerful pipelines involving different tasks for extracting, transforming, and persisting data across a whole process
- 
