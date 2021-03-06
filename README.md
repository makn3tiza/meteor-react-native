# Meteor React Native
A set of packages allowing you to connect your React Native app to your Meteor server, and take advantage of Meteor-specific features like accounts, reactive data trackers, etc.

[Full API Documentation](/docs/api.md)

If you're new to React Native, you can view a guide to using React Native with Meteor on the [Official Meteor Guide](https://guide.meteor.com/react-native.html)

# New Package Name
Meteor React Native is now published under `@meteorrn/core`. We will continue to publish updates to the `meteor-react-native` repository until `2.1.0`. We recommend updating to the new package name as soon as possible.

# Installation
1. `npm install --save @meteorrn/core`
2. Confirm you have peer dependencty `@react-native-community/netinfo` installed
3. Confirm you have `@react-native-community/async-storage@>=1.8.1` installed. If you are using Expo, or otherwise cannot use `@react-native-community/async-storage`, see *Custom Storage Adapter* below.


### A note on AsyncStorage
This package uses `@react-native-community/async-storage` by default. This may cause issues if you are using certain React Native versions, or if you are using Expo. To use a custom AsyncStorage implementation, pass it as an option in `Meteor.connect`:

```javascript
import { AsyncStorage } from 'react-native';

// ...

Meteor.connect("wss://myapp.meteor.com/websocket", { AsyncStorage });
```

If you are using the `AsyncStorage` API yourself, its important that you use the same version that MeteorRN is using, or issues could be caused due to the conflicting versions. Make sure you are using the same AsyncStorage you pass into Meteor (or `@react-native-community/async-storage` if you aren't passing anything), or you can use [MeteorRN's package interface](#package-interface). 

# Basic Usage

```javascript
import Meteor, { Mongo, withTracker } from '@meteorrn/core';

let MyCol = new Mongo.Collection("mycol");

Meteor.connect("wss://myapp.meteor.com/websocket"); // Note the /websocket after your URL 

class App extends React.Component {
    render() {
        let {myThing} = this.props;
        
        return (
            <View>
                <Text>Here is the thing: {myThing.name}</Text>
            </View>
        );
    } 
}

let AppContainer = withTracker(() => {
    Meteor.subscribe("myThing");
    let myThing = MyCol.findOne();
    
    return {
        myThing
    };
})(App)

export default AppContainer;
```

# Companion Packages

The `@meteorrn/core` package has been kept as light as possible. To access more features, you can use companion packages.

Companion packages are NPM packages that are made specifically to extend MeteorRN. And since Atmosphere isn't available in React Native, MeteorRN uses companion packages that contain the client-side/relevant code from a Meteor package to allow your MeteorRN app to use the same package API as your Meteor app.

Native Feature Packages:
- [`@meteorrn/local`](/companion-packages/meteorrn-local)(Beta): Package for storing of data locally that works seamlessly with MeteorRN by injecting data into a local minimongo collection

Atmosphere Packages:
- [`@meteorrn/ndev-mfa`](/companion-packages/meteorrn-ndev-mfa)(Beta): Package that allows your RN app to work with `meteor/ndev:mfa`

Planned/Upcoming Packages:
- `@meteorrn/queued-calls`: Package that allows you to queue Meteor calls that will be performed when internet/server is available

If you have an idea for a companion package, please open an issue. If you would like to publish your own companion package, we recommend a package name with the prefix `mrn-`. 

# Compatibility
For React Native >=0.60.0 use this package

For React Native <0.60.0 use [react-native-meteor](https://github.com/inProgress-team/react-native-meteor).

**Migrating from `react-native-meteor`:**
- cursoredFind is no longer an option. All .find() calls will return cursors (to match Meteor)
- `MeteorListView` & `MeteorComplexListView` have been removed
- `CollectionFS` has been removed
- `createContainer` has been removed
- Mixins (`connectMeteor`) have been removed
- `composeWithTracker` has been removed

# Changelog
The [GitHub Releases Tab](https://github.com/TheRealNate/meteor-react-native/releases) includes a full changelog

# Package Interface

To ensure that MeteorRN companion packages use the same versions of external packages like AsyncStorage as the core, `@meteorrn/core` provides a package interface, where companion packages can access certain packages. Currently package interface returns an object with the following properties:
- AsyncStorage 

### Usage
````
import Meteor from '@meteorrn/core';

const {AsyncStorage} = Meteor.packageInterface();
````
