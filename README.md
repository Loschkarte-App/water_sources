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

- **Letzte Datenänderung:** 2026-07-20
- **Datensätze:** 993.085

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
| [`data/baden-wurttemberg.geojson`](data/baden-wurttemberg.geojson) | 100.000 |
| [`data/baden-wurttemberg.part-02.geojson`](data/baden-wurttemberg.part-02.geojson) | 11.260 |
| [`data/bayern.geojson`](data/bayern.geojson) | 100.000 |
| [`data/bayern.part-02.geojson`](data/bayern.part-02.geojson) | 100.000 |
| [`data/bayern.part-03.geojson`](data/bayern.part-03.geojson) | 35.715 |
| [`data/berlin.geojson`](data/berlin.geojson) | 13.352 |
| [`data/brandenburg.geojson`](data/brandenburg.geojson) | 34.351 |
| [`data/bremen.geojson`](data/bremen.geojson) | 2.572 |
| [`data/hamburg.geojson`](data/hamburg.geojson) | 16.309 |
| [`data/hessen.geojson`](data/hessen.geojson) | 64.951 |
| [`data/mecklenburg-vorpommern.geojson`](data/mecklenburg-vorpommern.geojson) | 10.074 |
| [`data/niedersachsen.geojson`](data/niedersachsen.geojson) | 100.000 |
| [`data/niedersachsen.part-02.geojson`](data/niedersachsen.part-02.geojson) | 36.236 |
| [`data/nordrhein-westfalen.geojson`](data/nordrhein-westfalen.geojson) | 100.000 |
| [`data/nordrhein-westfalen.part-02.geojson`](data/nordrhein-westfalen.part-02.geojson) | 19.842 |
| [`data/ohne-zuordnung.geojson`](data/ohne-zuordnung.geojson) | 77.086 |
| [`data/rheinland-pfalz.geojson`](data/rheinland-pfalz.geojson) | 39.685 |
| [`data/saarland.geojson`](data/saarland.geojson) | 9.363 |
| [`data/sachsen-anhalt.geojson`](data/sachsen-anhalt.geojson) | 13.232 |
| [`data/sachsen.geojson`](data/sachsen.geojson) | 33.282 |
| [`data/schleswig-holstein.geojson`](data/schleswig-holstein.geojson) | 53.996 |
| [`data/thuringen.geojson`](data/thuringen.geojson) | 21.779 |

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
