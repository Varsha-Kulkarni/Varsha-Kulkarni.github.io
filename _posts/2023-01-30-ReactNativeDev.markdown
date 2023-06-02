---
layout: post
title:  "React Native for Native Mobile Developers?"
tags: Android Native Multiplatform ReactNative React JS Declarative
---

Recently, I had to start working on a project based on the React Native framework. Being a Native Android developer for quite some time and having worked on iOS too, having read few of devs talking about why not to use react native for mobile app development, I was skeptical to build anything using React Native. But since the whole team consisted of web developers, and they find it easy to develop using a JS based framework, they insisted on using React Native. I, being just a bit familiar with basics of vanilla javascript and having no idea about React or React Native, hesitated at first, but then I decided to take it as an opportunity to learn, I dived into it. It took almost a week for me to get the initial codebase running without the server disconnecting or failing to bundle and install. At the outset, I was given the mostly Android-native side of sensor data reading and bridging with React Native and emitting events. Once done, now the challenge was to use different packages for getting foreground service, location provider, permissions, alarms, and workmanager. When I was going through these packages, I saw issues like abandoned packages or no support for the latest Android versions, a lot of open issues that were not addressed etc.

I went the native route to almost implement everything, writing business logic and just building a bridge with React Native UI (developed by other team members). Unlike Flutter, React Native runs based on JS bridge. Though these packages couldn't be used, I found a lot of help in implementing bridges through their codebases. Trust me, I am an expert building these Bridges now!
In the journey I also got to learn the declarative way of development along with more concepts like promises, async/await, arrow functions, and hooks. I slowly started making small changes to UI code as well, which then inspired me to get back to learning Native declarative UI toolkit, Jetpack Compose. 

As a developer, switching between frameworks or programming languages is not that hard if you understand the basics. And learning multiple technologies at the time when AI is almost taking over our jobs is need of the hour! I had once tried Node.js frameworks; now I may explore more JS-based frameworks, but I will always be a native mobile developer, Android lover:)

I highly recommend everyone to read these posts before making the decision of react native development vs native:
- [React Native at Airbnb: The Technology](https://medium.com/airbnb-engineering/react-native-at-airbnb-the-technology-dafd0b43838)
- [React Native: A retrospective from the mobile-engineering team at Udacity](https://engineering.udacity.com/react-native-a-retrospective-from-the-mobile-engineering-team-at-udacity-89975d6a8102)
