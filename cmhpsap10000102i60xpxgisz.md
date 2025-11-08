---
title: "How I Built CommunoPlus: The Communauto Problem That Changed Everything"
seoTitle: "How I Built CommunoPlus: The Communauto Problem That Changed Everythin"
seoDescription: "The frustrating story of losing car-sharing vehicles to faster refreshers - and how it sparked the creation of CommunoPlus, an automation for Flex hunting"
datePublished: Sat Nov 08 2025 04:28:12 GMT+0000 (Coordinated Universal Time)
cuid: cmhpsap10000102i60xpxgisz
slug: how-i-built-communoplus-the-communauto-problem-that-changed-everything
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1762575742512/193bf9d9-5bd0-4c30-a171-37a768898715.webp
tags: flutter, cloud-computing, automation, mobile-development, car-sharing, communauto

---

It was a Saturday morning in Montreal, and I was standing on a street corner, frantically refreshing the Communauto app on my phone. Again. And again. And again.

I needed to pick up some furniture from a friend across town, then drop it off at my place. The metro? Sure, but good luck hauling a chair through the station. An Uber? $25 each way, so $50 total. But if I could just grab a Flex vehicle – any Flex vehicle – I could do the whole trip for under $15.

The app showed a Toyota Corolla two blocks away. Perfect. I tapped on it. Loading... Loading... And then:

**"Sorry, this vehicle is no longer available."**

Frustrated? Yeah, you could say that. 😤

## **The Refresh Game Nobody Asked For**

Here's the thing about using car-sharing services like Communauto Flex in a busy city: finding an available vehicle isn't just about *luck* – it's about *speed*. And not the speed of getting to the car, but the speed of your thumbs on the screen.

I'd see a vehicle pop up on the map, tap it as fast as I could, wait for the details to load, and by the time the booking button appeared, someone else had already grabbed it. Every. Single. Time.

It felt like playing a video game where the difficulty was permanently set to "impossible." 🎮

So what did I do? Like any reasonable person who just needed to get somewhere, I started developing a strategy:

1. Open the app
    
2. Refresh
    
3. See a car
    
4. Tap it instantly
    
5. Lose it to someone faster
    
6. Repeat steps 2-5 until either:
    
    * I miraculously won the refresh lottery
        
    * I gave up and took the metro
        
    * I was late to where I needed to be
        

I'm not exaggerating when I say I spent 15 minutes one morning just standing on the street, refreshing the app, watching cars appear and disappear like some kind of vehicular whack-a-mole game. 🚗💨

**Real talk:** I calculated once that I was probably refreshing the app 3-4 times per minute. That's over 45 refreshes in 15 minutes. And I still didn't get a car.

## **The Moment Everything Changed 💡**

One particularly frustrating morning, after losing yet another vehicle to a faster refresher, I had a thought that changed everything:

*"I'm a software engineer. What if I could automate this?"*

What if, instead of me sitting there refreshing the app like a maniac, there was a system that could **watch for available vehicles** on my behalf? What if it could **instantly block a car** the second it became available, before anyone else even saw it?

What if I could just set my location, tell it "find me a Flex vehicle within 500 meters," and then go about my morning while the system did the hunting for me?

That's when CommunoPlus was born. Not because I had some grand plan, but because I was genuinely tired of losing the car-sharing game to people with faster fingers. I just wanted to solve my own problem.

## **But Wait, Isn't That... Cheating? 🤔**

I know what you're thinking. "Dude, you're basically cutting in line."

And yeah, I get why it might feel that way. But here's how I saw it:

The official Communauto app makes you manually refresh and manually search. That's just... how it works. There's no notification system. There's no "alert me when a car becomes available near me" feature. You're expected to just *know* when cars become available and be faster than everyone else.

**That's not user experience. That's a competition.**

And like any competition, the person with the better tools wins. Some people had faster phones. Some had better internet connections. Some were just naturally faster at tapping through screens.

I just happened to have the ability to write code that could do it for me. 🤷‍♂️

## **The Real Problem I Discovered**

As I started building what would eventually become CommunoPlus, I realized something: I wasn't alone in this frustration.

I started talking to other Communauto users – friends, coworkers, people on Reddit – and the stories were all the same:

* **Weekend planners** who needed a car for errands or day trips but couldn't find one in time
    
* **Parents** trying to get their kids to activities across town
    
* **Students** missing classes because they spent 20 minutes trying to book a vehicle
    
* **Remote workers** who gave up on Flex entirely because it was "too unreliable"
    

The common thread? Everyone was playing the same refresh game. Everyone was frustrated. And everyone just accepted it as "how car-sharing works."

But it didn't have to be that way.

## **The Other Problems I Noticed 📝**

Once I started really paying attention to my Communauto usage, I noticed other problems too:

### **The 31-Day Booking Window Problem**

I wanted to book a car for a trip I was planning two months out. Can't do it. Communauto only lets you book 31 days in advance.

So I had to set a reminder for exactly 31 days before my trip, hope I remembered to check at that exact time, and hope a vehicle was available. And if I forgot? Well, I might be taking the bus on my vacation. 🙈

### **The Lost & Found Black Hole**

Left my AirPods in a Flex vehicle once. The official lost and found process involved calling customer service, filing a report, and... waiting. And hoping. And usually never hearing back.

There was no way to connect with the next person who used that car. No systematic way to match "I lost something" with "I found something." Just a void where your belongings disappeared forever.

### **The Mystery Vehicle Experience**

Ever book a car and have no idea what you're getting until you show up? No photo, no ratings, no reviews. You just see "Toyota Prius C" and hope for the best.

Sometimes you'd get lucky – a decent vehicle. Other times? Not so much. The AC doesn't work (those old Prius Cs...). There's a weird smell. The previous user left trash everywhere. Or worse – dog hair. Everywhere. On the seats, in the air vents, coating your jacket the moment you sit down.

There was no way to know if the vehicle you're about to book is pristine or... problematic. You just had to roll the dice. 🎲

## **Why I Decided to Build Something**

So there I was, a software engineer in Montreal, actively using Communauto multiple times a week, running into all these frustrations, and thinking:

**"Someone should fix this."**

And then the classic developer realization:

**"Wait... I'm someone. I can fix this."** 💪

I wasn't planning to build a competitor to Communauto. I loved the service! I just wanted to solve the user experience problems that were making my life harder.

I wanted:

* **Automation** for finding and blocking vehicles (solve the refresh game)
    
* **Smart scheduling** for long-term planning (solve the 31-day window)
    
* **Community features** for lost and found (solve the black hole)
    
* **Transparency** through vehicle ratings (solve the mystery vehicle)
    

## **The Journey Begins 🚀**

That Saturday morning frustration led to 18 months of development. Late nights after work. Weekends jumping from mobile development to backend architecture to cloud infrastructure. Learning about API integration, background job processing, and how to build a system that actually works reliably.

I was building CommunoPlus as a solution to *my* problem. The fact that other Communauto users found it helpful too – that hundreds of people now use it to enhance their car-sharing experience – that was the surprise.

And you know what? There *was* a better way. 🎯

## **What Happened Next**

Over the next 18 months, I built CommunoPlus from scratch. I launched it on the App Store and Google Play. I watched as real users started solving the same problems I had. I saw the Reddit posts from people sharing their success stories.

And you know what the coolest part was?

I stopped losing the refresh game. Because I wasn't playing it anymore. 🎯

In the coming posts, I'll walk you through how I built each feature – the technical challenges, the architecture decisions, the mistakes I made, and the lessons I learned. From Flex Radar's automated hunting system to the AI-powered Lost & Found, I'll share the entire journey.

But for now, I want to know:

**Have you ever experienced this frustration?** Are you tired of playing the refresh game? Have you missed opportunities because you couldn't find a Flex vehicle in time?

If so, I built CommunoPlus for you. And I'd love to hear your story.

---

**Ready to stop playing the refresh game?**

📱 **Download CommunoPlus:**

* iOS: [App Store Link](https://apps.apple.com/us/app/communoplus-flex-radar/id6740716220)
    
* Android: [Google Play Link](https://play.google.com/store/apps/details?id=com.communoplus.flexradar)
    
* Website: [communoplus.com](https://communoplus.com/en)
    

**Coming up next:** "Building Flex Radar - When Automation Meets Real Problems" – The technical deep dive into how I automated vehicle hunting and what I learned about building reliable background systems.

---

*Have a car-sharing frustration story? Drop a comment or reach out at* [*support@communoplus.com*](mailto:support@communoplus.com) *– I love hearing from fellow Communauto users!*