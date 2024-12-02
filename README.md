# Event Driven Architecture Hands-On

   
## Event-driven with SNS
Event-driven applications can be built using Amazon Simple Notification Service (SNS) alongside Amazon EventBridge. Amazon SNS is ideal for scenarios requiring high throughput or low latency messaging between applications or microservices, as it supports virtually unlimited throughput. It's also well-suited for applications demanding extensive fan-out to thousands or millions of endpoints. Messages in Amazon SNS are unstructured and can be formatted as needed.
1. Simple pub/sub
2. Message filtering
3. Advanced message filtering

## 1. Simple pub/sub
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/pub-sub.png)

In this module, you will start by building a simple pub/sub implementation. An Amazon SNS topic will be used to publish events to an Amazon SQS queue subscriber. This will allow to easily verify the successful delivery of the messages.


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

2.	Select Send and receive messages, On details screen, choose Poll for messages.
[Amazon SQS begins to poll the messages in the queue. The dialog box displays a message from the queue. A progress bar at the bottom of the dialog box displays the status of the message's visibility timeout].

The following example shows the ID, Sent, Size and Receive Count columns.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/PollMesage.png)

4.	Before the visibility timeout expires, select the message that you want to delete and then choose Delete 1 Message.
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/bef5cf0158c2656e80f5843379b1b59532dc22bf/assets/Event-driven%20with%20SNS/DeleteMessage.png)

** Successfully published a message to an SNS topic and verified that it was sent to the SQS queue subscription. 




## 2. Message Filtering
### What is Amazon SNS message filtering?

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MessageFiltering.png)

By default, an Amazon SNS topic subscriber receives every message published to the topic. To receive a subset of the messages, a subscriber must assign a filter policy to the topic subscription.A filter policy is a JSON object containing properties that define which messages the subscriber receives. Amazon SNS supports policies that act on the message attributes or on the message body.

When you publish a message to a topic, Amazon SNS compares the message properties with the properties in the filter policy for each of the topic's subscriptions. If any of the properties match, Amazon SNS sends the message to the subscriber. Otherwise, Amazon SNS skips the subscriber without sending the message.

You can simplify your use of Amazon SNS by consolidating your message filtering criteria into your topic subscriptions. This allows you to offload the message filtering logic from subscribers and the message routing logic from publishers, eliminating the need to filter messages by creating a separate topic for each condition. You can use a single topic, differentiating your messages using attributes. Each subscriber receives and processes only the messages accepted by its filter policy.


Filtering by message attributes
In this section, you will create a new SQS queue for EU Orders, and create a subscription to the Orders SNS topic with a filter policy to route each message to the EU Orders SQS queue. To indicate EU Orders, you will assign an attribute, location, with a value eu-west to the message.

#### Step 1: Create the Orders-EU SQS queue
Create an Amazon SQS queue, called Orders-EU, to durably store EU Order messages.
1.Open the AWS Management Console for SQS,choose Create queue at the top of your queue list.
2. Enter a queue name Orders-EU,select Standard Queue for the type of queue you want to configure.
3. Choose Create queue.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-Queuecreation.png)

#### Step 2: Subscribe the Orders-EU SQS queue to the Orders SNS topic
1. On details screen, select Subscribe to Amazon SNS topic.
2. From the Choose a Topic drop-down list, select the Orders topic to which to subscribe the Orders-EU queue, and then choose Subscribe.
3. Verify that the Orders-EU SQS queue is successfully subscribed to the Orders SNS Topic.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-SNSTopic.png)

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

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-EUOrderAttribute.png)

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

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-BookOrders.png)


#### Step 2: Subscribe the BookOrders SQS queue to the Orders SNS topic
1. Select Subscribe to Amazon SNS topic On details screen.
2. From the Choose a Topic drop-down list, select the Orders topic to which to subscribe the BooksOrders queue, and then choose Save.
3. Verify that the BookOrders SQS queue is successfully subscribed to the Orders SNS Topic.
 

#### Step 3: Create BookOrdersBody subscription filter policy
1. Open the AWS Management Console for SNS,from the list of topics, choose the Orders topic.
2. In the Subscriptions search box, enter BookOrders to find the BookOrders SQS queue subscription.
3. Select the BookOrders subscription and then choose Edit,expand the Subscription filter policy section.
5. Select Message Body option and in the JSON editor field paste the following filter policy into the JSON editor. 
   {
  "category": ["books"]
  }

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-BookOrdersBody.png)


6. Choose Save changes.


### Let's Review:
1. The OrdersQueue subscription does not have a filter policy, so the OrdersQueue SQS queue will receive all messages sent to the Orders SNS topic.
2. The Orders-EU subscription has a message attribute filter policy, so the Orders-EU SQS queue will only receive messages with a message attribute, location, with a value of eu-west.
3. The BookOrders subscription has a message body filter policy, so the BookOrders SQS queue will only receive messages with a message body containing the category with a value of books.

## Testing
Test the system behavior by publishing different messages to the Orders SNS topic as described below.

#### Message 1: No message attributes, plain text body
1. Choose Publish message to publish a test message to the Orders SNS topic.
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-Msg1.png)

#### Message 2: Non-matching message attributes, plain text body
1. For the attribute Type, select String. For the attribute Name, enter location. For the attribute Value, enter us-west.
2. Choose Publish message to publish the message to the Orders SNS topic.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-Msg2.png)


#### Message 3: Matching message attributes, plain text body
For the attribute Type, leave the selection as String. For the attribute Name, enter location. For the attribute Value, enter eu-west.
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-Msg3.png)

Choose Publish message to publish the message to the Orders SNS topic.

#### Message 4: No message attributes, JSON text body with a non-matching category.
1. Do not add any attributes (delete any existing attributes) and enter the following JSON for the message body:
   {
    "category": "groceries",
    "value": 555
}
2. Choose Publish message to publish the message to the Orders SNS topic.
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-Msg4.png)


#### Message 5: No message attributes, JSON text body with a matching "books" category
1. Do not add any attributes (delete any existing attributes) and enter the following JSON for the message body:
   {
  "category": "books",
  "value": 70
![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-Msg5.png)

2. Choose Publish message to publish the message to the Orders SNS topic.


### Verify message delivery
1. Open the AWS Management Console for SQS  in a new tab or window, so you can keep this step-by-step guide open.
2. Note that the Orders queue shows 5 messages delivered, the Orders-EU queue only shows 1 message delivered and the BookOrders queue shows 1 message.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/2b57d9a0327c80cd6092c1cb968f793a14ccc338/assets/Event-driven%20with%20SNS/MF-VerifyMsgDlvry.png)

** Successfully used subscription filters to route messages published to an SNS topic subscription and verified that it was sent to the correct SQS queue. 


## 3. Advance Message Filtering
In the previous section, you explored basic message filtering using string comparisons and demonstrated the SNS routing behavior using various message attribute configurations to verify message delivery. Next, you will expand on basic message filtering using additional comparison techniques and use numeric comparisons to route extra large Orders (quantity is 100 or greater).

#### Step 1: Create the Orders-XL SQS queue
Create an Amazon SQS queue, called Orders-XL, to durably store XL Order messages.

1. Open the AWS Management Console for SQS,choose Create New Queue at the top of your queue list.
2. Enter a queue name Orders-XL,select Standard Queue for the type of queue you want to configure.
3. Choose Create queue,The Orders-XL queue is created.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-Orders-XL.png)

#### Step 2: Subscribe the Orders-XL SQS queue to the Orders SNS topic
1. From the list of queues, choose the Orders-XL queue to which to subscribe the Orders Amazon SNS topic.
2. Select Subscribe to Amazon SNS Topic.From the Choose a Topic drop-down list, select the Orders topic to which to subscribe the Orders-XL queue, and then choose Save.
4. Verify that the Orders-XL SQS queue is successfully subscriber to the Orders SNS Topic and choose OK.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-OrdersXL-SNSTopic.png)
The queue is subscribed to the topic.

#### Step 3: Create Orders-XL subscription filter policy
1. Open the AWS Management Console for SNS,from the list of topics, choose the Orders topic.
2. In the search box, enter Orders-XL to find the Orders-XL SQS queue subscription.
3. Select the Orders-XL subscription and then choose Edit and expand the Subscription filter policy section.
5. Select Message Attributes, then in the JSON editor field, paste the following filter policy into the JSON editor.
   {
  "quantity": [
    {
      "numeric": [">=", 100]
    }
  ]
   }
 6. Choose Save changes.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-Orders-XL-FilterPolicy.png)

    Amazon SNS applies your filter policy to the subscription.

In this section, you will combine an EU string prefix comparison with the XL numeric comparison to route extra large, EU Orders using an AND comparison.

#### Step 4: Create the Orders-XL-EU SQS queue
Create an Amazon SQS queue, called Orders-XL-EU, to durably store XL EU Order messages.
1. Open the AWS Management Console for SQS,choose Create New Queue at the top of your queue list.
2. Enter a queue name Orders-XL-EU,select Standard Queue for the type of queue you want to configure.
3. Choose Create queue,The Orders-XL-EU queue is created.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-Orders-XL-EU.png)

#### Step 5: Subscribe the Orders-XL-EU SQS queue to the Orders SNS Topic
1. From the list of queues, choose the Orders-XL-EU queue to which to subscribe the Orders Amazon SNS topic.
2. From Queue Actions, select Subscribe Queue to SNS Topic.
3. From the Choose a Topic drop-down list, select the Orders topic to which to subscribe the Orders-XL-EU queue, and then choose Subscribe.
4. Verify that the Orders-XL-EU SQS queue is successfully subscribed to the Orders SNS Topic.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-OrdersXL-EU-SNSTopic.png)

The queue is now subscribed to the topic.

#### Step 6: Create Orders-XL-EU subscription filter policy
1. Open the AWS Management Console for SNS  in a new tab or window, so you can keep this step-by-step guide open.
2. From the list of topics, choose the Orders topic.
3. In the search box, enter Orders-XL-EU to find the Orders-XL-EU SQS queue subscription.
4. Select the Orders-XL-EU subscription and then choose Edit and expand the Subscription filter policy section.
6. In the JSON editor field, paste the following filter policy into the JSON editor.
   {
  "location": [
    { "prefix": "eu" }
  ],
  "quantity": [
    {
      "numeric": [">=", 100]
    }
  ]
}

7. Choose Save changes.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-Orders-XL-EU-FilterPolicy.png)

Amazon SNS applies your filter policy to the subscription.

#### Step 7: Publish test messages using the Publish message function
1. Select the Topics in the left pane of the Amazon SNS service.
2. Select Orders topic from the list of Topics.
3. Click on Publish message.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-Orders-XL.png)

#### Message 1: Non-matching numeric message attributes
1. Choose Add attribute.
2. For the Message attribute, select type Number. For the attribute Name, enter quantity. For the attribute Value, enter 50.
3. Choose Publish message to publish the message to the Orders SNS topic.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-Msg1.png)

#### Message 2: Matching numeric message attributes
1. For the Quantity attribute, enter 100.
2. Choose Publish message to publish the message to the Orders SNS topic.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-Msg2.png)

#### Message 3: Matching AND comparison message attributes
1. Choose Add attribute.
2. For the attribute Type, select as String. For the attribute Name, enter location. For the attribute Value, enter eu-west.
3. Choose Publish message to publish the message to the Orders SNS topic.

 
#### Step 8: Verify message delivery
1. Open the AWS Management Console for SQS,observe the list of Messages Available for the queues.
 - The OrdersQueue queue shows 3 messages delivered, because the Orders subscription has no filter policy.
 - The Orders-EU queue shows 1 message delivered, because one message was published with location = eu-west.
 - The Orders-XL queue shows 2 messages delivered, because two messages were published with quantity = 100.
 - The Orders-XL-EU queue shows 1 message delivered, because one message was published with quantity = 100 AND location = eu-west.

![img](https://github.com/Awadheshks/Event-Driven-Architecture/blob/d95c3961517f4e31077e5c1803c37c8c79a00d45/assets/Event-driven%20with%20SNS/AMF-VerifyMsgDelivery.png)

** Successfully used subscription filters to route messages published to an SNS topic subscription and verified that it was sent to the correct SQS queue.
