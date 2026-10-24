# Native Home backup and control specification

This document specifies what a reviewed device-specific helper must do. It is not an executable takeover tool. Availability of each native API and behavior must be established on the actual firmware.

## Baseline data

| Snapshot member | Native source | Type |
| --- | --- | --- |
| `Home` | Default app for `http://tizen.org/appcontrol/operation/main`, using an app-control bundle and `aul_get_default_app` | String |
| `Strings[db/appfw/home_appid]` | `vconf_get_str` | String |
| `Strings[db/appfw/startup_appid]` | `vconf_get_str` | String |
| `Strings[db/appfw/booting_appid_backup]` | `vconf_get_str` | String |
| `Strings[db/first_screen/type]` | `vconf_get_str` | String |
| `Autostart` | `vconf_get_int(db/menu/smart_hub/ontvsetting/auto_start)` | Integer |

Do not infer `Home` from `home_appid`. Do not assume strings or integer values match another device. Obtain a complete successful native query first.

The reference boot-eligibility query is `util_is_appbooting_supported_webapp_byappid(TARGET_APP_ID)`. A failing result must be investigated, not bypassed. App updates can change the result.

## Execution and build requirements

Review both the native helper and the mechanism used to invoke it. The community method uses an undocumented SDK package-name command workaround; it is not an ordinary unrestricted shell. Keep inspection and control separate.

Discover the device's .NET runtime and CPU before building a framework-dependent helper. The tested M7 used .NET 6/ARMv7 and SDK UID 901. These are not universal constants. Never execute a Windows apphost on the TV.

Guard actual device identity where available, exact compatible firmware, architecture and execution user. Replacing an existing guard requires compatibility evidence, not merely a desire to run the helper.

A callback-based runner must bind to the correct local LAN interface, validate the peer, limit input/output/time, propagate native failure, and clean up listeners, child processes and exact temporary files. Do not accept arbitrary remote scripts or run a general persistent service for this workflow.

## Required operations

### Query

- Read identity and current values without setters.
- Report native status, complete structured output and eligibility separately.
- Treat missing keys, null pointers, exceptions and truncated output as failure.
- Keep raw output and timestamps. Transport success alone is insufficient.

### Verify

- Validate snapshot schema and all expected keys/types.
- Read current native values independently.
- Compare every field with the supplied baseline; report drift explicitly.

### Apply Home only

```text
guard device and firmware
load and validate original snapshot
query current state
require exact expected pre-apply state
require target package installed and applicable eligibility checks passing
try:
    assign main Home operation to target
    assign home_appid to target
    preserve original autostart for Home-only scope
    query and validate intended values and derived changes
catch:
    restore exact original values
    verify restoration; report clearly if it also fails
```

Reference setters: `app_operation_set_current_app`, `vconf_set_str`, `vconf_set_int`. Check return values and immediate reads. Do not edit native databases directly.

### Restore

```text
guard device and compatible firmware
validate original snapshot
query current state
require understood original/applied state; reject unrelated drift
restore original main operation
restore all original strings
restore original integer autostart
query again and compare every field exactly
```

Restore must not require the target launcher to exist or remain boot-eligible. The baseline must be independently preserved and re-stageable after device temporary files disappear. Changing firmware can make recovery incompatible; plan ahead.

### Enable startup

```text
require reviewed startup mechanism for this firmware
require working target Home assignment and applicable boot eligibility
query and save immediately preceding state
make only justified startup changes
query and validate readback and derived fields
on failure, restore preceding state
```

Update the restore drift rules to accept the known startup-enabled state. Do not silently turn a Home-only request into an autostart change.

## Validation matrix

| Test | Evidence required |
| --- | --- |
| Launcher opens | Owner observes expected UI |
| Stock Home escape | Owner observes Samsung Home |
| Baseline capture | All native fields and identity validated |
| Same-value restore | Exact readback |
| Home apply | Expected native state read back |
| Physical Home | Owner presses Home from another app |
| Restore from applied state | Exact baseline plus physical stock Home |
| Reapply | Readback plus physical custom Home |
| Startup preference | Native readback, including derived changes |
| Normal off/on | Owner observes automatic launcher |
| Full cold boot | Separate owner-coordinated unplugged test |

Record skipped tests and unresolved results. Do not label a JSON file as tested recovery or a standby test as cold-boot validation.
