Building a Agora Live Streaming App Using a React Wrapper
The React wrapper library for Agora now has been available to a React developer community. React wrapper, often referred to as a higher order component, allows developers to access client objects and tracks' objects defined in Web SDK NG.
Before jumping right into the core topic regarding the development of a live streaming react app, we will shortly discuss new modifications of SDK extended from Agora Web SDK to Agora Web SDK NG. Then, we are going to explain how to use the React Wrapper library to develop a live streaming application.
Changes in SDK behaviors and API methods

Promises for asynchronous operation

The Agora Web SDK NG uses promises for asynchronous operation such as joining and leaving a channel, publishing and subscribing., etc. the Agora Web SDK, on the other hand,
tells users of the results for those async operations with callback or otherwise events.
Replacement of Stream object with Track object
Tracks are basically composed of video and audio. So, tracks in short make up a Stream. For simplicity, the modification has been made and Agora now replaces the Stream object with Track object. Agora Web SDK NG provides numerous interfaces for local and remote tracks. But, be noted that some methods and objects are only available locally and vice versa.

Rename events

For a better development experience, the consistency of event names are important, and Agora now renames events names. "For example, Agora renames "peer-online" and "peer-offline" as "user-joined" and "user-left", "stream-added" and "stream-removed" as "user-published" and "user-unpublished". For details, see API Change List."
There are several other important aspects of changes such as the improvements on event notification mechanism. you are strongly recommended to read further on those topics. For a more detailed explanation about the changes, you can review the official document which is available with the link down below.
Migrate Agora Web SDK to Agora Web SDK NG · Agora Web SDK NG
This guide helps developers who have implemented the Agora Web SDK in their apps to migrate from the Agora Web SDK to…agoraio-community.github.io
Creating an Account with Agora
First things first, you need to sign up at https://console.agora.io and log in to the dashboard. and create a project. Navigate to the Project List under the Project Management tab, and create a project by clicking the blue Create button.

Prerequisites

Node >= 10.16
npm >= 5.6
React ≥ 16.8

Preparation of Create-React-App

Open the terminal and execute the following command to create a React boilerplate project.

npx create-react-app my-app - template typescript
cd my-app

typescript is optional. Once you are done, cd into the directory of your app name, Then install react wrapper library using link below and npm package manager. After successfully installing this, you can run npm start and check the local host of 3000.
npm install git://github.com/AgoraIO-Community/agora-rtc-react#v1.0.0

Folder Structure

For the matter of simplicity, we are only using app.tsx and associated css defined in index.css. You can also access to the full code of this project at
https://github.com/TakaRaisonDetre/agora_blog_002

Step-by-Step Coding Tutorial

App.tsx

In order for us to use react wrapper for the SDK, we import followings interface from "agora-rtc-react" including "ClientConfig", "IAgoraRTCRemoteUser", "ICameraVideoTrack" and "IMicrophoneAudioTrack".
The wrapper provides you access to methods from SDK and you can employ react hooks to reach client and stream objects. We additionally import "AgoraVideoPlayer", "createClient" and "createMicrophoneAndCameraTracks" from "agora-rtc-react".
We also need to specify the configuration for the SDK client. The mode is specifically defined as "live" as a live streaming in this blog. You can also define "rtc" as a real time communication for a chat app. We're also using the vp8 codec for this application. Finally, we provide your appId as a string which you can obtain from your Agora management console previously configured for the project.
The createClient method with the above configuration permits us to reach an interface providing the local client with necessary functions for voice and video call including joining and leaving a channel, publishing tracks, or subscribing to tracks. With this react custom hook provided in the wrapper, we can easily access the createClient function like any other react custom hooks. Also, we need a string constant for your appId.

App component

First, we are going to use the useState statement for required states for the App component : inCall as boolean state, channelName as string state and host as boolean state. App.tsx has multiple react components. App component has child components: LiveStreamComponent and FormComponent where the view state is contingent upon the boolean state called "inCall". If the "inCall" state is true, the LiveStreamComponent is visible, otherwise the FormComponent is. VideoCall takes setInCall, channelName and host as props from App Component. ChannelForm also takes setInCall, setChannelName props and setHost props from App Component.

Next, we are going to define useClient for this app. createClient method in Web SDK creates and returns a client object and it can be called once each call session. This method takes a config parameter called "clientConfig" which defines the property of the client. The clientConfig is the object which has at least two properties : codec and mode.
The codec the Web browser uses for encoding and decoding.
"vp8": Sets the browser to use VP8 for encoding and decoding.
"h264": Sets the browser to use H264 for encoding and decoding.

Note: Set codec as "h264" as long as Safari is involved in the call.
Currently Agora Web SDK supports the following channel profiles:
"live": Sets the channel profile as live broadcast.
"rtc": Sets the channel profile as communication.

In order for us to create a local track object directly from the device's audio and video, the SDK provides several methods.
createCameraVideoTrack
createMicrophoneAudioTrack
createMicroPhoneAndCameraTracks

We then call useMicrophoneAndCameraTrack hook to reach createMicroPhoneAndCameraTrack method and attempt to access a ready state and an array of tracks. Once the tracks are initialized, the state for read is set to be true. Tracks contains an array of interfaces : IMicrophoneAudioTrack and ICameraVideoTrack.

VideoCall component

Next, we are going to create a VideoCall component which is the heart of this live streaming application. The component takes in three props from the app component including setInCall, channelName and host. The props called host is the prop which has a true state if the user's role is host.
IAgoraRTCRemoteUser[] array is the interface from SDK and it contains a list of remote users and we set the state of the list using useState from react. The start state is the boolean state which sets to be true when a user joins the channel.

We call the useClient hook to access the useClient method from the SDK in which it creates a client. Additionally, we destructure useMicroPhoneAndCameraTracks into a ready state and track array which has been described as above. Now we are ready to initial the client.
Initialization of Client

Employing the useEffect hook from react, we are going to define an init function that has a series of event listeners from SDK methods. When a remote user publishes an audio and video track, a user-published event has been triggered. This event has two parameters : one is user and the other is media type. Then we can initiate a subscription by calling subscribe. For user-unpublished events, we remove users from the array of users.

Lastly, we are going to use the join method which allows users to join a live streaming channel using required parameters including appID, channelName, token and uid which normally and tentatively set to be null. If the host is true, then we set the client role as Host and publish local tracks using the publish method. Then, we set the start state to be true.

In a JSX part of the component, we have two child components: Controls component and Videos Component which will be explained in the next section.

Videos Component

Since we are building a live streaming app, we only show host video and audio. We use the AgoraVideoPlayer component provided by wrapper to render a host video.

Controls Component

Control component is equipped with mute and unmute both audio and video functions as well as leaving channel functions. UseClient hook to acquire the client object. Mute function allows users to mute / unmute the audio/video using tracks method called setEnabled.
We use the leave method to leave the channel. The removeAllListeners method to remove the event listeners in order for an app to avoid memory leaks. and set state start and incall state to false.

ChannelForm Component
When inCall State is false, we render the tentatively designed simple form which takes a channel name as input from users, using onChange event handler called setChannelName.

You can employ any design style. But for reference we use below css and it is defined in index.css in the following link in the github.
https://github.com/TakaRaisonDetre/agora_blog_002/blob/master/src/index.css
