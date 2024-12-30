About me
========

# I - Profile

I am currently working as a __Embedded Software Team Manager (6 people) & Device Architect__, we are a small but innovative company.

I also try to maintain a mid-term technical roadmap and product vision that can be shared with my bosses, in collaboration with Product Owners and aimed at anticipating and mitigating risk. Hence I’m also endorsing some practical aspects of being a __Technical Director__.

# II - Current  Projects and Activities

VOGO is preparing for the future and expanding its brand of audio products “Vokkero” with its __next generation of radio conferencing devices__ that will leverage state-of-the-art Software-Defined-Radio (SDR) and prepare for __AI-based Voice Processing__.

On the paper, we are ready to take-on the harsh challenge of coping with new  radio perturbation sources and the demands of the Broadcasting Industry in terms of audio quality and QoS. Now, my team and I will be in charge of the hardware, Realtime OS and system libraries development, that lay ground for the signal processing layers developed by our experts.
We’ve also learned the lesson of the past, and seek inspiration in the industries out there that set the standards for the future of manufactured goods : __eco-design,__ software-defined things, __model-based and test-driven design__, continuous and automated testing, cybersecurity.

 
# III - Education and wonderful journey in the vast world of device software…

I graduated (M. Sc.) in 1998 from Polytech’ Grenoble (France) and FH Offenburg (Germany) with a double diploma in Automation and Computer Science, with an option in Optronics. My graduation internship related to Optical/Infrared Aperture-Synthesis (interferometry),
where my very modest contribution was mainly signal acquisition and tracking thanks to a CCD camera,
voice-coil and piezo actuators (in the end, a plain Moving Average and PID controller did the job).
Past this internship I didn’t practice electronics any further and went doing what I liked most : writing critical software to drive hardware and tackling performance and stability issues.

In the beginning, I spent 10 years with [Digigram](https://www.digigram.com/products/audio-over-ip-gateways/iqoya-x-link-dual-dual-stereo-audio-codec/), maintaining the __Reatime Audio Streaming & Processing boilerplate__ in Motorola DSP Assembly and writing C/C++ Windows software including kernel stuff.
I gradually took on software architecture and project management tasks, did some Hybrid System Design and development with MCUs instanciated in FPGAs and some simple linear PID controlling in VHDL. Finally in my later Digigram Years, I started working on __Embedded Linux__, mainly on the kernel, drivers and BPS/Distro Layers, and means to achieve RealTime, QoS and overall system performances, and fix related issues.

Eventually I felt the needed to try something different, joined [STMicroelectronics](https://www.st.com/en/digital-set-top-box-ics/stih416.html#overview) for 5 more years, working mainly on multi-core Linux kernel debugging and Multi-core execution trace tools (hardware-enabled low-intrusiveness software execution traces) providing tools to the division (†) that created large Media Processors for set-top-boxes and cable-modems.

Mistakes were made, good 3rd-party tech was overlooked, but great tools were also provided.

After STM, I was lucky to be tasked with deploying a __IEC61508 Safety Development Lifecycle__ at [ez-Wheel](https://www.ez-wheel.com/en/swd-150-safety-wheel-drive) and contributed to the design of a Safe Motor Control module. I felt this is where I learned about fact-driven rigor in software design and quality management, based on international standards.

I then spent few more years working mostly on Linux Kernel and BSP topics for the Automotive Industry (Yocto/Linux) mainly for the [Marelli/Stellantis](https://www.marelli.com/en/innovation/software-defined-vehicle.html) groups, first as a second rank contractor (at NXP in Sophia Antipolis), and later as an employee at Marelli Chatellerault, where I was part of the “Swiss Army Knife Team” in charge of __solving complex Embedded Linux performance and stability issues for the group__.

I finally joined [VOGO Bernin](https://www.vogo-group.com/en-us/category-solution/solution-audio-en-us/vokkero-elite-en-us/) (Vokkero) in 2022.

Nowadays, I take some pride into, and still enjoy __making sure that performance and stability issues are possibly avoided thanks to sensible and reasonable technical management and risk mitigation techniques and standards__. And I reckon I can be a bit obsessive and pedantic about that, but In a useful manner.

 
# IV - Little and Big things to make Device Development a success

This section is my two cents about practices, principles, tools and generally speaking hints on how to __make Device Software Development less risky, more interesting__ for good people you want to keep in you team, and more likely to hit the targeted market on time with __a product that will generate income__.

Some of the following is pretty basic, some is more advanced, some is absolutely mandatory in my experience while often overlooked.

This is mainly targeted at people who actually do the architectural work and/or are tech leaders of a device project. This a about not having to use LTTng or Ftrace or some other big guns, or spending 50k on high-value consulting to fix the project, and not ruining your companies reputation by pushing fundamentally flawed product into the hands or your customer.
I am skipping details and most of the justifications here, but this is solid input, I can elaborate/debate on request depending on my time available…

## General design rule:  do some modelling early on
_… instead of being sorry all the rest of the time_

Spend the time Identifying and documenting:

-	use-cases
-	operating modes : nominal exploitation but also technical modes like update, run-up, run-down, diag, production, commissioning, etc…And also failed/degraded modes.
-	__In each mode describe the user-experience and UI__, get help for this if needed and make sure your Product Owners did their necessary part.
Focus on the device data, imagine an ecosystem
-	Create an abstract data model and abstraction layers
-	Identify clock domains (data dynamics) and data bandwidth early on in the project
-	__Try to find an ecosystem ontology__ : If you product takes a temperature set-value from you end-user, it shall be modelled as a temperature, and not as “write 0xf7 at offset 0xA of 24CSM01 if PCB Rev. is A, or 0xa5 if PCB revision is C”.
-	your APIs and data-structure shall reflect an abstract data model and never the quirks of such-or-such electronic part or bus your are using. Quicks exist, drivers are real, but in most case they can be confined in a Hardware Abstraction Layer or Board Support Package layer.
-	If in your ecosystem you have a device or functional block with say a “volume” parameter, another with a “gain”, another misnamed “output-level” that is fundamentally also a gain, identify that is really the same concept ! The idea is to avoid writing yet another botch because "sorry it is legacy”. 
-	__Focus on the Data first, so that APIs and Interfaces are orthogonal__ and don’t overlap needlessly (cf ISO/OSI model).
-	Categorize data based on permissions and roles of the consumer/producers, and lifecycle I.e. how it shall be accessed in the various operating modes
-	All you need is some drawing tool, some time and common sense. No need to train for SysML or alike unless this is required by some applicable standard.
-	Do model-based design if you can : focus on the data, protocols, interfaces and generate the bindings, doc and test articles with generators.



## General efficiency : crank the compiler’s warning and error levels up ##
_… and fix findings!_


Instead of being proud of fixing a bug after 3 days of investigation, while it was hinted to at compile time already
-	Chances are, If you are still writing C or C++ code for your embedded software. Until Rust is the new default, you definitely cannot be your own reference on what good code is, neither can you rely on some genetic IDE linter good-for-all-an-good-for-nothing.

-	__Compile your stuff with _-Wall -Wextra_, and do read and fix the warnings__ ! Compile for both your embedded target and native PC, because the outputs will complement themselves. Ideally put some nightly build system in place to monitor warnings too. 

-	__Use your compiler’s features for code hardening and optimization__. If you don’t, well you are just ignoring 80% of the effort clever people have put into designing the compiler’s backend. If your code fails when optimized, it is a sign that your code is fundamentally buggy, not that you can skip some optimization. Besides you are probably ruining the potential for your product to evolve and damaging the commercial margin too. Note that with gnu compilers, even with 02 you can get decent debugger unwinding with the -fno-omit-frame-pointer directive, if this is your concern.

-	Use a code checker that supports standard rule sets early on in your project. Because this will save valuable time and frustration, and standard rule sets will allow you to monitor the reusability of you software for projects that must comply to standards (cysec, safety, mil, medical) .

Use some level of MISRA checking ! Here are few rules you should try to comply to, whether you use a code checker or not :

| Rule ID | Description |
|-----|-----|
|9.1	|Automatic variables must be initialized before use.|
|1.3	|Avoid undefined or critical unspecified behavior.|
|11.3|	Avoid casting between pointer types and non-pointer types.|
|11.8|	Avoid casting pointers to more restrictive types unless well-documented.|
|17.4|	Array indexing is the only allowed form of pointer arithmetic.|
|10.1|	No implicit type conversions to a different essential type.|
|10.3|	Cast expressions only to explicitly intended types.|
|14.1|	Loops must have a single exit point (except in exceptional cases).|
|16.3|	Functions must have a single point of exit (return statement).|
|13.2|	Do not use constant expressions as controlling conditions for loops.|
|15.2|	Switch statements must include a default case.|
|8.5	|Header files should only contain declarations, not definitions.|
|21.3|	Do not use dynamic memory allocation functions (malloc, free, etc.).|
|19.4|	Use macros only for inclusion guards or simple constants.|
|Cpp-3.4.1|	Variable should be declared in a scope that minimizes its visibility|


## General Quality Management and Quality Monitoring

-	Do code peer-reviews if you have the people, __no-one ought to be his own solo reference on what is good practice__, especially if he’s been doing the same stuff over and over during the last decade in the very same company.
-	Bring your team to a minimal shared understanding of what is Quality Management in the sense of ISO900x, describe and monitor your processes
-	use a tracker like JIRA or similar connected to github/lab to have traceability hooks.
-	Write a formal Test Strategy document early on, to set an explicit definition of Testing, you need both R&D-side testing AND Product-side testing, done by separate people.
-	If you can, setup a test automation infrastructure early on in the project, using for instance Jenkins, LAVA, or similar means.
 

## General productivity : don’t reinvent the wheel, even if you could

__Spend some time looking for existing solutions to your problem__, especially frameworks and libraries. Most common mistakes I see, that I’m finding upsetting, are:
-	people writing or reusing their own version of basic string or memory block handling, basic bit operations, basic registry cache, logs and traces on systems were the job has been done better, by a dedicated community
-	people not writing libraries on systems that support dynamic linkage
-	people writing proprietary non-portable build systems of their own on top of python alike rather than using portable solutions, compatible with cross-compiling.
The most stupid thing I was ever tasked to do, was to generate a full blown image with complete development SDK (gcc, gdb, etc…) for a small i.MX6 SBC, so that two contractors could log into it to develop and compile, rather than cross-compiling with yocto on a much much faster and convenient PC workstation. A slaughter in terms of productivity and profoundly stupid strategy !
 All this because the original ROS people had obviously no clue of what cross-compiling is neither about existing distribution management systems for the embedded and had only the Windows PC world as their scope.

 On micro-controllers, and even on not so small systems, try to avoid variadic functions, and string formatting, especially string protocols, because
 - they unroll tons of cycles for little to no service you cannot do some other fashion
 - they use buffers that can be spoofed and can lead to memory corruption
 - if you plan to use C++ in the embedded, you may be tempted to using STL as a generally good practice, however on small embedded platforms, this shall be proscribed int favor of projects like ETL. STL is difficult to debug, try unwinding c++ stacks, you'll see what I mean, and also it will move (meaning delete, allocate) objects in your back, and pull dynamic allocation back in your projet.
 - avoid C++ for the deeply embedded, unless all your contributors are familiar with this environment, and your guarantied not to pull generic C++ into your project.

## BSP and SoC: take nothing granted based on datasheets

SoC offer more IPs than most applications will actually use, and more signals that can be practically muxed to physical pins. In addition, lower-end versions of a chip can come with degraded performance on interconnect buses, compared to the higher-end (flagship) version that designers had initially as a target.
Hence, some IP mixes, while allowed on the paper, based on the datasheet, can lead to bus contention, or practically unachievable performances or even non-functional IP mixes.

You really MUST make sure you have:
- characterized each cell independently in terms of performances and robustness, and got familiar with its interfaces
- have some means to combine the unit tests of each cell into the mixes that match your application goals, with the proper dynamics. Suppose you need 5ms SPI transactions, plus periodic write to eMMC, than arrange a matching long run test to prove this will work.
- assert correct behavior of the combination of cells you need, when they work together
- have a working reference, for instance in an evaluation board, that your can compare to your prototypes, and replay when you need.

This is called writing a BSP test-suite, note that may be deemed an overcautious effort by some, but this is actually required because you will eventually need to provide some low-level bootstrap for the bringup of your prototype, and at some point also working software for EMC tests, non-regression etc...


## Linux : Chose Yocto over other build systems when you have the choice


I’ve practiced Linux-from-scratch, stlinux (rpmbuild), windRinver linux (precursor of openEmbedded), LTIB (clever sysroot spoofing by Freescale), Buildroot, Yocto, and frankly until something AI-driven comes out and unless you are working on a target so commonly used as a server or SBC that something like Ubuntu has become default, Yocto is from far the most powerful and quality-driven option.

Yocto’s bargain is mainly in the __available offer, the object oriented model and the overall rigor of it__. Yocto is just the superior distro and build system management framework. Kconfig menus are a distraction.



## Linux: Use DLT for Execution Traces

Sure you can use syslog, rsyslog or some other crappy logging tool that will most certainly do string formatting, use variadic function, shove data from one buffer to another, unroll tons of cycles for each log entry or traces you need, and sure you can come the conclusion that execution traces are invasive. OR … you can :

-	Avoid using string formatting and variadic functions, and log data as binary that can be parsed offline, or online but on a separate Debug/Supervision machine
-	Use an industry standard for low-intrusiveness traces, that is designed to run on small target and minimize CPU load and Bus bandwidth overhead due to traces. Check out DLT. 
-	__Do no write your own trace framework__, I know we’ve all done it, but don’t … use DLT…

[The DLT Daemon project](https://github.com/COVESA/dlt-daemon)

[The DLT viewer](https://github.com/COVESA/dlt-viewer/releases/tag/2.27.0)

 

## Linux : Setup a CoreDump Handler

Sure you can install gdbserver, setup a remote target connection to your IDE, rebuild in O0, redeploy, to the target etc… but

-	__You can definitely NOT ship un-optimized, unstripped binaries as a general way to doing things__
-	You need some way to keep track of software exception leading to a crash on shipping products running optimized/stripped versions
-	You need to be able to __investigate bugs that occurred on-field__, not when your debugger is hooked, which, by-the-way is likely to bias your system behavior and change the reproducibility of a bug, making it difficult to tackle (Heisenbugs).
-	A clever way to go is to __enforce a coredump handler__: build your kernel with KEXEC and COREDUMP support, and have a simple script handler configured, to store the core files in some dedicated flash partitions with some minimal partition size and incident notification management. 
-	Provides you are able to rebuild the very same binary with symbols (thanks yocto) it is very likely that you will find the faulty function within minutes one you load the core file and the symbols into GDB. 


## Linux : Systemd, Dbus, Udev
_…Hut ab Lennart!_


Unless you are running a specific variant of Linux with its dedicated init manager, à-la Android, __chose systemd over systemV, because it is just superior__ when it come to managing, monitoring, debugging and tuning dependencies and starting order between applications.
-	Make sure you apps are integrated as systemd services
-	Provide rules for dependencies solving, use mount or socket  and target units if needed
-	Use udev rules to start your app, if it manages an enumerated device (USB devices, tty, PCI devices etc…)
-	If you application relies on a real-time thread, __use the sd_notify API provided by libsystemd, to make sure systemd will restart you app in case it freezes__ or a RT constraint is screwed beyond repair. In case you have multiple threads, have them create a heartbeat mask, and sd_notify when the whole mask is verified (all threads pinging).

[An troduction to systemd](https://documentation.suse.com/smart/systems-management/html/systemd-basics/index.html#:~:text=systemd%20is%20used%20to%20manage,groups%20of%20units%20into%20targets.)
    
[Using sd_notify with systemd](https://blog.hackeriet.no/systemd-service-type-notify-and-watchdog-c/)
  
   

## Linux : readonly default filesystem system and overlays

How often is a system update failing, not because of a major programming bug, but because the device system setting, user data or licensing files are all over the place, no longer compatible and a nightmare to keep track of. 
It is easy to prevent critical system files or default/factory settings to get damaged, and manage return to factory settings, if they are shadowed in the readonly partition of your filesystem. Use for instance ext4 in ro mode (no need for dedicated rad-only file systems on most targets) plus overlayfs or an alternative like aufs for customized files you may need to reset to factory settings.

[An introduction to OverlayFs](https://linuxconfig.org/introduction-to-the-overlayfs)
   

## Linux : cysec and application sandboxing


Given that you will have to deal with cybersecurity aspects eventually let alone to prove that your user has not stored private data inadvertently or beyond his reach and control, do introduce users and groups management early on in your project.
-	Let root consoles remain available as a practical mean to debug and bringup your system until the very last moment in you development process
-	run as few apps as root as possible.
-	Use systemd and Udev to assign services and devices to groups.
-	If you need RBAC or UAC like SMACK, likewise, you want to introduce it early in the project, as this can be a pain refactor later. 

[a (really) practical guide to ISO-62443 for IIoT systems](https://www.se.com/fr/fr/download/document/998-20186845/)
   

## Linux : avoiding the Thread-a-palooza and signaling butchery 


So you learned about threads, those magic little virtual computation units that solve everything,  look fancy talking about at the coffee machine, and match so well your mental picture of your app that they can only be beneficials the more you spawn’em the better it gets, right ?…

What you need to identify and consider in the first place, before considering threads, are __“clock domains” or event sources__ if you will.

If you have a producer that is realtime (e.g. a V4L2 buffer callback, or gst or ALSA etc…same drill) and a consumer that has no realtime constraint, you don’t necessarily need several threads, __what you need may be as simple as a state machine__.

Threads will require you to implement some means to serialize data (semaphore, mqueue, circular buffers…) and will put some stress on your system for context management, it is __no free-lunch on small systems__.

This can become especially punishing depending on what sort of signaling you use between threads, for instance if you create lots of threads, each resumed based on the SIGVTALRM signal, you will see the PID value for each new thread grow very fast, which is a sign that your system is creating tons of shortlived thread just for the sake of signaling, and eating up CPU for nothing really useful. 
Considering clock domains will also let you identify when you need a circular buffer, or when you can do stuff in-place.

[LTT-ng to debug CPU load issues due to short-lived thread spamming](res/embl_debugging_4_lttng_system_analysis.pdf)
   
 
## Linux : do not write your own Inter Process Communication (IPC) layer


I’m sure you’ve learned about pipes, fifos, shm, mqueues, file tokens, unix or tcp sockets at school, but do not use them directly because you are no longer writing educational code, you are taking part to a project that has a commercial purpose ! If you have to have multiple apps or thread communicating, __look into well design and maintained IPCs__ like _DBUS_ or the _nanomq, nanomsg, zMQ_ family, because :

-	Those project will do the right thing and use the fastest implementation (shm, pipes, fifos, sockets) internally, based on the scheme you need
-	Those projects are maintained by people who’s expertise it is to design and optimize IPCs
-	In some occurrences, you will find generators, that will allow you to focus on your data-model and interfaces specification, rather than cabling the bindings over an IPC, for instant checkout FrancaIDL and Common API on top of DBUS.
-	My team and I have also developed model-based binding generation on top of MQTT for instance.

IPC is a domain and field of expertise on its own, sure you can fiddle something, but later you will have to solve tons of problems, like for instance : "how do I make sure A can start asynchronously of B, even if A need to subscribe an interface provided by B". And thats just one example. 

If your devices will have a machine to machine (M2M) integration, or some sort of 3rd Party integration, __look at standard M2M strategies and protocol__:

-	MQTT, LWM2M or similar
-	Google Buffers to shove json or yaml around (on larger systems)
-	RestFull API

So that your 3rd-party partner, or just a different team in your organization can focus on their functional blocks and data, rather than having to learn and link your stuff proprietary stuff.
[zMQ in a hundred words](https://zguide.zeromq.org/#ZeroMQ-in-a-Hundred-Words)
[MQTT versus HTTP for IoT](https://www.hivemq.com/blog/mqtt-vs-http-protocols-in-iot-iiot/)
    
   

# Bonus : Op-Ed or Rant, or Manifesto of some sort….

* __Note1: I’m never the one bringing up those topics in the first place, but while we’re at it …__
* __Note2 : this is my personal opinion and does not reflect any practices or endorsement any of my former of current employers__

## About good practices and valuable experience


So, if I may say at this point, putting modesty aside : I’ve seen the same mistakes, heard the same BS again and again (practiced some of it too in my early days), and I had to fight on many occasions recurring false-good-ideas. All I want and enjoy nowadays is __making sure mistakes are possibly avoided went it comes to embedded stuff__, and this is perhaps my main added value.

Oh, and __NO : ChatGPT will not provide you that ! yet…__ because you cannot trust what 80% of the quacks do and say, but ought to look instead at what is practiced by those industries who can neither afford to spend more than 3 years in development before hitting the market, nor to have 100k+ angry customers complaining for bugs.

  
## About personal Values and thoughts


I admire __talent, commitment and efficiency at work__.

I also learned to __praise kindness and magnanimity__. Because there are some nasty people out-there especially in the Linux/Open Source community. I once had to endure a poor lad as a colleague, that was tasked writing merely device-tree bindings (still does), waiting to (and seemingly obsessed by) making “a name for himself” whatever that means when you write opens-source software. He was raging of jealousy because I had committed a little something to mainstream Linux, to the point where he felt compelled to sabotage my work, literally breaking a setup I provided for a trade show display.

[kindness in management : virtue or curse?](https://www.bworldonline.com/banking-finance/2024/10/18/628839/kindness-in-management-virtue-or-curse/)
   

## About Tech Companies


While __multinational companies are the ones where you can learn a lot__, because they have the resources for ambitious projects that get you in touch with state of the art technology providers, __they’ve been plagued during most of this decade by an “ill-globalist” and “ill-progressist” agenda__, most of the time promoted or even imposed by non-elected bureaucrats based on ideology, moral mediocrity, carelessness or shear ignorance of the reality on the ground.

   

Just consider the EU press release from 2022 regarding suppression of CO2 emitting cars (because the German coal-based power plants do not emit CO2 as we all know…), that almost led to completely wiping the EU car industry.

Today SpaceX and Blue-origins, both private US rocket companies do first-stage reuse almost as a banality, while the “best of the best” of all EU states put together merely achieves an mid-life update of a launcher paradigm from the 1990s, already doomed to commercial failure.

[EU commission at its best](https://ec.europa.eu/commission/presscorner/detail/en/ip_22_6462)
[Ariane 6: Barely Launched, Already Outdated](https://www.challenges.fr/entreprise/aeronautique/premier-vol-d-ariane-6-faut-il-deja-lancer-le-successeur-ariane-next_898934)
  


Top leaders of most multinational groups are voluntarily infected by the cynical and factually failing agenda of the globalized institutions (banking system, economic forums and global leader societies of all sorts). Middle-management is most of the time just trying to remain under the radar and distinguish itself in implementing that stupid counter-productive agenda. 

In the EU, __small tech companies are the ones that can cut off the bullsh*t__, especially as the incoming years will unfortunately see the EU economy optimized (i.e. sacrificed) to sustain the economy of the USA while they prepare for a not-so-cold war against China and against what will remain of Russia (i.e. pretty much all of it). Something has to give; and it might well be our standard of living …

__Small companies must aim for the high-end, high added-value more than ever__, and must be accompanied with this by entities that are mindful of regional/[__national interests__](https://www.lanouvellerepublique.fr/chatellerault/marelli-une-chaine-de-production-marocaine-relocalisee-a-chatellerault) and specificities. There not much to expect from professional politicians who have nothing but having read _Madame Bovary_ (in France) as their sole qualification to show the way.
  


## About DEI

I positively don’t pay any attention and consideration to the looks, the gender, the religious and political believes, the way of living, the various inclination and tastes of anyone at work.

I definitively don’t care and I’m finding it a very disingenuous, shabby and a badly damaging thing to do, to promote or demote someone based on any other criteria then required commitment and talent for the proposed job.

[US State and University getting rid of DEI] (https://www.goldwaterinstitute.org/three-more-states-drop-dei-programs-at-their-public-universities/)
    
  People who keep pointing fingers at others ought to be fired, mind about your own trousers...

  <img src="res/monkeys.png" alt="wisdom">
  













