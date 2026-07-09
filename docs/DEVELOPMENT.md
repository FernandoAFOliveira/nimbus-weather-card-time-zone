SIRGON GLOBE
Author: Fernando Fonteles Oliveira (Sirgon Corp)

Date: July 2026

Technical Engineering Report & Change Ledger: Project Sirgon Globe
Executive Summary
This document provides a comprehensive technical overview of the development, rebranding, and architectural optimization of the Sirgon Globe custom Home Assistant dashboard card. Originally forked from the legacy nimbus-weather-card codebase, the project was established to decouple card-level time tracking from integration attributes. This implementation introduces a multi-tiered, fault-tolerant worldclock synchronization engine optimized for distributed family environments across multiple global time zones.

Technical Progress & Milestone Ledger
Phase 1: Global Namespace Sanitation & Asset Independence
The initial phase focused on decoupling the codebase from the old nimbus infrastructure to establish a completely isolated, independent custom card entity.

Namespace Migration: Performed a global case-sensitive refactoring pass across sirgon-globe.js to change all instances of nimbus to sirgon. This updated all internal CSS class selectors (e.g., .sirgon-drop), container IDs (e.g., #sirgon-cloud-filters), data attributes (e.g., data-sirgon-runtime-cloud), and top-level execution scope flags (e.g., window.sirgonUseMobileCloud).

XML Namespace Protection: Maintained the strict integrity of the W3C immutable XML namespace identifier within the dynamic vector graphics initialization loop:

JavaScript
const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
This identifier was preserved without modification, ensuring the browser layout engine correctly switches to vector layout parsing for clouds, fog, and lightning filter elements.

Phase 2: Automated Deployment & Continuous Integration (CI)
To eliminate manual file updating and provide streamlined updates via the Home Assistant Community Store (HACS), an automated release pipeline was introduced.

Static Class Exposure: Implemented a static getter block within the main class tree to surface runtime software versioning directly to the Home Assistant core engine:

JavaScript
static get version() {
  return 'v1.1.0';
}
*   **GitHub Actions Automation:** Designed and deployed a compilation workflow configuration file located at `.github/workflows/release.yml`. When a new release tag is created on GitHub, this workflow automatically checks out the codebase, extracts the tag string, updates files via `sed` stream-editing, and appends the final distribution files directly to the release notes:
    ```yaml
    name: "Publish Release"
    on:
      release:
        types: [published]
    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v4
          - name: Inject Version Tag
            run: |
              TAG_VERSION="${{ github.event.release.tag_name }}"
              sed -i "s/v1.0.0/${TAG_VERSION}/g" sirgon-globe.js
Phase 3: UI Editor State Bridging & Dynamic Entity Filtering
This phase addressed data visibility limitations within the visual UI card configurator panel and mitigated entity selection noise.

Editor Context Resolution: Resolved an issue where the Local Time Entity (Worldclock) selection dropdown menu appeared empty. This occurred because the Lovelace card editor class isolates its runtime state inside a protected sub-object (this._hass) rather than the standard instance scope (this.hass). The array filter was updated to safely evaluate both scopes:

JavaScript
const hassObj = this.hass || this._hass;
*   **Universal String Sniffer:** To prevent the entity selection dropdown from being flooded with hundreds of irrelevant system entities (e.g., backup logs, battery states, browser-mod elements), a universal regular expression sniffer was created. This filter screens for native `time.` platform domains, clock iconography, or entities emitting real-time matching clock text states (`HH:MM`), without hardcoding specific entity names:
    ```javascript
    if (e.startsWith('sensor.')) {
      const lowerE = e.toLowerCase();
      if (lowerE.includes('browser_mod') || lowerE.includes('backup') || lowerE.includes('battery')) return false;
      
      const stateStr = String(stateObj?.state || '');
      const timeRegex = /^\d{1,2}:\d{2}$/; 
      if (timeRegex.test(stateStr)) return true;
    }
Phase 4: Lifecycle Synchronization & State Persistence Overhaul
The final phase resolved a critical synchronization bug where all cards defaulted to the local browser timezone (Orlando) instead of honoring their uniquely assigned Worldclock entities (Glasgow, Porto, Sao Paulo, Utah).

[HA Card Configuration Panel]  ---> Saves data-key="local_time"
                                         |
                                         v
[Lifecycle setConfig(config)]  ---> BEFORE: Dropped 'local_time' out of schema
                               ---> AFTER: Preserves 'local_time' & forces use_local_timezone = false
                                         |
                                         v
[Clock Loop _clockParts()]      ---> Parses raw string (e.g. "14:45") via Regex
                               ---> Splices hours/minutes into local Date object
                               ---> Renders unique local card time successfully
Lifecycle Data Persistence: Fixed a property dropout bug inside the setConfig(config) initialization method. The original function failed to copy the local_time token string down to the internal instance schema (this._config). The configuration mapping was rewritten to explicitly capture the property and establish an automatic mutual exclusivity override:

JavaScript
this._config = {
  ...config,
  entity: config.entity,
  local_time: config.local_time || '',
  use_local_timezone: config.local_time ? false : (config.use_local_timezone !== false),
  timezone_offset: parseFloat(config.timezone_offset) || 0,
  sources
};
Clock Parsing Engine Overhaul: Overhauled the _clockParts() time generation loop. Because native worldclock integration entities return flat text face strings (e.g., "14:45") rather than complex objects with a time_zone attribute, the original code encountered silent unhandled evaluation failures and fell back to system defaults. The parsing engine was updated to securely split the raw text state and manually adjust the internal date object hours and minutes at every clock tick:

JavaScript
if (this._config?.local_time && this.hass && this.hass.states[this._config.local_time]) {
  const timeEntityState = this.hass.states[this._config.local_time];
  if (timeEntityState && timeEntityState.state && timeEntityState.state !== 'unknown' && timeEntityState.state !== 'unavailable') {
    const stateStr = String(timeEntityState.state);

    if (/^\d{1,2}:\d{2}$/.test(stateStr)) {
      timeStringOverride = stateStr;
      fallbackToWeather = false;

      const [hours, minutes] = stateStr.split(':').map(Number);
      now.setHours(hours);
      now.setMinutes(minutes);
    }
  }
}
Error Prevention with Optional Chaining: Added robust optional chaining guards (this.hass?.states?.[...]) across the remaining clock execution paths. This prevents the card from throwing null-pointer exceptions and crashing the user dashboard during background integration reloads or network drops.

Current Status & Verification
With the release of version v1.1.0, all architectural objectives have been successfully met. The card functions as a fully modular, independent entity within the Home Assistant environment. Testing confirms that when multiple instances of the card are deployed in a grid, each card reads its specific Worldclock configuration payload independently, overrides local system processing, and accurately calculates and displays different local times concurrently across all configured global zones.

## Version History

### v1.1.0

- First official Sirgon Globe milestone release
- Native Home Assistant WorldClock integration
- Multiple independent clocks
- Browser-independent time rendering
- Automatic daylight saving support
- Complete project rebranding
- New documentation
- HACS preparation