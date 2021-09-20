# Week 2

Working on the av-stack

## Run bazel install in to the OTTO_DIR folder

To build the entire av-stack, run:

```bash
bazel build //...
bazel run :install_avstack -- -q $OTTO_DIR
```

## Deploy Docker

./launch_docker_development.sh
docker exec -it av-stack-dev-env /bin/bash

## Download the files

in the docker env

```bash
download_log -n <log file name>
```
place to find the file
https://drivelogs.aptiv-amod.com/log-search
https://jira.ci.motional.com/atljira/browse/DTB-10621

new token generate using the key next to the icon, from the first link.


### In case the file is to big. 

Need to figure this out by myself. 
drivelogs download_log -n 2021.09.13.14.32.38_g2h-veh-8002_SNIPPET_0054500605 -f config.tar.gz LocalizationPose.db TransformStampedList.db VehicleStateENU.db config.tar.gz.enc LSNObjectList.db TrimbleGSOFState.db TrackedObjectListCast.db TrackedObjectFullInfo.db target_prediction_avt.db MergedPointCloud.db CAM_M_F_H264Frame.db CAM_M_F_H264FrameMeta.db


## Working pipeline

scs --frozen
ottolink
logplayer-gui <path_to_log_dbs>
aptiview-x86_64.AppImage --appimage-extract-and-run


## Run simple tracker
./config/config-generator.py task SimpleTrackerTask --vehicle g2h-veh-8006 --platform g2h --destination /home/scotty/ottomatika_package/config/ --scenario LasVegas_Strip

