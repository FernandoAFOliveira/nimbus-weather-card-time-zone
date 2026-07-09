SIRGON GLOBE (FORK)
Project Modifications & Refactoring Documentation
Author: Fernando Fonteles Oliveira (Sirgon Corp)

Date: July 2026

Base Software: Nimbus Weather Card Time Zone (v2.4.1) by Gerasimos Fokaefs (MIT License)

Target Element Type: custom:sirgon-globe

Executive Summary
This document serves as an explicit, high-signal technical breakdown of the architectural overhauls, core stability enhancements, and front-end interface adjustments made to the original Nimbus Weather Card Time Zone project.

The primary driver behind this fork is the implementation of a native, robust Worldclock Sync Engine. The original codebase relied exclusively on time attributes provided by varying weather integrations. These variables frequently drop out, lack standardized Daylight Saving Time (DST) tracking, or create layout breaking conflicts when displaying multiple cross-continental urban zones.

This fork decouples time zone tracking from weather attributes, introducing discrete entity bindings, multi-layered runtime safety guards, and an entirely rebranded, streamlined configuration experience under the Sirgon Globe umbrella.

1. Global Time Synchronization Engine
The core logic responsible for managing and displaying the card's clock was entirely rewritten to transition from passive attribute-reading to a strict, priority-tiered hierarchy.

Primary Tier (User-Defined Custom Worldclock Sensor): Implemented an evaluation hook that targets a specified, root-level custom entity. The engine reads the direct state of the timestamp sensor.

Secondary Tier (Automated Weather Integration Fallback): If no specialized time entity is defined, or if the chosen sensor's state returns as unknown or unavailable (frequent during Home Assistant server reboots), the logic falls back to evaluating the core weather entity's internal timezone string attribute.

Tertiary Tier (System Clock & Offset Fallback): If all network-tied integration endpoints fail to resolve, the engine falls back to a clean browser/system new Date() calculation, applying any user-defined numerical timezone_offset hours. This prevents the card from displaying NaN:NaN or completely locking the Lovelace frontend dashboard.

2. Frontend Configuration UI Refactoring
To map out the global timezone engine effectively, the visual layout form (_render() class method) and data validation loop (const upd = () => { ... }) were completely restructured.

The Array Extraction Step
Original State: The UI editor lacked visibility into time-based resources.

Modification: Added a targeted extraction line filtering out Home Assistant's global registry to populate a timeEntities collection:
```
    const timeEntities = (this.hass && this.hass.states) 
    ? Object.keys(this.hass.states).filter(e => e.startsWith('sensor.') || e.startsWith('time.')).sort() 
    : [];
    ```
HTML Form Structural Relocation
Original State: Attempting to place the dropdown inside individual source tabs caused memory leaks, variable resets during multi-source re-sorting, and displayed unfiltered system registries.

Modification: Relocated the picker out of the array loop and into the core <!-- REQUIRED --> section block. It sits globally right beneath the primary Weather Entity row.

Implementation: Used a native <select> drop-down mapping structure populated solely by the verified timeEntities list to keep the interface highly focused and concise:
```
<div class="row">
  <div><div class="label">Local Time Entity (Worldclock)</div></div>
  <select id="local_time">
    ${!c.local_time ? `<option value="">— select entity —</option>` : ''}
    ${timeEntities.map(e => `<option value="${e}" ${c.local_time===e?'selected':''}>${e}</option>`).join('')}
  </select>
</div>
```
Configuration State Binding (upd)
Original State: Variable assignment loops inside the configuration dispatcher clashed with multi-source arrays.

Modification: Restructured const upd = () => { ... } to read the unique element ID (local_time) straight from the shadow root container and bind it directly to the root level of the card's permanent configuration file schema, ensuring persistent saves.

Code Clean-Up: Purged a hidden duplicate declaration of const localTimeValue further down in the function loop that was triggering Uncaught SyntaxError compiler crashes.

3. Fault Tolerance & Runtime Safety Guards
To protect the Lovelace UI from crashing due to the raw template string approach used by this component, robust safety wrappers were introduced to the initialization layer:

Asynchronous Context Verification: Wrapped timeEntities, weatherEntities, sensorEntities, and sunEntities in functional validation blocks (e.g., typeof this._weatherEntities === 'function' ? this._weatherEntities() : []). This guarantees that if the visual editor tries to render the interface before Home Assistant passes the runtime context down to the element, the loops fail gracefully to empty arrays ([]) instead of throwing an unhandled Cannot read properties of undefined (reading 'map') error.

4. Branding, Re-Registration & Package Deployment
The component was completely detached from its old namespace to enable independent operation and a clean footprint in HACS.

Custom Element Definition: Renamed the registration blocks at the foot of the file to establish the new custom element registry:

```
customElements.define('sirgon-globe', NimbusWeatherCard);
customElements.define('sirgon-globe-editor', NimbusWeatherCardEditor);
```
Configuration Mappings: Changed all target type references throughout the file body to point exclusively to custom:sirgon-globe.

Card Picker Discovery: Modified the internal window object push arrays to cleanly output Sirgon Globe with updated descriptive strings when searched inside the Home Assistant Card picker UI.

Package Architecture: Rewrote hacs.json to assign the proper frontend title, updated the project LICENSE to preserve original copyright attribution while introducing 2026 ownership markers, and replaced the README.md with shortened, branded documentation centered on the tags and worldclock enhancements.