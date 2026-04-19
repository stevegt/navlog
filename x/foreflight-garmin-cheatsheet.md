# ForeFlight <-> Garmin Connext (SR20 G6 / G7) Flight Plan Transfer

Always review the route on the panel before activating. Menu names vary by avionics/software version.

## Terms (what's what)
- Garmin Connext: The Garmin connectivity feature ForeFlight talks to.
- Flight Stream (often 210/510): The Bluetooth bridge that enables Connext data transfer.

## 0) Before You Start (both G6 & G7)
1. Power avionics on and wait for full boot.
2. On iPad: turn Bluetooth on.
3. In ForeFlight: confirm your route is ready in Maps -> FPL.

## 1) Connect iPad to the Aircraft (both G6 & G7)
1. iPad: Settings -> Bluetooth.
2. Select the aircraft's Flight Stream / Connext device and pair (enter PIN if prompted).
3. ForeFlight: More -> Devices.
4. Confirm Garmin Connext shows Connected.

## 2) Send the Flight Plan (ForeFlight)
1. ForeFlight: Maps -> FPL and verify the route (airport IDs, procedures, alternates).
2. Tap Send To / Panel (label varies) and choose Send To Panel.
3. Wait for the sent confirmation in ForeFlight.

## 3) Load / Activate on SR20 G6 (Perspective+)
1. On the MFD, watch for a prompt like Pending Flight Plan / Flight Plan Received.
2. Open the prompt, review the route, then Load/Activate (wording varies).
3. If you miss the prompt: open FPL on the panel and look for an option to Load/Activate the received/standby plan.

## 4) Load / Activate on SR20 G7 (Perspective Touch+)
1. On the MFD, open Connext / Notifications / Messages (wording varies).
2. Select the Flight Plan Received item.
3. Load to standby (or Activate if appropriate) after reviewing the route.

## Quick Troubleshooting
- ForeFlight shows Not Connected: in iPad Bluetooth, Forget This Device, then re-pair; re-check More -> Devices.
- Transfer fails: simplify the route (fewer waypoints), and re-send; verify avionics fully booted.
- Safety: verify legs, procedures, and course against your briefing; don't activate blindly.

## References
- ForeFlight (Flight Stream Bluetooth pairing): https://support.foreflight.com/hc/en-us/articles/206972587-How-is-a-Flight-Stream-connected-to-an-iPad-or-iPhone-using-Bluetooth
