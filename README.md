### Simplifying location points

GPS tracks are collection of latitude, longitude, elevation, accuracy, timestamp etc. But some low-end
  devices can not provide accurate and reliable GPS tracks. Because of noisy and error prone GPS points, plotting these
  location tuples or analyzing them can be hard.<br><br>
We need to clean and process gps tracks before plotting them . Here, we will try to create APIs useful for cleaning,
    filtering, smoothing, reducing number of points, interpolating etc. these location points.<br><br>
For now we have distance and snap-to-road api using googlemaps api. Some of approach to reduce to reduce teh number
of points can be:<br>
1) Removing every nth point<br>
2) Removes consecutive points within certain distance<br>
3) Removes consecutive points more then certain distance in lesser time (google distance or haversine library)<br>
4) RDP Algorithm
(https://pypi.python.org/pypi/rdp or https://pypi.python.org/pypi/simplification/)<br>
5) Remove points within a radius.<br>

Some useful reads:<br>
https://www.toptal.com/gis/adventures-in-gps-track-analytics-a-geospatial-primer <br>
https://en.wikipedia.org/wiki/Ramer%E2%80%93Douglas%E2%80%93Peucker_algorithm<br>
https://en.wikipedia.org/wiki/Kalman_filter<br>
https://github.com/googlemaps/google-maps-services-python<br>

### Usage

##### Installation
```
pip install lokliner
```

##### Using on waypoints

```
locations =  [
    [12.9956927,77.7296046 ], [ 12.9940198,77.7269958 ], [ 12.9902847,77.7264299 ], [ 12.9901569,77.7242155 ],\
    [ 12.992211,77.7190483 ],[ 12.9921783,77.719092 ],[ 12.9871973,77.7090469 ],[ 12.985359,77.7081672 ],\
    [ 12.9888217,77.7022373 ],[ 12.9891085,77.6993243 ],[ 12.9888217,77.7022373 ],[ 12.9902847,77.7264299 ],\
    [ 12.9902847,77.7264299 ],[ 12.9888279,77.687049 ], [ 12.9885003,77.6858858 ],[ 12.9904586,77.6801263 ],\
    [ 12.9886829,77.6803986 ],[ 12.9885611,77.67955], [ 12.9885611,77.67955], [ 12.9885611,77.67955]
]
```

<br>


```
# Items in time_array should be equal to points in location array
# time_array is supposed to be time. Here we have taken numbers in increasing order

time_array = [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]
```
<br>


```
# Initialize Manipulation with google api key

from lokliner.smoother import Manipulation
sm = Manipulation(key='my_google_api_key')
```

```
# Calculate distance using haversine

distance_in_meters = sm.calculate_distance(point1=locations[0], point2=locations[1])
```
338.3744749682981

```
# calculate distance using google distance matrix api

distance_in_meters = sm.calculate_distance_using_google(locations[0], locations[1])
```
428

```
# Params: location, time_array, bound In meteres, miles False
# Returns: new_locations, new_locations_time, remove_locations, remove_locations_time

results = sm.remove_consecutive_same_location_using_google(locations,
                                            time_array,
                                            bound=2,
                                            miles=False
                                            )
```
([[12.9956927, 77.7296046], [12.9940198, 77.7269958], [12.9902847, 77.7264299],
[12.9901569, 77.7242155], [12.992211, 77.7190483], [12.9921783, 77.719092],
[12.9871973, 77.7090469], [12.985359, 77.7081672], [12.9888217, 77.7022373],
[12.9891085, 77.6993243], [12.9888217, 77.7022373], [12.9902847, 77.7264299],
[12.9888279, 77.687049], [12.9885003, 77.6858858], [12.9904586, 77.6801263],
[12.9886829, 77.6803986], [12.9885611, 77.67955]],
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 14, 15, 16, 17, 18],
[[12.9902847, 77.7264299], [12.9885611, 77.67955], [12.9885611, 77.67955]],
[13, 19, 20])


```
from lokliner.snapper import Snapper
sm = Snapper(key='my_google_api_key')
result = sm.snap_using_google_api(
            locations=locations,
            time_array=time_array,
            per_call=50,interpolate=True
        )

```
([[12.995724616124233, 77.72959949238394], [12.9953183, 77.72714289999999],
[12.995284999999999, 77.72707], [12.9952242, 77.7269906],
[12.995070799999999, 77.72686759999999], [12.994962899999999, 77.7268311],
[12.99402437404825, 77.72680809049558], [12.992393199999999, 77.7267681],
[12.9921693, 77.7267913], [12.991953899999999, 77.7268544], [12.9915707, 77.72707369999999],
[12.9911219, 77.7273788], [12.990135810286437, 77.7242082301882], [12.9905564, 77.7228217],
[12.9905564, 77.7228217], [12.990594999999999, 77.7227049], [12.990677999999999, 77.7224752],
[12.990803, 77.7220868], [12.990872999999999, 77.7218899], [12.990872999999999, 77.7218899],
[12.9909923, 77.7216581], [12.9909923, 77.7216581], [12.9912453, 77.7211954],
[12.9912453, 77.7211954], [12.9913248, 77.721042], [12.9913248, 77.721042],
[12.991343500000003, 77.72101289999999], [12.9914387, 77.7208375],
[12.991747199999999, 77.72031179999999], [12.991747199999999, 77.72031179999999],
[12.9902245, 77.7136634], [12.988746652377996, 77.70221618485269], [12.9881984, 77.6999693],
[12.987889599999999, 77.7010391], [12.987889599999999, 77.7010391],
[12.988175799999999, 77.70114459999999], [12.9884013, 77.701216], [12.9884013, 77.701216],
[12.989009099999999, 77.70138949999999], [12.989009099999999, 77.70138949999999],
[12.988801599999999, 77.7020067], [12.988746652377996, 77.70221618485269],
[12.98882807337878, 77.68704862523518], [12.988964699999999, 77.6870768],
[12.988964699999999, 77.6870768], [12.9890074, 77.68679569999999],
[12.9890074, 77.68679569999999], [12.9888414, 77.68674519999999],
[12.9887958, 77.6867255], [12.9887376, 77.6866914], [12.9886662, 77.6866282],
[12.988577099999997, 77.6865292], [12.988494699999999, 77.686432],
[12.9884361, 77.6862802], [12.9884105, 77.6861739], [12.9883922, 77.68607949999999],
[12.988385800000001, 77.6859868], [12.988391299999998, 77.68592],
[12.988401201802919, 77.68586667746874], [12.989937500000002, 77.6838659],
[12.989704099999999, 77.68466819999999], [12.989704099999999, 77.68466819999999],
[12.9894498, 77.684592], [12.9894498, 77.684592], [12.9891173, 77.68451139999999],
[12.9891173, 77.68451139999999], [12.988900999999998, 77.6844737],
[12.988900999999998, 77.6844737], [12.9886653, 77.6844272],
[12.9886653, 77.6844272], [12.9886923, 77.6841807],
[12.9886923, 77.6841807], [12.9887354, 77.68354099999999],
[12.9887354, 77.68354099999999], [12.988727299999997, 77.68282460000002],
[12.988727299999997, 77.68282460000002], [12.988727299999997, 77.68221539999999],
[12.988727299999997, 77.68221539999999], [12.988702900000002, 77.6814291],
[12.988702900000002, 77.6814291], [12.988670200000001, 77.680972],
[12.988670200000001, 77.680972], [12.9886742, 77.6807421],
[12.9886742, 77.6807421], [12.9886734, 77.68064369999999],
[12.9886734, 77.68064369999999], [12.988666399999998, 77.68039809999999],
[12.988666399999998, 77.68039809999999], [12.9885928, 77.6797093],
[12.9885928, 77.6797093], [12.98857052140498, 77.679548609688],
[12.98857052140498, 77.679548609688], [12.98857052140498, 77.679548609688]],
[1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 3, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 5, 6, 8, 9, 10, 10, 10, 10, 10, 10, 10, 10, 10, 11, 14, 14, 14, 14, 14, 14, 14, 14, 14, 14, 14, 14, 14, 14, 14, 14, 15, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 17, 17, 17, 17, 17, 17, 17, 17, 17, 17, 17, 17, 17, 17, 18, 19, 20])
