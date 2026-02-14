# case-scripts
Explanation how to fit an unlimited number of scripts into one, called "cases"

I no longer use separate bash scripts - one file for each task, which is why I deleted the repository named "convenience bash scripts". I moved to the so called "cases" which I'm gonna explain here how to create and use them.

"Why cases?" - Because case files means less separate files for each task. If until now you have had zero-compression.sh, max-compression.sh, max-compression-w-password.sh, now you need just one file. I used to have 260 .sh scripts. Now, thanks to the cases, I've reduced that numer to just 67 script files.

First, create an empty file to which (for your own convenience) you can give the .sh extension. For the following example and explanations I'm going to use file names and code from my own case scripts. But you can name them whatever you want.

Secondly, at the first and second lines of that empty file put this:

#!/usr/bin/env bash
set -euo pipefail

The pipefail code makes the script to stop execution, if it encounters any error.

For this explanation I'll start with probably the most used command ever - archiving files.

Leave one line empty after pipefail (that's just for better readability) and write this:
(please note that -mmt20 is the number of threads your CPU has; you can leave it as it is - if your CPU has less than 20 threads, it will still use all available threads. If it has more than 20 threads, I recommend you to change that number to match your CPU's threads)
(-mx0 means zero compression, otherwise known as "Store"; -mx9 means maximum compression or the highest available - slower but highly effective)

```
options0="-mx0 -mmt20"
options9="-mx9 -mmt20"

case "$1" in
     7z0)
        if [[ -z "${2:-}" ]]; then
            read -rp "Enter dir name to archive. Use double quotes if the name has spaces: " target
        else
            target="$2"
        fi
        7z a "$target".7z "$target" $options0
        ;;
    7z9)
        if [[ -z "${2:-}" ]]; then
            read -rp "Enter dir name to archive. Use double quotes if the name has spaces: " target
        else
            target="$2"
        fi
        7z a "$target".7z "$target" $options9
        ;;
    *)
        echo "Bla!"
        ;;
esac
```
Save the file as PACK-UNPACK-CASE.sh or whatever else you prefer that will indicate it contains a case script. After that you can create an alias and the usage will be like this:
```
$ aliasname casename argument
```
Here's a visual example of how I use this case script:
```
$ pack 7z0 NFS_PAYBACK
```
This will create NFS_PAYBACK.7z in the same directory where the game directory is located. In my case NFS_PAYBACK is in /B/GAMES, so it will create /B/GAMES/NFS_PAYBACK.7z.

A case script can have even $3, $4, $5 or as many arguments as you want.

I suppose this could look harder than the regular scripts, especially at first use, but it has one advantage: you define the variables outside the case block and after that just place them on their relevant places using $ in front of them.
This makes the script much easier to maintain and edit bc you only have to edit the variable, not every single line of the script.

The *) is there to tip you off, if you enter the wrong case name. You can replace the "Bla!" in the echo line with whatever you want. Personally, I prefer to list the available case names. The case name is 7z0.

And here's another example of how you can fit as many cases as you want in just one file. I have my reasons to dislike ZeroRPM, that's why I created the NVIDIA-CASE.sh:

```
#!/usr/bin/env bash
set -euo pipefail

case "$1" in
    #FOR RTX 3070 Ti THE MINIMUM SPEED IS 45%, MAX - 70%. 75%+ NOT RECOMMENDED!
    45|50|55|60|65|70)
        sudo nvidia-settings -a "[gpu:0]/GPUFanControlState=1" 
        sudo nvidia-settings -a "[fan:0]/GPUTargetFanSpeed=$1"
        ;;
    low)
        sudo nvidia-settings -a "[gpu:0]/GPUFanControlState=1" 
        sudo nvidia-settings -a "[fan:0]/GPUTargetFanSpeed=50"
        ;;
    med)
        sudo nvidia-settings -a "[gpu:0]/GPUFanControlState=1" 
        sudo nvidia-settings -a "[fan:0]/GPUTargetFanSpeed=60"
        ;;
    high)
        sudo nvidia-settings -a "[gpu:0]/GPUFanControlState=1" 
        sudo nvidia-settings -a "[fan:0]/GPUTargetFanSpeed=70"
        ;;
    auto)
        sudo nvidia-settings -a "[gpu:0]/GPUFanControlState=0"
        ;;
    *)
        echo "Cases: auto, 45, 50, 55, 60, 65, 70, low, med, high. Low, med, high = 50, 60, 70."
        ;;
esac
```
With NVIDIA-CASE.sh I have FULL control over the videocard's fan speed at my leisure. These are 9 different scripts all united into a single .sh file.
I use this one like this (usually):
```
$ fans med
```

I'll keep an eye on this repository, so if you have any questions, just post an issue.
