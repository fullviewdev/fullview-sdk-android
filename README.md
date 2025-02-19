# Android Developer Documentation

**MinSDK**: 28

**Current version:** 0.11.0

**Previous released versions:** [0.9.0, 0.9.1, 0.9.2]

Fullview.io Android SDK supports following frameworks:

- Native Android
  - Compose
  - Views

Other platforms supported:

  - iOS
  - React Native
  - Flutter

Soon-supported platforms:

- Ionic
- Cordova

## Setup

1. Add following line to `build.gradle` to import the fullview SDK</br></br>`implementation("io.fullview:fullview-sdk:{CURRENT_VERSION}")`

2. Import and attach the fullview SDK to your Application.

   ```kotlin
   class App : Application() {
       override fun onCreate() {
           super.onCreate()
           FullviewSDK.getInstance().attach(application = this)
       }
   }
   ```

3. Call register function to identify users with Fullview.

   ```kotlin
   (application as App).fullviewSDK.register(
       organisationId = "",
       userId = "",
       deviceId = "",
       name = "",
       email = ""
       region: Region
   )
   ```

## Fullview SDK API

`attach(application: Application, hostType: HostType? = HostType.NATIVE)`
`attach(activity: AppCompatActivity, hostType: HostType? = HostType.NATIVE)`
Attaches fullview SDK to the host app. Should be called in `onCreate()` in application class or as early in the apps lifecycle

Available HostTypes : `[NATIVE, FLUTTER, REACT_NATIVE, IONIC, OTHER]`

<br/>
<br/>

```kotlin
    fun register(
        organisationId: String,
        userId: String,
        deviceId: String,
        name: String,
        email: String,
        region: Region,
        hubLayout: HubLayout
    )
```

Registers user to be available in fullview.
Available HubLayouts: `[LOCKED_FULLSCREEN, FULLSCREEN, DEFAULT]`

<br/>
<br/>

`logout()`

Logs out the current user from fullview

<br/><br/>

`requestCoBrowse()`

Puts the user into a waiting queue requesting help from agents

<br/>
<br/>

`cancelCoBrowseRequest()`

Remove the user from the waiting queue

<br/>
<br/>

`val positionInCoBrowseQueue: SharedFlow<Int>`

SharedFlow emitting which position the user is in the queue

<br/>
<br/>

`val sessionState: SharedFlow<FullviewSessionState>`

SharedFlow emitting the current state of the SDK. (Used for custom UI)

## Key areas

### [Data Redaction](-)

Data redaction is done on device before transmitting any data to Fullview.io servers.

Helper classes for data redaction:

#### Views

```xml
    <io.fullview.fullview_sdk.helpers.DataRedactionView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

        ...

    </io.fullview.fullview_sdk.helpers.DataRedactionView>
```

**Example**

```xml
    <io.fullview.fullview_sdk.helpers.DataRedactionView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

        <TextView
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="\$1,252,309.20"/>

    </io.fullview.fullview_sdk.helpers.DataRedactionView>
```

#### Compose

```kotlin
      DataRedaction (
          modifier = Modifier,
          content = {...}
      )
```

**Example**

```kotlin
      DataRedaction {
          Text("\$1,252,309.20",)
      }
```

### Prevent clicks

If you want to prevent the agent to click on a specific view use the following helper functions:

Blocks clicks and runs the supplied block

```kotlin
    View.setBlockingClickListener(block : () -> Unit)
```

**Example**

```kotlin
    root.findViewById<Button>(R.id.button).setBlockingClickListener {
        Log.d("Click", "Button was clicked")
    }
```

If there is already an click listener attached to the view and can't be refactored into `.setBlockingClickListener()`, then add the following line to the view. It will block the propagation of click events from agents.

```kotlin
    View.blockClickEvent()
```

**Example**

```kotlin
    root.findViewById<Button>(R.id.button).blockClickEvent()
    root.findViewById<Button>(R.id.button).setOnClickListener {
        Log.d("Click", "Button was clicked")
    }
```

This is supported as a Composable modifier too.

**Example**

```kotlin
    Button(
        modifier = Modifier.blockClickEvent()
        onClick = {
            Log.d("Click", "Button was clicked")
        }
    )
```
