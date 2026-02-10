# Sky Culture Lite: Archaeoastronomy Computation Engine

## Project Context: The Lens vs. The Telescope

During the development of my Final Year Project, I realized a fundamental inefficiency in how we simulate ancient skies. Instead of building a new "telescope" (simulation engine) every time I needed to switch between Mayan, Chinese, or Egyptian contexts, it made more sense to simply change the "lens."

**Sky Culture Lite** is that lens.

It is a specialized Model Context Protocol (MCP) server that abstracts away the complexity of cultural astronomy. An AI agent can simply ask about a cultural object (like "Chak Ek" or "Yinghuo") and this engine handles the heavy lifting of orbital mechanics, calendar conversion, and coordinate transformation.

This modular approach ensures that the core simulation remains stable while the cultural context can be swapped dynamically.

## Scientific Basis

This engine is built upon rigorous astronomical standards:

*   **JPL DE421 Ephemeris**: Uses NASA's Jet Propulsion Laboratory Development Ephemeris for high-precision planetary positions (1900 BC to 2050 AD).
*   **IAU WGSN Standards**: Adheres to the naming conventions validated by the International Astronomical Union Working Group on Star Names.
    *   Reference: [IAU WGSN Bulletin](https://www.pas.rochester.edu/~emamajek/WGSN/WGSN_bulletin1.pdf)

## Technical Architecture

*   **Language**: Python 3.11
*   **Protocol**: Model Context Protocol (MCP)
*   **Physics Backend**: Skyfield (State-independent vector astrometry)
*   **Time Standards**: Barycentric Dynamical Time (TDB) for ephemeris lookups.

## Capabilities

### Multi-Calendar Support
The engine includes a custom temporal broker capable of parsing non-standard historical date formats:
*   **Mayan Long Count**: Accepts `M:baktun,katun,tun,winal,kin` (e.g., `M:9,10,0,0,0`).
*   **Julian Calendar**: Accepts `J:year,month,day` for dates prior to the Gregorian reform (e.g., `J:600,1,1`).

### Coordinate Computation
Transforms cultural observations into standard J2000 Right Ascension and Declination frames, verifying against the DE421 planetary model.

## Usage

### 1. Docker (Recommended)

Build the container to isolate the specific Python dependencies and the ephemeris data.

```bash
docker build -t sky-culture-lite .
docker run -i --rm sky-culture-lite
```

### 2. Local Execution

```bash
pip install -r requirements.txt
python server.py
```

## MCP Tools

The server exposes the following tools to the specific Agent environment:

### `convert_culture_to_coordinates`

The primary interface for the "lens." It accepts a cultural description and returns scientific coordinates.

**Arguments:**
*   `culture_id`: The identifier for the cultural context (e.g., "mayan", "chinese_han").
*   `object_name`: The specific cultural name of the object (e.g., "chak_ek").
*   `date_str`: The historical date string.

**Example Agent Call:**

```python
convert_culture_to_coordinates(
    culture_id="mayan",
    object_name="chak_ek",
    date_str="M:9,10,0,0,0"
)
```

**Return Value:**
A structured text block containing the computed Julian Day, UTC time, and J2000 RA/Dec coordinates derived from the DE421 ephemeris.

### `list_cultures`

Returns the catalog of currently loaded cultural contexts available for simulation.
