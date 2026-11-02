# Op-amp active low-pass filters

Two active filters from ECEN 214 (Electric Circuit Theory, Texas A&M, spring 2026). My lab partner, Andres Gumucio, and I designed them to spec, simulated them in LTspice, then built them on a breadboard and measured them on an oscilloscope. Both had to hit a 1 kHz target using only the resistors and capacitors in our lab kit, so most of the design work was finding series and parallel combinations that landed close enough.

I'm heading toward power engineering, where this kind of circuit shows up all the time. Low-pass filters like these sit in the voltage and current sensing paths of converters and motor drives to clean up switching noise before the controller reads the signal. The other habit this lab built, checking a simulation against real hardware and explaining the gap, carries over to any power work.

## Second-order Sallen-Key filter

The spec was a unity-gain Sallen-Key low-pass with a natural frequency of 1 kHz and a damping ratio of 0.5, so the step response should overshoot and ring briefly before settling.

For this topology the natural frequency and damping are set by

$$\omega_0 = \frac{1}{\sqrt{R_1 R_2 C_1 C_2}} \qquad \alpha = \frac{R_1 + R_2}{2 R_1 R_2 C_1} \qquad \zeta = \frac{\alpha}{\omega_0}$$

We fixed the capacitors at 100 nF and 10 nF, then solved for the resistors. The ideal values weren't in the kit, so R1 is 2.2 kΩ in parallel with 10 kΩ (1.8 kΩ) and R2 is 10 kΩ + 2 kΩ + 2 kΩ in series (14 kΩ). That gives f0 = 1003 Hz and ζ = 0.498.

With a 0 to 2 V square wave at the input:

| | Predicted | Measured | Error |
|---|---|---|---|
| Peak voltage | 2.33 V | 2.37 V | 1.7% |
| Time to peak | 0.575 ms | 0.515 ms | 10.4% |
| Damping factor α | 3130 s⁻¹ | 3277 s⁻¹ | 4.7% |
| Damped frequency ωd | 5460 rad/s | 6100 rad/s | 11.7% |

The overshoot came out almost exactly as designed, but the circuit rang faster than predicted. The most likely cause is capacitor tolerance. The ceramic caps in the kit are rated anywhere from ±10% to ±20%, and ω0 goes as one over the square root of C1·C2, so caps running a little low push the oscillation frequency up. The damping factor only depends on C1, which is consistent with it having the smaller error.

## First-order filter and frequency response

The second circuit is an RC low-pass buffered by a voltage follower, so H(s) = ω0 / (s + ω0) with ω0 = 2π 1 kHz. In LTspice I used 10 kΩ and 16 nF (fc = 995 Hz). On the breadboard we used 3.3 kΩ and 39 nF in parallel with 10 nF (fc = 984 Hz), because those were the parts we had.

We swept the input from 100 Hz to 10 kHz and measured gain and phase at each point. At 1 kHz the measured gain was -3.28 dB and the phase was -43.9°, against a predicted -3.01 dB and -45°. The measured points follow the simulation closely across the whole sweep.

## What didn't work

We also cascaded the stages into second- and third-order filters, and those measurements are not shown here because they weren't usable. Past a few kHz the output got small enough to disappear into the oscilloscope's noise floor. We read -20 dB at 10 kHz on the third-order filter, where theory says -60 dB. Next time I would raise the input amplitude, use the scope's averaging mode, or measure with a spectrum analyzer.

## Files

`ltspice/` has both circuits. Open them in LTspice and run the `.tran` or `.ac` directive. The Sallen-Key file uses the as-built resistor values.
