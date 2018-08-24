# The Cost Of JavaScript In 2018

> 原文地址：[The Cost Of JavaScript In 2018
](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4)

## 笔记

- To stay fast, only load JavaScript needed for the current page.
- Prioritize what a user will need and **lazy-load** the rest with **code-splitting**. 
- For mobile, aim for a JS budget of **< 170KB minified/compressed**. Uncompressed this is still ~0.7MB of code.
- Loading is a journey. We’re shifting to increasing caring about user-centric happiness metrics. Rather than just looking at onload or domContentLoaded, we now ask “when can a user actually **use** the page?”. If they tap on a piece of user-interface, does it respond right away?
	![](https://cdn-images-1.medium.com/max/2000/1*cY6o73ABky4uEQYZs-xa1w.png)
	- **Is it happening** is the moment you’re able to deliver some content to the screen. (has the navigation started? has the server started responding?)
	- **Is it useful** is the moment when you’ve painted text or content that allows the user to derive value from the experience and engage with it.
	- And then **is it usable** is the moment when a user can start meaningfully interacting with the experience and have something happen.
- A visualization of Time-to-Interactive highlighting how a poorly loaded experience makes the user think they can accomplish a goal, when in fact, the page hasn’t finished loading all of the code necessary for this to be true.
	![](https://cdn-images-1.medium.com/max/1600/1*ow6eliCJiSeX7-Ri4hOA5Q.gif)
- For a page to be interactive, it must be capable of responding quickly to user input. A **small** JavaScript payload can ensure this happens fast.
- Loading too much JavaScript into the **main thread** (via script, etc) is the issue. Pulling JS into a Web Worker or caching via a Service Worker **doesn’t have the same negative Time-to-Interactive impact**.
- One thing to keep in mind as an industry is that, if we want to be fast at JavaScript, we have to **download** it, **parse** it, **compile** it, and **execute** it quickly.
- Together, these take anywhere up to **30%** of the time it takes to process the JavaScript for your page — this is a real cost.
	![](https://cdn-images-1.medium.com/max/2000/1*NPtzv8seEPgfftIBQ1Fh7Q.png)
- But parse and compile are still **very costly** and it’s **rare** to see a large script actually execute in **under 50ms**, even if compiled off-thread.
- Another thing to keep in mind with JavaScript is that all bytes are not equal. A 200 KB script and a 200 KB image have **very different costs**.
	- A JPEG image needs to be **decoded**, **rasterized**, and **painted** on the screen. 
- **Measure, Optimize, Monitor, and Repeat.**
- PRPL is a performance pattern for efficient loading. It stands for **(P)ush** critical resources for the initial route, **(R)ender** initial route, **(P)re-cache** remaining routes, **(L)azy-load** and create remaining routes on demand
- PRPL (Push, Render, Precache and Lazy-Load) is a pattern for aggressively splitting code for every single route, then taking advantage of a service worker to pre-cache the JavaScript and logic needed for future routes, and lazy load it as needed.
- **Performance budgets** are critical because they keep everybody on the same page. They create a culture of shared enthusiasm for constantly improving the user experience and team accountability.
- Budgets define measurable constraints to allow a team to meet their performance goals. As you have to live within the constraints of budgets, performance is a consideration at each step, as **opposed to an after-thought**.
- Performance is more often a cultural challenge than a technical one.
	- Discuss performance during planning sessions and other get-togethers. Ask business stakeholders what their performance expectations are. Do they understand how perf can impact the business metrics they care about? Ask eng. teams how they plan to address perf bottlenecks. While the answers here can be unsatisfactory, they get the conversation started.
- **Load and get interactive in 5s. Size budgets can fall out of this. Keep JS < 170KB minified/compressed**
- My 3 Steps for any project: 
	- 1. Load page on Chrome, use Coverage Tool (DevTools => Ctrl+Shift+P => type "Coverage" => press Drawer: Coverage)
	- 2. Press reload icon and see how much unused code you are shipping init. page load
	- 3. Find those modules and lazy load with import()


