# HammerPlusPlus-Linux
Tutorial on getting [HammerPlusPlus](https://ficool2.github.io/HammerPlusPlus-Website/index.html) by ficool2 working on linux for Team Fortress 2.

Check out my [hammer scripts](https://github.com/rsedxcftvgyhbujnkiqwe/linux-hammer-scripts) for some helpful tools with material creation.

### Caveats
- This has only been tested on Team Fortress 2. I don't make maps for any other game and have not tested it there. I'm sure most of the steps still work but if not then please try to figure it out on your own, my only concern is TF2.
## Introductory notes
The main hurdles to jump over when running hammer are to make sure gameinfo.txt + other libraries are accessible via proton.

## Installation
### Hammer
To get hammer files on linux you will have to jump through some hoops. Essentially, when you install TF2 normally on linux, only the linux files are generated. No windows files or exes are generated, meaning the entirety of hammer will be missing. However, proton runs windows programs, so if you force TF2 to use proton through the compatability settings, it will generate the windows files. The problem is that if you then turn compatability off so you can actually play the game, steam will remove all the windows files it just generated. 

To quickly solve this problem, you can perform the following steps:
1. Set TF2 to use a proton compat tool and click Update so it generates the files
2. Navigate to your `Team Fortress 2` directory
3. Rename your `bin` folder to something else, like bin.bak
4. Reset TF2 so it doesn't use proton
5. Use a tool like `rsync` to move your bin files back, such as `rsync -av bin.bak/* bin/`
6. (Optional) Remove the backup bin now that you've resynced

You may need to start regular Hammer at least once to have it generate some files, you can use regular wine for it.

If TF2 hammer files ever update, you can follow these steps once again to regenerate the files necessary.

### Hammer++
Naturally you will need [hammer++](https://ficool2.github.io/HammerPlusPlus-Website/download.html) installed. Download and installation are the same as windows, just put the files in your bin/ folder. 

### Steam setup
In order to run hammer++ we will be using Steam as the runner.
1. On Steam in the bottom left, `Add a Game > Add a Non-Steam Game...`
2. Press `Browse` and navigate to the hammerplusplus.exe. You can find it at `/home/user/.local/share/Steam/steamapps/common/Team Fortress 2/bin/x64/hammerplusplus.exe`, you will have to navigate there manually.
3. `Add Selected Programs` and then in `Properties`, force the use of a compatibility tool and set it to use `Proton 7.0-*`. Higher protons versions do not work and have color problems.
4. Turn off `Enable the Steam Overlay while in-game` (optional)
5. Set your launch options to `PROTON_USE_WINED3D=1 %command%`. This is required to fix visual glitches
6. Run it from your library once. You will get an error about gameinfo.txt
### Proton Symlink setup
In order for hammer to run properly, it will need access to your TF2 files. We will symlink the directory to the proton prefix so that it can find all the necessary files

To figure out which folder you need to symlink, you can either use protontricks or find it manually. 

With **protontricks**, you can run `protontricks -l` and note the number for the hammerplusplus.exe entry. In my case, it was `3801452119`. This will be your compatdata folder

To find it **manually**, navigate to `/home/user/.local/share/Steam/steamapps/compatdata/` and look for the folder with the modified date of the current date.

Once you locate the directory, run the following to create the directoreis:
```
mkdir -p '/home/user/.local/share/Steam/steamapps/compatdata/{folder_id}/pfx/drive_c/Program Files (x86)/Steam/steamapps/common'
```
Then create the symlink
```
ln -s '/home/user/.local/share/Steam/steamapps/common/Team Fortress 2' '/home/user/.local/share/Steam/steamapps/compatdata/{folder_id}/pfx/drive_c/Program Files (x86)/Steam/steamapps/common/Team Fortress 2'
```
### Running hammer
At this point hammer++ should be ready to go. You can simply run hammer++ from your steam library
### Accessing files
If you are in h++ and try to open files, you may notice you can't see any dotfiles, which means if steam was installed in the default location you can't open your mapsrc folder. I would recommend symlinking your TF2 directory somewhere easy to access. You can also search up how to enable dotfiles in a wine prefix, though I won't include that here as I believe symlinks are a far superior way.

Run the following to link your TF2 directory into your home dir. Alternatively, replace the link location with wherever you'd like it to be located.
```
ln -s '/home/user/.local/share/Steam/steamapps/common/Team Fortress 2' '/home/user/TF2'
```
This will create a TF2 folder in your home directory, so when you enter the file picker you can simply go to /home/user/TF2 and access your files.
### Compiling maps
Compiling works just fine since hammer is being run in a proton environment. You can just compile maps the good old fashioned way with your F9 menu, and you can even use the custom build programs of your choosing.

If you'd like to use compilepal, I've had success running it with **wine** (NOT proton) as of V28-RC1. This is also the premiere tool for packing materials into your map, so get familiar with it
### Model browser
Try to turn off "Use grouped mod sorting" if it isn't loading. If it does load, no need to turn this off.
### Various missing hammer file errors
If you missed it at the start, you may need to run hammer.exe once to generate files for the first time, you can simply run the following to do so. You can close out after it launches.
### Notes
Wine >=10.3 may be able to launch hammerplusplus on it's own. If you prefer to use that, the steps are similar and require launching the h++ exe with your TF2 directory symlinked into the wine prefix.
