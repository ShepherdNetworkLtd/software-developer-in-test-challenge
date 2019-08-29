# Introduction

The following is a set of requirements for an analytics platform.

There are deployed a number of IoT sensors which emit JSON packets with the following schema:

	{
	  "$schema" : "http://json-schema.org/draft-04/schema#",
	  "title" : "Event",
	  "type" : "object",
	  "additionalProperties" : false,
	  "properties" : {
	    "eventId" : {
	      "type" : "string"
	    },
	    "sensorId" : {
	      "type" : "string"
	    },
	    "timestamp" : {
	      "type" : "integer"
	    },
	    "value" : {
	      "type" : "number"
	    }
	  },
	  "required" : [ "eventId", "sensorId", "timestamp", "value" ]
	}

where `id` is a **CUID** and `sensorId` is a **version 4 UUID**. An example payload looks like this:

    {
        "eventId" : "cj86g5ypk000004zvevipqxfn",
        "sensorId" : "fd0a635d-2aaf-4460-a817-6353e1b6c050",
        "timestamp" : "1506723249",
        "value" : "25.6734"
    }

An **Anomaly Detector** is a web service which accepts POST requests to `/api/event` of the above specified JSON event objects and returns a response indicating whether that reading is an anomaly or not. The response has the following schema:

	{
	  "$schema" : "http://json-schema.org/draft-04/schema#",
	  "title" : "Response",
	  "type" : "object",
	  "additionalProperties" : false,
	  "properties" : {
	    "eventId" : {
	      "type" : "string"
	    },
	    "sensorId" : {
	      "type" : "string"
	    },
	    "timestamp" : {
	      "type" : "integer"
	    },
	    "value" : {
	      "type" : "number"
	    },
	    "status" : {
	      "type" : "string",
	      "enum" : [ "NO_MODEL", "NO_ANOMALY", "ANOMALY", "ERROR" ]
	    },
	    "cause" : {
	      "type" : "string"
	    },
	    "message" : {
	      "type" : "string"
	    }
	  },
	  "required" : [ "eventId", "sensorId", "timestamp", "value", "status", "cause", "message" ]
	}

Here is an example response:

    {
        "eventId" : "cj86g5ypk000004zvevipqxfn",
        "sensorId" : "fd0a635d-2aaf-4460-a817-6353e1b6c050",
        "timestamp" : "1506723249",
        "value" : "25.6734"
        "status" : "ANOMALY",
        "cause" : "Upper Bound Threshold Detector",
        "message" : "Exceeds threshold"
    }

# The Challenge

This challenge has several parts, the goal is to achieve as much as you can within the allotted period of time. Remember to make your submissoin as clean and readable as possible, and include any instructions for compiling and running your application, as well as any specific instructions for testing the behaviour.

**NOTE**: This challenge should be done in either **Java** or **Scala**

## Part 0 

Fork this repository and rename this file to `instructions.md`. Include all your own build and running information in the `README.md` file.

For each of the following parts of the challenge please create a new branch from the last one so that the progress of your development can be tracked more easily. Also commit as often as you feel appropriate.

## Part 1

Our first anomaly detector implements a simple algorithm which returns `NO_ANOMALY` if the incoming value for that sensor is **below** a given threshold value, and `ANOMALY` if the value is **above** the threshold. Each sensor can have multiple models defined for it each of which is provided by a configuration file that specifies the sensor ID and the threshold which triggers an anomaly. A configuration file might look like:

	{
       "sensorId" : "fd0a635d-2aaf-4460-a817-6353e1b6c050",
	     "threshold": "27.0"
	}

Construct a test plan which can be executed to prove the quality and correctness of this anomaly detector.

## Part 2 

Our second anomaly detector implements an algorithm such that there is a window of size X (which is configurable) and stores the X most recent events, and a threshold. Every time a new event arrives the detector updates the window (evicting the oldest entries until it has size X), takes the average of all the events values and if the average is above the threshold resturns `ANOMALY` otherwise it returns `NO_ANOMALY`

A configuration file for this model may look like:
	

	{
       "sensorId" : "fd0a635d-2aaf-4460-a817-6353e1b6c050",
	     "window": "5"
	     "threshold": "27.0"
	}  

Construct a test plan which can be executed to prove the quality and correctness of this anomaly detector.

## Part 3

Using a language and tools of your choice implement at least 5 of the test cases from the previous 2 parts as automated tests. Feel free to make assumptions about the availability of mocks/stubs of any services, however, do make notes of whatever assumptions you make.

