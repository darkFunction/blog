---
title: "NSScotland 2013: Saturday "
date: 2013-10-19
categories: 
 - "NSScotland"
---

Notes from day one!

### Crazy and Focused (Daniel Steinberg) ###

![Photo](https://pbs.twimg.com/media/BW7kgV6IcAAqbFz.jpg)

Steinberg delivered a motivating presentation encouraging developers to focus in on the core elements of our apps, and how we should clearly and specifically identify our users. 

#### Key points ####
- If you ask your users what they want, they will ask for everything.  Stay focused on your core features.
- Apps can suck in subtle ways (for example, not using the email keyboard for email input).  Most of your work will never be noticed, but if you don't add the polish, people will notice.
- The "app definition statement" - For What, Whom and Why.  Brainstorm ideas and whittle down to key elements.
- If you try to reach everyone, you will reach noone. Be really specific about who you are targeting and try to connect with them.  The iPhone is way more personal than a computer or laptop. People carry your app with them and they touch it directly. They often use headphones and not speakers- you are "literally the voice inside their head".  This allows you to really narrow down your features. "Talk to one person, touch many".
- Don't try to change the world.  Try to change the world for one person.
- Keep it simple.
- "There are a thousand no's for every yes". You may not use all of your work, you keep the good stuff and discard the rest.
- You don't have infinite time- working on one thing often means abandoning another.

### Making Friends With Documents (Neil Inglis) ###

Didn't really take notes on this one. Demo of sample code for reading and writing to NSDocuments/UIDocuments.

#### In short ####
- TextEdit source is a good reference.
- Don't use NSCoder / NSArchiver - use an intermediary format.
- Advantages to UIDocument:
	- Asynchronous read and write.
	- iCloud.
	- Autosave. NSUndoManager automatically created.
	- Safe saving.
- Gotchas: Enums can be different on iOS / OSX, eg, NSTextAlignment.
- Document is composed of multiple files, eg, images etc. Users sees a document but it is actually a filesystem. Use NSFileWrapper, or custom format and Zip it.

### iBeacons, Bluetooth LE and Arduinos (Matthew Robinson) ###

A presentation on how to setup iBeacons. Demonstrated a pyrometer transmitting live temperature updates to an iPhone and OSX. Essentially the technology is promising, but still flaky, and the amount of code required to begin using iBeacons is trivial. Spoke to Matthew about whether an application can simultaneously act as Central and Peripheral, which he thought wouldn't be a problem since the OS monitors for multiple applications at the same time so it's not  a limitation with the hardware. He thinks the range can be around 25 metres, but it just depends on the device power. iPads transmit with greater power than iPhones.

#### Key points ####
- BluetoothLE is:
	- Low Energy.
	- Low bandwidth.
- Find BLE services with BLEExplorer app.
- Client aka "Central", Server aka "Peripheral".  iOS can act as both.
- Can homebrew external hardware for iPhones now, which is exciting.
- All services are identified by UUIDs.
- Services have Characteristics, eg, pyrometer service has 'temperature' and 'rate of change' 
	- Readable / writable / notify on change.
- Use CoreBluetooth (CB...) api's. 
	- 1) Scan for peripherals and receive callback.
	- 2) Ask for services and receive callback.	
	- 3) Ask for characteristics and receive callback.
	- 4) Register for updates.
- iBeacons
	- Supported in CoreLocation, don't need to worry about CoreBluetooth.
	- Hardware information still under NDA? 
	- The ibeacons can *maybe* run upto 2 years on a watch battery(!)
	- Peripheral sends advertisement data:
		- **Proximity UUID** (unique to group of iBeacons, for example, those owned by your company).
		- **Major** - a 16 bit integer - use for anything - maybe unique id for store or something.
		- **Minor** - another 16 bit integer - again, anything.
		- **Measured power** - received signal strength measured at one metre from the iBeacon, not precise.
	- Create CLBeaconRegion, monitor it instead of CLRegion.
	- Start monitoring region, will wake app even if not running(!).
	- CLProximity: UNKNOWN, IMMEDIATE, NEAR, FAR. 
		- If you have multiple beacons found, the nearest is the first object.
		- UNKNOWN is sorted before IMMEDIATE(!)
	- Maybe not quite ready for prime-time.
	- Temperamental, don't always get notifications or can take minutes to work.

### Core Data in Motion ###

**JUST ADDED: [Presentation slides](http://www.slideshare.net/wndxlori/core-data-in-motion)**

Mostly a demo of RubyMotion code for handling large datasets through CoreData. Actually didn't know about RubyMotion before the demo so it was quite hard to follow. RubyMotion allows you to write iOS applications in Ruby, being able to interface with the Apple frameworks but leverage the power of Ruby on top.

#### Some points ####
- Ray Wenderlich's tutorials and sample code ('Failed Bank', 'Locations') are excellent starting points for this kind of project.
- RubyMotion magic and Xcode magic hide quite a lot, although it takes surprisingly little code to manage huge and complex datasets manually.
	- Write models (entities) in code.
	- Write relationships in code.
	- Define your entities, first.
	- Lazily define entities attributes and relationships.


### Testing iOS Applications (Steven Baker)###
This was a high-level overview of testing strategies for iOS.  Pointed out that nowadays we have options when it comes to testing iOS.

I spoke to Steven after the presentation to ask about any advantages to using Kiwi, I was curious about whether it was just really the same thing wrapped in different language semantics. He said that it was, and that if you are already using OCUnit and you are happy with it, that there is no reason to switch.  He personally prefers and uses xUnit despite being the author of RSpec (which Kiwi is a clone of). I mentioned awkward mocking in OCMock, especially regarding value passing, and he said that's a side effect of the extreme difficulty of writing mocking frameworks, but that Kiwi is probably better in that respect.

#### Key points ####
- Big proponent of TDD, said he is unable to develop anything without it. 
- TDD is a design activty, results in more decoupled software. Your code is reusable by definition since the test and the application are both using it.
- xUnit (eg, OCUnit JUnit)
	- One approach to describing tests.
	- Same style across languages.
	- Can use it to learn new languages! Start with a failing test case, and continue from there.
- BDD
	- Merges TDD and DDD.
	- Aims to more accurately reflect intent.
	- Descibes behaviours over testing code.
- Acceptance testing
 	- "I will know that the software is complete when...".
	- Comprehensive regression test.
	- Start a new feature with an acceptance test.
- Mocks and stubs
	- Managing dependencies.
	- Improves isolation.
	- Ensures expected behaviours.
- Automation
	- Cucumber 
		- Provides Gherkin natural language.
		- Given, And, When, Then, natural language backed by functions.
	- Frank (this is what Steven Baker uses and recommends)
		- Cucumber for iOS apps.
			- Most Given/Then/When functions are pre-written.
	- Zucchini (the one to watch)
		- CoffeeScript view descriptions, not Gherkin natural language.
		- Can test against screenshots.
	- Calabash (Steven doesn't use this personally but has friends that recommend it)
		- Cucumber for iOS and Android.
		- "Only option really for cross platform".
	- Instruments
		- JS to control iOS app.
		- Horrible readability.
- Unit Testing Tools
	- OCUnit
		- Built in.
	 	- Very mature.
		- Recommended.
	- Kiwi
		- BDD Framework.
		- RSpec clone.
		- No Xcode integration.
		- Thinks it is less readable with all the syntactic sugar in Objective-C.

### 20 Years of Indie Mac Development (James Thomson) ###

![Intro](https://pbs.twimg.com/media/BXDBkubIIAI4s29.png)

James Thompson gave an account of his career developing for Apple Macs, working on DragThing and the MacOS dock, and rewriting PCalc many times for new platforms.  Some good anecdotes including one about Steve Jobs wanting to get rid of him because he was a remote developer working in Ireland, and so he had to have an office in San Francisco and pretend to be located there. Interesting talk and a lighthearted end to day one.

#### Lessons learned ####
- Comment your code well, might still be in use in 20 years.
- Get code ready for day one, eg, AppStore, iOS7. Huge spike in profits.
- Think about code reuse.
- Support from other developers is invaluable.
- Use some of the profits to fund the next development.
- Don't write games. (Why not?)
