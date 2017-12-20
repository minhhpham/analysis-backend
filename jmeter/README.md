# datatools-server jmeter tests

This folder contains various items that to run jmeter load tests on analysis-backend.

## Installation

Install jmeter with this nifty script:

```sh
./install-jmeter.sh
```

## Running

The jmeter test plan can be ran from the jmeter GUI or it can be ran without a GUI.  In each of these cases, it is assumed that a analysis-backend instance can be queried at http://localhost:7070.

### Starting jmeter GUI

This script starts the jmeter gui and loads the test script.

```sh
./run-gui.sh
```

## Test Plan

A single test plan file is used for maintainablility.  By default, the test plan runs 1 thread in 1 loop and will make a request to the regions endpoint.  The test plan goes as follows:

1.  Create a new region (POST call to `/api/region`)
2.  Wait until region has finished processing (GET call to `/api/region/{NEW_REGION_ID}`)
