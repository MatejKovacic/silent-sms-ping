# Silent SMS detector

Android application for detecting (and sending) *Class-0* silent SMS messages (binary messages) that does not require rooted device.

## What is silent SMS?

A silent SMS (*Short Message Service*) is a type of text message that is sent to a mobile phone without the knowledge or consent of the phone's user. Unlike regular SMS messages, silent SMS messages are invisible and do not trigger any notification or sound on the target phone.

This functionality is not some secret hack, but actually a part of the two mobile telecommunications standards, namely [3GPP 23.040 (originally GSM 03.40)](https://en.wikipedia.org/wiki/GSM_03.40) and [3GPP 23.038 (originally GSM 03.38)](https://en.wikipedia.org/wiki/GSM_03.38).

By sending silent SMS on a target phone, a sender can detect whether mobile phone is online or offline. Or more specifically - **a sender can detect if target SIM card is connected to the network or not**. That allows attackers to determine whether a specific mobile number is being active or not.

However, silent SMS **could also be used to determine the location of the target mobile device**. When a silent SMS is sent to the target device, it forces it to reveal its location, because it makes a connection to the nearest (available) serving base station in cellular network. In that case silent SMS messages could be used by law enforcement agencies for surveillance and tracking purposes, because they allow them to locate the position of a mobile phone without alerting the user. It is known that in the past, [German police has been using silent SMSes to track the suspects](https://edri.org/our-work/edrigramnumber10-2silent-sms-tracking-suspects/).

### SMS message types

There are several types of SMS messages. The SMS standard define a number of *binary SMS messages* that are typically send to the SIM card instead of the user. Each of these can be considered a silent SMS. However, our application *Silent SMS detector* can detect only certain type of them - *Class-0* SMS messages.

- **Class 0 SMS**
This message is displayed on the mobile phone immediately and a message delivery report is sent back to the sender. The message does not have to be saved in the mobile phone or on the SIM card (unless selected to do so by the mobile user). This type is also referred to as *Flash SMS*. Certain parameters (flags) for this SMS type results in the message not being displayed on the phone (and not saved on the phone), but the sender still receives a receipt. In that case *Class-0* message serves as silent SMS message. *Silent SMS detector* application can detect **only these (binary) messages**.

- **Class 1 SMS**
This is a normal SMS message. This message is stored in the memory of the mobile phone or the SIM card (depending on memory availability).

- **Class 2 SMS**
This type of message carries SIM card data. The SIM card data must be successfully transferred prior to sending acknowledgment to the sender (usually operator). An error message is sent to the sender if this transmission is not possible. Usually it is used for sending some technical data from the mobile operator to a SIM card. The receipt means that the data has been successfully transferred to the SIM card.

- **Class 3 SMS**
These are normal SMS messages that are forwarded from the receiving entity to an external device. The delivery acknowledgment is sent to the sender regardless of whether or not the message was forwarded to the external device. 

- **Type 0 SMS**
These are true silent SMS messages that do not show any notification on the phone, but return a delivery receipt to the sender. The `TP_PID` field in these messages is set to the value `0x40`. The purpose of the message is exclusively one - tracking users. The application *Silent SMS detector* **cannot detect these messages**.

In May 2010, Google [made a change in the Android code](https://android-review.googlesource.com/c/platform/frameworks/base/+/14069) to keep Type-0 SMS messages completely hidden. This means that these messages do not appear anywhere, are not saved on the phone and do not show any notification to the recipient. In theory, it would be possible to detect these messages by changing the Android code, however this would likely mean that your Android device is not compliant to mobile stadards. However, [research has shown](https://akaki.io/2022/transmission_and_detection_of_silent_sms_in_android) that receiving a Type-0 message triggers a record in Android logs (`GsmInboundSmsHandler: Received short message type 0, do not display or store. Send ACK.`). Unfortunately, this requires rooted Android device. As mentioned, the application *Silent SMS detector* cannot detect these messages.

## What is this application doing (and *what not*)?

This application, which is a fork of [Android Silent SMS Ping](https://github.com/itds-consulting/android-silent-ping-sms), can send silent SMS messages to determine if a target SIM card (phone number) is active or not. It can also detect received silent SMS messages and alert user that he received silent SMS. However, it is handling only *Class 0* SME messages and not *Type 0* SMS messages.

The application is running on new Android devices and does not require rooted device.

<img src="notification1.jpg" alt="Silent SMS notification" width="300"/>

It is important to understand, that receiving silent SMS **does not necessary mean you are being targeted by some malicious actor**. Silent SMS messages [could be used for various technical reasons](https://nickvsnetworking.com/gsm-with-osmocom-silent-sms-silent-calls/) and receiving a silent SMS is not a good indicator of being targeted by your cell carrier, government or hackers. These messages can also be used to send binary content such as ringtones, logos, or WAP Push messages as well as Over The Air (OTA) programming or configuration data. For instance, silent SMS messages could be sent to your SIM card for **roaming purposes**. On most of the SIM cards, there are fields defining preferred networks. The operator controlled PLMN list (Public land mobile network), so called OPLMN, is often updated by the home network operator over the air (OTA). This happens usually whenever you enter a new country. In that case binary SMS will be sent to your phone and *Silent SMS detector* will detect that SMS. However that does not means you are being tracked or that some bad thing is happening.

<img src="main_screen.png" alt="Main screen of Silent SMS detector" width="300"/>

On the other side, there are several other possibilities of mobile users tracking, so **the fact that you did not receive silent SMS does not means you are not being tracked**. If you want to avoid tracking, you should turn on the airplane mode (or switch your mobile phone off), but this of course heavily degrades user experience, so it is not really practical.

Oh, and BTW, did you know that your SIM card can be sending SMS messages without your knowledge? In 2021 David Allen Burgess found out that SIM cards can send data off your phone [without you or your phone's operating system knowing](https://www.youtube.com/watch?v=0Em-J_3QYu4). There is no public documentation about these messages, you can not see them and the mobile operators won't talk about it. In the investigated case, [Burgess found out](https://medium.com/telecom-expert/what-is-at-t-doing-at-1111340002-c418876c212c) that SIM card has been sending different data, including IMEI number of the current phone and IMEI number of the **previous phone** where SIM card has been inserted! So there are several things happening "behind the scenes" that users usually do not know about.

<img src="notification2.jpg" alt="Silent SMS detector" width="80"/>

**Anyway, notification that you received silent SMS message does not necessarily means something bad is happening, and absence of this notification does not means that you are safe from tracking.**

However, if you want to have greater transparency of what is happening "behind the scenes", *Silent SMS detector* could be interesting application. Because silent SMSes are meant to stay hidden from you, and with this application you can detect some of them. *Isn't that cool?*

### History

The original application, called [Android Silent SMS Ping](https://github.com/itds-consulting/android-silent-ping-sms) has been first developed in 2016. Unfortunately, it has not been maintained for several years and the original author archived it's Github repository in 2020.

In 2023 appication has been still accessible through F-Droid, but the Virustotal analysis has shown that APK has been infected.

In the beginning of 2023 we started a new development. In the first stage we have updated SDK (to version 33) and Java (to version 11). We have set up application permissions compatible with modern Android systems and improved notifications. We also designed new application icon.

Currently application is fully working (you can install it from APK below or compile it with Android Studio by yourself), but it still looks like the old one.

So in the second stage we plan to implement new design of the application. Later some new functionalities will be added. Specifically, we would like to implement data collection for threat analytics in order to collect the data about silent SMS messages and get some data which could help us estimate the scope of the problem. Stay tuned and check out the issues!

Application is now being developed by [Jure Poljšak](https://github.com/barracuda-fsh) (programming), [Matej Kovačič](https://github.com/MatejKovacic) (architecture and a little bit of programming) and Vesna Trenchovska (design).

### License

The project is licensed under the [GNU General Public License version 3 (or newer)](https://github.com/MatejKovacic/silent-sms-ping/blob/master/LICENSE).

### APK download (for testing)

Since this application is under heavy development, you can not install this application from Play store yet.

But you can download [testing APK from 22-04-2023](https://github.com/MatejKovacic/silent-sms-ping/blob/master/silent-sms-app-debug_22-04-2023.apk) and install it on your device. Click download button to get it on your device and then you can install APK directly. Please note that on your Android device you should allow installing unknown apps in that case, because APK is not digitally signed.

Another option is to clone this repository on your computer, install *Android Studio*, compile an app by yourself, and install it directly to your Android device.

You are of course always welcome to inspect the source code of the application in order to check that it does not contain some malicious code. And you can also contribute your code or ideas to the project.
