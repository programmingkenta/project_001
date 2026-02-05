# Isometric Shibuya Crossing Map

An interactive isometric visualization of Shibuya Crossing using OpenStreetMap data, inspired by the Isometric NYC project.

## Overview

This project parses OpenStreetMap XML data to extract building footprints, heights, and metadata, then renders them in an engaging isometric 3D perspective using HTML5 Canvas and JavaScript.

## Features

- **Real OpenStreetMap Data**: Parses .osm XML files for authentic building geometry
- **Isometric Projection**: Classic 3D perspective popular in city-building games
- **Building Details**: Extracts building heights, names, and types
- **Interactive Visualization**: Navigate and explore Shibuya Crossing from above
- **Lightweight**: Pure Python parsing with HTML5/JS rendering - no heavy frameworks

## How It Works

The pipeline mirrors professional mapping projects like Isometric NYC:

1. **Parse OSM XML**: Extract nodes (map points) and ways (building outlines)
2. **Transform Coordinates**: Convert lat/lon to local meters using equirectangular projection
3. **Isometric Projection**: Apply 2:1 pixel ratio transformation (26.565° angle)
4. **Render**: Generate interactive HTML with Canvas API

## Usage

```bash
python build_shibuya_map_002.py
```

The script expects an OSM file at `/home/kenta/Downloads/Shibuya Crossing Map.osm`. You can export OSM data for any area from [OpenStreetMap](https://www.openstreetmap.org/export).

## Output

Generates `shibuya_osm.html` with:
- Interactive isometric map
- Building colors by type (residential, commercial, etc.)
- Hoverable building names
- Smooth rendering with building outlines

## Data Source

- **OpenStreetMap**: Community-maintained map data under [ODbL license](https://www.openstreetmap.org/copyright)

## Technical Details

### Coordinate Transformation

```python
# Lat/Lon → Local meters (equirectangular)
x = (lon - origin_lon) * cos(origin_lat) * 111320
y = (lat - origin_lat) * 111320

# Meters → Isometric pixels
iso_x = (x - y) * scale_factor
iso_y = (x + y) * 0.5 * scale_factor - height * scale_factor
```

### Isometric Angle

The classic 26.565° angle (arctan(0.5)) creates the characteristic 2:1 pixel ratio of isometric projection.

## Inspiration

Modeled after [Isometric NYC](https://github.com/dataartist/isometric-nyc) by Robert Hodgin, which demonstrated that real map data + isometric rendering = compelling visualizations.

## Requirements

- Python 3.x
- Standard library only (xml.etree.ElementTree, json, math)
- Modern web browser

## License

Code: MIT  
Map Data: OpenStreetMap [ODbL](https://www.openstreetmap.org/copyright)
