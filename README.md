# React Native Wearables

A React Native module with a common interface for interacting with wearables on iOS and Android.

### Getting started

* Browse to your `react-native` app. (see [Demo](#demo-app) for a ready made example)

* Install react-native-wearables.
`npm install react-native-wearables`

* Install the peer dependencies.
`npm install rn-apple-healthkit react-native-google-fitness`

* Link the native modules.
`react-native link`

* Edit `android/app/src/main/java/.../MainApplication.java`. The line that looks like `new GoogleFitPackage()` should be changed to `new GoogleFitPackage(BuildConfig.APPLICATION_ID)`. _(this is a temporary fix to a known react-native limitation)_

* Add the following lines to ios/<name of your app>/Info.plist – these are what iOS shows your users when requesting access to HealthKit data.

```
<key>NSHealthShareUsageDescription</key>
<string>Read and understand health data.</string>
<key>NSHealthUpdateUsageDescription</key>
<string>Share workout data with other apps.</string>
```

* Enable HealthKit in your iOS app's Capabilities in Xcode.

![Xcode Capabilities HealthKit](https://user-images.githubusercontent.com/56902/41662490-2dd69e6a-7499-11e8-8989-560d94683b61.png)

### Demo app

Go to [yldio/react-native-wearables-demo](https://github.com/yldio/react-native-wearables-demo) to check out a super simple demo app that runs on both iOS and Android.

## Data

`Data` is a common interface to the health and fitness data system repositories on iOS and Android, respectively HealthKit and Google Fitness.

It lets you write code like this which works on both iOS and Android.

```js
import { Data } from "react-native-wearables";

Data.authorize([Data.Types.heartRateBpm])
  .then(() =>
    Data.read(Data.Types.heartRateBpm, {
      startDate: new Date("2018-05-01"),
      endDate: new Date("2018-05-09")
    })
  )
  .then(samples => console.log(samples))
  .catch(error => console.error(error));
```

### Motivation

This project builds on the great work of existing iOS- and Android-specific React Native modules to access wearables data on each platform. It abstracts the platform differences into a common interface that makes it easy to create great cross-platform experiences with wearables data.

### API Documentation

#### `Data.Types`

An object of constants representing each of the data types supported by the module.

- `Data.Types.heartRateBpm`, heart rate samples, unit is BPM (beats per minute)

#### `Data.authorize(dataTypes)`

Launches the OS-specific dialog to request permission from the user to read / write the data types requested.

##### Example

```js
import { Data } from "react-native-wearables";

Data.authorize([Data.Types.heartRateBpm])
  .then(() => console.log("permission granted"))
  .catch(() => console.log("permission not granted"));
```

##### Arguments

- `dataTypes`, an array of data types constants, found in `Data.Types`.

##### Returns

A promise which resolves when and if the user granted the requested permission to your app and rejects if the user cancelled or didn’t grant permission.

#### `Data.read(dataType, options)`

Retrieves data samples from HealthKit on iOS and Google Fitness on Android. Will fail if called before `Data.authorize` has been called with success.

##### Example

```js
import { Data } from "react-native-wearables";

Data.read(Data.Types.heartRateBpm, {
  startDate: new Date("2018-05-01"),
  endDate: new Date("2018-05-09")
})
  .then(samples => console.log(samples))
  .catch(() => console.log("failed"));
```

##### Arguments

- `dataType`, a data type, found in `Data.Types`
- `options`, an object of options - `startDate`, a `Date` instance - `endDate`, a `Date` instance

##### Returns

An array of data points, where

- `value` is a number
- `startDate` is a Date ISO string
- `endDate` is a Date ISO string

```js
[
	{
		value: 80,
		startDate: "2018-06-06T13:59:47.375+0100",
		endDate: "2018-06-06T13:59:47.375+0100",
	},
	...
]
```
