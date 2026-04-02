# karoo-osm-mapsforge-maps

Custom OpenStreetMap-based Mapsforge map files for Hammerhead Karoo cycling computers, built with Osmosis and the Mapsforge Map Writer plugin.

## Why?

The Hammerhead Karoo uses proprietary vector tiles that **do not include many useful OSM features** like mountain peaks (`natural=peak`), saddles, elevation data (`ele`), and mountain passes. By rebuilding `.map` files directly from OpenStreetMap data using Mapsforge, you get **full control over which features appear on your Karoo**.

This project pairs with [karoo-custom-mapstyle](https://github.com/dansoft-ch/karoo-custom-mapstyle) — a forked Elevate map style optimized for better visibility on the Karoo display.

---

## Already Prepared Maps for your Karoo per Country

1. Download your preffered country official map into Karoo device via standard process 
2. Download the prepared maps and XML for Karoo from (https://github.com/GeorgoLab/karoo-osm-mapsforge-maps/releases/)
3. Copy "country.map" to `(Karoo Storage)\offline\maps` and revrite the official map
4. Backup the offline_v*.xml in `(Karoo Storage)\` -> offline_v*_backup.xml
5. Copy the downloaded offline_v*.xml to `(Karoo Storage)\`

---

## Prerequisites

| Tool | Version | Download |
|------|---------|----------|
| Java (JDK/JRE) | 11+ | [Adoptium](https://adoptium.net/) |
| Osmosis | 0.49.2+ | [GitHub Releases](https://github.com/openstreetmap/osmosis/releases) |
| Mapsforge Map Writer Plugin | 0.27.0+ | [GitHub Releases](https://github.com/mapsforge/mapsforge/releases) |

> **Important:** Download the `mapsforge-map-writer-*-jar-with-dependencies.jar` file — the standard JAR will **not** work.

---

## Step-by-Step Guide (Windows)

### 1. Install Java

1. Download and install Java from [Adoptium](https://adoptium.net/) (Latest LTS, Windows x64, `.msi`)
2. During installation, check **"Set JAVA_HOME variable"** and **"Add to PATH"**
3. Verify in a new Command Prompt:
   ```cmd
   java -version
   ```

### 2. Install Osmosis

1. Download the latest `osmosis-*.zip` from [GitHub](https://github.com/openstreetmap/osmosis/releases)
2. Extract to `C:\Tools\osmosis`
3. Add `C:\Tools\osmosis\bin` to your system PATH:
   - Press `Win + S` → search "Environment Variables"
   - Edit `Path` under System variables → add `C:\Tools\osmosis\bin`
4. Verify in a new Command Prompt:
   ```cmd
   osmosis --help
   ```

### 3. Install the Mapsforge Map Writer Plugin

1. Download `mapsforge-map-writer-*-jar-with-dependencies.jar` from [Mapsforge Releases](https://github.com/mapsforge/mapsforge/releases)
2. Copy the JAR into:
   ```
   C:\Tools\osmosis\lib\default\
   ```
   Create the `default` folder if it doesn't exist.

### 4. Download OSM Data

1. Go to [Geofabrik Downloads](https://download.geofabrik.de/)
2. Navigate to your country/region and download the `.osm.pbf` file
3. Save to your working directory, e.g. `C:\MapWork\`

**Example:** Slovakia → https://download.geofabrik.de/europe/slovakia.html

### 5. Prepare Tag Mapping

1. Download the default tag mapping:
   ```
   https://raw.githubusercontent.com/mapsforge/mapsforge/master/mapsforge-map-writer/src/main/config/tag-mapping.xml
   ```
2. Save as `C:\MapWork\tag-mapping.xml`
3. Open in a text editor and add these entries inside the `<pois>` section:

   ```xml
   <!-- Mountain features -->
   <osm-tag key="natural" value="peak" zoom-appear="10"/>
   <osm-tag key="natural" value="volcano" zoom-appear="10"/>
   <osm-tag key="natural" value="saddle" zoom-appear="13"/>
   <osm-tag key="mountain_pass" value="yes" zoom-appear="12"/>
   ```

4. Save the file.

> A ready-to-use `tag-mapping.xml` with these additions is included in this repository.

### 6. Generate the Map File

Open Command Prompt in your working directory and run:

```cmd
cd C:\MapWork

osmosis --plugin-dir "C:\Tools\osmosis\lib\default" ^
  --read-pbf file=slovakia-latest.osm.pbf ^
  --mapfile-writer file=slovakia_custom.map ^
  type=hd ^
  tag-conf-file=tag-mapping.xml ^
  map-start-position=48.6725,19.7006 ^
  map-start-zoom=8
```

> **Adapt for your country:** Change the `.osm.pbf` filename and `map-start-position` coordinates accordingly.

This process takes **5–20 minutes** depending on country size and hardware.

### 7. Upload to Karoo

1. Connect your Karoo to your computer via USB
2. Navigate to the Karoo's maps storage folder
3. Copy the generated `.map` file to the device
4. Restart the Karoo

### 8. Apply the Map Style

For the best experience, use the [karoo-custom-mapstyle](https://github.com/dansoft-ch/karoo-custom-mapstyle) — an optimized Elevate theme fork with improved visibility for the Karoo display.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `osmosis` not recognized | Use full path: `C:\Tools\osmosis\bin\osmosis.bat` or add to PATH |
| `Task type mapfile-writer doesn't exist` | Ensure the `jar-with-dependencies.jar` is in `lib\default\`. Try adding `--plugin-dir "C:\Tools\osmosis\lib\default"` |
| `java.lang.OutOfMemoryError` | Run `set JAVACMD_OPTIONS=-Xmx4g` before the command (use `-Xmx8g` for large countries) |
| `Could not find tag-mapping.xml` | Use the full absolute path in the command |

---

## Folder Structure

```
C:\Tools\osmosis\
├── bin\osmosis.bat                                    # Osmosis executable
└── lib\default\
    └── mapsforge-map-writer-*-jar-with-dependencies.jar  # Plugin

C:\MapWork\
├── slovakia-latest.osm.pbf      # Downloaded OSM data
├── tag-mapping.xml               # Custom tag mapping
└── slovakia_custom.map           # Generated output
```

---

## ⚠️ Warning

**Use at your own risk.** Loading custom map files and styles onto your Hammerhead Karoo may cause:

- Unexpected device behavior, crashes, or freezes
- Increased battery consumption due to larger map files
- Slower map rendering or lag when panning/zooming
- Loss of default map data if original files are overwritten

**Always back up your Karoo's original map files before replacing them.** The authors of this project are not responsible for any damage, data loss, or bricked devices. This project is not affiliated with or endorsed by Hammerhead.

---

## Sources & References

| Resource | Description |
|----------|-------------|
| [OpenStreetMap](https://www.openstreetmap.org/) | Map data source — © OpenStreetMap contributors |
| [Geofabrik Downloads](https://download.geofabrik.de/) | Pre-built OSM country/region extracts (`.osm.pbf`) |
| [Osmosis](https://github.com/openstreetmap/osmosis) | OpenStreetMap data processing tool |
| [Mapsforge](https://github.com/mapsforge/mapsforge) | Map rendering library & map writer plugin |
| [Mapsforge Map Writer Docs](https://github.com/mapsforge/mapsforge/blob/master/docs/MapCreation.md) | Official map creation documentation |
| [Mapsforge Default Tag Mapping](https://github.com/mapsforge/mapsforge/blob/master/mapsforge-map-writer/src/main/config/tag-mapping.xml) | Default tag-mapping.xml configuration |
| [Adoptium / Eclipse Temurin](https://adoptium.net/) | Recommended Java distribution |
| [Elevate Map Style](https://www.openandromaps.org/en/legend/elevate-mountain-hike-theme) | Original map theme (basis for karoo-custom-mapstyle) |
| [karoo-custom-mapstyle](https://github.com/dansoft-ch/karoo-custom-mapstyle) | Companion project: optimized Elevate fork for Karoo |
| [Karoo-GPS-Style](https://github.com/lockevod/Karoo-GPS-Style) | Community Karoo map style project |

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

Map data © [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors, licensed under the [Open Data Commons Open Database License (ODbL)](https://opendatacommons.org/licenses/odbl/).

The tag-mapping.xml is derived from the [Mapsforge project](https://github.com/mapsforge/mapsforge), licensed under the GNU Lesser General Public License v3.0.

---

## Contributing

Contributions are welcome! Feel free to:
- Submit tag-mapping.xml improvements for additional map features
- Share pre-built `.map` files for other countries
- Report issues or suggest improvements

---

*Built with ❤️ for the cycling community*
