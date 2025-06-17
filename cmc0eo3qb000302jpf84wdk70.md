---
title: "Umami Kotlin"
datePublished: Tue Jun 17 2025 10:53:57 GMT+0000 (Coordinated Universal Time)
cuid: cmc0eo3qb000302jpf84wdk70
slug: umami-kotlin
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/hpjSkU2UYSU/upload/bd86ab3bd46202487d574486d825b417.jpeg
tags: analytics, ios, opensource, android, kotlin, google-analytics, umami, kotlin-multiplatform

---

One of the best parts of building with Kotlin Multiplatform is the promise of a truly unified codebase. You write your business logic, data handling, and presentation models once, and they run everywhere. But when it comes to analytics, that promise often hits a brick wall.

Let's talk about the elephant in the room: Google Analytics. Sure, you can wrangle it to work on Android and iOS through Firebase. But what about your Compose for Desktop app? Your server-side code on the JVM? Your other native targets? You hit a dead end. There is no native, unified support for the full KMP ecosystem.

This forces KMP developers into a messy compromise: either you litter your shared `commonMain` with `expect/actual` hacks for different analytics tools, or you simply give up on tracking analytics for your non-mobile targets. Both options feel like a defeat.

And on top of that technical headache, there's the philosophical one. Even where it *does* work, many of us are tired of bolting these heavy, data-hungry platforms onto our apps. We want to understand our users, not harvest their personal data.

The real challenge, then, is finding an analytics tool that checks two huge boxes:

1. It must be **truly multiplatform**, respecting the "write once" spirit of KMP.
    
2. It must be **lightweight and privacy-first**, respecting our users.
    

My search for a tool that could do both led me to Umami. And the need for a seamless way to integrate it into a KMP project led me to build `umami-kotlin`.

## So, Why Umami?

After hitting a wall with the usual analytics providers, I went searching for a tool that solved both the KMP support gap and the privacy problem. My search ended when I discovered [Umami](https://umami.is). It immediately stood out from the crowd, not by trying to do everything, but by doing a few things exceptionally well.

For any developers who haven't come across it, here’s what sold me on making it the foundation for my next project:

* **It’s Free and Open Source.** This is non-negotiable for many of us. With Umami, there's no black box. You can see exactly how it works, host it yourself, and contribute to its development. That level of transparency is a massive win.
    
* **You Control Your Data (Cloud or Self-Hosted).** This is huge. You can spin up Umami on your own server for complete data ownership—a must-have for the truly privacy-conscious. Or, if you prefer convenience, their affordable cloud version gets you running in minutes. The choice and control are yours.
    
* **It's a Living, Breathing Project.** A quick look at their GitHub repo shows you this isn't some abandoned project. It’s well-maintained and actively improving, which gives you the confidence to rely on it for your own applications.
    
* **A Clean, Hackable REST API.** For a developer, this is a dream. Under the hood, everything is powered by a straightforward REST API. No complex, proprietary SDKs to wrestle with—just clean, predictable HTTP requests. This platform-agnostic approach is what makes it a perfect candidate for KMP.
    
* **The Docs and Code Don't Lie.** The documentation is excellent, and the source code itself is easy to read. This is the ultimate green flag for a developer choosing a new dependency—it shows a project that genuinely cares about its community.
    

Umami was the perfect tool. It was lightweight, respected user privacy, and had a simple API that could theoretically work on any platform. There was just one piece of the puzzle missing: a simple, idiomatic way to use it from a shared Kotlin Multiplatform codebase.

And that’s where `umami-kotlin` comes in.

## Umami-kotlin comes into play

Umami was the perfect tool. It had a simple API, respected privacy, and was platform-agnostic. There was just one piece of the puzzle missing: a simple, idiomatic way to use it from a shared Kotlin Multiplatform codebase.

So, I decided to build it.

`umami-kotlin` is a lightweight, open-source Kotlin Multiplatform library designed to be the most direct and elegant way to interact with Umami's API from your `commonMain`. Think of it as a clean, type-safe abstraction layer over the Umami REST API. Instead of manually building HTTP requests and parsing JSON, you get a **simple and straightforward API**. You just create a client object and call its functions—that's it.

How does it achieve this multiplatform magic? Under the hood, `umami-kotlin` is powered by **Ktor**, the modern, asynchronous HTTP client from JetBrains. Because Ktor supports virtually every target in the Kotlin ecosystem, so does `umami-kotlin`. Whether you're building for **Android, iOS, Compose for Desktop (JVM), or even a server-side application**, you can use the exact same analytics code everywhere. No more `expect/actual` hacks for your analytics. That's the KMP promise, delivered.

But power isn't useful if it's complicated. The core design principle of `umami-kotlin` is simplicity. We've focused on creating a setup process that is, frankly, trivial. There are no complex configuration files or lengthy initialization steps. You provide your Umami URL and Website ID, and you’re ready to start tracking events in minutes. It’s designed to get out of your way so you can focus on building your app, not your analytics pipeline.

### **Show Me the Code: A Usage Sample**

Talk is cheap, so let's see just how simple the API truly is. Here’s the correct, minimal example of how to track events with `umami-kotlin`.

First, add the dependency to your `build.gradle.kts` file. You can find the latest version on Sonatype Central.

![Maven Central Version](https://img.shields.io/maven-central/v/dev.appoutlet/umami?style=for-the-badge&label=Maven%20Central&link=https%3A%2F%2Fcentral.sonatype.com%2Fartifact%2Fdev.appoutlet%2Fumami align="left")

```kotlin
// In your commonMain source set's dependencies
dependencies {
    // Check the badge above for the latest version
    implementation("dev.appoutlet:umami:LATEST_VERSION")
}
```

Now, you can start tracking events. The API is designed for maximum clarity: you create a client, then call the `event()` function.

```kotlin
import dev.appoutlet.umami.Umami
import dev.appoutlet.umami.api.event
import kotlinx.coroutines.runBlocking

fun main() = runBlocking {
    // 1. Create the client with your Umami instance details
    val umami = Umami.create(
        // The `baseUrl` is optional. It defaults to the Umami Cloud if not provided.
        baseUrl = "https://your-umami-instance.com",
        website = "your-website-id"
    )

    // 2. Track a page view by providing a URL and Title
    umami.event(url = "/home", title = "Home Screen")

    // 3. Track a specific interaction, like a button click
    umami.event(name = "signup-button-click")
}
```

As you can see, the API is direct and clean. You use the `Umami.create()` factory method to configure your client once. From there, you call the universal `event()` suspend function to send data to Umami. You can use it to track a page view (by providing a `url`), a custom event (by providing a `name`), or both at the same time. It's one simple function for all your tracking needs, available directly in your shared code.

For a deep dive into all available parameters and the complete API reference, head over to the official documentation site.

[**➡️ View the Full Documentation Here**](https://appoutlet.dev/umami-kotlin/)

### **Real-World Usage: Koin + Android ViewModel**

While the basic `main` function is great for seeing how the API works, you’ll want to integrate it cleanly into your app's architecture. Here’s a more realistic example showing how to set up `umami-kotlin` with Koin for dependency injection and use it inside an Android ViewModel.

#### **Step 1: Setting up the Koin Module**

First, we’ll define our `Umami` client as a singleton in a Koin module. This ensures we have one, and only one, instance of the client throughout our app, and we can easily inject it wherever we need it.

```kotlin
import dev.appoutlet.umami.Umami
import org.koin.dsl.module

val appModule = module {
    // Define Umami as a singleton, so there's only one instance
    single {
        Umami.create(
            // The `baseUrl` is optional and defaults to the Umami Cloud.
            baseUrl = "https://your-umami-instance.com",
            website = "your-website-id"
        )
    }

    // ... other dependencies like ViewModels
    viewModel { CheckoutViewModel(get()) }
}
```

#### **Step 2: Injecting and Using in a ViewModel**

With Koin set up, injecting the client into your ViewModel is trivial. Now you can track events related to user actions or screen views directly from your presentation logic using the `viewModelScope` to handle the suspend function.

```kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import dev.appoutlet.umami.Umami
import dev.appoutlet.umami.api.event
import kotlinx.coroutines.launch

class CheckoutViewModel(
    private val umami: Umami // Injected by Koin!
) : ViewModel() {

    fun onPurchaseCompleted(plan: String) {
        // Launch a coroutine from the viewModelScope for the suspend call
        viewModelScope.launch {
            umami.event(
                name = "purchase-completed",
                data = mapOf("selected_plan" to plan)
            )
        }
    }
}
```

This pattern keeps your code clean, decoupled, and easy to test. You can apply the same dependency injection principle on other platforms like iOS (using Koin or another DI framework) or on the server side, all while sharing the same analytics logic.

## The Road Ahead: Challenges & Current Stat**e**

Like any new open-source project, `umami-kotlin` is at the beginning of its journey. While the core functionality for event tracking is stable and ready to use, I want to be transparent about the current state and the exciting plans for the future.

One of the key architectural decisions was to split the library into two distinct modules to keep things lightweight and focused for the majority of users.

**1\. The Core Module:** `dev.appoutlet:umami` This is the tiny, hyper-focused library you've seen in the examples. It does one thing perfectly: tracking events. For the 99% of developers who just need to send analytics data from their app, this is the only dependency you'll ever need. It's designed to add virtually no overhead to your project.

**2\. The Admin Module:** `dev.appoutlet:umami-admin` (Coming Soon) The next major step is to build out the `umami-admin` module. This will be an optional, more powerful library for developers who need to interact with Umami's administrative APIs—things like authenticating, managing websites, or even building custom dashboards to view stats programmatically. To keep things simple, the `umami-admin` module will automatically include the core `umami` module, so you'll only need one import if you need the admin features.

So, to recap: the event tracking API is solid and ready to go today. The admin API is next on the list.

For a more detailed look at what's planned, you can check out the public roadmap. I'm always open to feedback and suggestions, so if you see a feature you’d love to have, feel free to open an issue on GitHub!

[**🗺️ View the Public Roadmap Here**](https://appoutlet.dev/umami-kotlin/roadmap/)

## **Your Turn to Build**

For too long, Kotlin Multiplatform developers have had to make compromises when it came to analytics—either by juggling multiple libraries for different targets or by using tools that don't align with the modern, privacy-first ethos.

My hope is that `umami-kotlin`, paired with the excellent Umami platform, provides a real solution. A tool that's not only technically sound for KMP but also philosophically aligned with building better, more respectful software.

But now, it's over to you. The library is live, documented, and ready for you to take it for a spin. Whether you're building a new KMP app from scratch or looking to replace an old analytics solution, I encourage you to give it a try.

* **⭐️ Star the project on GitHub:** If you find `umami-kotlin` interesting or useful, giving the repository a star is one of the best and easiest ways to show your support!
    
* **📥 Add it to your project:** Grab the dependency and see how it feels to have a unified analytics API across all your platforms.
    
* **💬 Provide feedback:** Open an issue to report a bug, suggest a feature, or just share your thoughts. All contributions are welcome.
    

Thanks for reading. Let's build some cool stuff.

[**🚀 Check out** `umami-kotlin` on GitHub](https://github.com/AppOutlet/umami-kotlin)