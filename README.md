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

## Model results (regression)

The mean baseline R² on the validation rows is -0.0212. That score comes from ignoring every feature and predicting the training-set average delivery time. An R² of 0 would mean this guess is only as good as predicting the validation average; a value near or below 0 means the average has no useful skill.

linear_regression won on validation with an RMSE of 4.8912 hours.

Depths 1 (train 11.6334, val 12.1324, gap 0.499), 2 (train 9.3687, val 10.4114, gap 1.0427), 3 (train 7.5295, val 8.7033, gap 1.1738), 4 (train 6.0999, val 7.3896, gap 1.2897) are high-bias: the training error stays high and the gap to validation is small, so the tree is too shallow to learn the pattern. Depths 8 (train 3.336, val 6.7447, gap 3.4087), 12 (train 1.3613, val 7.2691, gap 5.9078), unlimited (train 0.0, val 7.7734, gap 7.7734) are high-variance: the training error falls well below the validation error, so the tree is memorizing the training rows.

The best learning rate is 0.1 with a validation RMSE of 4.9173. 0.01 (val RMSE 10.0018) is too slow: 100 trees each take a tiny step, so the model stops short. 1.0 (val RMSE 7.4352) overshoots: each tree corrects too hard and the fit becomes unstable.

Cross-validation prefers gradient_boosting (mean R² 0.9352 ± 0.0045), while the single validation split prefers linear_regression. The model to ship is gradient_boosting, because five shared folds on 1,020 rows are a steadier check than one 180-row split. The test row still records linear_regression, which is the model with the lowest validation RMSE.

## Model results (classification)

The blind baseline accuracy is 0.7667. It predicts every parcel on time, so its recall is 0.0 and its precision is 0.0. The accuracy is only the share of parcels that were already on time. It never raises an alert, so it is useless as a delay detector.

The five noise columns together hold 0.2292 of the forest importance: label_print_batch (F-rank 8, importance 0.0463158085432208), scanner_build_no (F-rank 17, importance 0.0360147090588975), tape_roll_no (F-rank 12, importance 0.0532032878092112), sticker_serial_mod (F-rank 14, importance 0.0474762016112013), shelf_slot_no (F-rank 16, importance 0.0461899369065911). All five sit outside the top 7 by F-score.

The k table is k=5 F1 0.6988, k=7 F1 0.8471, k=10 F1 0.7857, k=12 F1 0.7848, k=17 F1 0.8. The highest validation F1 in that table is 0.8471 at k=7.

The untuned forest's validation F1 is 0.8471 and the chosen row is default_forest_same_7_features at 0.8471. Tuning did not improve on the plain forest. A 180-row validation set can make a small search look better by chance, and here it did not.

The alert threshold is 0.5. On the validation rows its precision is 0.8372 and its recall is 0.8571. It is the highest threshold that still keeps recall at or above 0.80, so some on-time parcels are flagged in exchange for missing fewer delayed ones.

On the test rows, precision is 0.6818, recall is 0.7317, and F1 is 0.7059. Validation recall at this threshold was 0.8571. The test set has only 41 delayed parcels, so one parcel changes recall by about 0.0244. A gap of a few parcels between test and validation is what that small count does.
