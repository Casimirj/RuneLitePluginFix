# OSRS Plugin Local

Combined local development setup for two RuneLite plugins:

- **Rainbow Rave** (`rainbow-rave/`)
- **Charge Calculator** (`charge-calc/`, merged into `rainbow-rave/`)

## Setup

1. Install a JDK (11+ recommended; the build is tested on JDK 17).
2. Install Gradle, or just use the project's bundled wrapper (`gradlew` / `gradlew.bat`).
3. No IntelliJ required — the run scripts launch the client directly.

## Running

### Linux / macOS

```bash
cd osrs-plugin-local
./run.sh
```

### Windows (PowerShell)

```powershell
cd C:\path\to\osrs-plugin-local
.\run.ps1
```

If PowerShell blocks the script, allow it for the current session:

```powershell
Set-ExecutionPolicy -Scope Process RemoteSigned
```

Both scripts:

- `cd` into `rainbow-rave/`
- set `GDK_SCALE=2` for HiDPI scaling
- apply the JDK 17 `--add-exports` flags needed by Gradle 6.6.1
- run `gradlew runClient`, which launches both plugins in one RuneLite client in `--developer-mode`

## How it works

The merge is done via a combined bootstrap in
`rainbow-rave/src/test/java/com/rainbowrave/CombinedPluginTest.java`, which loads both
plugin classes before starting RuneLite:

```java
ExternalPluginManager.loadBuiltin(RainbowRavePlugin.class, ChargePlugin.class);
```

The `com.charges` sources live in `rainbow-rave/src/main/java/com/charges/`, and both main
classes are declared in `rainbow-rave/runelite-plugin.properties`.

## Adding another plugin

1. Copy the plugin's sources into `rainbow-rave/src/main/java/` (keeping its package structure).
2. Add its plugin class to `rainbow-rave/runelite-plugin.properties` under `plugins=`.
3. Register it in `CombinedPluginTest.java`:

```java
ExternalPluginManager.loadBuiltin(RainbowRavePlugin.class, ChargePlugin.class, YourPlugin.class);
```

4. Run the launch script — the new plugin appears in the RuneLite plugin panel and can be toggled independently.

## Notes

- `gradle.properties` contains the `--add-exports` JVM args so builds work on newer JDKs.
- The two plugins remain independently toggleable in the RuneLite plugin panel.
