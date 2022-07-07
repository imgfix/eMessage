# eMessage
This is project allows Android clients to communicate using iMessage.

## Video Demonstration
<a href="http://www.youtube.com/watch?feature=player_embedded&v=rcoX-uiDNs4
" target="_blank"><img src="http://img.youtube.com/vi/rcoX-uiDNs4/0.jpg"
alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>

## Disclaimer
This project is a super alpha prototype. I am releasing it because I need your help. :)

## About

There are 4 parts to the eMessage project.
- **messages.applescript**
- **Java Web Server (JWS)**
- **OSX Client**
- **Android Client**

The [messages.applescript](./messages.applescript) is arguably the most important part of the project. It is what makes sending iMessages possible. This script is what sends an iMessage message.

The [OSX Client](./eOSXClient) & [JWS](./JavaWebServer) run on any OSX machine (Macbook, Mac, etc.).

If the [OSX Client](./eOSXClient) detects any changes to the "Messages" sqlite database file where a new message has been received, it will send the JWS a socket *'incoming'* JSON message. Incoming messages are detected from a change from the sqlite chat.db of the 'Messages' app whose default location is ~/Library/Messages/chat.db. I have provided a database schema to help visualize the database in the pdf, [MessagesSchema.pdf](./MessagesSchema.pdf).

The [JWS](./JavaWebServer) is what connects the OSX Client to the Android client. If the JWS receives a socket 'outgoing' JSON message from the Android client, it will pass it to the OSX Client to tell it to send the iMessage that was requested from the Android. If the JWS recieves a socket *'incoming'* JSON message from the OSX Client, it means the OSX Client has detected a new message and wants the JWS to notify the Android client.

The [Android client](./eMessage-Android/) connects to a socket that whose IP address is of the OSX device that is running the JWS and OSX Client. It then sends JSON messages to the JWS using that socket. It also receives JSON to show in list of any new incoming iMessages.

## Requirements
- OSX device
- Public IP for OSX device
- iCloud account w/ iMessage enabled
- Java JDK
- Android device (4.0+)

#### Optional Requirement
- IntelliJ IDEA
- Android Studio

## Set up
1. Open the Messages application and add your iCloud account in Messages > Preferences > Accounts.
2. Clone the eMessage project onto your OSX Device.
3. Edit the *socketAddress* value in [eOSXClient/src/Constants.java](./eOSXClient/src/Constants.java) to your public IP address that is linked to your OSX device.

You can do either terminal or GUI setup from here. If you will be working on this you will eventually need to set it up in IntelliJ/Android Studio or get it to work in your IDE of choice.

### Via Terminal
1. `cd eMessage/ && cp messages.applescript ~`
2. In one terminal window/screen: `cd eMessage/JavaWebServer && gradle run`. This will need to remain open.
3. In another window/screen: `cd eMessage/eOSXClient && gradle run`. This will need to remain open.
4. In a final window/screen: `cd eMessage/eMessage-Android && gradle installDebug`. This is simply to compile and load the application.

### Via GUI
1. Move [messages.applescript](./messages.applescript) to your ~ home directory (/Users/<username>).
2. Open the [JavaWebServer/](./JavaWebServer) as a project in IntelliJ. Run the [Server](./JavaWebServer/src/Server.java) class.
3. Open the [eOSXClient/](./eOSXClient) as a project in IntelliJ.
4. Run [eOSXClient](eOSXClient/src/eOSXClient.java) class.
5. Open [eMessage-Android/](./eMessage-Android/) as a project in Android Studio.
6. Compile apk to any Android device.

## WishList
Since I've moved onto other projects and haven't had time to finish this, there are few things that wanted to implement. It would be nice to combine the OSX Client and the JWS. Also the OSX Client sometimes timesout and loses socket connection over a 2 hour+ period. I'm not sure if this is my own internet, the OSX it self.

Also it is possible to recieve group messaging, just not send it. Unfortunately I couldnt figure out an applescript to send to multiple clients in a single conversation thread. You definitely can send multiple *individual* messages at once but that still isn't in the same conversation thread. The only reason why we can receive is because it's just a chat table in the sqlite database on the OSX device.

Photo/Video messages are definitely possible too. There is a place in the SQLite table named *message_attachment_join* and *attachment*. It just has to link that in some kind of protocol for the OSX, JWS, and Android client to implement.

There can be clients for any platform like Windows, web, BlackBerry OS, Windows Phone, a toaster, etc. Since the JWS, and OSX Client just take in a JSON to send & receive, one just has to implement a client that is similar to the way the Android client communicates with it.
