# onebusaway-gtfs-realtime-visualizer

# 🚀 A java-based application that consumes a GTFS Realtime feed and visualizes it on a map. Uses Apache Maven 🚀

https://github.com/coding-to-music/onebusaway-gtfs-realtime-visualizer

From / By https://github.com/OneBusAway/onebusaway-gtfs-realtime-visualizer

## GitHub

```java
git init
git add .
git remote remove origin
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:coding-to-music/onebusaway-gtfs-realtime-visualizer.git
git push -u origin main
```

# onebusaway-gtfs-realtime-visualizer [![Java CI with Maven](https://github.com/OneBusAway/onebusaway-gtfs-realtime-visualizer/actions/workflows/maven.yml/badge.svg)](https://github.com/OneBusAway/onebusaway-gtfs-realtime-visualizer/actions/workflows/maven.yml)

A demo application to visualize GTFS Realtime feeds.

## Introduction

The goal of the [GTFS Realtime](https://developers.google.com/transit/gtfs-realtime/) specification is to provide both transit agencies and developers with a consistent way to exchange real-time public transit data. A specific feature of GTFS Realtime is support for [vehicle positions](https://developers.google.com/transit/gtfs-realtime/vehicle-positions), which allow an agency to specify information about vehicle locations.

We want to make it easy for developers to work with vehicle position using the GTFS Realtime format, so we've put together a quick demo project that shows how to consume a GTFS Realtime vehicle positions feed.

![Screen Shot](/images/ScreenShot.png)

In the demo project, we'll walk you through a simple Java-based program that consumes a GTFS Realtime feed and visualizes it on a map.

If you want to follow along at home, you can [download a ZIP of the source-code for the demo project](https://github.com/OneBusAway/onebusaway-gtfs-realtime-visualizer/zipball/master) or [import the code from the Git repository directly](https://github.com/OneBusAway/onebusaway-gtfs-realtime-visualizer). The project is designed to be built with [Apache Maven](http://maven.apache.org/), so download that if you are interested in building and running the project.

## Verify Java is installed

```
openjdk 11.0.19 2023-04-18
OpenJDK Runtime Environment (build 11.0.19+7-post-Ubuntu-0ubuntu120.04.1)
OpenJDK 64-Bit Server VM (build 11.0.19+7-post-Ubuntu-0ubuntu120.04.1, mixed mode, sharing)
```

## Upgrade Apache Maven

https://maven.apache.org/

Current version of Maven

```
mvn --version
```

```java
Apache Maven 3.6.3
Maven home: /usr/share/maven
Java version: 11.0.19, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.4.0-153-generic", arch: "amd64", family: "unix"
```

Remove the existing Maven installation:

```java
sudo apt remove maven
```

Download the latest version of Apache Maven using wget:

https://maven.apache.org/download.cgi

```java
wget https://downloads.apache.org/maven/maven-3/3.9.3/binaries/apache-maven-3.9.3-bin.tar.gz
```

Extract the downloaded archive:

```java
tar -xzf apache-maven-3.9.3-bin.tar.gz
```

Move the extracted Maven directory to /usr/share to match the previous installation location:

```java
sudo mv apache-maven-3.9.3 /usr/share/maven
```

Update the symbolic link for mvn command:

```java
sudo ln -s /usr/share/maven/bin/mvn /usr/bin/mvn
```

Verify the Maven installation by running the following command:

```java
mvn --version
```

It should display the new Maven version (3.9.0).

```java
Apache Maven 3.9.3
Maven home: /usr/share/maven
Java version: 11.0.19, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.4.0-153-generic", arch: "amd64", family: "unix"
```

## Running the Demo Project

Before we dig into how the demo application the works, let's see it in action! After you've downloaded the code for the project, open up a terminal and change to the root directory of the project. From there, run Maven to compile and package the application:

    mvn package

Now that the project has been built, you should be able to run the resulting application bundle:

```
java -jar target/onebusaway-GTFS Realtime-visualizer-0.0.1-SNAPSHOT.jar \
  --vehiclePositionsUrl=https://cdn.mbta.com/realtime/VehiclePositions.pb
```

Getting this error:

```
Error: Unable to access jarfile target/onebusaway-GTFS
```

Subdirectory target (created by `mvn package`) contains (among other things):

```
original-onebusaway-gtfs-realtime-visualizer-0.0.1-SNAPSHOT.jar
onebusaway-gtfs-realtime-visualizer-0.0.1-SNAPSHOT.jar
```

Try this:

```
java -jar target/onebusaway-gtfs-realtime-visualizer-0.0.1-SNAPSHOT.jar \
  --vehiclePositionsUrl=https://cdn.mbta.com/realtime/VehiclePositions.pb
```

This will start the application up using [MBTA's GTFS Realtime feeds](http://mbta.com/rider_tools/developers/default.asp?id=22393). When the application starts, you should see a message like:

    =======================================================

      The GTFS-realtime visualizer server has
      started.  Check it out at:

        http://localhost:8080/

    =======================================================

Note: In Java 9 and later versions, you may get an error like: `java.lang.ClassNotFoundException: javax.annotation.PostConstruct`. Use this command:

```
java --add-modules java.xml.ws.annotation -jar target/onebusaway-gtfs-realtime-visualizer-0.0.1-SNAPSHOT.jar\
--vehiclePositionsUrl=https://cdn.mbta.com/realtime/VehiclePositions.pb
```

Refer [this issue](https://github.com/OneBusAway/onebusaway-gtfs-realtime-visualizer/issues/10) for details.

So browse to [http://localhost:8080/](http://localhost:8080/) and take a look! The map update uses a number of HTML5 technologies, so be sure to use a modern browser like [Google Chrome](https://www.google.com/chrome) for the best experience. If all goes well, you should see a map of real-time vehicle positions that update over time.

Works!

![VehicleMovements](/images/VehicleMovements.png)

Please note that if you use this application with your own GTFS-rt vehicle positions feed, the `VehicleDescriptor` must contain the `id` field, such as:

```
entity {
  id: "vehicle_position_2403"
  vehicle {
    position {
      latitude: 28.06235
      longitude: -82.45927
      bearing: 360.0
      speed: 0.0
    }
    vehicle {
      id: "2403" // This is required for this application
    }
  }
}
```

The `id` field is required so that the vehicle path history can be drawn on the map over multiple refreshes of the feed.

## Digging into the Code

So how does this all work? Let's look at the code! Most of the work with GTFS Realtime data is done in the following class:

[org.onebusaway.gtfs_realtime.visualizer.VisualizerService](https://github.com/OneBusAway/onebusaway-gtfs-realtime-visualizer/blob/master/src/main/java/org/onebusaway/gtfs_realtime/visualizer/VisualizerService.java)

The class does the following:

- Periodically downloads vehicle data from the GTFS Realtime feeds.
- Extracts relevant position data.
- Notifies listeners of updated data.

Let's step through the code for each of these steps.

We setup a recurring task that downloads data from the GTFS Realtime feeds. The data comes in the form of a [Protocol Buffer](http://code.google.com/p/protobuf/) stream, which we can parse to create a [FeedMessage](https://developers.google.com/transit/gtfs-realtime/reference#FeedMessage):

    FeedMessage feed = FeedMessage.parseFrom(_vehiclePositionsUrl.openStream());

The feed message contains a series of [FeedEntity](https://developers.google.com/transit/gtfs-realtime/reference#FeedEntity) objects that contains information about a vehicle, a trip, or an alert. In our case, we are interested in vehicle position data, so we will look for a [VehiclePosition](https://developers.google.com/transit/gtfs-realtime/reference#VehiclePosition) for each entity, along with a [Position](https://developers.google.com/transit/gtfs-realtime/reference#Position) for the vehicle.

    for (FeedEntity entity : feed.getEntityList()) {
      if (!entity.hasVehicle()) {
        continue;
      }
      VehiclePosition vehicle = entity.getVehicle();
      if (vehicle.hasPosition()) {
        continue;
      }
      Position position = vehicle.getPosition();

Getting access to a vehicle location is that simple!

Of course, we've left out a few details. We take advantage of a couple of OneBusAway libraries to simplify our application:

- The [onebusaway-gtfs-realtime-api](https://github.com/OneBusAway/onebusaway-gtfs-realtime-api/wiki) module provides pre-packaged Java classes generated from the [GTFS-realtime protocol buffer definition](https://developers.google.com/transit/gtfs-realtime/gtfs-realtime-proto).

## Next Steps

We provided this demo application as an example that you can build on when working with GTFS Realtime feeds. Integrating GTFS Realtime feeds with alerts, trip updates, and vehicle positions along with existing GTFS schedule data can be complex, but it can also allow you to build powerful applications.

Also be sure to check out [all our GTFS Realtime resources](https://github.com/OneBusAway/onebusaway/wiki/GTFS-Realtime-Resources).
