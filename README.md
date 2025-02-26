<div width="200" style="background-color: white; width: 200px">
 <img width="200" style="margin-bottom:-8px" src="https://raw.githubusercontent.com/alenrajsp/tcxreader/main/.github/logo/logo-white-background.png">
</div>

---

**tcxreader** is a reader for Garmin's TCX file format. It also works well with missing data!

# Warning!
### New version (0.4.0+ is not compatible with previous versions)
### Breaking changes between versions **0.3.15** and **0.4.0**.
 - Due to: new way of reading TPX and LX extensions in TCX files

## Objective

This is a simple TCX reader which can read Garmin TCX file extension files. The package currently does not
support laps and merges the whole exercise into one exercise object. The following data is currently parsed: longitude,
latitude, elevation, time, distance, hr_value, cadence, watts, TPX_speed (extension). The following statistics are
calculated for each exercise: calories, hr_avg, hr_max, hr_min, avg_speed, start_time, end_time, duration, cadence_avg,
cadence_max, ascent, descent, distance, altitude_max, altitude_min, altitude_avg, steps and **author data**.

GitHub requests appreciated.
[pypi](https://pypi.org/project/tcxreader/)
[github](https://github.com/alenrajsp/tcxreader)

## Features

Allows parsing / reading of TCX files.


## Installation

```
pip install tcxreader
```

## Example

An example on how to use the package is shown below.

```python
from tcxreader.tcxreader import TCXReader, TCXTrackPoint

tcx_reader = TCXReader()
file_location = 'example_data/cross-country-skiing_activity_1.tcx'

data: TCXTrackPoint = tcx_reader.read(file_location)
""" Example output:
data = {TCXExercise}
 activity_type = {str} 'Other'
altitude_avg = {float} 2285.6744874553915
altitude_max = {float} 2337.60009765625
altitude_min = {float} 1257.5999755859375
ascent = {float} 1117.9996337890625
author = {TCXAuthor} [TCXAuthor]
avg_speed = {float} 8.534458975426906
cadence_avg = {NoneType} None
cadence_max = {NoneType} None
calories = {int} 532
descent = {float} 118.19970703125
distance = {float} 5692.01
duration = {float} 2401.0
end_time = {datetime} 2020-12-26 15:54:22
hr_avg = {float} 141.1954732510288
hr_max = {int} 172
hr_min = {int} 83
laps = {list: 2} [TCXLap]
lx_ext = {dict: 0} {}
max_speed = {float} 23.50810546875
start_time = {datetime} 2020-12-26 15:14:21
tpx_ext_stats = {dict: 2} {'Speed': {'min': 0.0, 'max': 6.1579999923706055, 'avg': 2.2930514418784482}, 'RunCadence': {'min': 0, 'max': 95, 'avg': 40.81069958847737}}
trackpoints = {list: 486} [TCXTrackpoint]
 
    {TCXTrackPoint} 
     cadence = {NoneType} None
     distance = {float} 7.329999923706055
     elevation = {float} 2250.60009765625
     hr_value = {int} 87
     latitude = {float} 46.49582446552813
     longitude = {float} 15.50408081151545
     time = {datetime} 2020-12-26 15:14:28
     tpx_ext = {dict: 2} {'Speed': 0.7459999918937683, 'RunCadence': 58}
"""
```
## Classes explanation

Below figure explains the classes of **tcxreader** and the data they contain.

## TCXReader()
User initializes the tcxreader by creating a **TCXReader** class instance. To read the data of a **TCX activity** the user must use
**TCXReader.read(*filename*)** method. 
The output of **read()** is an instance of **TCXExercise** class.

### TCXExercise
Primary class that holds cumulative data of an exercise. TCXExercise contains **all** the **trackpoints** of an activity
(e.g. from all the laps merged).

### TCXLap
One TCX activity may contain multiple laps. In the TCX file they are visible by the **Lap** tag.
```xml
<Lap StartTime="2020-12-26T15:50:22.000Z">
...
</Lap>
```
TCXLap contains all the trackpoints of a lap.

### TCXTrackpoint
A point in an exercise. Almost always has **latitude, longitude, time**. Can also have *cadence, distance, elevation, hr_value, tpx_ext*.
The tpx_ext refers to individual extensions contained inside the trackpoint. An example of the Trackpoint (pre-parsing) 
in the TCX file is shown below.

```xml
<Trackpoint>
    <Time>2020-12-26T15:50:21.000Z</Time>
    <Position>
        <LatitudeDegrees>46.49732105433941</LatitudeDegrees>
        <LongitudeDegrees>15.496849408373237</LongitudeDegrees>
    </Position>
    <AltitudeMeters>2277.39990234375</AltitudeMeters>
    <DistanceMeters>5001.52978515625</DistanceMeters>
    <HeartRateBpm>
        <Value>148</Value>
    </HeartRateBpm>
    <Extensions>
        <ns3:TPX>
            <ns3:Speed>3.3589999675750732</ns3:Speed>
            <ns3:RunCadence>61</ns3:RunCadence>
        </ns3:TPX>
    </Extensions>
</Trackpoint>
```

### tpx_ext
The data parsed from the **trackpoint TPX Extensions**. Example of data (pre-parsing) is shown below.
```xml
<Extensions>
    <ns3:TPX>
        <ns3:Speed>3.3589999675750732</ns3:Speed>
        <ns3:RunCadence>61</ns3:RunCadence>
    </ns3:TPX>
</Extensions>
```
Can occur **once (1x)** in every **trackpoint**.
### tpx_ext_stats
Contains **minimum**, **maximum** and **average** values of the recorded **tpx_ext** key.

### lx_ext
The data parsed from the **lap LX Extensions**. Example of data (pre-parsing) is shown below.
```xml
<Extensions>
    <ns3:LX>
        <ns3:AvgSpeed>1.0820000171661377</ns3:AvgSpeed>
        <ns3:Steps>65</ns3:Steps>
    </ns3:LX>
</Extensions>
```
Can occur **once (1x)** in every **lap**. 

The tags which do not contain **Avg, Min, Max** in their name (e.g. steps) are
summed in the **TCXExercise** **lx_ext** dictionary. 

All tags are recorded in the **TCXLap** **lx_ext** dictionary


### Schema of the data
<div width="100%" style="background-color: white; width: 100%">
 <img width="100%" style="margin-bottom:-8px" src="https://raw.githubusercontent.com/alenrajsp/tcxreader/7c9af6dc88f9d83a8c6751b454f118220ecfd9a1/.github/images/data-explanation.svg">
</div>

## Datasets

Datasets available and used in the examples on the following links: [DATASET1](http://iztok-jr-fister.eu/static/publications/Sport5.zip)
, [DATASET2](http://iztok-jr-fister.eu/static/css/datasets/Sport.zip), [DATASET3](https://github.com/firefly-cpp/tcx-test-files).

## Licence

This package is distributed under the MIT License. This license can be found online
at [http://www.opensource.org/licenses/MIT](http://www.opensource.org/licenses/MIT).

## Disclaimer

This framework is provided as-is, and there are no guarantees that it fits your purposes or that it is bug-free. Use it
at your own risk!
