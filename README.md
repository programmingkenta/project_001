# Isometric Shibuya Crossing Map

An interactive isometric visualization of Shibuya Crossing using real OpenStreetMap data, demonstrating the power of combining open geospatial data with classic game-style rendering techniques.

## Overview

This project bridges the gap between real-world mapping data and engaging visual storytelling. By parsing OpenStreetMap XML data and applying isometric projection mathematics, it transforms latitude/longitude coordinates into the familiar tilted perspective of SimCity, Civilization, and Monument Valley.

**Why Isometric?** The 26.565° viewing angle (arctan(0.5)) creates a perfect balance: shows building heights clearly, maintains spatial relationships, and evokes the nostalgic aesthetic of classic strategy games - all while using real geospatial data.

## Features

- **Real OpenStreetMap Data**: Parses .osm XML files for authentic building geometry with metadata
- **Isometric Projection**: Classic 26.565° viewing angle (2:1 pixel ratio) from strategy games
- **Building Details**: Extracts building heights, names, and usage types (residential, commercial, etc.)
- **Interactive Visualization**: Pan and explore Shibuya Crossing from the iconic game-style perspective
- **Lightweight Architecture**: Pure Python parsing + HTML5 Canvas rendering - zero external dependencies
- **Color-Coded Buildings**: Visual distinction by building type for improved readability

## How It Works

The data processing pipeline mirrors professional mapping projects like Isometric NYC:

### Step 1: Parse OSM XML
Extract nodes (geographic points with lat/lon) and ways (building outlines as sequences of node references):
```python
nodes = {}  # node_id → {lat, lon}
for node in root.findall("node"):
    nodes[node.get("id")] = {
        "lat": float(node.get("lat")),
        "lon": float(node.get("lon"))
    }
```

### Step 2: Coordinate Transformation
Convert geographic coordinates (latitude/longitude) to local meters using equirectangular projection:
```python
x = (lon - origin_lon) * cos(origin_lat) * 111320  # meters east
y = (lat - origin_lat) * 111320  # meters north
```

### Step 3: Isometric Projection
Apply the classic 2:1 isometric transformation to create the tilted view:
```python
iso_x = (x - y) * scale_factor
iso_y = (x + y) * 0.5 * scale_factor - height * scale_factor
```
This creates the 26.565° angle (arctan(0.5)) characteristic of isometric games.

### Step 4: Canvas Rendering
Generate self-contained HTML with interactive Canvas API rendering - no server required

## Live Demo

Open [shibuya_osm.html](shibuya_osm.html) in any modern web browser to view the interactive isometric visualization.

**Visualization Features:**
- Color-coded buildings by usage type (residential, commercial, retail, etc.)
- Smooth anti-aliased rendering with building outlines
- Responsive canvas that adapts to window size
- Self-contained HTML - works offline, no server needed

## Usage

### Viewing the Visualization

**Quickest way:** Simply open [shibuya_osm.html](shibuya_osm.html) in your browser - no setup required!

### Regenerating or Creating Your Own

```bash
python build_shibuya_map_002.py
# Output: shibuya_osm.html
```

### Getting OSM Data

**Note:** The pre-generated `shibuya_osm.html` can be viewed directly without running the script. To regenerate the visualization or create your own:

The script expects an OSM XML file. To get data for any location:

1. Visit [OpenStreetMap Export](https://www.openstreetmap.org/export)
2. Navigate to your desired area (e.g., Shibuya Crossing: 35.6595°N, 139.7004°E)
3. Select the area using the "Manually select a different area" option
4. Click "Export" to download the `.osm` XML file
5. Save it to your project directory (e.g., `shibuya_crossing.osm`)
6. Update line 20 in `build_shibuya_map_002.py`:
   ```python
   osm_file = "shibuya_crossing.osm"  # Update this path
   ```

### Customization

Edit these variables in the script to customize the visualization:
- **`origin_lat`, `origin_lon`**: Center point of the map
- **`scale_factor`**: Zoom level (pixels per meter)
- **Building colors**: Modify the color dictionary for different building types

## Data Source

- **OpenStreetMap**: Community-maintained map data under [ODbL license](https://www.openstreetmap.org/copyright)
- **Coverage**: Global - works for any location with OSM building data
- **Data Quality**: Varies by region; well-mapped urban areas like Shibuya have excellent detail

## Key Technical Concepts

### 1. XML Parsing Strategy
**Challenge:** OSM XML has two-level references - ways reference nodes by ID
**Solution:** Build node dictionary first for O(1) lookups
```python
nodes = {}  # Pre-build lookup table
for node in root.findall("node"):
    nodes[node_id] = {"lat": lat, "lon": lon}

# Later: O(1) coordinate lookup per node reference
coords = [nodes[ref] for ref in way_node_refs]
```

### 2. Coordinate System Mathematics

**Equirectangular Projection:**
```python
# Earth's circumference: ~40,075 km → 111.32 km per degree
# Adjust longitude by latitude to handle Earth's curvature
x = (lon - origin_lon) * cos(origin_lat) * 111320  # meters
y = (lat - origin_lat) * 111320  # meters
```

**Isometric Transformation:**
```python
# The magic 2:1 ratio that defines isometric projection
iso_x = (x - y) * scale_factor
iso_y = (x + y) * 0.5 * scale_factor - height * scale_factor
```
The 0.5 multiplier creates the 26.565° angle (arctan(0.5)) that makes isometric projection feel "just right."

### 3. Building Height Estimation
OSM data often lacks explicit heights. The script uses heuristics:
- **`building:levels` tag**: Multiply by 3 meters per floor
- **Direct `height` tag**: Use if available
- **Fallback**: Default heights by building type (residential: 12m, commercial: 15m)

### 4. Canvas Rendering Optimization
- **Painter's Algorithm**: Render buildings back-to-front for correct overlapping
- **Color Coding**: Visual hierarchy makes building types immediately recognizable
- **Outline Strokes**: 2px borders separate adjacent buildings clearly

### 5. Isometric Mathematics Deep Dive

The 26.565° angle isn't arbitrary - it's mathematically special:
```
tan(26.565°) = 0.5
arctan(0.5) = 26.565°
```

This creates the 2:1 pixel ratio where horizontal distance = 2× vertical distance:
```
For a cube: width = 2 * height
Perfect for tile-based game grids
```

This is why SimCity, Civilization, and Monument Valley all use this exact angle.

## Skills Demonstrated

### Python Programming
- XML parsing with ElementTree (handling two-level references)
- Dictionary-based data structures for O(1) lookups
- Trigonometric coordinate transformations
- HTML generation with embedded JavaScript

### Geospatial Data Processing
- OpenStreetMap data format understanding
- Geographic coordinate system transformations
- Equirectangular projection for lat/lon → meters
- Handling Earth's curvature in coordinate calculations

### Computer Graphics & Mathematics
- Isometric projection mathematics (26.565° angle)
- 2D rendering optimization (painter's algorithm)
- Canvas API for dynamic rendering
- Color theory for visual hierarchy

### Data Engineering
- Two-stage processing pipeline (nodes → ways → visualization)
- Memory-efficient lookup tables
- Handling missing/incomplete data gracefully
- Self-contained output generation

## What I Learned

This project taught me that **coordinate transformations are everywhere** in geospatial work. The journey from lat/lon to isometric pixels involves:

1. **Understanding projections** - Why equirectangular? Because it's simple and accurate for small areas
2. **Trigonometry matters** - The `cos(lat)` correction for longitude isn't optional - Earth is a sphere!
3. **Historical knowledge** - The 26.565° angle has been the standard since 1982's Zaxxon arcade game

The biggest surprise was discovering that **OSM data quality varies dramatically**. Some buildings have perfect height data, others have nothing. Real-world data engineering means gracefully handling missing information.

**Isometric rendering is deceptively simple**: just linear transformations, yet it creates the illusion of 3D that's instantly recognizable from decades of games.

## Inspiration

Modeled after [Isometric NYC](https://github.com/dataartist/isometric-nyc) by Robert Hodgin, which demonstrated that real map data + isometric rendering = compelling visualizations. This project proves the concept works globally - from NYC to Tokyo.

## Requirements

- Python 3.x
- Standard library only (xml.etree.ElementTree, json, math)
- Modern web browser with Canvas API support
- No external Python dependencies required
- No web server needed - static HTML output

## Future Enhancements

Potential improvements for this visualization approach:
- **Road network rendering** - Add streets and pathways
- **Shadow simulation** - Time-of-day lighting effects
- **Interactive zoom** - Multi-scale rendering
- **Building metadata tooltips** - Show names and details on hover
- **Historical comparison** - Overlay different time periods

## License

**Code:** MIT License - Free to use, modify, and distribute
**Map Data:** OpenStreetMap [ODbL](https://www.openstreetmap.org/copyright) - Attribution required

## Acknowledgments

- **Robert Hodgin** - Original [Isometric NYC](https://github.com/dataartist/isometric-nyc) concept
- **OpenStreetMap contributors** - Global collaborative mapping effort
- **Classic game designers** - Establishing the 26.565° isometric standard
- **Claude Code** - AI pair programming for debugging and mathematics

---

**Real geospatial data + game-style rendering = engaging visualization.** This project demonstrates that compelling maps don't require 3D engines - just good mathematics and thoughtful design.
