# AWS Messaging Tips

## 1. Consuming SNS messages in your code running on EC2 or other server

**TL;TR** SNS messages cannot be consumed by the code running on the EC2 instance(or other host) directly.

“Amazon Simple Notification Service (Amazon SNS) is a web service that coordinates and manages the delivery or sending of messages to subscribing endpoints or clients.” 

![SNS Overview](https://github.com/szaitsev/aws-notes/blob/master/aws-messaging/images/img10.png)

Conceptually, It sounds exactly like standard ActiveMQ topic, but in reality it does not. SNS is working with predefined type of consumers. 
SNS has ability to call HTTP(s) endpoints, so it could be possible to make it deliver messages to the REST-like service. However, it does not allow to use “internal” addresses, basically SNS is not aware of you VPC and routing inside VPC, so you should make your HTTP(S) endpoint publicly available from the internet, which definitely is not working for many cases.
So, the only possible solution to implement consumption of SNS messages is via SQS subscriber. So, the integration will look like this:

![SNS Messages Consumption via SQS](https://github.com/szaitsev/aws-notes/blob/master/aws-messaging/images/img20.png)

#### Links:
General information:  https://docs.aws.amazon.com/sns/latest/dg/welcome.html

## 2. ActiveMQ Virtual Topic Implementation in AWS.

Virtual destinations in ActiveMQ are used to decouple producer and consumer(s) even more than regular publish-subscribe pattern.
One of use cases we have right now: 
Email notifications system need to notify client systems about email sending status. Email notifications send message to the topic, which has virtual queues attached to one per client application, each queue is consumed by single application. This way Notifications system (producer) does not know how many client applications (consumers) are really subscribed and listening. But each one guaranteed to get a copy of the message.

image 3

#### Links:
ActiveMQ virtual destinations: http://activemq.apache.org/virtual-destinations.html



