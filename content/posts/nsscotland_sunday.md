---
title: "NSScotland 2013: Sunday "
date: 2013-10-20
categories: 
 - "NSScotland"
---

Notes from day two! 

### How to Get Hired by Black Pixel (Matt Farrugia) ###

![Photo](https://pbs.twimg.com/media/BXAro7rIEAACrUq.jpg)

This was really a talk about how to improve your skills, that you should be proactive in making a plan to improve yourself and identify areas of strength and weakness. I think Matt raises a good point that you should have a 'hard copy' of your skillset and track what you know. 

- Becoming a better developer
	- [Love your work](http://www.imdb.com/title/tt1772925/)
	- Constantly look for areas of improvement
	- Write down your plan to improve
		- [Skills tree](http://i.imgur.com/WGEhRAe.png)
		- Skills table of technologies - (None | Beginner | Intermediate | Expert)
		- Everyone will have a different plan.
	- Software architecture
		- OO concepts
		- Scalability
		- Design patterns
	- Debugging
		- Instrumentation
		- Scientific method
		- Code compression
		- Reductionism
		- **Stop guessing**!
	- Knowledge 
		- Experience
		- Speed
		- Quality
		- Completeness
		- Rework
	- Aftermath
		- Simplicity
		- Test coverage
		- Technical debt
		- Code elegance
		- User experience
		- Support imapct
		- K.I.S.S.
- [Simple Made Easy](http://www.slideshare.net/evandrix/simple-made-easy)
- The Practice of Learning
	- Concrete experience
	- Reflective observation
	- Abstract conceptualisation
	- Active experimentation

### Core Audio (Gordon Murrison) ###

Gordon Murrison is a co-author of [Smoovie](http://www.smoovie.com/).  This presentation showed the use of CoreAudio to build a metronome, and manipulate sound (applying effects to a generated sine wave). Kicked off with a little bit of history of music technology.

- History
	- MIDI
		- Simple protocol, key pressed, key released, press strength
	- Digital audio
		- Mono is about 80KB/s, stereo is 160KB/s
	 - PCI cards
 - CoreAudio 
	- Generally, use NSSound and AVAudioPlayer - high level, simple, fast, and relatively advanced so you shoulnd't need to dig down further.
- Building a metronome
	- Can't rely on NSTimer accuracy under CPU load.
		- Use Audio File Services to work directly with queues for accurate timing.
- Audio Units
	- Plugins which process audio
	- DSP processes input- properties are set before running, parameters affect sound during run.
	- Unit has about 10ms to process data
	- Audio Unit Graphs
		- Nodes represent units
		- Sine wave generator, chained units, eg reverb, delay

### Virtually Understanding Memory (Jamie Montgomerie) ###

Technical overview of memory management on OS X and iOS.  Very fast paced and very informative, feel as though I learned the most from this talk although perhaps not much that I can immediately put into practice. Made virtual memory a bit less mysterious and a lot more interesting.

- Virtual memory on iOS- iOS does have a virtual memory system very similar to OS X.
- Application memory.
	- 64 bit address space can address more memory than could physically exist.
	- An app doesn't get access to all the space immediately.
	- Space is split into chunks (*pages*) 4k big, managed by OS kernel.
	- A set of continuous pages is called a memory "region". 
	- Application must use kernel APIs to request memory access.
	- Use vmmap to look at the virtual memory map for a running application. Works for simulator. (eg, vmmap vim -resident -dirty)
		- Columns: Type, Range, Size, Permissions, Maximum Permisssions, Share Mode (PRIvate, Copy On Write, SHared, ALIased)
		- All pages have no neccessary associaton with real physical memory.
	- Pages backed by real RAM are called "resident".
	- Pages that are in RAM but not on disk are dirty (eg, malloc).
	- File backed regions are read lazily.
	- A page fault occurs when reading memory which is not resident, or when a Copy On Write region is modified.
	- Instruments only tracks memory allocated through alloc, it doesn't track virtual memory.
		- For example, CALayer objects are just handles but exist much larger in real RAM.	
		- Some system frameworks bypass the kernel APIs and work directly with the virtual memory system, eg, NS/UIImage.
		- New instruments "all anonymous VM" can show virtual memory use (though not with any granularity).
- Framework code, resources and memory-mapped files(?) can be shared between applications and only loaded into RAM once.
- Allocated regions from malloc are not really allocated until they are used. Very efficient system.
- The system accounts for pages as ACTIVE | INACTIVE | FREE.
- Under pressure:
	- Removes clean cached files and purgable writable regions.
	- Writes dirty sections of shared files to disk.
- Using the VM system for yourself
	- Allocating anonymous memory
		- Just use malloc
	- Memory mapping a file (great for large, sparse data)
		- NSData	
		- BSD mmap C API (sys/mmap.h)
	- Sharing the memory between processes (POSIX semaphores)
- When not to use the VM system directly:
	- Dealing with files smaller than 4k page size, just malloc.
	- Mapping can fragment memory space.
	- Surprisingly easy to run out of address space in 32 bit.
	- XPC or distributed objects might be a better inter-process method of sharing memory.
- Purgable regions
	- Special kind of memory, not file backed, discardable
	- Good for data that is fast and easy to recreate
	- NSCache is based on this
		- NSPurgableData / NSPurgableContent
		- Can use these objects in NSCache, works well.
- iOS virtual memory is nearly identical to OS X, the only thing missing is dynamic paging on non-disk-backed data.
- Look for RPRVT column in *top* to see a program's virtual memory usage. 
- More info:
	- Apple docs 
		- Memory usage performance guidelines
	- *man vmmap*

### Accessibility (Matt Gemmell) ###

![Photo](https://pbs.twimg.com/media/BXBgMDCIcAAVsfl.png)

- Haptic feedback will be a big win in the future for accessibility but we are not there yet.
- How to use VoiceOver mode on the iPhone:
	- Select, navigate, activate
	- Keyboard- slide finger, other finger to select
	- Search
		- Can actually search entire screen, not just the content, so blind users actually have more power here.
	- Label 
		- Can actually relabel items manually and implement your own accessibility ID's *(temporary solution for automation?)*
	- Curtain 
		- Tap three times with three fingers to deactivate screen
	- Scrub cancel
		- Z-shaped gesture with two fingers to cancel/go back.
	- Battery lasts longer when not powering the screen.
	- Magic tap
		- A two-finger double-tap that performs the most-intended action.
	- The rotor - changes interaction mode
		- For example, change navigation mode to only navigate links / words / characters.
- Why should you care?
	- These devices are a lifeline for people with disabilities.
	- They are tools of inclusion and independence.
	- 285 million people worldwide are visually impaired (90% are in the developing world).
- One simple API, UIAccessibility.
	- *Label* is a brief name, ie, "Play".
	- *Hint* is what the item does, ie, "Plays the current song".
- Acccessibility inspector in the simulator is a bit shit, should test on device.
- Can make VoiceOver say something at specific times, read content.
- Skip animations if in VoiceOver mode?
- Don't rely on gestures- VoiceOver will own them.
	- **Potential issue with slide-to-profile?**
- "Label your bloody icons"
- "Blind people won't use my app" - Blind people *will* use your app.
- What are blind users doing with smartphones?
	- Colour identification
		- Matching clothes
	- Is it charged?
	- Did i leave the lights on?
	- Games
		- Board / card / word / puzzle
- Trust the technology, it is simple, quick to implement and really matters.

### Seeing The Bigger Picture (Simon Wolf) ###

Didn't take notes. A talk about the general usage of dual screens and catering for them in Mac applications. 

#### Included: ####
- Brief history of Airplay
- Coding for multiple screens and airplay mirroring
 	- *Look* (at start) and *listen* for screens becoming active/inactive.
- Dealing with overscan 

(... no more notes)

### Architectural Patterns for Wetware Systems (Graham Lee) ###

![Photo](https://pbs.twimg.com/media/BXB7z8SIEAA9zgc.jpg)

A talk not related to programming but a look at how we interact with each other, problem solve and patterns of human behaviour stemming from mental schemata.  Very entertaining talk but a little meta, the notes don't really detail much but Graham Lee touched on a lot of interesting history about physics, beliefs, and psychology.  Fun end to the conference.

#### What was talked about ####
- Interacting with others.
- (Using wave particle duality vs. Einstein example), should model reality, not try to make reality fit your view.
- If user finds a way to do something other than the most optimal way, they will stick to it anyway.
- Internal schemata:
	- Behaviours
	- Beliefs
	- Attitudes
	- Values
	- False beliefs based on coincidence
- Predjudice
	- "Easier to think that people who are different to me are all similar"
- Cognitive dissonance (internal story to reinforce false beliefs)
	- The more a metaphor breaks down, the more effort to maintain the belief.
- Attentional blindness, we filter out a lot. Example: motorists not seeing cyclists.
- Phonological loop. Sound eats into conciousness after about 2 seconds.
- Bystander apathy.
- Focus narrows as you become busier.
- Romantic / Classic quality (Zen and the Art of Motorcycle Maintenance)
	- Misunderstand or misunderestimate what the other quality is about.
	- Non-appreciation of underlying code.
	- Non-appreciation of end product.
	- Apple accounts for both- bottom up (classic) and top down (romantic).
- Bias blind spot.

