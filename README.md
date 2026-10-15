## LOADED THE DATA IN CSV FORMAT AND CHECKED ITS SHAPE
The obtained shape is (20631, 26)

## ARRANGED THE GIVEN RAW DATA INTO 26 COLUMNS AND NAMED IT

|index|engine|cycle|setting1|setting2|setting3|s1|s2|s3|s4|s5|s6|s7|s8|s9|s10|s11|s12|s13|s14|s15|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|1|1|-0\.0007|-0\.0004|100\.0|518\.67|641\.82|1589\.7|1400\.6|14\.62|21\.61|554\.36|2388\.06|9046\.19|1\.3|47\.47|521\.66|2388\.02|8138\.62|8\.4195|
|1|1|2|0\.0019|-0\.0003|100\.0|518\.67|642\.15|1591\.82|1403\.14|14\.62|21\.61|553\.75|2388\.04|9044\.07|1\.3|47\.49|522\.28|2388\.07|8131\.49|8\.4318|
|2|1|3|-0\.0043|0\.0003|100\.0|518\.67|642\.35|1587\.99|1404\.2|14\.62|21\.61|554\.26|2388\.08|9052\.94|1\.3|47\.27|522\.42|2388\.03|8133\.23|8\.4178|
|3|1|4|0\.0007|0\.0|100\.0|518\.67|642\.35|1582\.79|1401\.87|14\.62|21\.61|554\.45|2388\.11|9049\.48|1\.3|47\.13|522\.86|2388\.08|8133\.83|8\.3682|
|4|1|5|-0\.0019|-0\.0002|100\.0|518\.67|642\.37|1582\.85|1406\.22|14\.62|21\.61|554\.0|2388\.06|9055\.15|1\.3|47\.28|522\.19|2388\.04|8133\.8|8\.4294|

## EXTRACTED COLUMNS MEANING:

-- engine which is an engine
-- cycle is how many times it has run the 3 setting columns are operating conditions, and s1 to s21 are sensor readings.

## Checked the health of data

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

## the target: RUL

the data never says “remaining life”. We create it. Each engine runs until it fails, so its last cycle is the failure point. RUL (Remaining Useful Life) = last cycle - current cycle.
|index|engine|cycle|RUL|RUL\_capped|
|---|---|---|---|---|
|187|1|188|4|4|
|188|1|189|3|3|
|189|1|190|2|2|
|190|1|191|1|1|
|191|1|192|0|0|

### Why the cap: early in an engine’s life the sensors show almost no sign of wear, so predicting “300 cycles left” vs “250” isn’t realistic. We cap at 125, which is a common choice.
At an engine’s final cycle, RUL is 0.

## Found useless sensors:

s19    0.000000e+00
s18    0.000000e+00
s16    1.556432e-14
s10    4.660829e-13
s5     3.394700e-12
s1     6.537152e-11
s6     1.388985e-03
s15    3.750504e-02
s8     7.098548e-02
s13    7.191892e-02
s21    1.082509e-01
s20    1.807464e-01
s11    2.670874e-01
s2     5.000533e-01
s12    7.375534e-01
s7     8.850923e-01
s17    1.548763e+00
s3     6.131150e+00
s4     9.000605e+00
s14    1.907618e+01
s9     2.208288e+01
dtype: float64
Nearly constant sensors: ['s19', 's18', 's16', 's10', 's5', 's1', 's6']

## PLOT

lines that drift upward or downward as the cycle increases. That drift is the signal the model will learn from.
<img width="1189" height="790" alt="download" src="https://github.com/user-attachments/assets/a5237516-288c-49a9-b410-b369189e2517" />

data shape,
engine lifetimes,
flat sensors,
what the plots showed
