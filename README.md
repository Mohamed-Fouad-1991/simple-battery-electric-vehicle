# Simple BEV Longitudinal Model (Simulink)

A minimal battery‑electric vehicle (BEV) longitudinal dynamics model in Simulink. It computes vehicle speed from traction minus resistive forces and integrates acceleration in discrete time. The model is intended for learning, quick benchmarking, and as a starting point for more detailed powertrain studies.

## Features

* **Traction force** from motor torque at the wheel: `F_trac = T_wheel / r_wheel`
* **Resistive forces:**
   * Aerodynamic drag: `F_drag = 0.5·ρ·Cd·A·v_rel·|v_rel|`, with `v_rel = v − v_wind`
   * Rolling resistance: `F_rr = Crr·m·g·cos(θ)`
   * Grade: `F_grade = m·g·sin(θ)`
* **Net force and dynamics:**
   * `F_net = F_trac − (F_drag + F_rr + F_grade)`
   * `a = F_net / m`
   * `v[k+1] = v[k] + a[k]·Ts` (Discrete‑Time Integrator)
* Single sample time for the whole model (fixed step)
* No gearbox (can be added later), optional regen via negative torque

## Files

* `BEV.slx` - Main Simulink model file
* `BEV.slxc` - Compiled model cache file  
* `slprj/` - Simulink project files and cache
* `README.md` - This documentation

## Requirements

* MATLAB/Simulink R2025a or later (earlier versions may work)
* Simulink toolbox
* Fixed‑step discrete solver (e.g., Ts = 1e−3 s)

## Physics & Equations

The model implements fundamental longitudinal vehicle dynamics:

* `v_rel = v − v_wind`
* `F_drag = 0.5·ρ·Cd·A·v_rel·|v_rel|`
* `F_rr = Crr·m·g·cos(θ)`
* `F_grade = m·g·sin(θ)`
* `F_trac = T_wheel / r_wheel`
* `F_net = F_trac − (F_drag + F_rr + F_grade)`
* `a = F_net / m`
* `v[k+1] = v[k] + a[k]·Ts`

**Sign convention:** Forward motion is positive; tailwind is positive `v_wind` (reduces drag); resistive forces are subtracted in the net force sum.

## Default Parameters

* `m = 1500 kg` (vehicle mass)
* `r_wheel = 0.32 m` (wheel radius)
* `Cd = 0.28`, `A = 2.2 m²` (drag coefficient and frontal area)
* `ρ = 1.225 kg/m³` (air density)
* `Crr = 0.008` (rolling resistance coefficient)
* `g = 9.81 m/s²` (gravitational acceleration)
* `θ = 0 deg`, `v_wind = 0 m/s` (flat road, no wind)
* `T_max = 300 N·m` (motor torque limit, no gear reduction)
* `Ts = 0.001 s` (sample time)

With these values on flat road, the steady top speed is about **46.6 m/s** because `F_trac,max ≈ 300/0.32 ≈ 938 N` equals `F_rr + F_drag(v)`.

## Quick Start

1. **Open MATLAB** and navigate to the repository folder
2. **Open the model:**
   ```matlab
   open('BEV.slx')
   ```
3. **Run simulation** from Simulink toolbar or:
   ```matlab
   sim('BEV.slx')
   ```
4. **Examine results:** velocity rises and asymptotes near 46.6 m/s; at that speed `a → 0` and `F_net → 0`

## Customization

* **Increase top speed:** Raise torque limit, add fixed gear ratio `G` and driveline efficiency `η` (`F_trac = η·G·T_motor/r`), or reduce `Cd/A/Crr`
* **Add regeneration:** Allow negative torque saturation and include in traction computation
* **Different scenarios:** Change `θ` (grade) and `v_wind` (head/tailwind)
* **Realistic motor:** Use torque–speed or constant‑power limit above base speed

## Model Outputs

The simulation provides:
* Vehicle speed `v` (m/s)
* Acceleration `a` (m/s²)
* Forces: `F_trac`, `F_drag`, `F_rr`, `F_grade`, `F_net` (N)
* Motor torque and power

## Known Limitations

* No gearbox by default; no detailed motor or battery model
* No thermal, tire slip, or lateral dynamics  
* Single‑track, straight‑line motion only
* Simplified aerodynamics and rolling resistance

## Contributing

Feel free to fork this repository and submit pull requests for improvements such as:
* Adding battery state-of-charge modeling
* Including thermal effects
* Implementing more sophisticated motor characteristics
* Adding different drive cycles

## License

This project is open source and available under the MIT License.
