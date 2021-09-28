# GOFS

General On-Demand Format Specification (GOFS) defines a format for On-Demand services. It allows On-Demand service providers to define their service in a format that can be consumed by transport application in an interopeable way. 

GOFS supports On-Demand service that don't have any fixed component, ordered in real time or booked in advance. Example of supported services include : ridehail (like taxis or Uber), on-demand micro-transit (like [Metro Micro](https://micro.metro.net) or [DRT On Demand](https://www.durhamregiontransit.com/en/travelling-with-us/planning-your-travel.aspx#On%20Demand)) and paratransit. Unsupported services include fixed or flexible public transit service where a schedule is defined (GTFS and GTFS-Flex supports those use-cases). 

GOFS is meant to be specifically for On-Demand service and doesn't have a bagage of other specification. It is however meant to reuse as much common ground as possible with [GTFS](https://github.com/google/transit/) and [GBFS](https://github.com/NABSA/gbfs) when the context allows it. 

