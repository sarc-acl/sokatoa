<br>

# Sokatoa CLI

<br>

- [Sokatoa CLI](#sokatoa-cli)
  - [Launching the CLI](#launching-the-cli)
    - [Windows](#windows)
    - [Linux](#linux)
    - [macOS](#macos)
  - [Help](#help)
  - [Output](#output)
    - [Progress events](#progress-events)
  - [Profiles and replays](#profiles-and-replays)
  - [CLI Commands](#cli-commands)
    - [`capture`](#capture)
    - [`replay`](#replay)
    - [`new-profile`](#new-profile)
    - [`query`](#query)
    - [`query-frames`](#query-frames)
  - [Global options](#global-options)
  - [CLI/GUI Considerations](#cligui-considerations)

<br>
<br>

Sokatoa has a command line interface in addition to the GUI.  The CLI is used to [create a profile](#capture), [query a capture data set](#query), [perform a replay](#replay), and other key features without opening the Sokatoa window. The CLI is ideal for automation scripts, integrating with other applications, and AI agent interactions.

<br>

## Launching the CLI

The launch command depends on your platform.  In the rest of this page, `...` stands for it.

### Windows

Use `sokatoa-cli.exe`, installed next to `Sokatoa.exe`:

```
sokatoa-cli.exe <command> [options...]
```

<br>

### Linux

Use the Sokatoa AppImage with the `cli` argument:

```
./Sokatoa-<version>.AppImage cli <command> [options...]
```

<br>

### macOS

Use the `sokatoa` command inside the app bundle:

```
./Sokatoa.app/Contents/Resources/bin/sokatoa <command> [options...]
```

<br>
<br>

## Help

Starting the CLI app without a command or with the `--help` argument displays a help message, which lists available commands.

To get help for a specific command use,
```
sokatoa-cli.exe <command> --help 
```

<br>
<br>

## Output

Each command returns human-readable text by default. Optionally, the output can be formatted as JSON by using the `--json` argument.

- **Default**: a readable result on stdout, or the error on stderr.
- **`--json`**: one JSON object per line on stdout. Every line before the last is an
  `{"event": ...}`; the last line is always the outcome: `{"result": ...}` on success, or
  `{"error": {"message": "..."}}` on failure. Scripts and AI agents should use `--json`.

The exit code is `0` on success, and non-zero on an error or a canceled capture or replay.

The default output form of the [`capture`](#capture) and [`replay`](#replay) commands shows their progress on stdout, and the errors and warnings along the way on stderr.  Errors and warnings have the format:

```
[capture] error: ...
[capture] warning: ...
```

The commands have a well-defined end marker.  On success, on stdout:

```
Capture complete: ...
Replay complete: ...
```

On failure, on stderr:

```
Capture failed: ...
Replay failed: ...
```

Logging is hidden unless you pass `--verbose`.

<br>

### Progress events

With `--json`, `capture` and `replay` can print event lines before the outcome, for example:

```
{"event": "message", "severity": "I"|"W"|"E"|"D", "message": "..."}
{"event": "progress", "message": "...", "work": {"done": 1, "total": 2}, "depth": 0, "timing": {"totalMs": 5000}}
{"event": "controlAvailable", "commandId": "Stop", "label": "Stop"}
{"event": "controlUnavailable", "commandId": "Stop"}
```

The `controlAvailable` and `controlUnavailable` messages tell you which commands are available while a capture is running (see [capture](#capture)). `work` and `timing` are optional, and `depth` is how deeply a step is nested within the capture (`0` is the top level).

A `progress` event with `timing` starts a step that is reported once, not as a running count, such as waiting for a timed start or capturing. To show the time elapsed, count from that event. When the step ends by itself, `totalMs` is after how many ms, e.g. the delay of a timed start or the duration of a timed capture.

<br>
<br>

## Profiles and replays

Commands that work on an existing profile take `--profile <path>`.  The path is the profile folder, or its `profile.sokatoa` file, either absolute or relative to the current directory.

Many of them also take an optional `--replay-base <identifier>` to work on one of the profile's replays instead of its original capture.  The `identifier` is either the replay id or the name reported when [the replay was created](#replay). The id is the safer choice since it does not change when the replay is renamed.

Range inputs (e.g., `--frame-range`, `--queue-submit-range`) support multiple formats:
- `10`: a single work unit, for example frame 10
- `10-20`: a standard range (inclusive endpoints)
- `10-`: from the value to the end
- `-20`: from the start to the value

<br>
<br>

## CLI Commands

### `capture`

```
... capture --config <config-path> [--device <id>] [--profile-repo <dir>] [--name <name>]
```

Creates a new profile. The only required argument is `--config`, which sets the capture configuration using a configuration file (a `.sokc` file).  [A configuration file is created from the GUI and exported to a `.sokc` file.](./profiles.md#importing--exporting-saved-configurations)

Some configuration options can be overridden by CLI arguments:
- `--device <id>`: the device to capture on.
- `--profile-repo <dir>`: the folder to create the profile in.
- `--name <name>`: the new profile's name.

Reports `profilePath`: the new profile's `profile.sokatoa` file.

**Manual captures:** some captures need user input, for example to start or stop a manually triggered capture, or to unlock the device.  Type the command (such as `Start`, `Stop` or `Unlock`) when prompted, and press Enter.  The available commands are also reported as
[progress events](#progress-events) when the `--json` flag is used.

<br>

### `replay`

```
... replay [--config <config-path>] [--profile <path>] [--device <id>] [--replay-base <id>] [--name <name>] [--frame-range <range> | --queue-submit-range <range>]
```

Replays an existing capture data set on a device and adds the result to the profile as a new replay.

Give `--profile`, `--config`, or both.  The other arguments are optional.  Any argument that overlaps a setting in the specified configuration file (if one is specified) overrides values in the config.

- `--config <config-path>`: the replay configuration file (a `.replay.json` file) containing the replay parameters.  Create one with "Export" in the [New Replay dialog](./profiles.md#replays), opened with "New Replay..." in the Profile Explorer.  It contains what you set in the dialog, such as the frames, replacement shaders and changed settings.  Everything else comes from the profile, or from the replay it is based on.  A replay configuration is different from [a new profile configuration](./profiles.md#importing--exporting-saved-configurations), and they cannot be used interchangeably.
- `--profile <path>`: the profile to replay. Without it, the profile the config file names.
- `--device <id>`: the device to replay on.
- `--replay-base <identifier>`: the replay to base this one on (its id or name).
- `--name <name>`: the replay name. Without a config file, the default is `Replay`, plus the range
  if you give one.
- `--frame-range <range>`: the frames to replay.
- `--queue-submit-range <range>`: the queue submissions to replay instead of frames.

Without `--config`, the replay uses the settings of the profile, or of the replay given with `--replay-base`.

`--frame-range` and `--queue-submit-range` are optional.  Without either, the replay keeps what its config file or base replay replays, and replays all frames otherwise, or all the queue submissions of a capture without frames.

The GFXR replay settings come from your current preferences, picked by whether you replay on the device the profile was captured on or on a different one.

The replay goes into the replay group its config file names, or else its base replay's group.

Reports `replayId` and `replayName`: the id and name of the new replay. Pass either to
`--replay-base` of later commands.

<br>

### `new-profile`

```
... new-profile --file <file-path> [--file <file-path>...] [--name <name>] [--profile-repo <dir>] [--reference]
```

Creates a profile from existing raw trace files (`.gfxr`, and `.perfetto`, `.perfetto-trace` or `.pftrace`), without capturing on a device. Repeat `--file` for each file.

- `--name <name>`: the new profile's name. The command fails if a profile of that name exists. Default: the first file's name, with a `_<number>` suffix if a profile of that name exists.
- `--profile-repo <dir>`: the folder to create the profile in (default: `sokatoa/profiles` in your home folder).
- `--reference`: use the files where they are, instead of copying them into the profile.

Reports `profilePath`: the new profile's `profile.sokatoa` file.

<br>

### `query`

```
... query --data-type <gfxr|perfetto> --profile <path> (--query-string <sql> | --query-file <file>)... [--replay-base <id>]
```

Runs SQL queries on part of the profile's capture data set:
- `--data-type gfxr` to query the GFXR data
- `--data-type perfetto` to query Perfetto data

Define each query with either the `--query-string` or the `--query-file` argument (not both).  To execute multiple queries in one batch, include multiple query strings or files.  For a large trace this is faster than one command per query, as the trace is opened only once.

Reports one `{"headers": [...], "rows": [...]}` per query, in order.

<br>

### `query-frames`

```
... query-frames --profile <path> [--frame-range <range>] [--replay-base <id>]
```

Extracts images of frames from a target profile. If a range is not specified using `--frame-range`, the command returns every frame in the data set.

If the images do not already exist in the capture data set, the command makes them by replaying the GFXR trace on a connected device, preferring the device the profile was captured on.  This doesn't add a replay to the profile.

Reports `{"screenshots": [{"frame": <n>, "path": "..."}, ...], "missingFrames": [<n>, ...]}`.

<br>
<br>

## Global options

- `--json`: see [Output](#output).
- `--verbose`: show logging on stderr.
- `--help`: see [Help](#help).
- `--version`: the Sokatoa version.

<br>
<br>

## CLI/GUI Considerations

- Each command starts the core Sokatoa.  This is a cold start and may take a few seconds to start processing the command.
- The CLI application does not connect to an open Sokatoa window.
- Device use is not coordinated across processes.  Concurrent operations on the same device by separate CLI commands, by the CLI and the GUI, or directly via `adb` will likely result in errors.
- Ensure a profile is not opened in the GUI before running `replay` on that profile.  This can result in inconsistencies between the GUI and the profile data set.
- The CLI uses the first `adb` found on the `PATH` it is started with.  This is different from the GUI.  On Linux and macOS, the GUI uses the `PATH` of your login shell.  If no adb server is running yet, put `adb` on that `PATH`, or set the `ADB.ClientPath` preference to its full path.
- Only some options can be set with dedicated command line arguments.  The rest are set in a configuration file exported from the New Profile or New Replay dialog.
