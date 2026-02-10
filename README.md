# Sky Culture Lite: Archaeoastronomy Computation Engine

## The Philosophy: Changing the Lens, Not the Telescope

In my previous work translating the *Surya Siddhanta*, I spent months manually calculating planetary positions to verify ancient Sanskrit verses against modern ephemerides. It was a tedious, repetitive process of building bespoke simulations for a single cultural context.

When I moved on to Mayan and Chinese astronomy, I realized I was making the same mistake: I was building a new "telescope" (simulation engine) for every culture.

**Sky Culture Lite** represents a shift in architecture. Instead of rebuilding the telescope, I simply changed the lens.

This engine serves as a modular "Interchangeable Lens" for AI Agents. It abstracts the complexity of orbital mechanics and calendar systems into a standardized protocol. Whether the input is a Mayan Long Count date or a Han Dynasty star name, this microservice handles the physics, allowing the Agent to focus on the cultural interpretation.

## Technical Architecture

This module implements the **Model Context Protocol (MCP)** to expose high-precision astronomical functions to Large Language Models (LLMs) like Claude.

*   **Ephemeris**: NASA JPL DE421 (Covers 1900 BC to 2050 AD).
*   **Physics Engine**: Skyfield (Vector astrometry).
*   **Time Standard**: Barycentric Dynamical Time (TDB).
*   **Standards Compliance**: IAU Working Group on Star Names (WGSN).

## Integration Guide: Claude Desktop

To use this engine as a "Lens" for Claude, you must register it in your `claude_desktop_config.json`.

**Prerequisite**: You must have Docker installed and running.

1.  Locate your configuration file:
    *   **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
    *   **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`

2.  Add the `sky-culture-lite` entry to the `mcpServers` object:

```json
{
  "mcpServers": {
    "sky-culture-lite": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "nishanthabimanyu/sky-culture-lite"
      ]
    }
  }
}
```

*Note: Replace `nishanthabimanyu/sky-culture-lite` with your local image tag if you haven't pushed to Docker Hub yet (e.g., `sky-culture-lite`).*

## API Reference (MCP Tools)

### `convert_culture_to_coordinates`

This is the primary lens function. It takes a cultural description and resolves it to a J2000 epoch and coordinate frame.

**Parameters:**
*   `culture_id` (str): The cultural context key (e.g., "mayan", "chinese_han").
*   `object_name` (str): The native name of the celestial body (e.g., "chak_ek").
*   `date_str` (str): The historical date.
    *   **Mayan**: `M:baktun,katun,tun,winal,kin` (e.g., `M:9,10,0,0,0`)
    *   **Julian**: `J:year,month,day` (e.g., `J:600,1,1`)

**Return Payload:**
Returns a structured text block containing:
1.  **Julian Day Number (JD)**: For rigorous time verification.
2.  **UTC ISO Timestamp**: For standard cross-referencing.
3.  **J2000 RA/Dec**: Astrometric coordinates aligned with the ICRF.

## References

*   **NASA Jet Propulsion Laboratory**: Development Ephemerides (DE421)
*   **International Astronomical Union**: [WGSN Bulletin (Star Names)](https://www.pas.rochester.edu/~emamajek/WGSN/WGSN_bulletin1.pdf)
