This module is for those who want to really stretch their battery and is designed to be highly configurable.

The main goal is to maximize battery life as much as possible while still remaining functional.

Most of the options are safe, meaning 

1. You dont have to worry about bricking or bootloops.

2. Some options may produce lag, late mesaages, or missed alarms. 

You should probably start slow and activate 1 at a time with a little testing.

Everything has been built and tested on a Pixel 5 running ProtonAOSP.

# Disclaimer
I am NOT responsible for any damages or data loss this software may cause. By using this software you accept the risks of using it.

# What can this do?
- It can kill, renice, or suspend apps
- It can put CPU cores into powersave mode.
- It can completely disable CPU cores.
- It can disable Google Play Services(gms).
- It can manage process priorities

# Installation

1. Install using Magisk Manager and reboot.
2. Configure the options in the config file `/data/local/tmp/XtremeBS/XtremeBS.conf`

# Configuration
After you reboot from installation, XtremeBS drops a premade config file in `/data/local/tmp/XtremeBS/XtremeBS.conf`.

Every time you change the config, you must reboot or reload using `XBSctl reload`.

## Config Options

The config uses a simple `key=value` format

### trigger
This option specifies when to trigger power saving.

Options:

`trigger=boot` The Battery saver starts at boot and is manually controlled using `XBSctl`. SAFE, BUT NOT RECCOMMENDED.

`trigger=auto` RECCOMMENDED/DEFAULT. This will trigger when Androids "Battery Saver" is toggled.

`trigger=manual` For manual control using XBSctl

### delay

It sets a delay in seconds between checking for the trigger or commands to not spam the CPU.

Default is 3.

`delay=3`

### allowlist
This takes a path to a file.

Default is `allowlist=/data/local/tmp/XtremeBS/apps.allow`

This file is not created, you must create it yourself and add **package** names to it, 1 per line.

As a bare minimum, you will need: your keyboard app, magisk manager (which is probably hidden, so wont be com.topjohnwu.magisk), and a terminal emulator (Termux).

That would look something like this

`com.topjohnwu.magisk
com.termux
com.google.android.inputmethod.latin`

You can find the package name of an app by long pressing the app you want on your home screen or app drawer, then selecting app info, and scroll to the bottom of that page.

If you set `handle_apps=suspend` you should probably add whatever you know youre going to use because you wont be able to open any other apps when the trigger is active.

If you fuck this up and dont add things to the allowlist for suspend mode, you will have to use adb from a PC.
`adb shell XBSctl safe`

### denylist
By design, handle_apps only deals with user installed apps and not system apps to maintain stability.
If you so desire, you can use this denylist to deal with system apps.

default is `denylist=/data/local/tmp/XtremeBS/apps.deny`

You will have to create it to use it. It isnt mandatory.


### handle_apps
This setting turns on or off the handling of apps.

Options:

`handle_apps=suspend` RECCOMMENDED
This will suspend every app. You cannot open suspended apps.
Be sure to have a whitelist in place.

`handle_apps=nice` DEFAULT

`handle_apps=kill` this just force stops all apps.
They can be restarted by other apps and you. 
This may take up more battery because of wasted cpu cycles.


### handle_cores
This is like `handle_apps` but with CPU cores instead.

You might be asking, "this seems kind of risky. *how are* cores handled?". Thats a good question, with a simple answer. If this setting is turned on, it changes the CPU governors to powersave if its supported.

`handle_cores=auto` RECCOMMENDED, XtremeBS will figure it out for you.

`handle_cores=false` DEFAULT

`handle_cores="cpu4 cpu5"` a manual selection of the cores you want to put in powersave mode.

### disable_cores
Which CPU cores to disable, if any.

`disable_cores=false` DEFAULT. Turns the setting off

`disable_cores=auto` RECCOMMENDED.
This automagically disables only the high power cores which *might* produce a tiny bit of lag, if youre okay with that.

`disable_cores="cpu6 cpu7"` you can manually set the cores you want to disable.

### handle_gms
This decides how to handle Google Play Services, which is a battery hog.

`handle_gms=false` sets to not even bother with gms

`handle_gms=nice` DEFAULT. Sets the nice level to 19, the lowest priority.

`handle_gms=kill`
WARNING! If you use this option, it will break google apps until you change the option. Also, YOU WILL FAIL SAFETYNET AND PLAY INTEGRITY. It just disables the package.


### handle_proc
Whether we should renice system daemons and processes. This is touchy. Whatever you put in the proc_file, will be reniced with a value of 10. You might get messages late, you might miss alarms, who knows. Just be careful.

`handle_proc=false` DEFAULT

`handle_proc=true`

### proc_file
This file is where you put the processes you want to use less CPU power on. Personally, i put netd and system_server in here. You dont have to give it full paths, but you might want to, just to be on the safe side. One process per line.

`proc_file=/data/local/tmp/XtremeBS/proc.list` DEFAULT

Here is an example of what my proc_file looks like:
`/system/bin/netd
system_server`

If you want to find processes that use high cpu, you can use this command and it will sort the highest to the bottom. `su -c ps -eo "%cpu pid cmd" | sort -n -k1,1`

### low_ram
This sets the low_ram flag to true, so the system tries to use less and therefore save power.

`low_ram=true` DEFAULT

`low_ram=false`


## XBSctl
This is how you control the daemon. 

commands are:

`start` Starts XtremeBS

`stop` Stops XtremeBS

`pause` Pauses trigger handling.

`resume` Resumes trigger handling or gets out of safe mode.

`reload` Reloads the config file. You must not be paused for this.

`safe` Safemode. If you mess up, use this. It stops XtremeBS and waits until you tell it to Resume, Then reloads the config.



