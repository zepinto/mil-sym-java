# mil-sym-java

A comprehensive Java library for rendering military symbols according to MIL-STD-2525D Change 1 and MIL-STD-2525E Change 1 standards.

## Table of Contents

- [About](#about)
- [Understanding MIL-STD-2525](#understanding-mil-std-2525)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Usage Examples](#usage-examples)
- [Symbol ID Structure](#symbol-id-structure)
- [Modifiers](#modifiers)
- [Multi-Point Symbols](#multi-point-symbols)
- [Project Structure](#project-structure)
- [Building](#building)
- [Resources](#resources)
- [License](#license)

## About

mil-sym-java is a battle-tested set of Java libraries that have been used in US Army Mission Command software for years. In November 2013, Mission Command was given approval to release and maintain these libraries as public open source. 

This library is a continuation of that effort and provides full support for MIL-STD-2525D Change 1 and MIL-STD-2525E Change 1 standards. The older [2525C Renderer](https://github.com/missioncommand/old-mil-sym-java) has been retired.

### Key Features

- ✅ Full MIL-STD-2525D Change 1 support
- ✅ Full MIL-STD-2525E Change 1 support
- ✅ Single-point icon rendering
- ✅ Multi-point tactical graphics
- ✅ Extensive modifier support
- ✅ SVG and raster (PNG, JPEG, etc.) output
- ✅ Customizable rendering settings
- ✅ High-performance caching

## Understanding MIL-STD-2525

MIL-STD-2525 is a military standard that defines symbols used to display military information on maps, displays, and overlays. The standard ensures consistent representation of units, equipment, installations, and tactical operations across different systems and organizations.

### What is MIL-STD-2525?

The standard provides a comprehensive symbol set that includes:

- **Units**: Military organizations from team level to army group
- **Equipment**: Vehicles, weapons systems, and other military hardware
- **Installations**: Facilities, structures, and fixed positions
- **Activities**: Operations, missions, and tasks
- **Control Measures**: Tactical graphics like boundaries, routes, and areas
- **METOC**: Meteorological and oceanographic symbols

### Symbol Components

A MIL-STD-2525 symbol consists of:

1. **Frame**: The basic shape that indicates standard identity (friendly, hostile, neutral, unknown)
2. **Icon**: The central symbol representing the entity type
3. **Modifiers**: Additional graphics and text providing context:
   - **Amplifiers**: Echelon, task force indicator, installation
   - **Text Modifiers**: Unique designation, higher formation, quantity, etc.
   - **Sector Modifiers**: Additional icon modifications

### Symbol ID Code

Each symbol is identified by a 20-30 character Symbol Identification Code (SIDC):

```
Position  1-2:  Version (10=2525D, 15=2525E)
Position  3-4:  Standard Identity (Context + Affiliation)
Position  5-6:  Symbol Set (01=Air, 10=Land Unit, 30=Sea Surface, etc.)
Position  7:    Status (Present, Planned, etc.)
Position  8:    HQ/TF/Dummy
Position  9-10: Amplifier/Echelon
Position 11-16: Entity/Entity Type/Entity Subtype
Position 17-20: Modifier codes
```

## Quick Start

```java
import armyc2.c5isr.renderer.MilStdIconRenderer;
import armyc2.c5isr.renderer.utilities.*;

import java.util.HashMap;
import java.util.Map;

public class QuickStart {
    public static void main(String[] args) {
        // Create renderer instance
        MilStdIconRenderer renderer = MilStdIconRenderer.getInstance();
        
        // Create a friendly infantry unit symbol
        String symbolID = "10031000141211000000"; // 2525D Friendly Infantry
        Map<String, String> modifiers = new HashMap<>();
        Map<String, String> attributes = new HashMap<>();
        
        // Add text modifiers
        modifiers.put(Modifiers.C_QUANTITY, "200");
        modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "1-501 IN");
        
        // Set rendering attributes
        attributes.put(MilStdAttributes.PixelSize, "100");
        
        // Render the icon
        ImageInfo icon = renderer.RenderIcon(symbolID, modifiers, attributes);
        
        // Save to file
        if (icon != null) {
            icon.SaveImageToFile("/path/to/output.png", "png");
        }
    }
}
```

## Installation

### Maven

```xml
<dependency>
    <groupId>io.github.missioncommand</groupId>
    <artifactId>mil-sym-java</artifactId>
    <version>2.5.1</version>
</dependency>
```

### Gradle

```gradle
implementation 'io.github.missioncommand:mil-sym-java:2.5.1'
```

### Manual Build

```bash
git clone https://github.com/missioncommand/mil-sym-java.git
cd mil-sym-java
./gradlew build
```

The JAR file will be in `build/libs/`

## Usage Examples

### Example 1: Basic Infantry Symbol

```java
import armyc2.c5isr.renderer.MilStdIconRenderer;
import armyc2.c5isr.renderer.utilities.*;

import java.util.HashMap;
import java.util.Map;

public class BasicInfantryExample {
    public static void main(String[] args) {
        MilStdIconRenderer renderer = MilStdIconRenderer.getInstance();
        
        // 2525D Friendly Infantry, Present, Squad
        String symbolID = "10031000141211000000";
        
        Map<String, String> modifiers = new HashMap<>();
        modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "A/1-501");
        modifiers.put(Modifiers.C_QUANTITY, "9");
        
        Map<String, String> attributes = new HashMap<>();
        attributes.put(MilStdAttributes.PixelSize, "128");
        
        ImageInfo icon = renderer.RenderIcon(symbolID, modifiers, attributes);
        icon.SaveImageToFile("infantry_squad.png", "png");
    }
}
```

### Example 2: Armored Unit with Echelon

```java
// 2525D Friendly Armor (Tank), Battalion
String symbolID = "10031000161204000000";

Map<String, String> modifiers = new HashMap<>();
modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "1-64 AR");
modifiers.put(Modifiers.M_HIGHER_FORMATION, "3 IBCT");
modifiers.put(Modifiers.B_ECHELON, "16"); // Battalion

Map<String, String> attributes = new HashMap<>();
attributes.put(MilStdAttributes.PixelSize, "150");
attributes.put(MilStdAttributes.LineColor, "FF0000FF"); // Blue

ImageInfo icon = renderer.RenderIcon(symbolID, modifiers, attributes);
icon.SaveImageToFile("tank_battalion.png", "png");
```

### Example 3: Hostile Aircraft

```java
// 2525D Hostile Fixed-Wing Aircraft
String symbolID = "10061000001101000000";

Map<String, String> modifiers = new HashMap<>();
modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "MIG-29");
modifiers.put(Modifiers.AL_ALTITUDE_DEPTH, "25000 FT");
modifiers.put(Modifiers.AN_AZIMUTH, "045");

Map<String, String> attributes = new HashMap<>();
attributes.put(MilStdAttributes.PixelSize, "120");

ImageInfo icon = renderer.RenderIcon(symbolID, modifiers, attributes);
icon.SaveImageToFile("hostile_aircraft.png", "png");
```

### Example 4: Rendering as SVG

```java
// Render symbol as SVG instead of raster image
String symbolID = "10031000141211000000";

Map<String, String> modifiers = new HashMap<>();
modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "B/1-501");

Map<String, String> attributes = new HashMap<>();

// Get SVG output
SVGSymbolInfo svg = renderer.RenderSVG(symbolID, modifiers, attributes);
System.out.println(svg.getSVG()); // Print SVG markup
```

### Example 5: Custom Colors and Outline

```java
String symbolID = "10031000141211000000";

Map<String, String> modifiers = new HashMap<>();
modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "C/2-502");

Map<String, String> attributes = new HashMap<>();
attributes.put(MilStdAttributes.PixelSize, "150");
attributes.put(MilStdAttributes.LineColor, "FF00FF00"); // Green
attributes.put(MilStdAttributes.FillColor, "8000FF00"); // Semi-transparent green
attributes.put(MilStdAttributes.OutlineSymbol, "true");
attributes.put(MilStdAttributes.OutlineColor, "FF000000"); // Black outline

ImageInfo icon = renderer.RenderIcon(symbolID, modifiers, attributes);
icon.SaveImageToFile("custom_colors.png", "png");
```

### Example 6: Equipment Symbol

```java
// 2525D Friendly Self-Propelled Artillery
String symbolID = "10031500151211050000";

Map<String, String> modifiers = new HashMap<>();
modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "M109A7");
modifiers.put(Modifiers.C_QUANTITY, "18");
modifiers.put(Modifiers.V_EQUIP_TYPE, "155MM");

Map<String, String> attributes = new HashMap<>();
attributes.put(MilStdAttributes.PixelSize, "140");

ImageInfo icon = renderer.RenderIcon(symbolID, modifiers, attributes);
icon.SaveImageToFile("artillery.png", "png");
```

## Symbol ID Structure

The Symbol ID is a 20-30 character code that uniquely identifies a symbol. Here's how to construct one:

### Building a Symbol ID

```java
import armyc2.c5isr.renderer.utilities.SymbolID;

// Start with a base template
String baseID = "10031000000000000000";

// Version: 2525D Change 1
baseID = SymbolID.setVersion(baseID, SymbolID.Version_2525Dch1);

// Standard Identity: Friendly
baseID = SymbolID.setStandardIdentity(baseID, 
    SymbolID.StandardIdentity_Context_Reality * 10 + 
    SymbolID.StandardIdentity_Affiliation_Friend);

// Symbol Set: Land Unit (10)
baseID = SymbolID.setSymbolSet(baseID, SymbolID.SymbolSet_LandUnit);

// Status: Present (0)
baseID = SymbolID.setStatus(baseID, SymbolID.Status_Present);

// HQ/TF/Dummy: None (0)
baseID = SymbolID.setHQTFD(baseID, SymbolID.HQTFD_Unknown);

// Echelon: Company (15)
baseID = SymbolID.setAmplifierDescriptor(baseID, 15);

// Entity Code: Infantry (1211)
baseID = SymbolID.setEntityCode(baseID, 121100);

// Result: "10031000151211000000"
```

### Common Symbol Sets

| Code | Symbol Set | Description |
|------|-----------|-------------|
| 01 | Air | Aircraft and air defense |
| 05 | Space | Space systems |
| 10 | Land Unit | Military units |
| 11 | Land Civilian | Civilian organizations |
| 15 | Land Equipment | Vehicles and equipment |
| 20 | Land Installation | Facilities and structures |
| 25 | Control Measure | Tactical graphics |
| 30 | Sea Surface | Surface vessels |
| 35 | Sea Subsurface | Submarines |
| 40 | Activities | Operations and missions |

### Common Affiliations

| Code | Affiliation | Frame Color |
|------|------------|-------------|
| 03 | Friendly | Blue |
| 04 | Neutral | Green |
| 06 | Hostile | Red |
| 01 | Unknown | Yellow |

### Common Echelons

| Code | Echelon | Symbol |
|------|---------|--------|
| 11 | Team/Crew | ● |
| 12 | Squad | •• |
| 13 | Section | ••• |
| 14 | Platoon | ••• |
| 15 | Company | \| |
| 16 | Battalion | \|\| |
| 17 | Regiment | \|\|\| |
| 18 | Brigade | X |
| 21 | Division | XX |
| 22 | Corps | XXX |
| 23 | Army | XXXX |

## Modifiers

Modifiers add context and detail to symbols. There are numerous modifier types:

### Essential Modifiers

```java
import armyc2.c5isr.renderer.utilities.Modifiers;

Map<String, String> modifiers = new HashMap<>();

// Text modifiers
modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "1-501 IN");    // Unit designation
modifiers.put(Modifiers.M_HIGHER_FORMATION, "1 BCT");        // Higher formation
modifiers.put(Modifiers.C_QUANTITY, "150");                  // Personnel/equipment count
modifiers.put(Modifiers.H_ADDITIONAL_INFO_1, "MECHANIZED");  // Additional info
modifiers.put(Modifiers.G_STAFF_COMMENTS, "READY");          // Staff comments
modifiers.put(Modifiers.V_EQUIP_TYPE, "M1A2");               // Equipment type

// Operational modifiers
modifiers.put(Modifiers.F_REINFORCED_REDUCED, "+");          // Reinforced (+), Reduced (-)
modifiers.put(Modifiers.AL_ALTITUDE_DEPTH, "5000 FT");       // Altitude
modifiers.put(Modifiers.AM_DISTANCE, "15 KM");               // Distance
modifiers.put(Modifiers.AN_AZIMUTH, "270");                  // Direction (degrees)
modifiers.put(Modifiers.AS_SPEED, "45 KPH");                 // Speed

// Location modifiers
modifiers.put(Modifiers.W_DTG_1, "121400ZJAN24");           // Date-Time Group
modifiers.put(Modifiers.Y_LOCATION, "N39.05 W77.11");        // Coordinates

// Signature equipment
modifiers.put(Modifiers.AE_PLATFORM_TYPE, "M1A2");           // Platform type
modifiers.put(Modifiers.AF_COMMON_IDENTIFIER, "71234");      // Common ID
```

### Graphic Modifiers

Some modifiers affect the symbol's appearance rather than adding text:

```java
// The echelon is specified in the Symbol ID (position 9-10)
// But can be overridden with modifier
modifiers.put(Modifiers.B_ECHELON, "18"); // Brigade level

// Task force and headquarters indicators are in Symbol ID position 8
// But some symbols support additional graphic modifiers
```

## Multi-Point Symbols

Tactical graphics like boundaries, routes, and areas require multiple control points:

```java
import armyc2.c5isr.renderer.utilities.*;
import armyc2.c5isr.RenderMultipoints.clsRenderer;

import java.awt.geom.Point2D;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

public class MultiPointExample {
    public static void main(String[] args) {
        // Create a boundary tactical graphic
        String symbolID = "100250000100000000000000000000"; // Phase Line
        
        // Define control points (lat/lon coordinates)
        ArrayList<Point2D> coordinates = new ArrayList<>();
        coordinates.add(new Point2D.Double(-77.05, 38.88)); // Washington DC
        coordinates.add(new Point2D.Double(-77.04, 38.89));
        coordinates.add(new Point2D.Double(-77.03, 38.90));
        
        Map<String, String> modifiers = new HashMap<>();
        modifiers.put(Modifiers.T_UNIQUE_DESIGNATION, "PL RED");
        
        // Create the symbol object
        MilStdSymbol symbol = new MilStdSymbol(symbolID, "id", coordinates, modifiers);
        symbol.setLineWidth(4);
        symbol.setUnitSize(50);
        
        // Create a point converter for coordinate transformation
        IPointConversion converter = new PointConversion(
            1000, 800,          // canvas width, height
            -77.10, 38.85,      // lower-left lon, lat
            -77.00, 38.95       // upper-right lon, lat
        );
        
        // Render the symbol
        clsRenderer.renderWithPolylines(symbol, converter, null);
        
        // Draw to graphics context
        // Graphics2D g2d = ... // your graphics context
        // SymbolDraw.Draw(symbol, g2d, 0, 0);
    }
}
```

### Common Tactical Graphics

| Symbol ID Pattern | Description |
|------------------|-------------|
| 1X0250000100000... | Boundaries |
| 1X0250000200000... | General Areas |
| 1X0250000300000... | Aviation Areas |
| 1X0250000400000... | Fire Support |
| 1X0250000500000... | Combat Service Support |
| 1X0250000600000... | Command and Control |

Where `X` in position 4 indicates affiliation (3=Friendly, 4=Neutral, 6=Hostile, etc.)

## Project Structure

```
mil-sym-java/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── armyc2/c5isr/
│   │   │       ├── renderer/              # Main rendering engine
│   │   │       │   ├── MilStdIconRenderer.java     # Primary renderer class
│   │   │       │   ├── SinglePointRenderer.java    # Single-point symbols
│   │   │       │   └── utilities/         # Helper classes
│   │   │       │       ├── MilStdSymbol.java      # Symbol data structure
│   │   │       │       ├── Modifiers.java         # Modifier constants
│   │   │       │       ├── MilStdAttributes.java  # Attribute constants
│   │   │       │       ├── ImageInfo.java         # Image output
│   │   │       │       ├── SymbolID.java          # SIDC utilities
│   │   │       │       └── RendererSettings.java  # Global settings
│   │   │       ├── JavaLineArray/         # Line rendering
│   │   │       ├── RenderMultipoints/     # Multi-point graphics
│   │   │       └── JavaTacticalRenderer/  # Tactical graphics
│   │   └── resources/
│   │       └── data/                      # Symbol definitions
│   └── test/
│       └── java/                          # Unit tests
└── build.gradle
```

## Building

### Requirements

- Java 8 or higher
- Gradle (included via wrapper)

### Build Commands

```bash
# Build the project
./gradlew build

# Run tests
./gradlew test

# Generate JavaDoc
./gradlew javadoc

# Create JAR files
./gradlew jar

# Clean build directory
./gradlew clean
```

### Generated Artifacts

After building, you'll find:
- `build/libs/mil-sym-java-<version>.jar` - Main library
- `build/libs/mil-sym-java-<version>-sources.jar` - Source code
- `build/libs/mil-sym-java-<version>-javadoc.jar` - Documentation
- `build/docs/javadoc/` - HTML documentation

## Resources

### Documentation

- [JavaDocs](https://missioncommand.github.io/javadoc/2525D/java/index.html) - Complete API documentation
- [Wiki](https://github.com/missioncommand/mil-sym-java/wiki) - Additional guides and examples
- [2525C Renderer Overview](https://github.com/missioncommand/mil-sym-java/wiki/2525C-Renderer-Overview) - Legacy renderer information

### Standards

- [MIL-STD-2525D](https://www.jcs.mil/Portals/36/Documents/Doctrine/Other_Pubs/ms_2525d.pdf) - Official standard document
- [MIL-STD-2525E](https://www.jcs.mil/Portals/36/Documents/Doctrine/Other_Pubs/ms_2525e.pdf) - Latest version

### Ports and Related Projects

- [Java](https://github.com/missioncommand/mil-sym-java) - This library
- [Android](https://github.com/missioncommand/mil-sym-android) - Android implementation
- [TypeScript](https://github.com/missioncommand/mil-sym-ts) - TypeScript/JavaScript version

### External Libraries

- [jsvg](https://github.com/weisJ/jsvg) 1.6.1 - SVG rendering ([MIT License](https://github.com/weisJ/jsvg/blob/master/LICENSE))
- [Geodesy](https://github.com/mgavaghan/geodesy) 1.1.3 - Geographic calculations ([Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0))

## Advanced Configuration

### Renderer Settings

```java
import armyc2.c5isr.renderer.utilities.RendererSettings;

RendererSettings settings = RendererSettings.getInstance();

// Enable/disable symbol caching for performance
settings.setCacheEnabled(true);

// Set label font
settings.setLabelFont("Arial", Font.BOLD, 14);

// Set text background rendering method
settings.setTextBackgroundMethod(
    RendererSettings.TextBackgroundMethod_COLORFILL
);

// Set default icon scaling
settings.setScaleMainIcon(true);

// Set outline width for tactical graphics
settings.setOutlineSPControlMeasures(true);
```

### Performance Optimization

The library includes caching mechanisms to improve rendering performance:

```java
// Caching is enabled by default
RendererSettings.getInstance().setCacheEnabled(true);

// Clear cache if needed (e.g., after memory pressure)
// Note: Cache clearing methods are internal
```

## License

This project is licensed under the Apache License 2.0 - see the LICENSE file for details.

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues on GitHub.

## Support

For questions, issues, or feature requests:
- Open an issue on [GitHub](https://github.com/missioncommand/mil-sym-java/issues)
- Check the [Wiki](https://github.com/missioncommand/mil-sym-java/wiki) for additional documentation

---

**Note**: This library is maintained by the US Army Mission Command community and represents years of battle-tested code used in operational military systems.
