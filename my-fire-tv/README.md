# My Fire TV Stick

Switches off Amazon's tracking, ads, promos and the apps you don't use. Nothing is uninstalled and one
command undoes it. Amazon's home screen itself stays (Fire OS won't let anything replace it), so the
sponsored banner at the top stays unless you also do the optional DNS step at the end.

**Stays on:** Netflix, Prime Video, Disney+, YouTube, Max, Alexa voice search (mic button), firmware
updates, the Appstore, the remote and Bluetooth.
**Goes off:** Amazon telemetry and ad services, sponsored rows and previews, the Live tab and free
channels, Amazon Music / Photos / Kids / Silk / IMDb, Alexa extras (alarms, music, notifications),
screen mirroring, casting from phone apps, and the other streaming apps (Paramount+, Hulu, Peacock,
Tubi, Pluto TV, Univision, FOX, ESPN, fubo…). The full list with a note on each line is
[`debloat.txt`](debloat.txt). To keep something, put `#` at the start of its line.

## 1. On the Fire TV (2 minutes)

1. **Settings → My Fire TV → Developer options → ADB debugging: ON.**
   No Developer options? Go to **Settings → My Fire TV → About**, highlight the first line (the
   stick's name) and press select 7 times, then go back.
2. Note the stick's IP address: **Settings → My Fire TV → About → Network** (e.g. `192.168.1.50`).
3. While you're in Settings, switch these off by hand (no computer needed):
   - **Settings → Preferences → Privacy Settings:** Device Usage Data, Collect App Usage Data,
     Interest-based Ads.
   - **Settings → Preferences → Featured Content:** Allow Video Autoplay, Allow Audio Autoplay.

## 2. On a computer on the same Wi-Fi

The easy way: open Claude Code in this repository and say
*"Apply my-fire-tv/debloat.txt to my Fire TV Stick at 192.168.1.50, in small batches, and check it
still works between them."* Accept the "Allow USB debugging?" prompt on the TV when it appears.

By hand, it needs Python 3:

**Windows (PowerShell)**

```powershell
git clone https://github.com/jbdltq-mx/TVchange; cd TVchange
python -m venv tools\venv; tools\venv\Scripts\python.exe -m pip install -r tools\requirements.txt
$env:TV_HOST = "192.168.1.50"                               # your stick's IP
tools\venv\Scripts\python.exe -I tools\tv.py --probe          # accept the prompt on the TV; shows the model
powershell -ExecutionPolicy Bypass -File tools\disable.ps1 my-fire-tv\debloat.txt
```

**Mac / Linux**

```bash
git clone https://github.com/jbdltq-mx/TVchange && cd TVchange
python3 -m venv tools/venv && tools/venv/bin/pip install -r tools/requirements.txt
export TV_HOST=192.168.1.50                                   # your stick's IP
tools/venv/bin/python -I tools/tv.py --probe                  # accept the prompt on the TV; shows the model
tools/disable.sh my-fire-tv/debloat.txt
```

Some lines print an error such as "Unknown package" or "Cannot disable". That's expected: the list
covers several Fire TV models, and Amazon protects a few packages. Everything else still applies.

When you're done, switch **ADB debugging** back off. Nothing needs it to keep working.

## Undo

```bash
tools/restore.sh my-fire-tv/debloat.txt com.amazon.tv.launcher                       # Mac / Linux
```
```powershell
powershell -ExecutionPolicy Bypass -File tools\restore.ps1 my-fire-tv\debloat.txt -Stock com.amazon.tv.launcher
```

To bring back just one app, find it in **Settings → Applications → Manage Installed Applications**
and choose Enable, or run `tools/venv/bin/python -I tools/tv.py 'pm enable com.example.package'`
(Windows: `tools\venv\Scripts\python.exe -I tools\tv.py 'pm enable com.example.package'`).

A Fire OS update can switch some packages back on. If ads or apps reappear, run the disable command again.

## Optional: blank the sponsored banner

The banner and sponsored row are downloaded from `amazon-adsystem.com`. Blocking that domain with an
ad-blocking DNS such as AdGuard DNS (`94.140.14.14` and `94.140.15.15`) stops them loading. Two ways:

- **Whole house:** set those two DNS servers in your router.
- **Just the stick:** Settings → Network → highlight your Wi-Fi → Forget, reconnect, choose
  **Advanced**, and enter a fixed IP, your router as gateway, prefix length `24` and the two DNS servers.

Untested on this stick. If an app stops playing or the stick says it has no internet, undo it (put
the router back, or reconnect the stick's Wi-Fi without Advanced). It doesn't remove ads inside
YouTube or other apps.
