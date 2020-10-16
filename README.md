# DJANGO CHANNELS

<https://channels.readthedocs.io/en/latest/introduction.html>

* Channels allows you to use WebSockets and other non-HTTP protocols in your Django site.
* For example you might want to use WebSockets to allow a page on your site to immediately receive updates from your Django server without using HTTP long-polling or other expensive techniques.

---

## Tutorial Part 1: Basic Setup

It will have two pages:

An index view that lets you type the name of a chat room to join.
A room view that lets you see messages posted in a particular chat room.

* Creating a project
* Add the index view
* Integrate the Channels library
  * pip install channels

---

## Tutorial Part 2: Implement a Chat Server

We will now create the second view, a room view that lets you see messages posted in a particular chat room.

* Add the room view
* Write your first consumer
* Enable a channel layer
  * docker run -p 6379:6379 -d redis:5
  * python3 -m pip install channels_redis

Channel layer provides the following abstractions:

* A channel is a mailbox where messages can be sent to. Each channel has a name. Anyone who has the name of a channel can send a message to the channel.
* A group is a group of related channels. A group has a name. Anyone who has the name of a group can add/remove a channel to the group by name and send a message to all channels in the group. It is not possible to enumerate what channels are in a particular group.
* In our chat application we want to have multiple instances of ChatConsumer in the same room communicate with each other. To do that we will have each ChatConsumer add its channel to a group whose name is based on the room name. That will allow ChatConsumers to transmit messages to all other ChatConsumers in the same room.

* When a user posts a message, a JavaScript function will transmit the message over WebSocket to a ChatConsumer. The ChatConsumer will receive that message and forward it to the group corresponding to the room name. Every ChatConsumer in the same group (and thus in the same room) will then receive the message from the group and forward it over WebSocket back to JavaScript, where it will be appended to the chat log.

---

## Tutorial Part 3: Rewrite Chat Server as Asynchronous

* Rewrite the consumer to be asynchronous
  * Synchronous consumers are convenient because they can call regular synchronous I/O functions such as those that access Django models without writing special code.
  * Asynchronous consumers can provide a higher level of performance since they don’t need to create additional threads when handling requests.

---

## Tutorial Part 4: Automated Testing

### Testing the views

These tests will ensure that:

* when a chat message is posted then it is seen by everyone in the same room
* when a chat message is posted then it is not seen by anyone in a different room

Setup

* Install the Chrome web browser, if you do not already have it.
* Install chromedriver.
  * brew cask install chromedriver
* Install Selenium. Run the following command:
  * python3 -m pip install selenium

TO RUN TESTS:

* python3 manage.py test chat.tests

---
