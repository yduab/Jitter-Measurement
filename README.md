diff --git a/README.md b/README.md
new file mode 100644
index 0000000000000000000000000000000000000000..d79391d31b6e8873e5610ebceb7f618caa2f6251
--- /dev/null
+++ b/README.md
@@ -0,0 +1,79 @@
+# Jitter Measurement for Sonic Interaction Design
+
+This repository contains an MVP concept for a **live musical jitter monitor**:
+
+- capture live audio from one or more musicians
+- estimate timing jitter in short windows
+- provide immediate feedback (beep or click) when jitter crosses a threshold
+- optionally log jitter events for post-session analysis
+
+## What "jitter" means here
+
+In this project, jitter is short-term instability in event timing (for example, uneven onset-to-onset intervals while playing).
+
+A practical way to estimate it in real time:
+
+1. detect note/transient onsets
+2. compute inter-onset intervals (IOI)
+3. calculate variability (standard deviation or robust MAD) over a rolling window
+4. trigger feedback when variability exceeds a threshold
+
+## MVP architecture
+
+1. **Audio input** (microphone, pickup, or direct line)
+2. **Onset detector** (energy + spectral flux)
+3. **Jitter estimator** (rolling IOI variance)
+4. **Feedback engine** (short click/beep)
+5. **Recorder/logger** (store audio + time-stamped jitter events)
+
+## Suggested feedback design for musicians
+
+- use a short, unobtrusive sound (e.g., 1–2 kHz click, 20–40 ms)
+- gate repeats (cooldown 150–300 ms) to avoid overwhelming the performer
+- use levels that are clearly audible but not disruptive
+- optionally map severity to beep intensity or pitch
+
+## Multi-player mode
+
+If two musicians play simultaneously, use either:
+
+- separate inputs (best) and calculate per-player jitter, or
+- one mixed input plus source separation (harder, less reliable in real time)
+
+Start with two channels if possible, then compute:
+
+- individual jitter scores
+- optional ensemble synchrony error (difference between corresponding onsets)
+
+## Files in this repository
+
+- `prototype/live_jitter_monitor.py` — a small reference prototype showing rolling IOI jitter and beep feedback.
+
+## Next steps
+
+- calibrate threshold per instrument/player
+- add GUI meter (green/yellow/red)
+- record sessions and analyze threshold hit rate
+- run user tests with musicians to tune feedback character
+
+
+## Quick run
+
+Because live mode needs local audio dependencies/devices, you can validate behavior using simulation:
+
+```bash
+python prototype/live_jitter_monitor.py --simulate --duration 10 --tempo-bpm 120 --jitter-threshold-ms 10 --csv-log logs/rehearsal_events.csv
+```
+
+For real microphone/instrument input (live mode):
+
+```bash
+pip install numpy sounddevice
+python prototype/live_jitter_monitor.py --live
+```
+
+
+CSV output columns:
+- `timestamp_utc`
+- `jitter_ms`
+- `alert` (1 when alert/beep condition is triggered, otherwise 0)
