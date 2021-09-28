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


## Git command
```bash 
git fetch 
git checkout -b feature/PERCPRED-xxxx/my-task-tody
```

## Run test
```bash
- create jira ticket (close and create new ones often)
- git checkout new branch
- make local changes
- local test: can it run?
  --- bazel test //av/perception/modules/simple_tracker/... 
- local test: run on a log and watch?
  --- bazel run :install_avstack -- -q $OTTO_DIR to prepare binaries so we can resim logs
  
- create Merge Request on gitlab?
  --- gitlab av-stack main page, browse to your new branch (search for its name), click Create MR button
  --- note: a branch name
  --- note: MR number! (MR marked as draft for now)
- statistics on VATS:
  --- build with params!!!  see below
  --- https://jenkins.ci.motional.com/job/av-stack/view/change-requests/job/MR-4538 (use actual number)
  --- go into Build with Parameters, look for and check: (1) Publish Docker, (2) Run_perception_regression
  --- wait and tests will be automatically done on VATS
  --- (bundle name!!!)
- request DT
  --- run your code on a car by testing team;
  --- bundle name needs to be in the ticket.
```

1. run local test: 

```bash
bazel test //av/perception/modules/simple_tracker/... 
bazel test //av/perception/task/simple_tracker/... 
```

2. Copy the simple tracker to the OTTO package
```bash                                                                  
./config/config-generator.py task SimpleTrackerTask --vehicle g2h-veh-8006 --platform g2h --destination /home/scotty/ottomatika_package/config/ --scenario LasVegas_Strip

bazel run :install_avstack -- -q $OTTO_DIR
```

3. make a commit
```bash
git add . 
git commit -m "my commit something"
git push "this would not work, use the suggestion instead"
git push --set-upstream origin $branch name$
```
## Development Workflow
