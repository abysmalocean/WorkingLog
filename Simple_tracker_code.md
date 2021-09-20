# SimpleTracking code review

This file is used for review the SimpleTracker code. 

## TODO list

- [x] Tracker.hpp
- [x] SimpleTrackerParams.hpp
- [x] TrackTypeArray.hpp
- [x] core/type_def.hpp
- [x] Check what is wise_enum.h
- [x] WorldHelper.hpp
- [x] measurement_model/createMeasurementModels.hpp
- [x] core/StateSpace.hpp
- [x] measurement_model/CVMeasurementModel.hpp
- [x] measurement_model/MeasurementModelBase.hpp>
- [x] CVMeasurementModel.cpp
- [x] createMeasurementModels.hpp
- [x] createMeasurementModels.cpp
- [x] Working on the measurment.hpp and Measurement.cpp 
- [ ] track
- [ ] handler
- [ ] lifecycle
- [ ] association folder
- [ ] Core folder
- [ ] io_monitor
- [ ] utility
- [ ] simpleTracker.cpp & simpleTracker.hpp. The main file for the tracker. 
- [ ] Tracker.cpp // the base class for tracker. 
- [ ] 




## Questions

1. what is `GetLSNHeadROrder`; 
2. 


## SimpleTracker class and Tracker class
1. TrackerOutput defined multiple palce

```cpp
// in SimpleTracker.hpp
struct SimpleTrackerOutput
{
    TimePoint timestamp;
    std::vector<std::shared_ptr<TrackBase>> tracks;
};
// in Tracker.hpp
struct TrackerOutput
{
    std::vector<std::shared_ptr<TrackBase>> tracks;
};
```

## Data Structure

### ObjectType

The object type is defined in av-stack/av/trefoil_knot/src/nft/core/ObjectBoundary.h
```cpp
/*
 * Represents different tracked object classes
 */
enum class ObjectType : std::int8_t
{
    Ego = -1,
    Generic = 0,
    Vehicle = 1,
    Pedestrian = 2,
    Bicyclist = 3,
};
```

## C++ knowledge

The = 0 syntax declares a pure virtual function, it has nothing to do with the return value.
If a class contains at least one pure virtual function, that makes the class "abstract", which means it cannot be instantiated.
In practice, such classes need to be concretized by subclassing and implementing the virtual function(s).




## Things could do to improve the SimpleTracker. 

1. Use soft confidence score. Use existence score to represent a track. 
Two condition to confirm a track now. 
    1.  Total hit measurement > m_minMeasRequired
    2.  confidence > m_minConfidenceToInstantConfirm
    3.  Track groomer also easier. 
1. 


2. Use bicycle-model for cycle and cars. 
3. Multiple Thread Tracker. 
4. Gating
   1. Image embedding. 
   2. 