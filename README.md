# GOFS-lite

General On-Demand Format Specification lite (GOFS-lite) defines a format for On-Demand services. It allows On-Demand service providers to define their service in a format that can be consumed by transport applications in an interoperable way. 

GOFS supports on-demand services:
- without fixed routes
- operated from curb-to-curb, stop-to-stop, or door-to-door
- providing private and/or shared trips
- that can be either ordered in real time or booked in advance.
Examples of supported services include: ridehail (like taxis or Uber), on-demand microtransit (like [Metro Micro](https://micro.metro.net) or [DRT On Demand](https://www.durhamregiontransit.com/en/travelling-with-us/planning-your-travel.aspx#On%20Demand)) and paratransit. Unsupported services include fixed or flexible public transit services where a schedule is defined (GTFS and GTFS-Flex supports those use cases). 

GOFS-lite is meant to be specifically for On-Demand services and doesn't have a baggage of other specifications. It is however meant to reuse as much common ground as possible with [GTFS](https://github.com/google/transit/) and [GBFS](https://github.com/NABSA/gbfs) when the context allows it. It's meant to be compatible with the GTFS-OnDemand with more restricted functionnalities. 

