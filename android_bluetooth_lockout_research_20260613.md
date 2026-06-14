# Security / android-gpt-chat-client
## Research note: Android Bluetooth disablement and network-stack lockout

Date: 2026-06-13  
Status: initial research note

### User request
Find a way to disable Bluetooth and lock it out of the network/connectivity stack on an Android phone. Generate suggestions and save them under the Security project, Android GPT chat client subfolder.

### Initial conclusion
The strongest non-root approach is not the normal Settings toggle. The strongest official approach is to enroll the phone under Android Enterprise / device-owner management and apply a policy that disables Bluetooth entirely. On ordinary consumer Android without device-owner control, the realistic options are layered mitigations: turn Bluetooth off, remove paired devices, deny app Bluetooth permissions, stop Android Auto/accessory auto-reenable paths, and monitor for reenable events.

### Suggested control levels

#### Level 1 — Basic user controls
- Turn Bluetooth off in Android Settings and Quick Settings.
- Remove/forget all paired Bluetooth devices.
- Disable Nearby Share / Quick Share Bluetooth-related paths where applicable.
- Disable Android Auto auto-launch and accessory behaviors if Bluetooth is being reenabled during car/USB use.
- Review apps with Nearby devices / Bluetooth permissions and deny them unless required.

Pros: safe and reversible.  
Cons: not a true lockout; the user, system components, or privileged integrations may be able to reenable Bluetooth.

#### Level 2 — App permission hardening
- On Android 12+, review apps that request BLUETOOTH_SCAN, BLUETOOTH_ADVERTISE, and BLUETOOTH_CONNECT.
- Deny Nearby devices permission for apps that do not clearly need Bluetooth.
- Remove suspicious or unnecessary apps that request Bluetooth or location-adjacent permissions.

Pros: reduces app-level use of Bluetooth.  
Cons: does not prevent the OS or privileged/system apps from using or reenabling Bluetooth.

#### Level 3 — ADB-assisted audit and reset
- Use ADB from a trusted computer to inspect app packages and permissions.
- Avoid relying on old hidden ADB commands as a durable Bluetooth lockout; Android versions vary and hidden commands can break.
- Use ADB mainly for inventory, diagnostics, and removing/limiting suspicious packages where safe.

Pros: useful for inspection and repeatable audit notes.  
Cons: not a policy-grade lockout unless paired with device-owner provisioning or root-level control.

#### Level 4 — Android Enterprise / device-owner policy
- Enroll the device as fully managed / device owner.
- Apply a policy equivalent to DISALLOW_BLUETOOTH or Android Management API bluetoothDisabled=true.
- Prefer full Bluetooth disablement over merely blocking Bluetooth configuration.

Pros: strongest official non-root route; designed for enterprise/kiosk control.  
Cons: usually requires device provisioning and may require factory reset or MDM/Android Enterprise tooling.

#### Level 5 — Root/custom-ROM approach
- On a rooted device, Bluetooth packages/services/modules may be disabled or removed.
- This can break system stability, Android Auto, emergency/accessory functions, or OTA updates.
- Recent Android versions may crash or reboot if Bluetooth system components are disabled incorrectly.

Pros: potentially deepest control.  
Cons: high risk; device-specific; not recommended without full backups and rollback path.

### Important distinctions
- “Bluetooth configuration disabled” is weaker than “Bluetooth disabled.”
- Bluetooth sharing restrictions do not necessarily block receiving or all Bluetooth operation.
- App Bluetooth permissions reduce application access but do not disable the Bluetooth radio globally.
- A real lockout needs policy-level device ownership, root/custom OS control, or physical/radio-level isolation.

### Practical next steps
1. Identify the phone model, Android version, and whether it is personally owned or can be factory reset/provisioned.
2. Check whether the user is willing to use Android Enterprise / device-owner mode.
3. If not, implement a layered non-root mitigation checklist.
4. If yes, research minimal Android Enterprise/device-owner setup for a single personally owned test phone.
5. Create a verification script/checklist: Bluetooth off, no paired devices, no apps with Bluetooth permissions, no Android Auto reenable, no Quick Share path, and post-reboot status check.

### Open research questions
- What is the simplest safe single-device Android Enterprise/device-owner setup for the user's current phone?
- Can an open-source device-owner/DPC tool enforce DISALLOW_BLUETOOTH reliably on the user's Android version?
- Does the user's Motorola Android build expose additional OEM restrictions?
- Does Android Auto or any system integration reenable Bluetooth despite user settings?
- What verification method best proves Bluetooth remains disabled across reboot, app launch, Android Auto connection, and location scanning events?
