
# Building Fish

The fish build process, especially on Windows, is one that could be charitably described as "nontrivial." We figured out the steps, so you don't have to.

<img src="images/building/building_fish.png" height="200" alt="building fish">

## Linux

Coming soon...

## Mac

Coming soon...

## Windows

### Prerequisites:
* [CMake version >= 3.24.1](https://cmake.org/download/) (This link actually takes you to the latest version)
* [JetBrains CLion IDE](https://www.jetbrains.com/clion/) (Or not if you like your CMake builds raw)
* [Build Tools for Visual Studio 2022](https://aka.ms/vs/17/release/vs_BuildTools.exe) (The build tools may or may not just come with regular Visual Studio) (If you don't have this, consider being visited by the magic binary distributor fairy)
* [Intel IPP](https://www.intel.com/content/www/us/en/developer/tools/oneapi/ipp.html) (I don't have a joke for this one)

### Steps:

1. Go through the Intel IPP installer. Or, just run the following:

```
curl --output oneapi.exe https://registrationcenter-download.intel.com/akdlm/irc_nas/19078/w_BaseKit_p_2023.0.0.25940_offline.exe
./oneapi.exe -s -x -f oneapi
./oneapi/bootstrapper.exe -s -c --action install --components=intel.oneapi.win.ipp.devel --eula=accept -p=NEED_VS2022_INTEGRATION=1 --log-dir=.
```

2. Clone the repo with `git clone --recurse-submodules https://github.com/ArdenButterfield/Fish` (Recursive to make sure git clone also hits JUCE).

3. Inside your local project, navigate to `Source/lib/lame` (this is a quirked up version of [LAME](https://lame.sourceforge.io/) included in the project, so even if you have LAME already installed, you still need to do this step).

4. Copy the file called `configMS.h` into `libmp3lame/config.h` (you would presumably copy one of the other config files if you were on a different OS).

5. Launch the command window `x64 Native Tools Command Prompt for VS 2022` which you can find from the start menu if you have Build Tools for Visual Studio 2022 installed.

<p>
    <img src="images/building/start_menu.png" height="200" alt="As easy as tpying in 'x64' into the start menu">
    <br>
    <em>As easy as tpying in "x64" into the start menu</em>
</p>

6. Inside the command window, run `cd [Path to root directotry of local project]/Source/lib/lame`.

7. Then run `nmake dll -f Makefile.MSVC comp=msvc asm=n`.

8. Hopefully the build should succeed, and you can now see the built file `Source/lib/lame/output/libmp3lame-static.lib`.

<p>
    <img src="images/building/legendary_artifact.png" height="100" alt="This sight will never not be satisfying">
    <br>
    <em>This sight will never not be satisfying</em>
</p>

9. Before we open up the project in CLion, there's even more hoops we need to jump through. (Isn't that what you wanted to hear?) CLion must be run with administrator privileges to install the VST file correctly. If you installed CLion yourself, then you can probably right-click, "Run as Administrator". If you installed CLion through JetBrains Toolbox, click on the vertical dots next to CLion in the list, click "Settings" in the dropdown, the click on "Configuration". Toggle "Run as administrator".

<p>
    <img src="images/building/toolbox_settings.png" height="200" alt="The Cool kids use JetBrains Toolbox">
    <br>
    <em>The Cool kids use JetBrains Toolbox</em>
</p>

10. Open up the project in CLion. It's gonna start trying to load the CMake project and there's going to be errors because we need to change the CMake settings first.

11. Go to `File | Settings | Build, Execution, Deployment | CMake`. In the `CMake options:` field, place in `-DLAME_LIB=Source\lib\lame\output\libmp3lame-static.lib`, the path to our built file.

12. Now reload the CMake project.

13. CLion should detect a bunch of CMake Application Run Configurations. Select the one called `Fish_VST3`. You may have to edit the configuration and set the `Executable:` as `Tests`.

14. Also when editing the configuration, check "Run with Administrator privileges". Otherwise we're gonna get a permissions denied build error.

<p>
    <img src="images/building/run_config.png" height="400" alt="A well-configured Run Configuration">
    <br>
    <em>A well-configured Run Configuration</em>
</p>

15. Run `Fish_VST3`.

16. After taking 12 years to build, the built plugin should be placed into `C:\Program Files\Common Files\VST3\Fish.vst3\Contents\x86_64-win\Fish.vst3`. It is now ready to be used wherever you want.

### Notes:

* If the build fails and you get a message like `file INSTALL cannot set permissions on "C:\Program Files\Common File/VSt3/Fish.vst3": Permission denied.`, it might be for two reasons. The first is that you didn't follow my instructions on giving the build adminsitrator privileges. The second is because your audio workstation is currently using the previously built VST file. You have to close it to rebuild.
