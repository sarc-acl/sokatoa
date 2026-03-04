<div style="padding-top:8px; text-align:center">
    <h1>Sokatoa Release Notes</h1>
</div>

## Known Issues

-   Very large GFXR captures can take longer to load and result in degraded UI performance. It's best to try to keep GFXR capture to around 100 frames or less.
-   Certain apps may fail to run properly when GFXR capturing is enabled.  There are different memory tracking modes that you can try in the GFXR tab in the capture dialog if you see issues with the default. See <https://github.com/LunarG/gfxreconstruct/blob/dev/USAGE_android.md#understanding-gfxreconstruct-layer-memory-capture> for more details.
-   Replaying a GFXR file on a different device from the one it was created on, or even a different driver build on the same device, is not guaranteed to work.  For best results, replay on the same device/driver combination when possible.
-   The performance view may not show data for some profiles. Sharing the profile and the Sokatoa log file will help us debug the issue.

## Reporting issues

For issues that may be specific to a certain workload, it's very helpful to have access to the profile and the Sokatoa log file. You can zip up the entire profile
directory and send it to us. You can find the sokatoa.log file here:

-   Windows: `%APPDATA%\Sokatoa\logs\`
-   Linux: `~/.config/Sokatoa/logs/`
-   macOS: `~/Library/Application Support/Sokatoa/logs/`

---

## Version 1.0.0

-   Initial public release
-   Added support for all VkFormat types
-   Prevent early manual stop of GFXR capture which could result in a corrupt GFXR file
-   Many improvements to the command tree and the performance and pipeline views
-   Many improvements to the data viewers (geometry, images and buffers)
-   Updated to the latest GFXR with many improvements and bug fixes
-   Updated all shader tools to the latest release versions
-   Updated to the latest Perfetto which has some improved UI
-   With the Perfetto update, track pinning rules have changed to be workspace templates
-   Slices have been changed to scope filters within a capture view rather than separate views
-   Added a Vulkan Info tab to the capture view which shows Vulkan features supported by the capture device
-   Removed support for x64 Macs
-   The installation path on Windows changed, so if you had the beta installed, you can uninstall it
    -   `C:\Program Files\Google\sokatoa\Uninstall sokatoa.exe`
-   The package ID of the Sokatoa APK changed, so if you had the beta installed, you can uninstall the old APK
    -   `adb uninstall com.google.sokatoa`

## Version 0.0.8

-   Improved GFXR parsing behavior and performance
-   Improved UI performance for larger GFXR captures
-   Added support for dynamic rendering
-   Added support for additional descriptor types in descriptor view
-   Added support for descriptor set element rollover for writes/copies
-   Added support for dynamic offset tracking in descriptor sets
-   Added support for push descriptors
-   Improved support for raytracing pipeline stages
-   Several improvements to the texture and geometry viewers
-   Added buffer viewer
-   Several improvements to performance view data for all GPUs
-   Fixed an issue detecting when the app has ended
-   Updated GFXR to pick up various fixes
-   Misc performance and stability improvements

## Version 0.0.7

-   Draw resources (geometry, textures) for draw calls in secondary command buffers is now supported
-   The geometry view now supports indirect draw calls (vkCmdDrawIndirect, vkCmdDrawIndexedIndirect)
-   You can now maximize the geometry and textures views
-   Added a new profile summary view
-   You can now customize which tracks are pinned in the system view by default by using the Track Pinning Rules editor
-   You can now see enabled layers, extensions and features for devices and instances in the pipeline view
-   Added a new CPU Frame track to the system view which shows you the CPU time for each frame
-   Several fixes and improvements to the performance view
-   Updated GFXR to pick up various fixes, including capture and replay performance improvements
-   Added annotation support to the command tree (you will be able to annotate many more profile elements in a future release)
-   Misc performance and stability improvements

## Version 0.0.6

-   Added support for graphics pipeline libraries
-   Fixed several issues with comparisons
-   Slices are now opened when created
-   Now showing debug object names in the command tree
-   Minimum supported Ubuntu version is now 22.04
-   Fixed several issues in the geometry and textures views
-   Improved Perfetto performance on arm64 Macs
-   Added support for x64 Macs
-   Updated GFXR to pick up various fixes
-   Added drag selection and mouse wheel zoom to the timeline navigator
-   Misc performance and stability improvements
-   More flexibility in new profile naming with added substitution variables

## Version 0.0.5

-   Added support for traceRay\* commands
-   Improved command tree performance when there are many debug labels
-   Fixed bug where pipeline state for selection in command tree could be incorrect
-   Show frame navigator for GFXR-only profiles
-   Pinned tracks group in the System view is not scrollable
-   Updated GFXR to pick up fixes for issues with GPL, image samplers and more
-   Misc performance and stability improvements
