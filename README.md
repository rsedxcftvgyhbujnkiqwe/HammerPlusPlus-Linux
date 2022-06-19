# HammerPlusPlus-Linux
Tutorial on getting [HammerPlusPlus](https://ficool2.github.io/HammerPlusPlus-Website/index.html) by ficool2 working on linux for Team Fortress 2.

Table of contents:

[Step 1: Requirements](#step-1-requirements)

[Step 2: Proton Setup](#step-2-proton-setup)

[Step 3: Running hammer](#step-3-running-hammer)

[Step 4: Configuring hammer](#step-4-configuring-hammer)

[Step 5: Compiling maps](#step-5-compiling-maps)

[Errors](#Errors)



### Caveats
- This has only been tested on an arch based distro, because that's what I use. I don't know how well it works anywhere else.
- This has only been tested on Team Fortress 2. I don't make maps for any other game and have not tested it there. I'm sure most of the steps still work but if not then please try to figure it out on your own, my only concern is TF2.
- You will be unable to access the Tools->Options menu to edit hammer configuration. All hammer configuration must be done through the hammerplusplus [config files](#hammerplusplus_gameconfigtxt).
- You will not be able to use compilepal and will have to go through the map compilation process manually. If you get compilepal working on linux, please let me know.

## Introductory notes
- This tutorial utilizes a lot of symlinks. Make sure you read through the whole tutorial, because if you skip later on and don't understand where I get a directory from, it's probably because I used a symlink.

## Step 1: Requirements
You will obviously need steam and proton. In my case I have proton 7.0.3 and experimental installed. This guide is not intended to handhold you through the process of installing proton, it is assumed that since you are using linux you at least know how to use a search engine to get it installed.
### Hammer
To get hammer files on linux you will have to jump through some hoops. Essentially, when you install TF2 normally on linux, only the linux files are generated. No windows files or exes are generated, meaning the entirety of hammer will be missing. However, proton runs windows programs, so if you force TF2 to use proton through the compatability settings, it will generate the windows files. The problem is that if you then turn compatability off so you can actually play the game, steam will remove all the windows files it just generated. You can easily solve this by going into your TF2/bin/ folder, compressing/moving all the newly generated windows files, then turning off compatability and moving them back. Note that if you ever change compatability settings hammer will re-delete these files. I keep them in a tar.gz just in case I need to quickly put them back.
### Hammer++
Naturally you will need [hammer++](https://ficool2.github.io/HammerPlusPlus-Website/download.html) installed. Download and installation are the same as windows, just put the files in your bin/ folder. 
### proton-caller
My weapon of choice for this exercise is [proton-caller](https://github.com/caverym/proton-caller) by caverym, a rust wrapper for proton that makes it easy to run exes. This will be our method of invoking proton to run hammer.

Install proton-caller through whichever way you prefer (AUR for arch users).
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
ln -s '/home/user/.share/local/Steam/steamapps/common/Team Fortress 2' '/home/user/TF2'
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
I was unable to get compilepal to work. Therefore, we will use the traditional tools. If you intend to have hammer automatically do these steps, make sure you follow the order at the end of this section, or it will not work.
  
**The following steps all assume you are in the expert window of the run map menu. Access this by pressing F9 and clicking "Expert" at the botom. You can either edit the default or make a new one, I edit the default config for my example.**
### Compiling
Compiling works just fine since hammer is being run in a proton environment. You can just compile maps the good old fashioned way with your F9 menu, and you can even use the custom build programs of your choosing (they can be set in the [config file](#hammerplusplus_gameconfigtxt) or by manually editing the run steps).
### Packing
To automate packing, we will use bppu by Squishy. It's a python script that automates the packing of the file. You will need to install python in order to run this, which is something I am not including in this tutorial because it can easily be found on search engines.
  
At this point, the bsp is still in the mapsrc folder. So we'll need to move the step that copies the map from the mapsrc folder to the tf/maps folder up, so that it happens before the packing step. If you still want the finished map to go into your mapsrc folder, you can add an additional copy file step that has the paths in reverse order. Note that if you end up doing this, the file in mapsrc will not be the final version of the map unless you do an additional copy step to put it back (though that should be done post-repack). You may also choose to have multiple Copy File steps for convenience, just make sure you keep track of where the file is during any given step. 
  
This command is in most of the run steps, but if you want it for reference I will put it here.
- $path -> mapsrc
- $bspdir -> tf/maps
  
Command:
```
Copy File
```
Parameters:
```
$path\$file.bsp $bspdir\$file.bsp
```

Anyways, let's get on to the packing. In order to run python, we will have to call it with bash within wine which is annoying. Therefore we will make a batch script, that calls a bash script, that calls python, that calls bppu. **This step is incomplete as of now, as the run steps do not wait for the packing to finish before continuing. I will figure out a workaround**
  
In my example I will be putting the script in my TF2 folder. You don't need to use this directory, I am using it once again for convenience. The file structure will look like the following:
```
common/Team Fortress 2/hammerscripts/bppu/bppu.py
```

Now we will create the two scripts that we have to use in order to invoke bppu. Where you put these scripts is your choice, I choose to put them in the common/Team Fortress 2/hammerscripts directory, which makes everything easier later for the paths,

#### runbppu.bat  
````
start /unix /usr/bin/bash /home/mare/TF2/hammerscripts/runbppu.sh
````
#### runbppu.sh
```
#! /usr/bin/bash
python bppu/bppu.py /maps/$1.bsp -parse -pack
```
### Cubemaps
Running cubemaps is just as hacky as the python script. **This step is incomplete as of now, as the run map does not wait for the game to finish before continuing. I will figure out a workaround**
  
Where you put these scripts is your choice, I choose to put them in the common/Team Fortress 2/hammerscripts directory, which makes everything easier later for the paths.
#### runcubemaps.bat
```
start /unix /home/user/TF2/hammerscripts/runcubemaps.sh %1%
```
#### rungame.sh
The following file is an example of cubemap settings you can use. I borrowed these from the [compilepal settings](https://github.com/ruarai/CompilePal/blob/master/CompilePalX/Compilers/CubemapProcess.cs), you can fine tune to your needs. You NEED to have -game /path/to/tf, +mat_specular 0, +map $1, and -buildcubemaps. Everything else is your decision.
  
The reason this is done this way is because TF2 must be run through the steam runtime, so we call the runtime first and then pass it the game's hl2.sh. Since I symlinked my TF2 directory and not my steam directory, in this case I have to manually navigate to it for the run.sh path, though you can feel free to symlink it if you choose. This path is never used again however so it's not necessary.
```
#! /usr/bin/bash
. /home/user/.local/share/Steam/ubuntu12_32/steam-runtime/run.sh /home/user/TF2/hl2.sh -game /home/user/TF2/tf -windowed -novid -sound +mat_specular 0 0 +map $1 -buildcubemaps -noborder -x 4000 -y 2000
```
#### Run step
You will need two steps for cubemaps. As alluded to at the beginning, you'll need a step to copy the file from mapsrc to tf/maps. You can use the default step provided in the run map menu, but if you want an example:
Next will be the actual cubemap run step. If you need to determine what your drive letter and path is, click 'Cmds' on the right and manually navigate to the batch file. The $file parameter is passed to the batch script as %1%, which is then passed to the shell script as $1.

Command:
```
D:\user\TF2\hammerscripts\rungame.bat
```
Parameters:
```
$file
```
### Repacking
Repacking is as simple as running the bspzip.exe onto the map. If you ran the cubemap step, the map will be in the tf/maps folder ($bspdir\$file.bsp). If you didn't run the cubemap step, you'll have to target the mapsrc folder ($path\$file.bsp). In my example I am assuming you ran the cubemap step and therefore the map is in tf/maps. I am also assuming the bspzip is in the default directory, change to suit your needs.
  
Command:
```
D:\user\TF2\bin\bspzip.exe
```
Parameters:
```
-game $gamedir -repack -compress $path\$file.bsp
```
The repacking step is run at the very end, so after this you can go ahead and do a Copy File step or just leave it as is. This is where the final location of the map will be at the end, though if you followed all the steps above by default it will be in tf/maps.
# Errors
I only ran into a few, and should people make issues with other bugs I may address them here if they are noteworthy.
### proton exited with: code: 53
You likely do not have the [hammer windows files](#hammer) in.
### various missing hammer file errors
You may need to run hammer.exe once to generate files for the first time, you can simply run the following to do so. You can close out after it launches.
```
proton-call -r hammer.exe
```
### My error isn't listed!
Submit an issue detailing your problem and how to reproduce the bug and I'll see if I haven't already come across it yet. However if you followed the steps properly, there should be no issues. If you are experiencing problems, try redoing steps or starting over before posting here.
