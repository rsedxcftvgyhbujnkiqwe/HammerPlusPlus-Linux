# HammerPlusPlus-Linux
Tutorial on getting [HammerPlusPlus](https://ficool2.github.io/HammerPlusPlus-Website/index.html) by ficool2 working on linux.

### Caveats
- This has only been tested on an arch based distro, because that's what I use. I don't know how well it works anywhere else.
- You will be unable to access the Tools->Options menu to edit hammer configuration. All hammer configuration must be done through the hammerplusplus_gameconfig.txt file situated in bin/hammerplusplus, and other such files for other configuration options.
- As of writing this I have not fully gotten FGDs to function, so custom entity models and sprites do not work (such as from Spud's FGD)
- As of writing this I have not tested compiling. I may write an additional guide on getting Compilepal working, but if it doesn't work then I suspect it will just require you to manually run the build steps with wine.

## Step 1: Requirements
You will obviously need steam and proton. To get proton installed if you don't have it already, with steam play enabled, go to your steam library and display tools to manually install proton. Alternatively, right click a game and go to compatability settings, then force a specific compatability tool and set it to whichever proton version. In my case I have proton 7.0.3 and experimental installed.
### Hammer
To get hammer files on linux you will have to jump through some hoops. Essentially, when you install TF2 normally on linux, only the linux files are generated. No windows files or exes are generated, meaning the entirety of hammer will be missing. However, proton runs windows programs, so if you force TF2 to use proton through the aforementioned compatability settings, it will generate the windows files. The problem is that if you then turn compatability off so you can actually play the game, steam will remove all the windows files it just generated. You can easily solve this by going into your TF2/bin/ folder, compressing/moving all the newly generated windows files, then turning off compatability and moving them back. Note that if you ever change compatability settings hammer will re-delete these files! I keep them in a tar.gz just in case I need to quickly fix the files.
### Hammer++
Naturally you will need [hammer++](https://ficool2.github.io/HammerPlusPlus-Website/download.html) installed. Download and installation are the same as windows, just put the files in your bin/ folder. 
### proton-caller
My weapon of choice for this exercise is [proton-caller](https://github.com/caverym/proton-caller) by caverym, a rust wrapper for proton that makes it easy to run exes. This will be our method of invoking proton to run hammer.

Install proton-caller through whichever way you prefer (AUR for arch users).
You will need to create the ~/.config/proton.conf file in order for proton-caller to function properly. I used the example settings from the repo page. 

**The most important thing in this step is that you note where your 'data' directory is. In my case, it's stored in /home/user/Documents/Proton/env/. You do not have to use this, but I'm going to be using this path for the rest of the tutorial so substitute yours instead if you use an alternate path.**

## Step 2: Proton setup
Run the following to create the directories we need:
```
mkdir -p '/home/user/Documents/Proton/env/Proton 7.0/pfx/drive_c/Program Files (x86)/Steam/steamapps/common'
```
If you're unfamiiar with how wine and proton works, it essentially tricks the program into thinking it's running in a windows environment by recreating the windows filestructure. Hammer cares deeply about running in a windows filestructure, so this is important.

Next, the likely single most important step, we create a symbolic link from our TF2 directory to this dummy directory. This way, hammer will still be tricked into running in the windows filesystem, but it will have access to all of our TF2 files in our actual system, so we don't have to duplicate the whole TF2 folder structure and waste space.
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
proton-caller -r hammerplusplus.exe
```
I would recommend something to the effect of the following for an alias, appended to your ~/.bashrc file.
```
alias starthammer='proton-caller -r "/home/user/.local/share/Steam/steamapps/common/Team Fortress 2/bin/hammerplusplus.exe"'
```
## Step 4: Configuring hammer
Once hammer is running, you're ready to start configuring it. 
### gameconfig.txt
To be done
### accessing files
If you are in h++ and try to open files, you may notice you can't see any dotfiles, which means if steam was installed in the default location you can't open your mapsrc folder. You have two ways around this. I would recommend enabling dotfiles first, because hammer tends to remember your directory after the first time, which makes the symlink a bit overkill. If you need to enter the directory multiple times, you are probably better off with the symlink.
- Edit the winecfg for proton to allow dotfiles

Run the following 
```
WINE_PREFIX='/home/user/Documents/Proton/env/Proton 7.0/pfx/' winecfg
```
If you get an error, check to make sure that the wine process is stopped with `ps aux | grep "wine"`. You can run a kill -3 <id> on all wine processes if they are being stubborn.
- Symlink your TF2 directory to ~
  
Run the following
```
ln -s '/home/user/.share/local/Steam/steamapps/common/Team Fortress 2' '/home/user/TF2'
```
This will create a TF2 folder in your home directory, so when you enter the file picker you can simply go to /home/user/TF2 and access your files.

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
Submit an issue detailing your problem and how to reproduce the bug and I'll see if I haven't already come across it yet.
