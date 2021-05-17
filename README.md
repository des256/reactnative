# reactnative
React Native experiment space

## Init Environment

### Install React Native

```
npm install -g react-native-cli
npm install -g react-native-config
```

### Create Android Signing Key

Edit `~/.gradle/gradle.properties`:

```
MY_RELEASE_STORE_FILE=my_release.keystore
MY_RELEASE_KEY_ALIAS=my_release
MY_RELEASE_STORE_PASSWORD={some 32-character password}
MY_RELEASE_KEY_PASSWORD={some other 32-character password}
```

Generate keystore key file:

```
keytool -genkey -v -keystore ~/.gradle/my_release.keystore -alias my_release -keyalg RSA -keysize 2048 -validity 10000
```

## Init

```
react-native init APP_NAME
cd APP_NAME
```

### Enable vendor name in Android

In `/android`, replace all `com.VENDORapp` with `com.VENDOR.app`.

Update directory structure:

```
mkdir android/app/src/main/java/com/VENDOR
mv android/app/src/main/java/com/VENDORapp android/app/src/main/java/com/VENDOR/app
```

### Make the necessary env files

Create `.env.development` in project root:

```
APP_ID=com.VENDOR.app
APP_ENV=development
APP_VERSION=0.1.0
APP_BUILD=1
API_HOST=http://api.app.VENDOR.local
```

And `.env.staging` in project root:

```
APP_ID=com.VENDOR.app
APP_ENV=staging
APP_VERSION=0.1.0
APP_BUILD=1
API_HOST=http://api.app.VENDOR.staging
```

And `.env.production` in project root:

```
APP_ID=com.VENDOR.app
APP_ENV=production
APP_VERSION=0.1.0
APP_BUILD=1
API_HOST=http://api.app.VENDOR.com
```

### Link signing key to project

With a symlink:

```
ln -s ~/.gradle/my_release.keystore android/app/my_release.keystore
```

### Configure the gradle build

In file `android/app/build.gradle`, add at line 2:

```
apply from: project(':react-native-config').projectDir.getPath() + "/dotenv.gradle"
```

And replace parts of `defaultConfig`:

```
applicationId project.env.get("APP_ID")
versionCode project.env.get("APP_BUILD") as Integer
versionName project.env.get("APP_VERSION")
```

Add `release` part to `signingConfigs`:

```
signingConfigs {
    release {
        storeFile file(MY_RELEASE_STORE_FILE)
        storePassword MY_RELEASE_STORE_PASSWORD
        keyAlias MY_RELEASE_KEY_ALIAS
        keyPassword MY_RELEASE_KEY_PASSWORD
    }
}
```

Add the `signingConfig` in `buildTypes > release` section:

```
buildTypes {
    release {
        signingConfig signingConfigs.release
    }
}
```

### Make sure the right SDK is linked

Create the file `local.properties` in `/android`:

```
sdk.dir = /home/$USER/Android/Sdk
```

### Configure iOS build

