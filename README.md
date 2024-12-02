# Event-Driven-Architecture
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/4793d116243f32bfb344fc3a7e53021fcd4b0c7b/assets/EventBridge.png)

### Workshop:
https://catalog.us-east-1.prod.workshops.aws/workshops/63320e83-6abc-493d-83d8-f822584fb3cb/en-US

#### Event Driven Architecture Hands-On on "Event-driven with SNS"
1. Simple pub/sub
2. Message filtering
3. Advanced message filtering

   

## Event-driven with SNS
In addition to Amazon EventBridge, event-driven applications can also be developed using Amazon Simple Notification Service (SNS). Amazon SNS is recommended when you want to build an application that reacts to high throughput or low latency messages published by other applications or microservices (as Amazon SNS provides nearly unlimited throughput), or for applications that need very high fan-out (thousands or millions of endpoints). Messages are unstructured and can be in any format.

In this module you will start to build on a simple pub/sub implementation using Amazon SNS as our publishing service and Amazon SQS as a subscriber (you will use the queue to validate message delivery). We will build on this architectural style over the course of the module, and introduce more features that allow us to take advantage of message filtering and advance message filtering.


## Simple pub/sub
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/pub-sub.png)


In this module, you will start by building a simple pub/sub implementation. An Amazon SNS topic will be used to publish events to an Amazon SQS queue subscriber. This will allow to easily verify the successful delivery of the messages.

An Amazon SNS topic is a logical access point that acts as a communication channel. A topic lets you group multiple endpoints (such as AWS Lambda, Amazon SQS, HTTP/S, or an email address).

To broadcast the messages of a message-producer system (for example, an e-commerce website) working with multiple other services that require these messages (for example, checkout and fulfilment systems), you can create a topic for your producer system.

#### Step 1: Create the Orders SQS queue

Create an Amazon SQS queue, also called Orders, to durably store Order messages.
1.	Open the AWS Management Console for SQS , click Create queue at the top of your queue list.
3.	Enter a queue name 'OrdersQueue',for Type, select Standard.
5.	Choose Create queue,the OrdersQueue queue is created and selected in the queue list.
   ![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/QueueCreation.png)

#### Step 2: Subscribe the OrdersQueue SQS queue to the OrdersQueue SNS topic
To receive messages published to a topic, you must subscribe an endpoint to the topic. In this case, we will subscribe the SQS queue that you created in the previous step as the endpoint. When you subscribe an endpoint to a topic, the endpoint begins to receive messages published to the associated topic.

1.	From the list of queues, choose the OrdersQueue queue to which to subscribe the Orders Amazon SNS topic.
2.	From Queue Actions, select Subscribe to Amazon SNS topic.The Subscribe to Amazon SNS topic screen is displayed.
3.	From the Specify an Amazon SNS topic drop-down list, select the Orders topic to which to subscribe the OrdersQueue queue, and then choose Save.
4.	Verify that the OrdersQueue SQS queue is successfully subscribed to the Orders and is displayed in SNS Subscriptions tab.
 ![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/SubSNSTopic.png)

The queue is subscribed to the topic. Note that the Access policy tab displays the SQS policy that was added, allowing access for the Orders SNS topic to send messages to the OrdersQueue SQS queue.   

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/AccessPolicy.png)


#### Step 3: Publish a test message using the Publish Message functionality
To verify the result of the subscription, you will use the Publish Message functionality to publish to the topic and then view the message that the topic sends to the queue.
1.	Select Topics in the left pane of the Amazon SNS service, select Orders topics from the list of Topics and click on Publish message.
2.	Enter Test message in the Message body.
3.	Choose Publish message to publish the message to the Orders SNS topic.
   
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/PublishMessahe.png)

#### Step 4: Verify message delivery
1.	Open the AWS Management Console for SQS,from the list of queues, choose the OrdersQueue queue. Note the OrdersQueue queue now shows the value 1 in the Messages Available column in the list. This means the Orders SNS topic has successfully sent the message to the queue.
   
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/Verifymessage.png)

2.	select Send and receive messages, On details screen, choose Poll for messages.
   
Amazon SQS begins to poll the messages in the queue. The dialog box displays a message from the queue. A progress bar at the bottom of the dialog box displays the status of the message's visibility timeout.

The following example shows the ID, Sent, Size and Receive Count columns.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/PollMesage.png)

4.	Before the visibility timeout expires, select the message that you want to delete and then choose Delete 1 Message.
   
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/DeleteMessage.png)
You have successfully published a message to an SNS topic and verified that it was sent to the SQS queue subscription. 


## Next Section - Message Filtering
### What is Amazon SNS message filtering?


By default, an Amazon SNS topic subscriber receives every message published to the topic. To receive a subset of the messages, a subscriber must assign a filter policy to the topic subscription.

A filter policy is a JSON object containing properties that define which messages the subscriber receives. Amazon SNS supports policies that act on the message attributes or on the message body.

When you publish a message to a topic, Amazon SNS compares the message properties with the properties in the filter policy for each of the topic's subscriptions. If any of the properties match, Amazon SNS sends the message to the subscriber. Otherwise, Amazon SNS skips the subscriber without sending the message.

You can simplify your use of Amazon SNS by consolidating your message filtering criteria into your topic subscriptions. This allows you to offload the message filtering logic from subscribers and the message routing logic from publishers, eliminating the need to filter messages by creating a separate topic for each condition. You can use a single topic, differentiating your messages using attributes. Each subscriber receives and processes only the messages accepted by its filter policy.



Filtering by message attributes
In this section, you will create a new SQS queue for EU Orders, and create a subscription to the Orders SNS topic with a filter policy to route each message to the EU Orders SQS queue. To indicate EU Orders, you will assign an attribute, location, with a value eu-west to the message.

First, create a new SQS queue for EU Orders.

#### Step 1: Create the Orders-EU SQS queue
Create an Amazon SQS queue, called Orders-EU, to durably store EU Order messages.
1.Open the AWS Management Console for SQS,choose Create queue at the top of your queue list.
2. Enter a queue name Orders-EU,select Standard Queue for the type of queue you want to configure.
3. Choose Create queue.

PIC Create Queue

#### Step 2: Subscribe the Orders-EU SQS queue to the Orders SNS topic
1. On details screen, select Subscribe to Amazon SNS topic.
2. From the Choose a Topic drop-down list, select the Orders topic to which to subscribe the Orders-EU queue, and then choose Subscribe.
3. Verify that the Orders-EU SQS queue is successfully subscribed to the Orders SNS Topic.

   PIC

   The queue is subscribed to the topic.

#### Step 3: Create EUOrdersAttributes subscription filter policy
1. Open the AWS Management Console for SNS ,from the list of topics, choose the Orders topic.
2. In the Subscriptions search box, enter Orders-EU to find the Orders-EU SQS queue subscription.
3. Select the Orders-EU subscription and then choose Edit.
4. On the Edit subscription page, expand the Subscription filter policy section.
5. Select Message Attributes, then in the JSON editor field, paste the following filter policy into the JSON editor.
   {
  "location": [
    "eu-west"
   ]
  }



PIC

6. Choose Save changes.
   Amazon SNS applies your filter policy to the subscription.

#### Filtering by message body
Now you will learn how to filter messages based on their body. You will create a new SQS queue for book orders, and create a subscription to the Orders SNS topic with a filter policy to route each message to the BookOrders SQS queue. To indicate book orders, you will have an attribute with the value "category": "books" in the body of the message (please note that the message must be a valid JSON message for content filtering to work).

First, create a new SQS queue for book orders.

#### Step 1: Create the BookOrders SQS queue
Create an Amazon SQS queue, called BookOrders, to durably store book orders messages.
1. Open the AWS Management Console for SQS, choose Create queue at the top of your queue list.
2. Enter a queue name BookOrders,select Standard Queue for the type of queue you want to configure.
3. Choose Create queue.

   PIC


#### Step 2: Subscribe the BookOrders SQS queue to the Orders SNS topic
1. On details screen, select Subscribe to Amazon SNS topic.
2. From the Choose a Topic drop-down list, select the Orders topic to which to subscribe the BooksOrders queue, and then choose Save.
3. Verify that the BookOrders SQS queue is successfully subscribed to the Orders SNS Topic.

 PIC

 The queue is subscribed to the topic.

#### Step 3: Create BookOrdersBody subscription filter policy
1. Open the AWS Management Console for SNS,from the list of topics, choose the Orders topic.
2. In the Subscriptions search box, enter BookOrders to find the BookOrders SQS queue subscription.
3. Select the BookOrders subscription and then choose Edit.
4. On the Edit subscription page, expand the Subscription filter policy section.
5. Select Message Body option and in the JSON editor field paste the following filter policy into the JSON editor. Remember to use the Copy to clipboard icon in the upper-right corner.
   {
  "category": ["books"]
  }

6. Choose Save changes.

Amazon SNS applies your filter policy to the subscription.

### Review:
1. The OrdersQueue subscription does not have a filter policy, so the OrdersQueue SQS queue will receive all messages sent to the Orders SNS topic.

2. The Orders-EU subscription has a message attribute filter policy, so the Orders-EU SQS queue will only receive messages with a message attribute, location, with a value of eu-west.

3. The BookOrders subscription has a message body filter policy, so the BookOrders SQS queue will only receive messages with a message body containing the category with a value of books.

Next, using the Publish message functionality, you will generate test messages and verify their delivery to the appropriate queues.

## Testing
Test the system behavior by publishing different messages to the Orders SNS topic as described below.

#### Message 1: No message attributes, plain text body
1. Choose Publish message to publish a test message to the Orders SNS topic.
   PIC

#### Message 2: Non-matching message attributes, plain text body
1. For the attribute Type, select String. For the attribute Name, enter location. For the attribute Value, enter us-west.
2. Choose Publish message to publish the message to the Orders SNS topic.

PIC

#### Message 3: Matching message attributes, plain text body
For the attribute Type, leave the selection as String. For the attribute Name, enter location. For the attribute Value, enter eu-west.

Choose Publish message to publish the message to the Orders SNS topic.

#### Message 4: No message attributes, JSON text body with a non-matching category.
1. Do not add any attributes (delete any existing attributes) and enter the following JSON for the message body:
   {
    "category": "groceries",
    "value": 555
}
2. Choose Publish message to publish the message to the Orders SNS topic.
   PIC

#### Message 5: No message attributes, JSON text body with a matching "books" category
1. Do not add any attributes (delete any existing attributes) and enter the following JSON for the message body:
   {
  "category": "books",
  "value": 70

2. Choose Publish message to publish the message to the Orders SNS topic.


### Verify message delivery
1. Open the AWS Management Console for SQS  in a new tab or window, so you can keep this step-by-step guide open.
2. Note that the Orders queue shows 5 messages delivered, the Orders-EU queue only shows 1 message delivered and the BookOrders queue shows 1 message.

   PIC
You have successfully used subscription filters to route messages published to an SNS topic subscription and verified that it was sent to the correct SQS queue. 
