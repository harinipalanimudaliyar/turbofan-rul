# Remaining Useful Life Prediction on NASA Turbofan Data

Predicting how many operating cycles a jet engine has left before it
fails, using its sensor history. Work in progress (Phase 1 of 5 done).

## Dataset
NASA C-MAPSS turbofan engine degradation data (simulated, run-to-failure).
Currently using subset FD001: 100 training engines, 20,631 rows,
26 columns (engine ID, cycle, 3 operating settings, 21 sensors).
The data is not stored in this repo. Download it from the NASA
Prognostics Data Repository and place it in `data/`.

## Phase 1: Data exploration (done)
- No missing values. Engine lifetimes range from 128 to 362 cycles
  (mean about 206, median 199).
- Target: RUL = last cycle minus current cycle, capped at 125, because
  sensors show little wear early in an engine's life.
- 7 sensors are nearly constant (s1, s5, s6, s10, s16, s18, s19) and
  will be dropped.

EXAMPLE FLOW DONE:
### Loaded the data in csv format and checked its shape:
The obtained shape is (20631, 26)

### Arranged the given raw data into 26 columns and named it for convenience:
|index|engine|cycle|setting1|setting2|setting3|s1|s2|s3|s4|s5|s6|s7|s8|s9|s10|s11|s12|s13|s14|s15|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|1|1|-0\.0007|-0\.0004|100\.0|518\.67|641\.82|1589\.7|1400\.6|14\.62|21\.61|554\.36|2388\.06|9046\.19|1\.3|47\.47|521\.66|2388\.02|8138\.62|8\.4195|
|1|1|2|0\.0019|-0\.0003|100\.0|518\.67|642\.15|1591\.82|1403\.14|14\.62|21\.61|553\.75|2388\.04|9044\.07|1\.3|47\.49|522\.28|2388\.07|8131\.49|8\.4318|
|2|1|3|-0\.0043|0\.0003|100\.0|518\.67|642\.35|1587\.99|1404\.2|14\.62|21\.61|554\.26|2388\.08|9052\.94|1\.3|47\.27|522\.42|2388\.03|8133\.23|8\.4178|
|3|1|4|0\.0007|0\.0|100\.0|518\.67|642\.35|1582\.79|1401\.87|14\.62|21\.61|554\.45|2388\.11|9049\.48|1\.3|47\.13|522\.86|2388\.08|8133\.83|8\.3682|
|4|1|5|-0\.0019|-0\.0002|100\.0|518\.67|642\.37|1582\.85|1406\.22|14\.62|21\.61|554\.0|2388\.06|9055\.15|1\.3|47\.28|522\.19|2388\.04|8133\.8|8\.4294|

### Checked the health of data
Rows: 20631
Engines: 100
Missing values: 0
count    100.000000
mean     206.310000
std       46.342749
min      128.000000
25%      177.000000
50%      199.000000
75%      229.250000
max      362.000000
Name: cycle, dtype: float64

### The target: RUL
Each engine runs until it fails, so its last cycle is the failure point. 
RUL (Remaining Useful Life) = last cycle - current cycle.
The last 5 cycles of the ENGINE 01

|index|engine|cycle|RUL|RUL\_capped|
|---|---|---|---|---|
|187|1|188|4|4|
|188|1|189|3|3|
|189|1|190|2|2|
|190|1|191|1|1|
|191|1|192|0|0|

#### Why the cap: early in an engine’s life the sensors show almost no sign of wear, so predicting “300 cycles left” vs “250” isn’t realistic. We cap at 125, which is a common choice.
At an engine’s final cycle, RUL is 0.

### Faulty sensors detected:
Nearly constant sensors: ['s19', 's18', 's16', 's10', 's5', 's1', 's6']

### Plot
lines that drift upward or downward as the cycle increases. That drift is the signal the model will learn from.
Sensors s2, s7, s11 and s12 drift as engines approach failure, and
this drift is the signal the models will learn from:

<img width="1189" height="790" alt="download" src="https://github.com/user-attachments/assets/a5237516-288c-49a9-b410-b369189e2517" />


## Phase 2: Baseline models (done)
Split: 80 training / 20 validation engines (split by engine to avoid
leakage). Scaling fit on training data only. Test set: last cycle of
each of the 100 official test engines.

|index|model|val\_RMSE|test\_RMSE|test\_NASA\_score|
|---|---|---|---|---|
|0|Naive \(mean\)|41\.72|41\.98|33780\.97|
|1|Linear Regression|19\.9|20\.55|1209\.16|
|2|Random Forest|16\.98|17\.07|896\.66|
|3|XGBoost|16\.91|17\.11|965\.96|

- [2-3 sentences: which model won, and how far it beat the naive baseline]
- PCA: [1 sentence: how many components kept accuracy]

### Plot
<img width="585" height="455" alt="download" src="https://github.com/user-attachments/assets/0e8b5d5c-505b-4dd0-87da-e1f54e08b61b" />


  
## Roadmap
- [x] Phase 1: Explore the data
- [x] Phase 2: Baseline models and fair evaluation
- [ ] Phase 3: LSTM
- [ ] Phase 4: Transfer test and uncertainty
- [ ] Phase 5: Final results and write-up
