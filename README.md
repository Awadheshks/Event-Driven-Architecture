# Event-Driven-Architecture
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/4793d116243f32bfb344fc3a7e53021fcd4b0c7b/assets/EventBridge.png)

### Workshop:
https://catalog.us-east-1.prod.workshops.aws/workshops/63320e83-6abc-493d-83d8-f822584fb3cb/en-US

#### Event Driven Architecture Hands-On on "Event-driven with SNS"
1. Simple pub/sub
2. Message filtering
3. Advanced message filtering


## Simple pub/sub
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/4793d116243f32bfb344fc3a7e53021fcd4b0c7b/assets/EventBridge.png)


In this module, you will start by building a simple pub/sub implementation. An Amazon SNS topic will be used to publish events to an Amazon SQS queue subscriber. This will allow to easily verify the successful delivery of the messages.

An Amazon SNS topic is a logical access point that acts as a communication channel. A topic lets you group multiple endpoints (such as AWS Lambda, Amazon SQS, HTTP/S, or an email address).

To broadcast the messages of a message-producer system (for example, an e-commerce website) working with multiple other services that require these messages (for example, checkout and fulfilment systems), you can create a topic for your producer system.

### Step 1: Create the Orders SQS queue

Create an Amazon SQS queue, also called Orders, to durably store Order messages.
1.	Open the AWS Management Console for SQS , click Create queue at the top of your queue list.
3.	Enter a queue name 'OrdersQueue',for Type, select Standard.
5.	Choose Create queue,the OrdersQueue queue is created and selected in the queue list.

   PIC

### Step 2: Subscribe the OrdersQueue SQS queue to the OrdersQueue SNS topic
To receive messages published to a topic, you must subscribe an endpoint to the topic. In this case, we will subscribe the SQS queue that you created in the previous step as the endpoint. When you subscribe an endpoint to a topic, the endpoint begins to receive messages published to the associated topic.

1.	From the list of queues, choose the OrdersQueue queue to which to subscribe the Orders Amazon SNS topic.
2.	From Queue Actions, select Subscribe to Amazon SNS topic.The Subscribe to Amazon SNS topic screen is displayed.
3.	From the Specify an Amazon SNS topic drop-down list, select the Orders topic to which to subscribe the OrdersQueue queue, and then choose Save.
4.	Verify that the OrdersQueue SQS queue is successfully subscribed to the Orders and is displayed in SNS Subscriptions tab.
PIC

The queue is subscribed to the topic. Note that the Access policy tab displays the SQS policy that was added, allowing access for the Orders SNS topic to send messages to the OrdersQueue SQS queue.

PIC access policy


### Step 3: Publish a test message using the Publish Message functionality
To verify the result of the subscription, you will use the Publish Message functionality to publish to the topic and then view the message that the topic sends to the queue.
1.	Select Topics in the left pane of the Amazon SNS service, select Orders topics from the list of Topics and click on Publish message.
2.	Enter Test message in the Message body.
3.	Choose Publish message to publish the message to the Orders SNS topic.
   
PIC publish message

### Step 4: Verify message delivery
1.	Open the AWS Management Console for SQS,from the list of queues, choose the OrdersQueue queue. Note the OrdersQueue queue now shows the value 1 in the Messages Available column in the list. This means the Orders SNS topic has successfully sent the message to the queue.
   
PIC verifymesage

2.	select Send and receive messages, On details screen, choose Poll for messages.
   
Amazon SQS begins to poll the messages in the queue. The dialog box displays a message from the queue. A progress bar at the bottom of the dialog box displays the status of the message's visibility timeout.

The following example shows the ID, Sent, Size and Receive Count columns.

PIC Poll message

4.	Before the visibility timeout expires, select the message that you want to delete and then choose Delete 1 Message.
   
PIC Delete Message

You have successfully published a message to an SNS topic and verified that it was sent to the SQS queue subscription. 

In the next section, you will review how to apply message filtering to route messages to subscription endpoints.

   
