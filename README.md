# Wasserentnahmestellen — Löschkarte.app

Offene Daten zu Wasserentnahmestellen für die Feuerwehr in Deutschland:
Hydranten (Unter-, Überflur, Wandhydranten), Löschwasserbehälter, Zisternen,
Saugstellen und offene Gewässer.

Die Daten stammen von [Löschkarte.app](https://loeschkarte.app), wo Feuerwehren
die Wasserentnahmestellen ihrer Gemeinde pflegen, ergänzt um Bestände aus
OpenStreetMap.

**Dieses Repository wird täglich automatisch aktualisiert.** Es dient zugleich als
Offsite-Backup und als Bulk-Download für alle, die den kompletten Datenbestand
möchten, ohne die REST-API zu paginieren.

- **Letzte Datenänderung:** 2026-07-18
- **Datensätze:** 992.647

## Lizenz

Open Data Commons Open Database License (ODbL) v1.0 — siehe [`LICENSE.txt`](https://opendatacommons.org/licenses/odbl/1-0/).

> Wasserentnahmestellen: © Löschkarte.app-Mitwirkende und © OpenStreetMap-Mitwirkende, lizenziert unter ODbL 1.0

Der Datenbestand enthält Daten aus OpenStreetMap und ist damit insgesamt eine
abgeleitete Datenbank im Sinne der ODbL. Die ODbL ist eine Share-alike-Lizenz:
Sie dürfen die Daten frei nutzen, teilen und verändern — auch kommerziell —
solange Sie

1. die oben genannte Namensnennung mitführen,
2. veränderte Datenbanken ebenfalls unter der ODbL weitergeben und
3. keine technischen Schutzmaßnahmen einsetzen, die andere an diesen Rechten hindern.

OpenStreetMap-Urheberrecht: https://www.openstreetmap.org/copyright

**Wichtig:** Die Daten werden ohne Gewähr bereitgestellt. Eine Wasserentnahmestelle
kann defekt, zugeparkt oder nicht mehr vorhanden sein. Sie ersetzen keine
Einsatzplanung und keine Prüfung vor Ort.

## Aufbau

Ein Verzeichnis `data/` mit je einer Datei pro Bundesland im Format
[GeoJSON](https://geojson.org/) (`EPSG:4326` / WGS 84, wie im Standard
vorgeschrieben). Nicht zugeordnete Stellen liegen in `ohne-zuordnung.geojson`.
Sehr große Bundesländer werden auf mehrere Teile (`.part-02.geojson`) aufgeteilt,
damit jede Datei handhabbar bleibt.

| Datei | Datensätze |
| --- | --- |
| [`data/baden-wurttemberg.geojson`](data/baden-wurttemberg.geojson) | 2.829 |
| [`data/bayern.geojson`](data/bayern.geojson) | 91.982 |
| [`data/brandenburg.geojson`](data/brandenburg.geojson) | 775 |
| [`data/hessen.geojson`](data/hessen.geojson) | 13.689 |
| [`data/mecklenburg-vorpommern.geojson`](data/mecklenburg-vorpommern.geojson) | 440 |
| [`data/niedersachsen.geojson`](data/niedersachsen.geojson) | 22.636 |
| [`data/nordrhein-westfalen.geojson`](data/nordrhein-westfalen.geojson) | 7.685 |
| [`data/ohne-zuordnung.geojson`](data/ohne-zuordnung.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-02.geojson`](data/ohne-zuordnung.part-02.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-03.geojson`](data/ohne-zuordnung.part-03.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-04.geojson`](data/ohne-zuordnung.part-04.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-05.geojson`](data/ohne-zuordnung.part-05.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-06.geojson`](data/ohne-zuordnung.part-06.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-07.geojson`](data/ohne-zuordnung.part-07.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-08.geojson`](data/ohne-zuordnung.part-08.geojson) | 100.000 |
| [`data/ohne-zuordnung.part-09.geojson`](data/ohne-zuordnung.part-09.geojson) | 41.251 |
| [`data/rheinland-pfalz.geojson`](data/rheinland-pfalz.geojson) | 761 |
| [`data/schleswig-holstein.geojson`](data/schleswig-holstein.geojson) | 10.599 |

Jede Datei ist eine vollständige `FeatureCollection`. Die Features stehen
zeilenweise, nach `id` sortiert — dadurch bleiben die Git-Diffs zwischen zwei
Tagen klein und nachvollziehbar.

### Eigenschaften

| Feld | Bedeutung |
| --- | --- |
| `id` | Stabile ID der Wasserentnahmestelle bei Löschkarte.app |
| `type` / `type_label` | Art der Entnahmestelle (`unterflur`, `ueberflur`, `zisterne`, …) |
| `name` | Bezeichnung, sofern vergeben |
| `size_label` | Nennweite bzw. Größe in Kurzform (z. B. `DN 100`) |
| `volume_m3` | Volumen in Kubikmetern (bei Behältern/Zisternen, normalisiert) |
| `usability_status` / `usability_label` | Verwendbarkeit (`usable`, `limited`, `unusable`, `unknown`) |
| `has_open_report` | Es liegt eine offene Störungsmeldung vor |
| `gemeinde` / `bundesland` | Zuständige Gemeinde, sofern zugeordnet |
| `origin` | Herkunft des Datensatzes (`manual`, `osm_import`, …) |
| `last_maintained_at` | Letzte dokumentierte Prüfung |
| `osm_tags` | Rohe OpenStreetMap-Tags (z. B. `fire_hydrant:diameter`, `couplings`) |

Felder ohne Wert werden weggelassen. Personenbezogene Daten sind **nicht**
enthalten: weder wer einen Datensatz angelegt hat noch Foto-Metadaten.

## Import

**QGIS** — `Layer ▸ Layer hinzufügen ▸ Vektorlayer hinzufügen` und die
gewünschte `.geojson` auswählen. Mehrere Bundesländer lassen sich gleichzeitig
auswählen.

**ogr2ogr / GDAL** — z. B. nach GeoPackage oder PostGIS:

```bash
ogr2ogr -f GPKG wasserentnahmestellen.gpkg data/bayern.geojson
ogr2ogr -f PostgreSQL PG:"dbname=gis" data/bayern.geojson -nln wasserentnahmestellen
```

**Python** — mit GeoPandas:

```python
import geopandas as gpd

gdf = gpd.read_file("data/bayern.geojson")
print(gdf.head())
```

**Kommandozeile** — mit `jq`, etwa alle Zisternen über 50 m³:

```bash
jq '.features[] | select(.properties.type == "zisterne" and .properties.volume_m3 > 50)' \
  data/bayern.geojson
```

**Alles auf einmal** — die Bundesländer zu einer Datei zusammenführen:

```bash
ogrmerge.py -single -o deutschland.geojson data/*.geojson
```

## Andere Zugänge

Für den laufenden Betrieb ist meist eine der Live-Schnittstellen passender als
dieser Bulk-Download:

- **REST-API** — `https://loeschkarte.app/api/v1/water-sources`
- **WMS** — für Leitstellen- und GIS-Software
- **XYZ-Kacheln** — für Einsatz-Apps
- **Einzelexporte** (GeoJSON, CSV, KML, auch bbox-begrenzt) direkt auf der Website

Details unter [loeschkarte.app](https://loeschkarte.app).

## Fehler gefunden?

Korrekturen bitte nicht als Pull Request — dieses Repository wird bei jedem Lauf
maschinell überschrieben. Melden Sie Fehler stattdessen direkt in der Karte auf
[loeschkarte.app](https://loeschkarte.app), damit die zuständige Feuerwehr sie
prüfen kann.
