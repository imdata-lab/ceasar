# CEASAR: Joint Attention Construct Calculation

## Overview
The Shared View (SV) metric was developed to track the overlap of device screens, serving as a proxy for joint attention. By assessing the degree of field of view (FOV) overlap between two devices under specific conditions, the SV metric offers a quantitative representation of the shared visual attention.

## Conditions for Shared View
For an accurate calculation of the shared view between the paired devices, the following criteria must be satisfied:

- **Co-location**: Both devices are at the same location.
- **Scene Match**: Both have the same scene loaded.
- **Time Alignment**: They are at the same simulation time.

If any of these conditions are unmet, the overlap is considered to be 0, implying that the devices do not have a shared view.

## Calculation Process

1. **Device Heading**:
   Defined by two angles - vertical (up/down) and horizontal (left/right).
   
2. **Field of View**:
   Determined as a 60x60 degree area, representing the smallest observable field within the simulation.
   
<p align="center">
  <img width="350" src="https://github.com/imdata-lab/ceasar/assets/54631893/8b773f47-e2a6-42d5-b18f-0a548e2ebc17" alt="FOV">
</p>

3. **Reference Coordinates**:
   The top-left corner of this viewable field is ascertained by a 30-degree adjustment both vertically and horizontally. The simulation's spherical nature requires special consideration when determining distances between specific headings.

4. **Overlap Quantification**:
   Trigonometry is employed to compute the overlap between the views of the two devices. This value can span from 0 to 3600, the latter being the maximum possible overlap area.

<p align="center">
  <img width="550" src="https://github.com/imdata-lab/ceasar/assets/54631893/d31b33f9-f522-455c-9302-e41f1c590086" alt="SV Calculation">
 </p>
  
  ```
f(x1, x2, y1, y2) = {
(60 - |x1 - x2|) * (60 - |y1 - y2|) if |x1 - x2| ≤ 60 and |y1 - y2| ≤ 60,
0 otherwise
}
  ```

Where:
- `x1`, `y1`: The x and y coordinates of the first device's view.
- `x2`, `y2`: The x and y coordinates of the second device's view.

Formula Explanation:
- `|x1 - x2|`: The absolute horizontal difference between the two devices' views.
- `|y1 - y2|`: The absolute vertical difference between the two devices' views.
- If both `|x1 - x2|` and `|y1 - y2|` are within 60 degrees, we calculate the product of (60 - `|x1 - x2|`) and (60 - `|y1 - y2|`) to find the overlap area.
- If either absolute difference is greater than 60 degrees, the overlap is considered zero, indicating no shared view.


5. **SV Metric Calculation**:
   The overlap is then divided by 3600 to get the SV metric. Thus, the SV values fall between 0 (no FOV overlap) and 1 (entire FOV overlap).

## Transformation from SV to Joint Attention Features
The SV metric serves as the foundation for deducing Joint Attention features. While the SV metric quantifies screen overlap, the Joint Attention features categorize this overlap into distinct states or scenarios. The table below presents how specific overlap scenarios (determined by the SV metric) map to distinct Joint Attention features:

| Feature                                 | Description                                                                                |
|-----------------------------------------|--------------------------------------------------------------------------------------------|
| JA I: Inactivity                        | No events triggered within a 20-second timeframe.                                          |
| JA II: No Scene Overlapping             | Either students are exploring different scenes, or only one student is active.             |
| JA III: Scene Overlapping in Earth/Star | Both students are active in either the Earth or Star scene.                                |
| JA IV: No Scene Overlapping in Horizon  | Both students are in the Horizon scene without any screen overlap.                         |
| JA V: Scene Overlapping in Horizon      | An overlap in the Horizon scene is observed across the paired devices.                     |

_An illustration of JA V Scene Overlapping in the Horizon scene across three devices: MS HoloLens2 (top right) and tablets (bottom right and left)._
<p align="center">
  <img width="500" src="https://github.com/imdata-lab/ceasar/assets/54631893/f4333532-ede1-477b-adc6-9792a51fc167" alt="Example of JA V Scene Overlapping">
</p>

Link to specific code
