---
layout: post
title:  "React Native for Native Mobile Developers?"
tags: Android Native Multiplatform ReactNative React JS Declarative
---

Recently, I had to start working on a project based on the React Native framework. Being an Android developer for quite some time and having worked on iOS too, having read few of devs talking about why not to use react native for mobile app development, I kept wondering why not native. But since the whole team consisted of web developers, and they find it easy to develop using a web-like framework, they insisted on using React Native. Me being just a bit familiar with basic vanilla javascript and having no idea about React or React Native, I hesitated at first, but then I decided to take it as an opportunity to learn, I dived into it. It took almost a week for me to get the initial codebase running without the server disconnecting or failing to bundle and install. I was given the mostly Android-native side of sensor data reading and bridging with React Native and emitting events. Once done, now the challenge was to use different packages for getting foreground service, location provider, permissions, alarms, and workmanager. When I was going through these packages, I saw issues like no support for the latest Android versions, a lot of open issues that were not addressed, a few of them implemented only for iOS, etc.

Then I went the native route to almost implement everything, writing business logic and building a bridge with React Native UI. I also got to learn the declarative way of development along with more concepts like promises, async/await, arrow functions, and hooks. That even inspired me to get back to learning Jetpack Compose. As a developer, switching between frameworks or programming languages is not that hard if you understand the basics. And learning multiple technologies at the time when AI is almost taking over our jobs is need of the hour! I had once tried Node.js frameworks; now I may explore more JS-based frameworks, but I will always be a native mobile developer, Android lover:)

I highly recommend everyone to read these posts before making the decision of react native development vs native:
- [React Native at Airbnb: The Technology](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)
- [React Native: A retrospective from the mobile-engineering team at Udacity](https://engineering.udacity.com/react-native-a-retrospective-from-the-mobile-engineering-team-at-udacity-89975d6a8102)
