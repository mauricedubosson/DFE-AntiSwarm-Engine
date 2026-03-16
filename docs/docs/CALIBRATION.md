markdown
# Field Calibration & Environmental Tuning

The **Dubosson-Feynman Engine (DFE)** is self-learning, but ballistic accuracy over 800m requires environmental compensation. Use this guide to tune the `InterceptionEngine` parameters.

## 1. Wind Compensation (The Crosswind Law)
Crosswind is the primary cause of missed shots. The DFE can extract the wind drift law by observing the first burst's impact (via radar/thermal feedback).

- **Parameter:** `wind_vector` (Vector3)
- **Physics:** $Drift \approx K \cdot V_{wind} \cdot t_{flight}^2$
- **Tuning:** If shots fall to the left, increment the `wind_x` variable in the `predict_lead_point` function.

## 2. Air Density (Altitude & Temperature)
Air resistance (Drag) changes with altitude. A bullet flies faster and further in thin, hot air.


| Condition | Adjustment | Effect on $V_{bullet}$ |
| :--- | :--- | :--- |
| **High Altitude (>1500m)** | Increase `v_bullet` by 2% | Less drag, flatter trajectory. |
| **Extreme Heat (>35°C)** | Increase `v_bullet` by 1% | Lower air density. |
| **Extreme Cold (<0°C)** | Decrease `v_bullet` by 2% | Higher air density, more drop. |

## 3. Sensor Alignment (Zeroing)
To ensure the **Dubosson Regulator** and the **Machine Gun** are perfectly synced:
1. **Static Zero:** Aim the gun at a fixed point 500m away.
2. **Sensor Check:** Ensure the Radar/Acoustic center aligns with the optical reticle.
3. **Dynamic Test:** Fire a 3-round burst at a fixed practice target. If the `FeynmanExplorer` shows a constant offset, apply the `STATIC_OFFSET` constant in the code.

## 4. The "Shock" Sensitivity
In the `DubossonRegulator`, the `persistence` parameter (default: 0.85) controls how much the system "trusts" previous data.
- **High Persistence (0.9+):** Best for straight-flying Shaheds (High stability).
- **Low Persistence (<0.7):** Best for agile FPV drones (High reactivity to maneuvers).
