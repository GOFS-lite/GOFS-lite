# GOFS-lite

The General On-Demand Format Specification lite (GOFS-lite) allows on-demand service providers to define their service in a lightweight format that can be consumed by transport applications in an interoperable way.

GOFS-lite, as an offshoot of the GOFS project, is meant to be compatible with GTFS-OnDemand while offering more restricted functionalities. GOFS-lite defines a lightweight format for purely on-demand transport services to provide information about their offering, much like the General Transit Feed Specification (GTFS) exists for public transit and the General Bikeshare Feed Specification (GBFS) exists for bikeshare, shared e-scooters, carshare, and other free-floating, self-service options.  GOFS-lite is intended to have as much common ground as possible with [GTFS](https://github.com/google/transit/) and [GBFS](https://github.com/NABSA/gbfs) when the context allows it. 

GOFS-lite supports on-demand services:
- without fixed routes
- operated from curb-to-curb, stop-to-stop, or door-to-door
- providing private and/or shared trips
- that can be either ordered in real time or booked in advance.
Examples of supported services include: ridehail (like taxis or Uber), on-demand microtransit (like [Metro Micro](https://micro.metro.net) or [DRT On Demand](https://www.durhamregiontransit.com/en/travelling-with-us/planning-your-travel.aspx#On%20Demand)) and paratransit. 

Unsupported services include fixed or flexible public transit services where a schedule is defined (GTFS and GTFS-Flex support those use cases).

GOFS-lite is a work in progress, there are currently important missing functionalities like accurate pricing, travel time estimations, etc. Questions or comments can be sent to gofs-lite@transitapp.com
