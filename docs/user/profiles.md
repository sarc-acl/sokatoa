<br>

# Getting to Know Profiles

<br>

- [Creating and viewing your first profile](#creating-and-viewing-your-first-profile)
- [New Profile Configuration Basics](#new-profile-configuration-basics)
- [Profile Configuration Details](#profile-configuration-details)
  - [Common](#common)
    - [New Profile Name Variables](#new-profile-name-variables)
  - [Perfetto](#perfetto)
  - [GFXR](#gfxr)
  - [Menus and Saved Configurations](#menus-and-saved-configurations)
    - [Open a Saved Configuration](#open-a-saved-configuration)
    - [Importing & Exporting Saved Configurations](#importing--exporting-saved-configurations)
- [Export a Profile](#export-a-profile)
- [Import a Profile](#import-a-profile)
  - [Quick Profile Importing](#quick-profile-importing)
- [Replays](#replays)
- [Comparing Captures](#comparing-captures)

<br>
<br>

A **profile** is the central container for all performance or debugging data collected from an application.  Perfetto and GFXR are the primary data generators, but plug-ins can also augment the profile data set.

The process of collecting data for a profile is called 'capturing'.  A profile consists of one or more **captures** (also referred to as capture datasets).  There are two primary types of captures: a **baseline** and **replays**.  The first capture is the baseline and any subsequent captures are called replays.

During your analysis workflow you might make changes to shaders or want to collect different data. These are called **replays**.

You may also create a **slice** by selecting a subset of a capture data set and saving it as a separate data set.  For example, if your baseline spans 20 frames, you might create a slice to focus on only a few frames of interest.

<br>

## Creating and viewing your first profile

1. Click the 'New Profile...' icon button.
 
   ![New Profile Button](./images/profiles/new-profile.png)

2. Define the [profile configuration](#new-profile-configuration-basics).
3. Press the 'Capture' button in the 'New Profile' dialog.


After the capture process completes, you will see the new profile in the **Profile Explorer**

The Profile Explorer panel shows a list of profiles.  Double clicking on a capture node opens that profile in a new tab.  The capture properties are shown in the Profile Properties pane below the profile tree.  From here you can see metadata such as settings used at capture time, and various Android properties.

![Profile Explorer](./images/profiles/profile-explorer.png)


Learn more about [viewing profile data.](./viewing-profiles.md)



<br>
<br>

## New Profile Configuration Basics
Capturing profile data starts by configuring what data you want to collect.  From here you define which data producers will contribute to the profile and what data elements you want.  

![New Profile](./images/profiles/new-profile-configuration.png)


By default, you start with a blank configuration.  There are at least three sections: [Common](#common), [Perfetto](#perfetto), and [GFXR](#gfxr).  Common defines general capture parameters.  Perfetto and GFXR are built-in capture participants.  Extensions can add additional capture participants.

You can save your configuration settings by clicking the save icon button at the top right.

Clicking the Capture button starts the capture process.  When completed the new profile is added to [the Profile Explorer](#creating-and-viewing-your-first-profile) and opened in a new tab.

<br>
<br>

## Profile Configuration Details
#### Common
The Common section is divided into three sets of fields: [Profile](#profile), [Target](#target) and [Triggers](#triggers).  The Profile fields describe the intent of the profile.  The Target describes the on-device application to profile, and the Triggers set defines how to start and stop the capture process.

<br>

##### **Profile**
![Common/Profile](./images/profiles/common-profile.png)

-   **Name**: A label you give the profile, which is shown in the Profile Explorer and the name of the profile directory. The default is a variable expression `${package_name}_${capture_triggers}_${device}`, which will use the name of the selected package, the capture triggers, and device ID. [See below for a list of the available variables](#new-profile-name-variables) in the different contexts for new captures.
-   **Description**: Optional text to give a fuller description of the intent and purpose of the profile.
-   **Location**: The directory on disk in which to create the profile.
-   **Device**: Select the device you wish to capture from. This uses `ADB` to get the list of connected devices.
-   **Lock the configuration to this specific device**: Check this when the configuration is specific to the currently connected device. Doing so will prevent the device selector from automatically selecting another connected device when this device is not connected.
-   **Collect device's Vulkan profile and capabilities**: When enabled, Vulkan information will be retrieved from the device.  The collected data is viewable from the capture's Vulkan Info tab.

<br>

##### **Target**
![Common/Target](./images/profiles/common-target.png)
-   **Mode**:
    -   **Launch APK**: Launch the selected **Package**/**Activity** on the device with the given **Arguments**. The package list can be filtered to user-installed and debuggable packages, and refreshed from the device, using the buttons next to the package selector.
    -   **System**: Attach to the device and capture without launching a package/activity. NOTE: GFXR capture requires starting a package/activity as the Vulkan API is tracked from application start. Therefore, you can only capture Perfetto data in this mode.
    -   **Process (started from adb shell)**: Start a process via the device shell using **Command to run**, with optional **Environment variables**, **Working directory**, and **Process name**.
-   **Detect application crashes**: When enabled, application crashes are detected using `am monitor` and the crash details will be saved as a file in the created profile. Not available in Process mode.
-   **Enable Vulkan validation**: Enables the Vulkan [Validation layer](https://docs.vulkan.org/guide/latest/validation_overview.html).

<br>

##### **Triggers**
![Common/Triggers](./images/profiles/common-triggers.png)
-   **Start**:
    -   **Manual**: The capture starts when you press the start button on the capture progress dialog.
    -   **Delay from launch (ms)**: The capture starts in the given number of milliseconds after the application was started.
    -   **Frame (inclusive)**: The capture starts when the given frame is rendered. Frame numbers start at 1.
-   **Stop**:
    -   **Manual**: The capture stops when you press the stop button on the capture progress dialog.
    -   **Duration (ms)**: The capture stops in the given number of milliseconds after the capture was started.
    -   **Frame (inclusive)**: The capture stops after the given frame is rendered.  This option is only available if `Start` is `Frame`.
    -   **Frames since capture start** / **Queue submissions since capture start** (depending on the work unit): The capture stops after the given number of frames or queue submissions since the capture started. This option is not available in System mode.
-   **Work unit**: Describes the high-level unit of work: frames or queue submissions.
-   **Terminate the application at end of capture**: When enabled, the app will be terminated at the end of the capture. Not shown in Process mode, where the process is always terminated.


<br>

##### New Profile Name Variables

| Variable Name    | New Profile | New Replay | Arguments | Description                                                                                                                                                                              |
| ---------------- | ----------- | ---------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| activity         | ✔️          |            | N/A       | The name of the activity that is launched, or "Default" for the package's default activity.                                                                                              |
| capture_triggers | ✔️          | ✔️         | N/A       | A précis of the capture triggers, e.g., "manual", "frames_1000-1050", or "from_1000ms_to_1500ms"                                                                                          |
| device           | ✔️          | ✔️         |           | Information about the device on which the capture is performed, selected by the argument. Without any argument, expands to the device identifier, e.g., `${device}` for "21231JEGR02186". |
|                  |             |            | id        | The device identifier, e.g., `${device:id}` for "21231JEGR02186".                                                                                                                         |
|                  |             |            | name      | The device name, e.g., `${device:name}` for "My Pixel 6a".                                                                                                                                |
| mode             | ✔️          |            | N/A       | The capture mode, either "launch" or "attach".                                                                                                                                           |
| package_name     | ✔️          |            | N/A       | The name of the selected package. Replays always use the replay application.                                                                                                             |
| timestamp        | ✔️          | ✔️         |           | The date and time when the capturing process was initiated, in YYYYMMDDHHmmss format, e.g., 20250312112100.                                                                               |
|                  |             |            | -         | The timestamp in hyphenated format, e.g., 2025-03-12_11-21-00                                                                                                                             |
|                  |             |            | iso       | The timestamp in ISO 8601 format, e.g., 2025-03-12T11:21:00.523Z                                                                                                                          |
|                  |             |            | locale    | The timestamp in JavaScript locale-specific format, e.g., 3/12/2025, 11:21:00 AM                                                                                                          |
|                  |             |            | utc       | The timestamp in JavaScript UTC format, e.g., Wed, 12 Mar 2025 15:21:00 GMT                                                                                                               |

<br>

#### Perfetto

![Perfetto section](./images/profiles/perfetto-tab.png)

-   **Enabled**: When enabled, Perfetto data will be captured. This data is presented in the System view of the capture, and contains data collected from both the CPU and GPU.
-   **Customize Perfetto properties**: Enable this option to manually define the Perfetto capture properties.  Use this for fine-grained Perfetto settings or when the UI does not provide an interface for a desired setting.  See <https://perfetto.dev/docs/concepts/config> for more information.

Perfetto capture adds some overhead, but it is minor.

<br>

#### GFXR
![GFXR section](./images/profiles/gfxr-tab.png)

-   **Enabled**: When enabled, GFXR data will be captured. This data is presented in the Performance, Pipeline and Resources views of the capture. It also enables fetching screenshots, images, meshes, and other data for certain events, and allows replaying with shader replacement.

**NOTE:** As GFXR requires the use of a Vulkan validation layer, the target APK must be debuggable, a process started from `adb shell`, or the device must be rooted.

GFXR-based data collection can add significant overhead (~30%).  Keep this in mind when looking at performance measurements. It is recommended to perform an initial baseline capture that includes GFXR and then perform a replay of the captured baseline to evaluate graphics performance as replays do not include the GFXR overhead experienced during baseline capture. Alternatively, you may want to capture Perfetto only for system level performance analysis.

The default values for the rest of the settings should be fine for most users. For more information on the GFXR settings, please see <https://github.com/LunarG/gfxreconstruct/blob/dev/USAGE_android.md>.

If your app installs its own signal handlers, or you use a third-party crash reporter SDK, they may conflict with GFXR's memory tracking modes, Page Guard and User Fault FD.

<br>

### Menus and Saved Configurations
Anchored to the top of the dialog are the profile name and actions.  There are many helpful actions here.

#### Open a Saved Configuration
To use a previously saved configuration, click the middle icon button.  A menu shows all saved configurations in alphabetical order.  Clicking an option loads that saved configuration.  Use this for quick access to your saved configurations when starting a new profile.

![Saved configurations menu](./images/profiles/open-configs-menu.png)

<br>

Alternatively, a full list of saved configurations is accessible from either the kebab or context menus.  Use this view for general management of your configurations in addition to selecting one for a new profile.

![Kebab menu](./images/profiles/kebab-menu.png)

![Saved Configurations](./images/profiles/saved-configurations.png)

<br>
<br>


#### Importing & Exporting Saved Configurations
Capture configurations can be shared or used in the [CLI](./cli.md).  You can export a capture configuration from the kebab menu, the context menu, or the Saved Configurations view.  Configurations can be exported in bulk from the Saved Configurations view.

![Export configuration](./images/profiles/kebab-menu-export.png)

You can import capture configurations from the kebab menu or from the Saved Configurations view.

<br>
<br>
<br>

## Export a Profile
It is common to share or archive profiles.  Sokatoa profiles can be imported and exported making it convenient to manage profiles.  The export process creates a Sokatoa Profile Archive (.spa) file.

To export a profile, right-click on the profile you wish to export.  From this context menu, select the `Export` option.

![Export Profile](./images/profiles/export-1.png)

This opens a dialog for you to select a destination and have the option to rename the profile archive.  Clicking the `Export` button starts the archival process.

![Export Profile Dialog](./images/profiles/export-2.png)

<br>
<br>
<br>

## Import a Profile
To import a profile archive, open the ‘File’ menu and select the ‘Import’ option.  A system file picker opens.  Use this to select the Sokatoa profile archive (spa) file you wish to import. From here, Sokatoa will import the profile archive into the profile repository directory.

![Import Profile](./images/profiles/import-1.png)

After the import process completes, you have the option to add the profile to the Profile Explorer or open the profile for viewing.

![Import Profile Dialog](./images/profiles/import-2.png)

#### Quick Profile Importing
There are two other options for importing profile archives.  From the system’s file system viewer (e.g., Windows File Explorer), you can double-click the archive file.  This triggers the import process.

The other option is to drag the archive file onto the Sokatoa window.

<br>
<br>

## Replays

A replay uses GFXR to replay the profile on the device. This allows the tool to capture new Perfetto data and new screenshots. You can replay the entire baseline, a slice, or one or more selected frames. If you have modified one or more shaders, you can choose which modified shaders to use during the replay. This allows you to see differences in both rendering and in performance.

Note that GFXR replays start from the beginning of the capture regardless of which replay region is selected. For long captures you may notice some activity before the replay region is shown on the device. Data is only collected for the replay region and the replay timeline will be limited to the region selected.

![Replay Slice](./images/profiles/replay.png)

<br>
<br>

## Comparing Captures

You can compare the baseline with a replay or a replay with a replay. Simply select the two nodes in the profile explorer that you want to compare, right-click and select Compare.

![Open Compare](./images/profiles/open-compare.png)

![Comparison](./images/profiles/comparison.png)

The two timelines are automatically synced and remain in sync as you zoom and pan. Depending on the shader changes, you may see visual differences in the frames or changes in frame performance.

![Comparison Toolbar](./images/profiles/comparison-toolbar.png)

The comparison toolbar provides some functions in addition to the normal timeline toolbar. The first button allows you to switch the order of the comparison views. This will also switch the order of the stacked timelines. The second button allows you to flip the orientation of the comparison from horizontal to vertical. The last two buttons are the same as the timeline toolbar.
