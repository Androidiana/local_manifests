# How to build
General assumption is, that you are familiar with building ROMs and how to use git etc.
The [LineageOS build instructions (example: hotdog device)](https://wiki.lineageos.org/devices/hotdog/build) should provide you with needed additional informations. 

## Initialize the build tree
Create the directory, which should contain your build tree and 'cd' into it.
```Shell session
repo init -u https://github.com/LineageOS/android.git -b lineage-17.1 --groups=all,-notdefault,-darwin,-mips
```

## Decide, what you want to do
The main purpose of this organization is to build the "hardened microG" variants for below listed devices:
- OnePlus 3T (oneplus3) [XDA Thread oneplus3](https://forum.xda-developers.com/t/rom-unofficial-10-0-microg-signed-hardened-lineageos-17-1-oneplus-3-3t.4219383/)
- OnePlus 7T Pro (hotdog) [XDA Thread hotdog](https://forum.xda-developers.com/t/rom-unofficial-10-0-microg-signed-hardened-lineageos-17-1-oneplus-7t-pro.4222945/)

You would like to build the "hardened microG" build variant for any of the above devices

## build the "hardened microG" variant for any of the above listed devices
Of course, you can use the below instructions for ANY device, but if you would like to build for any other device, 
you have to first replicate the device-specific repositories of your device, such as device-config, kernel and vendor blobs. 
To do so, the local_manifests directory needs to be updated accordingly (I am not going to explain that part, there is plenty of info available).

### Step 1 - Basic tree synchronization
```Shell session
cd .repo
git clone https://github.com/Androidiana/local_manifests 
cd local_manifests 
git checkout lineage-17.1
cd ../.. 
repo sync --no-tags
```

### Step 2 - Finalize tree setup
Copy and execute the scripts as shown below. 

**IMPORTANT:** `repo sync` performs a checkout without branch (detached state). 
The below scripts perform a 'real' checkout and will create the branch to be checked out. 
```Shell session
cp z_patches/croot-scripts/* .
./switch_microG.sh reference
./switch_microG.sh default
./switch_microG.sh microG
./switch_microG.sh hmalloc
./switch_microG.sh reference
```

### Step 3 - Adapt your preferences
If you want to **sign** your build with your own signing keys, make sure that the directoty `~/.android-certs` exists and contains your signing keys.
You have in the 'root' of your build tree a collection of shell scripts of the type build_*devicename*.sh, e.g. build_hotdog.sh for the hotdog device.
Edit the script of your choice to define your own ccache directory and (optionally) your build output directory (comment out, if you would like to use 
the default location `out/` within your build tree).

### Step 4 - Checkout the correct branches and build
- For the devices amami, h850, osprey and the android emulator, execute the checkout script as follows: `./switch_microG.sh microG`.
- For the devices hotdog and oneplus3, do `./switch_microG.sh hmalloc`
- To build without signing (to be more precise: sign with the publicly known test keys), execute `./build_*device*.sh test` (e.g. `./build_hotdog.sh test`)
- To build using your own signing key (which must be present in `~/.android-certs` !), execute `./build_*device*.sh sign` (e.g. `./build_hotdog.sh sign`)
- To build the android emulator, execute `./build_emulator.sh test` and to start it, execute `./start_emulator.sh`

### IMPORTANT: How to re-synch the build tree
Always execute `./switch_microG.sh reference`, before you perform another `repo sync` to synchronize your tree.
After having synchronized (you may have to perform a --force-sync, if further forks have been created), check whether the switch script has been updated in directory `z_patches/croot-scripts` and copy it into the 'root' of your build tree. Afterwards, switch to the specific build variant. 








