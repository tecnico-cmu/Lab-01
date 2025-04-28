
# MEIC/METI 2024/2025


# Mobile and Ubiquitous Computing


# Lab Guide 1


# Introduction to Android and Kotlin


#### 




---


#### Objectives:


This first lab introduces students to Android application development using **Android Studio Meerkat (2024.3.1 Patch 2)** and **Kotlin**. By the end of this lab, students should be able to:


* Understand the structure of an Android project using Kotlin.
* Set up and run an app on an emulator or a physical device.
* Create and navigate between activities.
* Build simple UI layouts using Jetpack Compose.
* Handle user input and basic events.
* Use Logcat and Android Studio's debugger.
* Build a simple TODO list app.


This lab is designed for **autonomous learning**: follow the instructions, explore the official documentation when needed, and work at your own pace. You can discuss with colleagues, but each student must complete their own work.


#### Material:


* [Class Slides](slides.pdf)




---


## 
 Exercise I – Hello Android (Two Activities)


In this exercise, you will build a basic Android application composed of two activities (shown in Figure 1). The first activity, shown when the app starts, will contain a text input and a "Send" button. When the user types a string and presses the button, a second activity will launch, displaying the message that was entered. This is your own variant of a "Hello World" application.



![](firstapp.png)
Figure 1: Activities implemented by the Hello Android app.

The exercise is broken down into:
 - **1.1:** Creating the project and understanding its structure
 - **1.2:** Testing the app on the emulator or physical device
 - **1.3:** Building the second activity and connecting it to the first. By completing this section, you will become familiar with the basic app lifecycle, UI building blocks, user input handling, and activity navigation.




---


#### 1.1. Create a New Android Project


1. Open Android Studio.
2. Select **"New Project" > "Empty Activity"**.
3. Set the following:
    * **Name**: `HelloAndroid`
    * **Language**: Kotlin
    * **Minimum SDK**: API 24 or higher
4. Click **Finish** to create the project.


**a. Explore the Project Structure:**  Navigate through the project in the **Project** view (not Android view) and describe the role of the following files (Hint: read ["Projects overview"](https://developer.android.com/studio/projects)):

 * `app/src/main/java/.../MainActivity.kt`
* `app/src/main/AndroidManifest.xml`
* `build.gradle.kts` (project and module level)


**b. Managing the Emulator:** To run your app on an Android emulator:
 1. Open **Device Manager** in Android Studio (toolbar > Device Manager).
 2. Click **"Create Device"** and choose a phone model (e.g., Pixel 6).
 3. Choose a system image (e.g., API 34) and click Next.
 4. Configure any desired settings and click Finish.
 5. Start the emulator by pressing the green play icon.


**c. Running on a Physical Device (Optional):** To run your app on your phone:
 1. Connect your Android device to your computer via USB.
 2. Enable **Developer Options** on your device:
 - Go to Settings > About Phone > tap "Build Number" seven times.
 - Go back to Settings > System > Developer Options.
 - Enable **USB Debugging**.
 3. Your device should appear in the Run dropdown. Click the **Run** button to deploy.


**Reference:** [Create your first Android app](https://developer.android.com/codelabs/basic-android-kotlin-compose-first-app), [Run apps on a hardware device](https://developer.android.com/studio/run/device)




---


#### 1.2. Logging and Feedback


Before continuing to implement the full two-activity app, learn how to monitor and debug your app.


**a. Logcat Messages** In `MainActivity.kt`, add the following line in the `onCreate()` method:



```kotlin

  Log.d("MainActivity", "App started successfully")
```

Also include:



```kotlin

  import android.util.Log
```

Open the **Logcat** tab at the bottom of Android Studio to view logs. Filter by your app name or tag.


**b. Toast Messages:** Add a toast to confirm button clicks by replacing the content of function `Greeting()` as follows:



```kotlin

  val context = LocalContext.current

  Box(
      modifier = Modifier
          .fillMaxSize(),
      contentAlignment = Alignment.Center
  ) {
      Button(
          onClick = {
              Toast.makeText(context, "Button clicked!", Toast.LENGTH\_SHORT).show()
          }
      ) {
          Text("Click Me")
      }
  }
```

You will need to include the following libraries:



```kotlin

  import android.widget.Toast
  import androidx.compose.foundation.layout.Box
  import androidx.compose.material3.Button
  import androidx.compose.ui.platform.LocalContext
```

1. Confirm that this code creates a button and shows a Toast when it is clicked.
2. Add also a log message after the toast and view it in Logcat.



> 
> **Question:** Where can you configure how logs are filtered in Logcat?
> 
> 
> 


**Reference:** [Logcat documentation](https://developer.android.com/studio/debug/logcat)




---


#### 1.3. Create a Second Activity (Jetpack Compose)


In this step, you will complete the "Hello Android" app using **Jetpack Compose**. The app will allow the user to type a message and tap a **Send** button, which will launch a second screen displaying the entered message.


**a. Extend the UI in `MainActivity.kt`:** The project template already uses Jetpack Compose. Replace or modify the default `Greeting` composable to build a screen that includes:


* An `OutlinedTextField` to collect user input.
* A `Button` that shows a `Toast`, logs the typed message using `Log.d(...)`, and starts the second activity using an `Intent`.


Use `remember` to store and update the user's input text, and use `LocalContext.current` to launch the new activity.


**b. Create the second activity:**
* In Android Studio, right-click your package name and choose **New > Activity > Empty Views Activity**.
* Name it `DisplayMessageActivity`.
* In `DisplayMessageActivity.kt`, adapt the template for the `DisplayMessageActivity` class to inherit from `ComponentActivity()`
*  Extend `DisplayMessageActivity` to retrieve the message using `intent.getStringExtra("EXTRA_MESSAGE")`.
* Use Compose to display this message using a `Text` composable.



**c. Declare the activity in the manifest:** Ensure `DisplayMessageActivity` is declared in your `AndroidManifest.xml` inside the `<application>` tag.


**Code Snippet Examples:**


**In `MainActivity.kt`:**



```kotlin

  import android.widget.Toast
  import androidx.compose.foundation.layout.Column
  import androidx.compose.foundation.layout.Spacer
  import androidx.compose.foundation.layout.fillMaxWidth
  import androidx.compose.foundation.layout.height
  import androidx.compose.material3.Button
  import androidx.compose.material3.OutlinedTextField
  import androidx.compose.runtime.mutableStateOf
  import androidx.compose.runtime.remember
  import androidx.compose.runtime.getValue
  import androidx.compose.runtime.setValue
  import androidx.compose.ui.platform.LocalContext
  import androidx.compose.ui.unit.dp
```


```kotlin

  val context = LocalContext.current
  var text by remember { mutableStateOf("") }

  Column(
      modifier = Modifier
          .padding(16.dp)
          .fillMaxWidth()
  ) {
      OutlinedTextField(
          value = text,
          onValueChange = { text = it },
          label = { Text("Type your message") },
          modifier = Modifier.fillMaxWidth()
      )

      Spacer(modifier = Modifier.height(16.dp))

      Button(
          onClick = {
              Toast.makeText(context, "Button clicked!", Toast.LENGTH\_SHORT).show()
              Log.d("MainActivity", "User typed: $text")
              val intent = Intent(context, DisplayMessageActivity::class.java).apply {
                  putExtra("EXTRA\_MESSAGE", text)
              }
              context.startActivity(intent)
          }
      ) {
          Text("Send")
      }
  }
```

**In `DisplayMessageActivity.kt`:**



```kotlin

  val message = intent.getStringExtra("EXTRA\_MESSAGE") ?: "No message received"

  setContent {
      HelloAndroidTheme {
          Box(
              contentAlignment = Alignment.Center,
              modifier = Modifier.fillMaxSize()
          ) {
              Text(text = message, fontSize = 24.sp)
          }
      }
  }
```


> 
> **Question:**
> 1. What is the role of the `Intent` object in this app?
> 2. What is the role of `remember` and `mutableStateOf`?
> 3. Why do we need `LocalContext.current` in a composable to launch an activity?
> 
> 
> 
> 


**Reference:** [Introduction to activities](https://developer.android.com/guide/components/activities/intro-activities)




---


### Exercise 2 – Introduction to Kotlin


This section introduces Kotlin, the modern language recommended by Google for Android development. If you're coming from a Java background, Kotlin offers:


* Concise syntax
* Null safety
* Functional features like lambdas and collection operations
* Extension functions
* Data classes and sealed types


You will progressively learn and apply these features by enhancing the Hello Android application from Section 1. By the end, your app will not only echo the input string but process it into a structured object (a "WordBundle") and display its properties.




---


#### 2.1. Kotlin Basics: Variables, Control Flow, and Null Safety


Extend `DisplayMessageActivity.kt` to perform additional processing on the message. Split the message into individual words and count them.


**Tasks**
1. Declare and assign variables using `val` (immutable) and `var` (mutable).
2. Use Kotlin string manipulation to split the input string into a list of words.
3. Implement basic control flow (`if`, `when`) to generate a response depending on the number of words.





**Example Kotlin Snippet**

```kotlin

  val words = message?.split(" ") ?: listOf()
  val wordCount = words.size

  val summary = when {
      wordCount == 0 -> "No words found!"
      wordCount == 1 -> "Just one word!"
      else -> "You entered $wordCount words."
  }
```





> 
> **Question:** How would the same logic look in Java? What does `?.split()` mean? What is the `?:` operator doing?
> 
> 
> 




---


#### 2.2. Data Classes and List Operations


Create a new Kotlin class called `WordBundle` that encapsulates a list of words and can compute some statistics about them (e.g., total count, average word length, the longest word).


**Tasks**
1. Create a file `WordBundle.kt`.
2. Use a `data class` with one property: `val words: List<String>`.
3. Add methods (or computed properties) to return:
    * Total word count
    * Average word length
    * The longest word





**Example Kotlin Snippet**

```kotlin

  data class WordBundle(val words: List) {
 val count = words.size
 val averageLength = words.map { it.length }.average()
 val longestWord = words.maxByOrNull { it.length } ?: "N/A"
 }
```


> 
> **Question:** Why is `maxByOrNull` preferred over `maxBy` in Kotlin? What does `?: "N/A"` do?
> 
> 
> 




---


#### 2.3. Functions and Lambdas


Move the logic for computing the summary into a top-level function (outside any class). Use Kotlin lambdas to filter the list of words based on length.


**Tasks**
1. Write a function `fun generateSummary(bundle: WordBundle): String`.
2. Inside it, filter words longer than a threshold using a lambda expression.
3. Return a summary string including all statistics.





**Example Kotlin Snippet**

```kotlin

  fun generateSummary(bundle: WordBundle): String {
      val longWords = bundle.words.filter { it.length > 4 }
      return "Words: ${bundle.count}, Avg Length: ${bundle.averageLength},
        Long words: ${longWords.joinToString()}"
  }
```


> 
> **Question:** What is the type of the lambda passed to `filter`? How is it different from a Java anonymous class?
> 
> 
> 




---


#### 2.4. Object-Oriented and Functional Kotlin


Now update your `DisplayMessageActivity` to use the class and function you just wrote.


1. Take the string from the intent, split it, build a `WordBundle`.
2. Generate the summary string and display it in a `Text` composable.


You can replace the original echo message with this richer, processed output.


**Example Kotlin Snippet**

```kotlin

  val words = message.split(" ").filter { it.isNotBlank() }
  val bundle = WordBundle(words)
  val summary = generateSummary(bundle)
```


> 
> **Question:** Where would you store helper classes like `WordBundle` in a larger Android project? (See the Android Developers Guide on  [app architecture](https://developer.android.com/topic/architecture) for recommendations on organizing your app into packages like `ui`, `data`, `domain`, and `util`)
> 
> 
> 




---


**References:**


* [Learn the Kotlin programming language](https://developer.android.com/kotlin/learn)
* [Kotlin style guide](https://developer.android.com/kotlin/style-guide)
* [Kotlin Language Reference](https://kotlinlang.org/docs/reference/)
* [Kotlin Playground](https://play.kotlinlang.org/)




---


### Exercise 3 – Debugging and Logcat


This section will help you become comfortable with essential debugging techniques in Android Studio using Logcat, breakpoints, and the debugger view. This section comes **before** building a more complex TODO app in the next exercise because these debugging tools will help you test and troubleshoot more confidently.




---


#### 3.1. Logcat Filters


1. Add different log messages to your app using `Log.d`, `Log.i`, and `Log.e`.
2. Open the Logcat window in Android Studio.
3. Experiment with the filtering options:
    * By tag (e.g., `"MainActivity"`)
    * By log level
    * By package name



> 
> **Exercise:** Try to trigger an intentional error (e.g., a division by zero) and observe the exception in Logcat.
> 
> 
> 




---


#### 3.2. Using Breakpoints and the Debugger


1. Place a breakpoint in the `onCreate` method.
2. Run the app in **debug mode** using the **bug icon**.
3. Use the debugger to:
    * Step into and over lines
    * Inspect variables and expressions
    * Explore the call stack
    * Resume program execution



> 
> **Question:** What happens when you inspect an object in the Variables panel? What does "Step Over" vs "Step Into" do?
> 
> 
> 




---


#### 3.3. Inspecting Runtime State


1. Trigger an event (e.g., button click).
2. Pause execution inside the handler.
3. Inspect:
    * State variables
    * Running threads
    * Memory usage


**Reference:** [Android Studio Debugging Tools](https://developer.android.com/studio/debug)




---


### Exercise 4 – TODO List App (Jetpack Compose)


In this final section, you'll build a simple **TODO list application** using **Jetpack Compose**. The app will consist of a single activity and demonstrate everything you’ve learned so far: state handling, composables, Kotlin programming, list rendering, and user interaction.


#### The app should support:


* A **text input field** to type new tasks.
* A **button** to add the task to the list.
* A **scrollable list** that displays all added tasks.




---


#### 4.1. Build the UI Structure


Start by creating a new project or a new activity in your existing project.


In your main composable, build the UI using a `Column` that contains:


* An `OutlinedTextField` for typing tasks
* A `Button` labeled **"Add Task"**
* A `LazyColumn` that displays all the tasks



```kotlin

  val taskList = remember { mutableStateListOf<String>() }
  var text by remember { mutableStateOf("") }

  Column(modifier = Modifier.padding(16.dp)) {
      OutlinedTextField(
          value = text,
          onValueChange = { text = it },
          label = { Text("New task") },
          modifier = Modifier.fillMaxWidth()
      )
      Spacer(modifier = Modifier.height(8.dp))
      Button(onClick = {
          if (text.isNotBlank()) {
              taskList.add(text)
              text = ""
          }
      }) {
          Text("Add Task")
      }
      Spacer(modifier = Modifier.height(16.dp))
      LazyColumn {
          items(taskList) { task ->
              Text(task, modifier = Modifier.padding(8.dp))
          }
      }
  }
```



---


#### 4.2. Task Data Model


Instead of just storing strings, create a `Task` data class to represent each task:



```kotlin
data class Task(val title: String, val isDone: Boolean = false)
```

Update your list to use `mutableStateListOf<Task>()`, and modify the list UI to show task completion status (e.g., strike-through text or a checkbox).




---


#### 4.3. Event Handling


When the user presses the **Add Task** button:


* Read the task from the input
* Add it to the list (as a `Task` object)
* Clear the input field


**Optionally:**


* Ignore empty input
* Avoid adding duplicates




---


#### 4.4. Optional Advanced Features


Add one or more of these features to make your TODO app more powerful:


* **Task deletion**: Tap a task to remove it
* **Task completion**: Add a `Checkbox` or toggle its style when marked done




---


#### References


* [Jetpack Compose LazyColumn](https://developer.android.com/jetpack/compose/lists)
* [Working with lists in Compose](https://developer.android.com/jetpack/compose/lists)




---


### Appendix A – Explore and Learn More


Congratulations on completing Lab 1! By now, you've built and debugged your first Android application using Kotlin and learned how to use some of Android Studio's core tools. But the best developers are lifelong learners – so here are some resources and exercises to help you go further on your own.




---


#### A.1. Practice Kotlin Online


If you want to sharpen your Kotlin skills in isolation, try these free platforms:


* [Kotlin Playground](https://play.kotlinlang.org/) – Try snippets, test functions, and play with Kotlin’s syntax.
* [Kotlin Koans](https://play.kotlinlang.org/koans/overview) – Interactive Kotlin exercises for learning key features step-by-step.
* [Exercism Kotlin Track](https://exercism.org/tracks/kotlin) – Real-world Kotlin programming challenges with mentoring support.
* [JetBrains Kotlin Hands-On](https://play.kotlinlang.org/hands-on/overview) – Great for learning Kotlin in a guided, hands-on way.




---


#### A.2. Android SDK: Speed Up Development


Here are some tips and tricks to become more productive with Android Studio:


* Use **live templates**: Type `logd` + `Tab` to auto-insert `Log.d(...)`.
* Learn **keyboard shortcuts**: e.g., `Ctrl+Shift+A` to find actions or settings quickly.



> 
> Tip: Go to `Help > Productivity Guide` in Android Studio to discover useful features you're not using yet.
> 
> 
> 




---


#### 5.3. Working Smarter with the Emulator


The emulator can be demanding. Here's how to make it more manageable:


* Prefer x86\_64 system images and **enable hardware acceleration (HAXM)** if supported.
* Create a **smaller virtual device** with low resolution (e.g., 4" screen, 512 MB RAM).
* Enable **cold boot vs. quick boot** for more consistent testing.
* Use the **emulator toolbar** to simulate events (e.g., GPS, SMS, calls).
* Try **Wireless ADB** for testing on a real phone without USB.


**Reference:** [Emulator Best Practices](https://developer.android.com/studio/run/emulator-speed)




---


#### A.4. Learn Android APIs


Here are essential API areas you should feel comfortable exploring based on this lab:


* `android.content.Intent`: Activity navigation.
* `android.util.Log`: Debug logging.


**Where to learn more:**


* [Android API Reference](https://developer.android.com/reference)
* [Android Developer Guides](https://developer.android.com/guide)
* [Jetpack Overview](https://developer.android.com/jetpack) – Android's modern app toolkit.




---


#### A.5. Suggested Challenges (at Home)


Want to learn more? Try extending your app or building a small new one on your own. Here are some ideas:


* Add input validation and visual feedback to your TODO app.
* Create a "Clear All" or "Undo" button with Snackbars.
* Share your app with a friend by generating an APK (Build > Build Bundle/APK > Build APK).




---










