# Canvas Signature Bug Tests

Debug test suite for investigating blank signature issues in web applications using HTML5 Canvas, specifically targeting Windows Firefox 145/146.

## The Bug

Users on Windows Firefox 145/146 are submitting blank/empty signatures. Symptoms include:
- Image dimensions preserved but data URI varies in length
- For text placeholders: height can be 1px or 5px (incorrect)
- Canvas appears to be blanked or filled with transparent pixels

## Running the Tests

### Quick Start

```bash
cd canvas-tests
python3 -m http.server 8080
# Open http://localhost:8080
```

Or visit the hosted version: https://salamaashoush.github.io/canvas-tests/

### Testing with Limited Firefox Resources (macOS)

1. Create a low-memory Firefox profile:
   - Open Firefox and go to `about:profiles`
   - Click "Create a New Profile" and name it "LowMem"

2. Configure the profile in `about:config`:
   ```
   gfx.canvas.accelerated = false
   layers.acceleration.disabled = true
   javascript.options.mem.max = 524288000
   gfx.webrender.all = false
   ```

3. Launch Firefox with the profile:
   ```bash
   # With limited memory profile
   /Applications/Firefox.app/Contents/MacOS/firefox -P LowMem

   # With GPU disabled
   MOZ_DISABLE_GPU=1 /Applications/Firefox.app/Contents/MacOS/firefox
   ```

4. Monitor memory in Activity Monitor

### Testing with Limited Firefox Resources (Windows)

1. Create the profile as above via `about:profiles`

2. Configure `about:config` settings as above

3. Launch Firefox:
   ```cmd
   set MOZ_DISABLE_GPU=1
   "C:\Program Files\Mozilla Firefox\firefox.exe" -P LowMem
   ```

4. Monitor memory in Task Manager

## Test Files

| Test | Description |
|------|-------------|
| 01-font-metrics-zero.html | Tests if `actualBoundingBox*` metrics can return 0 |
| 02-font-loading-race.html | Tests font loading race conditions |
| 03-canvas-clear-behavior.html | Tests canvas clear behavior on resize |
| 04-font-size-loop-zero.html | Tests font-sizing algorithm edge cases |
| 05-toDataURL-timing.html | Tests timing issues with `toDataURL()` |
| 06-firefox-specific.html | Firefox-specific: DPR, f32 precision, fingerprinting |
| 07-complete-signature-flow.html | End-to-end signature workflow simulation |
| 08-edge-cases.html | Context loss, size limits, transforms, privacy |
| 09-memory-pressure-pdf.html | Real PDF loading with PDF.js under memory pressure |

## Hypotheses Being Tested

- Font metrics returning 0 dimensions
- Font not loaded when measured
- Missing clearRect before draw
- Font size loop reaching 0
- toDataURL timing with forceRedraw
- Firefox f32 precision issues
- Fingerprint resistance
- Canvas context null/lost
- willReadFrequently hint
- Canvas size limits exceeded
- Transform state issues
- Cross-origin canvas tainting
- GPU/hardware acceleration
- Memory pressure with PDF rendering

## How to Interpret Results

- **PASS** (green): Test passed, this is not the bug cause
- **FAIL** (red): Test failed, this could be the bug cause
- **WARNING** (yellow): Potential issue detected, needs investigation

## Browser Compatibility

Tests should be run on:
- Firefox 145/146 on Windows (target bug environment)
- Chrome on macOS (control)
- Safari on macOS (control)
- Firefox on macOS (comparison)
