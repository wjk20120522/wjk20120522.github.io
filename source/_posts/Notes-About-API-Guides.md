title: Notes About API Guides
date: 2015-11-26 09:44:13
tags:
---

Google API Guide的阅读笔记，温故知新！
#### Introduction
Apps provide multiple entry points
Apps adapt to different devices

<!-- more -->
* **App Fundamentals**
the Android system implements the principle of least privilege.

	* App Components
		* Activities: single screen with a user interface
		* Sevices: runs in background to perform long-running operations or to perform work for remote processes
		* Content providers: manages a shared set of app data
		* Broadcast receivers: "gateway" to other components  

	* Activating Components
		* Activity: startActivity(), startActivityForResult() with Intent
		* Service: startService(), bindService with Intent
		* Broadcast receivers: sendBroadcast(), sendOrderedBroadcast(), sendStickyBroadcast() with Intent
		* Content providers query() on ContentResolver

* **Device Compatibility**
Do not need consider about device compatible(pass the CTS), but consider app compatible.
	* Device features
	* Platform version
	* Screen configuration

* **System Permissions**
A particular permission may be enforced at a number of places during your program's operation:
	* At the time of a call into the system, to prevent an application from executing certain functions.
	* When starting an activity, to prevent applications from launching activities of other applications.
	* Both sending and receiving broadcasts, to control who can receive your broadcast or who can send a broadcast to you.
	* When accessing and operating on a content provider.
	* Binding to or starting a service.

#### App Components

* **Intents and Intent Filters**
The primary information contained in an Intent is: Component name, Action, Data, Category, Extras, Flags
An implicit intent is tested against a filter by comparing the intent to each of the three elements:<action>, <data>, <category>
* **Activities**
onPause() is the last method that's guaranteed to be called before the process can be killed—if the system must recover memory in an emergency, then onStop() and onDestroy() might not be called.
	* Fragments
	lifecycle methods: onAttach(), onCreate(), onCreateView(), onActivityCreated(), onStart(), onResume(), onPause(), onStop(), onDestroyView(), onDetroy(), onDetach()
	* Loaders
	LoaderManager + Loader
	* Tasks and Back Stack
	multitasking on Android
	* Overview Screen
	
* **Services**


* **Content Providers**


* **App Widets**


* **Processes and Threads**















#### App Resources