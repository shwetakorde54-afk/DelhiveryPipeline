# DelhiveryPipeline
Capstone Project — Delhivery Last-Mile Delay Intelligence Pipeline

Part A - Data findings
(i) Express is delayed most often, at a rate of 0.4897 (119 of 243 shipments).

(ii) The delay rate rises from 0.0400 on Tier 1 hubs to 0.4400 on Tier 3 hubs.

(iii) The delay rate rises across distance bands: 0-200 at 0.0219, 201-400 at 0.1368, 401-600 at 0.4364, 601+ at 0.8523.

(iv) The three columns most strongly correlated with delivery_hours are promised_hours (0.8372), distance_km (0.4013), n_stops (0.3247).

(v) On the filled declared_value_inr column the mean is 1533.05 and the median is 1121.5, so the mean sits above the median. After the natural log the mean is 7.028 and the median is 7.022, which are nearly the same, because the log compresses the long right tail.

Part B - Findings
model_1 is the scaler-plus-linear pipeline, and every model is fit on X_train and scored on y_val. The test row is the model with the lowest validation RMSE, scored once on X_test.
