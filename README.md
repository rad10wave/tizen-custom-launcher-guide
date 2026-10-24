# Samsung Tizen Custom Launcher Guide: Replace Smart Hub, Customize Home & Enable Autostart

**Turn your Samsung TV or Smart Monitor into a screen that feels like yours.** Put your favourite apps, a beautiful wallpaper, and the information you care about on a custom home screen—then make it open from the remote's **Home** button and, on compatible devices, automatically at startup.

This step-by-step **Samsung Tizen custom launcher guide** covers installing a launcher, replacing the default Smart Hub Home destination, enabling autostart, and keeping a recovery path. It includes a verified Samsung Smart Monitor M7 / Tizen 9 example and instructions for investigating other devices.

## Why use a custom Samsung home screen?

Imagine turning on your monitor and seeing your own mountain wallpaper and favourite apps, ready to use. Press Home after watching something, and you return to that same personal dashboard.

| Benefit | What it means for you |
| --- | --- |
| **Your apps, front and centre** | Arrange the services you actually use, with less hunting through menus. |
| **A calmer home screen** | Choose a launcher that keeps promotional rows and unwanted recommendations out of its own interface. |
| **A look that fits your space** | Use your own wallpaper, colours and layout to suit a living room, gaming setup or desk. |
| **A familiar remote experience** | Navigate with arrows and OK; a verified Home-button assignment brings you back to your launcher. |
| **Your dashboard at power-on** | On compatible firmware, start the launcher automatically instead of opening it manually each time. |
| **More than an app grid** | Depending on the launcher, add a clock, weather, input shortcuts or a photo screensaver. |
| **Use the hardware you already own** | Run a compatible launcher on the Samsung device itself, without adding a streaming box solely for a different home screen. |

The exact features depend on the launcher you choose. This guide helps you install and integrate one; it does not supply every feature in the table. A cleaner launcher also does **not** remove ads inside YouTube or other streaming apps.

## What the finished setup can feel like

1. **Switch on:** your custom dashboard appears automatically after startup has been verified.
2. **Pick an app:** use the remote to open a favourite streaming service.
3. **Press Home:** return to your personal launcher after Home routing has been verified.
4. **Reach Samsung settings:** use the launcher's tested Samsung Home escape when you need the stock interface.

For our M7 setup, the owner confirmed both the Home-button behavior and automatic launch during their usual off/on routine. See the evidence below for the exact tested scope.

This is a **documentation repository**, not a universal installer. App installation uses Samsung's developer workflow. Home reassignment uses undocumented native behavior and needs device-specific engineering. There is no supported, universal “default launcher” setting for all Samsung Tizen devices.

## What this guide covers

1. Identify your device and install a compatible launcher.
2. Back up the actual native Home/startup configuration.
3. Prepare and test an exact-value restore path.
4. Assign the remote Home button to your launcher.
5. Configure and physically test automatic startup.
6. Maintain and recover the setup.

**Do not run another owner's apply script unchanged.** A matching Tizen version alone does not establish compatibility. A failed change can leave Home opening a missing or unusable app. Firmware updates can invalidate both the change and its recovery tools. Keep the remote and physical access available.

## Evidence and limits

This procedure was informed by a successful Samsung Smart Monitor M7 session:

| Item | Recorded result |
| --- | --- |
| Model | LS43FM700UWXXL |
| Firmware | T-RSLMFWWC-0090-1311.1 |
| Platform | Tizen 9, ARMv7, 32-bit; SDK UID 901; .NET 6.0.9 available |
| Launcher | Arn0-derived web app, installed version 9.0.2 |
| Home button | Native assignment read back; owner confirmed Home opens launcher |
| Recovery | Home-only takeover restored to exact original values; owner confirmed Samsung Home |
| Automatic startup | Native autostart enabled; owner confirmed usual off/on opens launcher |
| Still unverified | Separate unplugged cold boot; restoration after the final startup-enabled variant |

These observations apply to one device and firmware. They do not certify another M7, television, projector, or software version. This guide does not cover Android TV, Google TV, LG webOS, or Samsung Orsay.

## Prerequisites

- A compatible Samsung Tizen device and working remote.
- A computer on the same trusted LAN.
- A compatible launcher `.wgt` and its source/manifest where possible.
- [Apps2Samsung](https://github.com/Apps2Samsung/Apps2Samsung/releases) or Samsung Tizen Studio and TV/certificate extensions.
- Your own Samsung signing credentials; complete login and verification yourself.
- For native Home work: ability to review/build a guarded helper, establish a compatible execution path, and preserve recovery files off the device.

USB-C or HDMI does not supply the network developer connection used here. Do not expose developer ports through your router.

## Step 1 — Record the exact device

Copy [device.example.json](examples/device.example.json) into a private, ignored `.local/` folder. Populate it using actual observations:

- Model and full firmware string.
- Tizen version, CPU architecture, runtime and SDK execution UID.
- Device IP and computer LAN IP.
- Developer tool locations and versions.
- Launcher application ID, package ID, installed version and signing-profile path.

Do not publish serial numbers, DUIDs, MACs, private IPs, raw logs, certificates or tokens. Public examples in this repository contain placeholders only.

## Step 2 — Enable Developer Mode

1. Find your computer's current LAN IP. On Windows, run `ipconfig`.
2. On the Samsung device, open **Home → Apps / Apps Settings**. Menu placement varies.
3. Enter **12345**. With a slim remote, use its **123** on-screen number pad or the SmartThings remote keypad.
4. Enable Developer Mode and enter the **computer's IP** as Host PC IP.
5. Restart as directed. If the developer service is still closed, coordinate a full power cycle; do not interrupt an update.
6. Read the device's own IP under **Network Status → IP Settings**, or equivalent.

Follow [Samsung's device connection instructions](https://developer.samsung.com/smarttv/develop/getting-started/using-sdk/tv-device.html). Do not reset Smart Hub or factory-reset as a routine prerequisite.

## Step 3 — Verify the developer connection

Replace every placeholder before running commands.

Official Samsung SDK examples:

```powershell
& '<OFFICIAL_SDB_PATH>' connect '<DEVICE_IP>:26101'
& '<OFFICIAL_SDB_PATH>' devices
```

Separate [TizenSdb](https://github.com/PatrickSt1991/tizen-sdb) utility examples:

```powershell
& '<TIZEN_SDB_PATH>' capability '<DEVICE_IP>'
& '<TIZEN_SDB_PATH>' apps '<DEVICE_IP>'
```

These are different utilities with different argument formats. Check the installed version's help. Avoid competing developer clients.

Require actual device capabilities, not only a successful TCP probe. The optional local REST endpoint `http://<DEVICE_IP>:8001/api/v2/` may expose model and Developer Mode information, but REST success alone does not prove developer access. REST can also be unavailable while SDB works.

## Step 4 — Install and test the launcher

1. Inspect `config.xml`: confirm app/package IDs, minimum platform version, TV profile and required privileges. A native `.tpk` also needs appropriate architecture compatibility.
2. In Apps2Samsung, select the verified device.
3. Select **Custom WGT / TPK**, browse to your launcher, and click **Download & Install**.
4. Complete required Samsung certificate sign-in. Use the privilege/signing level the package requires; do not blindly change certificate toggles.
5. Require explicit installation success, then confirm the installed identity/version.
6. Open the launcher from Samsung Apps. Test remote navigation and launching other apps.
7. Test an explicit **Samsung Home** escape inside the launcher. Confirm it visibly opens stock Home.

Preserve the app ID and author certificate for updates. An Android APK or Windows executable is not a Tizen app. Sideloading does not provide paid-service or DRM entitlements.

If you only want a dashboard, stop here. You can open the launcher manually without changing native Home behavior.

## Step 5 — Back up actual Home/startup settings

Use a reviewed **read-only native query helper**. Capture the actual main Home operation plus every preference in [the native procedure](docs/NATIVE-HOME.md#baseline-data).

Normal retail `sdb shell` access may not execute arbitrary commands. A blank result is not a backup. The community reference method uses an undocumented SDK package-name execution workaround to run a temporary native/.NET helper. Review the complete transport and source before adapting it.

Save these off the device:

- Immutable original snapshot with exact values and types.
- Raw successful query output and verified device identity.
- Reviewed source revision, query/control binaries, runtime files and hashes.
- Exact recovery commands and required developer tools.

Use a separate folder per device/firmware. Keep another copy elsewhere. This is a **configuration snapshot**, not a full firmware or account backup. Do not substitute the example schema for actual reads.

## Step 6 — Prepare and test restoration

Before changing Home, implement and review device-specific `query`, `verify`, `apply` and `restore` operations. Requirements and pseudocode are in [NATIVE-HOME.md](docs/NATIVE-HOME.md).

Require:

- Exact firmware, architecture and execution-identity guards.
- Snapshot validation and unexpected-drift detection.
- Checked native writes, immediate readback, and rollback on partial failure.
- Restoration of the original native assignment and all saved preferences.
- Recovery independent of whether the custom launcher still exists.

Present concrete proposed writes and recovery commands to the owner. Obtain any authorization still required for native changes. Test writing back the current original values and checking exact readback. A same-value restore is still a write.

## Step 7 — Assign the Home button

Only proceed once compatibility and recovery are established.

1. Query current settings and confirm the expected baseline.
2. Confirm the installed target is eligible under the chosen helper's checks. Do not bypass a failing eligibility guard.
3. Assign the native **main Home operation** and `db/appfw/home_appid` to the target app ID.
4. For Home-only scope, preserve the original autostart value.
5. Read back all saved fields, including platform-derived changes.
6. From another app, physically press Home and confirm the launcher opens.
7. Restore the original snapshot; verify every value and physically confirm Samsung Home.
8. Reapply if authorized, then confirm Home again.

There is intentionally no universal copy/paste apply command here. Paths, baseline values, firmware guards and helper builds must be resolved for the actual device. Use the reference projects as source to review, not as turnkey support for every model.

## Step 8 — Enable automatic startup

Treat startup as a separate operation from Home-button routing.

1. Start from working launcher/Home behavior and a prepared restore path.
2. Recheck target eligibility and current configuration.
3. Establish the applicable startup mechanism for this firmware.
4. On the tested M7, setting `db/menu/smart_hub/ontvsetting/auto_start` to `1` caused the platform to update `startup_appid` to the launcher. This is an observation, not a universal rule.
5. Change the minimum justified setting; read back derived changes. Do not blindly overwrite every startup preference.
6. Roll back to the immediately preceding state if the change fails.
7. Ensure restore accepts the known startup-enabled state while retaining the immutable original baseline.
8. Physically test usual off/on, standby wake and unplugged cold boot separately as needed. Record which tests actually passed.

An autostart value or successful launch command alone does not prove startup behavior.

## Troubleshooting

| Symptom | Next check |
| --- | --- |
| Developer port refused | Host/device IPs, Developer Mode, restart and power-cycle state |
| Connections repeatedly close | Close competing developer clients; preserve logs; reconnect one tool |
| Install button disabled | Read installer logs; check signing/device-ID retrieval before assuming success |
| Certificate mismatch | Preserve current installation; resolve author consistency |
| Home still opens Samsung UI | Read current native assignment and preferences; distinguish failed writes, reversion and ineffective routing |
| Callback timed out during a change | State is unknown; query before further writes |
| Helper rejects firmware | Stop and review compatibility; do not delete the guard |
| Home points to a missing app | Use the prepared exact-value restore procedure; do not keep retrying apply |

## Maintenance

**Restore native Home before uninstalling the launcher or changing its app ID.** Keep signing identity and recovery files. Firmware updates can invalidate the helper, including restore, so review recovery before updating.

Do not factory-reset automatically. Do not disable security protections globally or publish developer ports. Record unknowns rather than claiming success from incomplete output.

## Using an AI agent

Use [AGENT-HANDOFF.md](docs/AGENT-HANDOFF.md) as your starting prompt. It separates preparation, authorization, readback and physical verification.

## Frequently asked questions

### Can I change the default launcher on a Samsung Tizen TV?

On some devices, a reviewed native helper can redirect the Home assignment to a custom launcher. This is undocumented behavior, not a universal Samsung setting. Verify the exact model, firmware and recovery procedure first.

### Can a custom launcher open when I press the Samsung remote's Home button?

That is the goal of Step 7. It was physically confirmed in the M7 reference session. Installing the app alone does not remap the button.

### Can I make a Tizen launcher start automatically?

Automatic startup was confirmed during normal off/on on the reference M7. Step 8 explains how to investigate startup separately; behavior can differ across firmware, standby and cold boot.

### Does this replace Tizen OS or remove Samsung Smart Hub?

No. The launcher runs as an app on Tizen. The method changes the Home destination and, when requested, startup configuration. Samsung's underlying operating system and stock interface remain, and a verified route back is part of the procedure.

### Will this make my TV faster or remove every advertisement?

There is no performance guarantee or system-wide ad blocking. The benefit is control over your launcher's layout and content. Performance depends on the app and hardware; advertisements inside other apps are unaffected.

## References and attribution

- [Samsung developer device connection](https://developer.samsung.com/smarttv/develop/getting-started/using-sdk/tv-device.html)
- [Apps2Samsung](https://github.com/Apps2Samsung/Apps2Samsung)
- [TizenSdb](https://github.com/PatrickSt1991/tizen-sdb)
- [Arn0 launcher and setup](https://github.com/NelsonEU/arn0-tv/blob/main/docs/setup-tv.md)
- [Clean Home takeover guide](https://github.com/chaosmanage/samsung-tv-launcher/blob/main/docs/HOME-TAKEOVER.md)
- [Clean Home native controller](https://github.com/chaosmanage/samsung-tv-launcher/blob/main/scripts/native-home/Control.cs)
- [Clean Home SDK runner](https://github.com/chaosmanage/samsung-tv-launcher/blob/main/scripts/native-home/run.py)
- [CleanFrame setup guide](https://github.com/nthnclrk/CleanFrame/blob/main/docs/agent-tv-setup-guide.md)

Recheck current source before use. This repository is independent of Samsung and does not include upstream code, signing credentials, personal device backups or a universal takeover executable. If you redistribute adapted upstream code, retain its applicable license and notices.
