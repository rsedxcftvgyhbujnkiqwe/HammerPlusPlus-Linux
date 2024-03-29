# HammerPlusPlus-Linux
Tutorial on getting [HammerPlusPlus](https://ficool2.github.io/HammerPlusPlus-Website/index.html) by ficool2 working on linux for Team Fortress 2.

Check out my [hammer scripts](https://github.com/rsedxcftvgyhbujnkiqwe/linux-hammer-scripts) for some helpful tools with material creation.

Table of contents:

[Step 1: Requirements](#step-1-requirements)

[Step 2: Proton Setup](#step-2-proton-setup)

[Step 3: Running hammer](#step-3-running-hammer)

[Step 4: Configuring hammer](#step-4-configuring-hammer)

[Step 5: Compiling maps](#step-5-compiling-maps)

[Step 6: Miscellaneous](#step-6-miscellaneous-tips-and-info)

[Errors](#Errors)



### Caveats
- This has been tested on Artix and Gentoo and it worked in both (worked better in gentoo actually). I don't know how well it works in Debian distros or anything else
- This has only been tested on Team Fortress 2. I don't make maps for any other game and have not tested it there. I'm sure most of the steps still work but if not then please try to figure it out on your own, my only concern is TF2.
- You will be unable to access the Tools->Options menu to edit hammer configuration. All hammer configuration must be done through the hammerplusplus [config files](#hammerplusplus_gameconfigtxt).
- Compilepal has a buggy UI on linux (must be running >V28-RC). I suspect this has to do with what windows version wine is using, but I haven't tested it.
## Introductory notes
- This tutorial utilizes a lot of symlinks. Make sure you read through the whole tutorial, because if you skip later on and don't understand where I get a directory from, it's probably because I used a symlink.

## Step 1: Requirements
You will obviously need steam and proton. In my case I have proton 7.0.3 and experimental installed. This guide is not intended to handhold you through the process of installing proton, it is assumed that since you are using linux you at least know how to use a search engine to get it installed.
### Hammer
To get hammer files on linux you will have to jump through some hoops. Essentially, when you install TF2 normally on linux, only the linux files are generated. No windows files or exes are generated, meaning the entirety of hammer will be missing. However, proton runs windows programs, so if you force TF2 to use proton through the compatability settings, it will generate the windows files. The problem is that if you then turn compatability off so you can actually play the game, steam will remove all the windows files it just generated. You can easily solve this by going into your TF2/bin/ folder, compressing/moving all the newly generated windows files, then turning off compatability and moving them back. Note that if you ever change compatability settings hammer will re-delete these files. I keep them in a tar.gz just in case I need to quickly put them back.

Note that you will potentially need to start regular hammer at least once for it to generate some initial files. You can run it later on using proton caller or with wine, whichever method just to gen the required files.
### Hammer++
Naturally you will need [hammer++](https://ficool2.github.io/HammerPlusPlus-Website/download.html) installed. Download and installation are the same as windows, just put the files in your bin/ folder. 
### proton-caller
My weapon of choice for this exercise is [proton-caller](https://github.com/happymintyo/Proton-Caller) by ~~caverym~~ happymintyo(repo was changed?), a rust wrapper for proton that makes it easy to run exes. This will be our method of invoking proton to run hammer. Alternatively, you can proton in some other way, but keep in mind you will need to determine where the prefix is located.

Install proton-caller through whichever way you prefer (AUR for arch users, binary for everyone else).
You will need to create the ~/.config/proton.conf file in order for proton-caller to function properly. I used the example settings from the repo page. 

**The most important thing in this step is that you note where your 'data' directory is. In my case, it's stored in /home/user/Documents/Proton/env/. You do not have to use this directory, but I'm going to be using this path for the rest of the tutorial so substitute yours instead if you use an alternate path.**

## Step 2: Proton setup
Run the following to create the directories we need:
```
mkdir -p '/home/user/Documents/Proton/env/Proton 7.0/pfx/drive_c/Program Files (x86)/Steam/steamapps/common'
```
If you're unfamiliar with how wine and proton works, it essentially tricks the program into thinking it's running in a windows environment by recreating the windows filestructure. Hammer cares deeply about running in a windows filestructure, so this is important.

Next, the likely **single most important step**, we create a symbolic link from our TF2 directory to this dummy directory. This way, hammer will still be tricked into running in the windows filesystem, but it will have access to all of our TF2 files in our actual system, so we don't have to duplicate the whole TF2 folder structure and waste space. Note that hammer WILL be looking in the dummy directory for most things, such as gameinfo.txt which is required to even open the program.
```
ln -s '/home/user/.local/share/Steam/steamapps/common/Team Fortress 2' '/home/user/Documents/Proton/env/Proton 7.0/pfx/drive_c/Program Files (x86)/Steam/steamapps/common/Team Fortress 2'
```
Confirm this worked by running:
```
ls '/home/user/Documents/Proton/env/Proton 7.0/pfx/drive_c/Program Files (x86)/Steam/steamapps/common/Team Fortress 2'
```
If you see files from the TF2 directory (bin/tf/hl2) then it worked and you can move on. If not, please confirm the paths you used are correct in the symlink section above
## Step 3: Running hammer
At this point hammer should be ready to go. If you've set up proton-caller and proton correctly, and have made sure the windows hammer files are your TF2/bin/ folder, you can run the following command to start hammerplusplus. Note that this assumes your pwd is bin/, if you aren't within it or are making a bash alias, simply append the full file path to the exe name.
```
proton-call -r hammerplusplus.exe
```
I would recommend something to the effect of the following for an alias, appended to your ~/.bashrc file.
```
alias starthammer='proton-call -r "/home/user/.local/share/Steam/steamapps/common/Team Fortress 2/bin/hammerplusplus.exe"'
```
## Step 4: Configuring hammer
Once hammer is running, you're ready to start configuring it. 
### accessing files
If you are in h++ and try to open files, you may notice you can't see any dotfiles, which means if steam was installed in the default location you can't open your mapsrc folder. You have two ways around this. I would recommend enabling dotfiles first, because hammer tends to remember your directory after the first time, which makes the symlink a bit overkill. If you need to enter the directory multiple times, you are probably better off with the symlink.
#### Edit the winecfg for proton to allow dotfiles
Run the following 
```
WINEPREFIX='/home/user/Documents/Proton/env/Proton 7.0/pfx/' winecfg
```
If you get an error, check to make sure that the wine process is stopped with 
```
ps aux | grep "wine"
```
You can run a kill -9 <id> on all wine processes if they are being stubborn.
#### Symlink your TF2 directory to home
Run the following
```
ln -s '/home/user/.local/share/Steam/steamapps/common/Team Fortress 2' '/home/user/TF2'
```
This will create a TF2 folder in your home directory, so when you enter the file picker you can simply go to /home/user/TF2 and access your files.
### FGDs 
The installation process for FGDs is the same as on windows. If you want to install ABS, run the installer exe using proton-call so that it will install it to your TF2 directory. If you are exceptionally stubborn and didn't want to symlink your TF2 folder to proton back in step 2, at this stage you will suffer the consequences.

### hammerplusplus_gameconfig.txt
As for actually loading the fgds, this can be done in the hammerplusplus_gameconfig.txt file, located in bin/hammerplusplus/. The following are what I have in my file, loading both the fgd for propper and spud's fgd. If you wish to use any other fgds, you can add them here. Spud does not require manaully adding the ABS fgd.
```
"Hammer"
{
"GameData0"		"C:\Program Files (x86)\Steam\steamapps\common\Team Fortress 2\bin\propper.fgd"
"GameData1"		"C:\Program Files (x86)\Steam\steamapps\common\Team Fortress 2\bin\tf2_spud.fgd"
}
```
In this file you can also edit certain configs, such as DefaultSolidEntity (when you change a brush to entity) and DefaultPointEntity (default point entity when created with the entity tool).
### hammerplusplus_settings.ini
This ini file, also situated in bin/hammerplusplus, contains a few groups of settings that are locked in the Tools->Options menu. Therefore you can edit this file in order to change them. There are far too many settings for me to cover in this section, so I'd just recommend opening it up and skimming through it and seeing if any settings catch your eye.
## Step 5: Compiling maps
**The following steps all assume you are in the expert window of the run map menu. Access this by pressing F9 and clicking "Expert" at the botom. You can either edit the default or make a new one, I edit the default config for my example.**
### Compiling
Compiling works just fine since hammer is being run in a proton environment. You can just compile maps the good old fashioned way with your F9 menu, and you can even use the custom build programs of your choosing (they can be set in the [config file](#hammerplusplus_gameconfigtxt) or by manually editing the run steps).

However, compilepal actually works with **wine** (NOT proton) as of V28-RC1. You can use this as a better method for packing. Somethings to keep in mind:
- Requires wine, proton doesn't work properly with it. You'll need to symlink the TF2 directory into your wine prefix in the same way to how the proton prefix is symlinked.
- You will (probably) need to edit the gameConfiguration.json manually to fix the paths. Since it's running through wine, they should be windows paths (starting with C:\) and not linux paths.
- The UI turns black when focused, only showing what you are hovering over. However technically all the buttons still work and there's nothing else wrong with the compiling itself, so if you can handle the UI issues it's the superior compiling method (considering it is one of the only options on linux for automated packing)
- Regular hammer, and compilepal, cannot start the game for you since you don't have an exe for TF2. If you need to compile cubemaps, you'll need to do that manually. You can peruse the compilepal code to see exactly what options it does, but your best bet is just going to the valve wiki to learn how to compile cubemaps manually.
## Step 6: Miscellaneous tips and info
Section for anything extra that you should know that doesn't fit into anywhere else
### Laggy screen updating
In my experience when using an arch based distro, whenever I change ANYTHING, all 4 windows do not update - only the screen the change was made on. Changes include boxing, selection, zooming, etc. You have to mouse over them to make them update. Get into the habit of doing little circles with your mouse near the center to update all the screens every once and a while. I didn't have this problem on gentoo using just i3 and xorg, so it might have been specific to KDE. Regardless, keep your eye out for it. It might seem annoying at first but you'll get used to it.
### Model browser
Try to turn off "Use grouped mod sorting" if it isn't loading. If it does load, no need to turn this off.
# Errors
I only ran into a few, and should people make issues with other bugs I may address them here if they are noteworthy.
### proton exited with: code: 53
You likely do not have the [hammer windows files](#hammer) in.
### various missing hammer file errors
If you missed it back in step 1, you may need to run hammer.exe once to generate files for the first time, you can simply run the following to do so. You can close out after it launches.
```
proton-call -r hammer.exe
```

### My error isn't listed!
Submit an issue detailing your problem and how to reproduce the bug and I'll see if I haven't already come across it yet. However if you followed the steps properly, there should be no issues. If you are experiencing problems, try redoing steps or starting over before posting here.
