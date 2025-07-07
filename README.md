# 🏫 ICT in Building Design – Energy Optimization and Prediction

**Optimizing, Simulating, and Predicting Energy Performance in a Smart School Building**

---

## 📘 Overview

This project focuses on **optimizing, simulating, and predicting the energy consumption of a school building in Turin**, leveraging advanced ICT technologies, simulation tools, and machine learning models. It integrates surrogate modeling, building energy simulation, IoT sensor emulation, real-time data storage, and predictive analytics into a cohesive workflow. The aim is to enhance energy efficiency, reduce consumption, and support smart building management in educational facilities.

---

## 🛠 Methodology Highlights 

✅ **1) Building Modeling & Energy Simulation**

* A 12-zone educational building was designed in **DesignBuilder**, including classrooms, circulation areas, and services, configured with glazing, internal gains, and HVAC schedules.
* The model was exported to an `.idf` file for detailed simulation in **EnergyPlus**, with **hourly weather data** from two `.epw` files:

  * Typical Meteorological Year (TMY)
  * Actual year 2012 weather
* Key outputs included hourly heating, cooling, and electricity demands (`eplusout2005.csv`, `eplusout2012.csv`).

✅ **2) Optimization with Surrogate Modeling & NSGA-II**

* Latin Hypercube Sampling (LHS) was used to efficiently generate 40 design alternatives by varying glazing type, SHGC, U-value, and window-to-wall ratio.
* Energy demands of these alternatives were simulated with EnergyPlus.
* An **Artificial Neural Network (ANN)** surrogate model was trained on the LHS dataset, using:

  * 80% for training, 20% for testing
  * ReLU activation
  * Adam optimizer
  * MSE loss function
* The surrogate was validated with a regression R² of 0.998 on training and 0.96 on testing.
* A **Non-dominated Sorting Genetic Algorithm II (NSGA-II)** was run on the surrogate model to optimize three objectives: minimizing heating, cooling, and total electricity demands simultaneously.
* Final Pareto-optimal solutions included a glazing SHGC of 0.4 and U-value of 2.1 W/m²K, leading to \~19% reduction in annual energy use.

✅ **3) EnergyPlus & Eppy Integration**

* The **eppy library** was used to read, modify, and run `.idf` and `.epw` files programmatically.
* Post-processing of simulation results included automated extraction of hourly energy demands into `.csv` files.
* Comparison of simulation outputs for different weather years was performed to assess building sensitivity to climate variations.

✅ **4) Sensors Emulation & IoT Data Management**

* Developed MQTT-based emulation of temperature sensors:

  * `1.Publisher.ipynb` generated synthetic temperature data based on hourly energy consumption.
  * `2.Subscriber.ipynb` received published messages to simulate data collection.
* Controlled simulation scenarios were executed in `Control_simulation.ipynb` and `Send_simulation.ipynb`.
* Sensor data were stored in **InfluxDB**, a time-series database optimized for high-resolution temporal data.
* Data could be visualized in **Grafana** dashboards (not included in repo) for near-real-time monitoring.

✅ **5) Energy Signature Analysis**

* Conducted linear regression of energy consumption against temperature difference ΔT (outdoor – indoor setpoint), deriving **energy signatures** for heating and cooling demands.
* Analysis was performed at different aggregation scales (hourly, daily, monthly) to evaluate sensitivity and linearity, revealing higher accuracy at monthly resolution (R² ≈ 0.82).
* Detected heating and cooling balance points to estimate building performance thresholds.

✅ **6) Predictive Modeling with LSTM Networks**

* Configured **Long Short-Term Memory (LSTM)** networks for time-series forecasting of heating and cooling demands.
* Architecture:

  * Input: Sliding window of previous 24 hours of energy demand
  * Layers: 1 LSTM (32 neurons) + Dense output layer
  * Hyperparameters tuned via grid search: batch size (16, 32), number of epochs (50–150), and number of lags (12, 24)
* Trained on normalized hourly data; inverse-transformed predictions to compare with actual demands.
* Evaluated performance with RMSE, showing high predictive accuracy within 5% relative error for short-term forecasts.

---

## 📁 Project Structure

```bash
ICT_in_Building_Design_Team_4/
├── 1.Optimization&Surrogate_Model/
│   ├── double_glaze.idf
│   ├── Optimization.ipynb
│   └── Torino_IT-hour.epw
│
├── 2.Energy_Simulation/
│   ├── double_glaze.idf
│   ├── eplusout2005.csv
│   ├── eplusout2012.csv
│   ├── EPPY_Best.ipynb
│   ├── ITA_PM_Torino.AF.160595_TMYx.epw
│   └── Torino_IT-hour.epw
│
├── 3.Sensors_emulation_&_InfluxDB/
│   ├── 1.Publisher.ipynb
│   ├── 2.Subscriber.ipynb
│   ├── Control_simulation.ipynb
│   └── Send_simulation.ipynb
│
├── 4.Energy_signature/
│   ├── Energy_Signature_Analysis.ipynb
│   ├── influx_eplusout2005.csv
│   └── influx_eplusout2012.csv
│
├── 5.Prediction/
│   ├── Prediction_Cooling.ipynb
│   └── Prediction_Heating.ipynb
│
├── e_school.dsb                       # DesignBuilder project file
├── Presentation.pptx                  # Project presentation        
├── README.md                       
└── Report.pdf                         # Full project report
```

---

## 🗝 Key Techniques

- **EnergyPlus + eppy**: Used for hourly energy simulations with `.idf` and `.epw` data.
- **Surrogate Modeling with ANN**: Trained on Latin Hypercube samples to capture nonlinear relationships in energy consumption.
- **Optimization (NSGA-II)**: Multi-objective evolutionary algorithm exploring parameter spaces for optimal energy profiles.
- **MQTT + InfluxDB + Grafana**: Real-time sensor data emulation, storage, and visualization in an IoT-like environment.
- **Energy Signature Analysis**: Applied OLS regression to quantify energy responsiveness to ΔT under different sampling rates.
- **LSTM Models**: Configured with sliding window approach; evaluated performance across varying prediction horizons.

---

## ⚙️ How to Run

1. **Set up Environment**
   Install dependencies:

   ```bash
   pip install numpy pandas matplotlib scikit-learn tensorflow eppy paho-mqtt influxdb-client
   ```

2. **Optimize & Simulate**

   * Run `Optimization.ipynb` to build the surrogate model.
   * Execute `EPPY_Best.ipynb` for EnergyPlus simulation and output processing.

3. **Emulate IoT Sensors**

   * Start `1.Publisher.ipynb` and `2.Subscriber.ipynb` to publish/subscribe sensor data.

4. **Energy Signature**

   * Analyze building energy signature using `Energy_Signature_Analysis.ipynb`.

5. **Prediction**

   * Run `Prediction_Cooling.ipynb` and `Prediction_Heating.ipynb` to forecast energy demands.

---

## 📊 Results Summary

| Module            | Highlights                                       |
| ----------------- | ------------------------------------------------ |
| Optimization      | Reduced total energy demand to 3.99e10 J         |
| Energy Signature  | Achieved R² up to 0.82 (monthly resolution)      |
| Prediction (LSTM) | RMSE \~1.3e6 J for heating & 1.5e6 J for cooling |