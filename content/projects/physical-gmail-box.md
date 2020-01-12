---
title: Physical Gmail Notifier
cover_image: ./images/gmail-notifier-cover.jpeg
description: "The Physical Gmail Notifier is a smart IoT device and accompanying app that alerts a user when a new message hits their inbox. The flag on the side of the mailbox raises and lowers at a 90° angles and the screen illuminates when a new message arrives. The screen displays the login information and the total number of new messages. The mailbox can be programed to respond only to specific gmail labels or filters."
---

The Physical Gmail Notifier is an IoT device and accompanying app that alerts a user when a new message arrives in their inbox. The flag on the side of the mailbox raises and lowers at a 90° angles and the screen illuminates when a new message arrives. The screen displays the login information and the total number of new messages. The mailbox can be programed to respond only to specific gmail labels or filters.

This project was developed in 2012.  The mailbox was developed using the [Arduino](https://arduino.cc) Uno microcontroller and several off-the-shelf hardware components.  The flag of the mailbox is raised and lowered using a **miniature servo motor**, user information is displayed using a 16x2 character **LCD display**, and a single button on the side of the device is used for user interaction.

The microcontroller was programed in the Arduino language (essentially C++ with some domain-specific libraries).

There is also an accompanying OSX application used to log into Gmail and communicate with the microcontroller over serial communication.

![Gmail Notifier OSX App](./images/mailbox-app.png)

The OSX application was written in Python 2 using [Tkinter](https://python.readthedocs.io/en/v2.7.2/library/tkinter.html) to draw the GUI and (imaplib)[https://docs.python.org/2/library/imaplib.html] to establish a connection with the Google's servers. It then used [PySerial](https://pypi.org/project/pyserial/) to communicate with the Arduino over a serial channel.

#### Python:
```python
conn = imaplib.IMAP4_SSL("imap.gmail.com", 993)
...
conn.login(user.get(),password.get())
setup()
send_username = user.get() + ">"
ser.write(send_username.encode('utf-8'))
```

It sends a `>` character as a termination identifier, that the Arduino looks for to identify the user's email address.

#### Arduino:
```cpp
while (Serial.available())
{
  delay(10);
  char c = Serial.read();

  if (c == ',')
  {
    break;
  }

  if (c == '>')
  {
    user = readString;
    readString = "";
    Serial.println(user);
    break;
  }

  readString += c;   
}
```

Using a 10ms delay, the Arduino reads each 8 bit `char` coming over the serial connection and concatenates them onto a `Arduino::String` until it reads a `>`.  (It would have been better to use a null-terminating character here, but I didn't have formal CS experience at the time).

#### The full project can be viewed here on [Github](https://github.com/p-gonzo/physical-gmail-notifier).