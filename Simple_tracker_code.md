# SimpleTracking code review

This file is used for review the SimpleTracker code. 

## TODO list

- [x] Tracker.hpp
- [ ] SimpleTrackerParams.hpp
- [ ] TrackTypeArray.hpp
- [ ] core/type_def.hpp


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

## C++ knowledge

The = 0 syntax declares a pure virtual function, it has nothing to do with the return value.
If a class contains at least one pure virtual function, that makes the class "abstract", which means it cannot be instantiated.
In practice, such classes need to be concretized by subclassing and implementing the virtual function(s).
