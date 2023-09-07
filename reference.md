## General On-Demand Format Specification Reference

This document defines the format and structure of the files that comprise a GOFS-lite dataset.

## Table of Contents

1. [Document Conventions](#document-conventions)
   - [Term Definitions](#term-definitions)
   - [Presence](#presence)
   - [Field Types](#field-types)
2. [Dataset Files](#dataset-files)
3. [File Requirements](#file-requirements)
   - [Auto-discovery](#auto-discovery)
   - [Versioning](#versioning)
   - [Localization](#localization)
   - [Output Format](#output-format)
4. [Field Definitions](#field-definitions)
   - [gofs.json](#gofsjson)
   - [gofs_versions.json](#gofs_versionsjson)
   - [system_information.json](#system_informationjson)
   - [service_brands.json](#service_brandsjson)
   - [vehicle_types](#vehicle_typesjson)
   - [zones.json](#zonesjson)
   - [operating_rules.json](#operating_rulesjson)
   - [calendars.json](#calendarsjson)
   - [fares.json](#faresjson)
   - [wait_times.json](#wait_timesjson)
   - [wait_time](#wait_time)
   - [booking_rules.json](#booking_rulesjson)
   - [realtime_booking](#realtime_booking)



## Document Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

### Term Definitions

* JSON - [JavaScript Object Notation](https://www.w3schools.com/js/js_json_datatypes.asp) is a lightweight format for storing and transporting data. This document uses many terms defined by the JSON standard, including field, array, and object.
* GeoJSON - [GeoJSON](https://geojson.org/) is a format for encoding a variety of geographic data structures.

### Presence

Presence conditions applicable to fields and files:

* REQUIRED - The file or the field MUST be included in the dataset, and a value MUST be provided in the field for each record.
* OPTIONAL - The file or the field MAY be omitted from the dataset, and a value MAY be omitted in the field for any record.
* Conditionally REQUIRED - The file or the field is REQUIRED under certain conditions, which are outlined in the file or field description. Outside of these conditions, the file or the field is OPTIONAL.

### Field Types

- **Array** - A JSON element consisting of an ordered sequence of zero or more values.
- **Color** - A color encoded as a six-digit hexadecimal number. Refer to [https://htmlcolorcodes.com](https://htmlcolorcodes.com) to generate a valid value (the leading "#" must not be included). <br> *Example: `FFFFFF` for white or `000000` for black.*
- **Date** - Service day in the YYYYMMDD format. Since time within a service day may be above 24:00:00, a service day may contain information for the subsequent day(s). <br> *Example: `20211109` for November 9th, 2021.*
- **Email** - An email address. <br> *Example: `example@example.com`*
- **Enum** - An option from a set of predefined constants listed in the "Description" column. <br> *Example: If provided, the `wheelchair_boarding` field MUST indicate either `boarding_accessible`, `boarding_inacessible`, or any other options available in the list.*
- **GeoJSON FeatureCollection** - A FeatureCollection as described by the [IETF RFC 7946-3.3](https://tools.ietf.org/html/rfc7946#section-3.3).
- **GeoJSON MultiPolygon** - A Geometry Object as described by the [IETF RFC 7946-3.1.7](https://tools.ietf.org/html/rfc7946#section-3.1.7).
- **ID** - Should be represented as a string that identifies that particular entity. An ID:
    * MUST be unique within like fields (e.g. `id` MUST be unique among zones)
    * does not have to be globally unique, unless otherwise specified
    * MUST NOT contain spaces
    * MUST be persistent for a given entity (zone, plan, etc).
- **Language** - An IETF BCP 47 language code. For an introduction to IETF BCP 47, refer to [http://www.rfc-editor.org/rfc/bcp/bcp47.txt](http://www.rfc-editor.org/rfc/bcp/bcp47.txt) and [http://www.w3.org/International/articles/language-tags/](http://www.w3.org/International/articles/language-tags/). <br> *Example: `en` for English, `en-US` for American English or `de` for German.*
- **Non-negative Integer** - An integer greater than or equal to 0.
- **Float** - A floating point number.
- **Object** - A JSON element consisting of key-value pairs (fields).
- **Phone number** - A phone number. The phone number MUST include the country calling code. The phone number MUST NOT include any punctuation marks or dialable text. <br> *Example: the North American phone number "(987) 654-3210" MUST be provided as `+19876543210`; the French phone number "01.23.45.67.89" MUST be provided as `+33123456789`*.
- **String** - A text string of UTF-8 characters, which is aimed to be displayed and which must therefore be human readable.
- **Time** - Time in the HH:MM:SS format (H:MM:SS is also accepted). The time is measured from "noon minus 12h" of the service day (effectively midnight except for days on which daylight savings time changes occur). For times occurring after midnight, enter the time as a value greater than 24:00:00 in HH:MM:SS local time for the day on which the trip schedule begins. <br> *Example: `14:30:00` for 2:30PM or `25:35:00` for 1:35AM on the next day.*
- **Timezone** - TZ timezone from the [https://www.iana.org/time-zones](https://www.iana.org/time-zones). Timezone names never contain the space character but may contain an underscore. Refer to [http://en.wikipedia.org/wiki/List\_of\_tz\_zones](http://en.wikipedia.org/wiki/List\_of\_tz\_zones) for a list of valid values. <br> *Example: `Asia/Tokyo`, `America/Los_Angeles` or `Africa/Cairo`.*
- **URL** - A fully qualified URL that includes http:// or https://, and any special characters in the URL must be correctly escaped. See the following [http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html](http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html) for a description of how to create fully qualified URL values.
- **Currency code** - String containing 3 letters currency code as defined by ISO 4217. Ex: "CAD", "USD". 
* **Latitude** - WGS84 latitude in decimal degrees. The value MUST be greater than or equal to -90.0 and less than or equal to 90.0. Example: `41.890169` for the Colosseum in Rome.
* **Longitude** - WGS84 longitude in decimal degrees. The value MUST be greater than or equal to -180.0 and less than or equal to 180.0. Example: `12.492269` for the Colosseum in Rome.

## Dataset Files

By default, on-demand services are not available anywhere. To represent on-demand services in operation, at least one zone and one operating rule MUST be defined respectively in `zones.json` and `operating_rules.json`.

File Name | Presence | Description
---|---|---
`gofs.json` | REQUIRED | Auto-discovery file that links to all of the other files published by the data producer of the on-demand service system.
`gofs_versions.json` | OPTIONAL | Shows the different versions available for the same GOFS feed.
`system_information.json` | REQUIRED | Defines the attributes of the on-demand service system (e.g. operator, location, year implemented, URL, contact info, timezone, etc.).
`service_brands.json` | REQUIRED | Details the different on-demand service brands available to the riders.
`vehicle_types.json` | Conditionally REQUIRED | Describes the vehicle types used for operating the on-demand services. This file is REQUIRED if any vehicle types are referenced in `operating_rules.json`.
`zones.json` | REQUIRED | Geographically defines zones where on-demand services are available to the riders.
`operating_rules.json` | REQUIRED | Defines rules for intra-zone and inter-zone trips as well as operating times.
`calendars.json` | REQUIRED | Defines dates and days when on-demand services are available to the riders.
`fares.json` | OPTIONAL | Defines static fare rules for a system. 
`wait_times.json` | Optionally REQUIRED | Defines global wait time for defined areas of service. Either `wait_times.json` or `wait_time` MUST be provided if there are no `booking_rules` or at least one `booking_rule` is `booking_type=real-time`.
`wait_time` | Optionally REQUIRED | Returns a wait time for queried areas. Either `wait_times.json` or `wait_time` MUST be provided if there are no `booking_rules` or at least one `booking_rule` is `booking_type=real-time`.
`booking_rules.json` | OPTIONAL | Returns rules for booking in queried areas. 
`realtime_booking.json` | OPTIONAL | Returns details for available booking when static booking details can't be provided

## File Requirements

### Auto-Discovery

Publishers SHOULD implement auto-discovery of GOFS-lite feeds by linking to the location of the `gofs.json` auto-discovery endpoint.

* The location of the auto-discovery file SHOULD be provided in the HTML area of the on-demand service's landing page hosted at the URL specified in the URL field of the `system_infomation.json` file.
* This is referenced via a _link_ tag with the following format:
  * `<link rel="gofs" type="application/json" href="https://www.example.com/data/gofs.json" />`
  * References:
    * https://microformats.org/wiki/existing-rel-values
    * https://microformats.org/wiki/rel-faq#How_is_rel_used
  * An on-demand service's landing page MAY contain links to auto-discovery files for multiple systems.

### Versioning

To enable the evolution of GOFS-lite, including changes that would otherwise break backwards-compatibility with consuming applications, GOFS-lite documentation is versioned. The GOFS-lite versions are named "vX.Y" where `X.Y` is the version number.

* The current release is v1.0.

### Localization

Each set of data files SHOULD be distributed in a single language as defined in system_information.json. A system that wants to publish feeds in multiple languages SHOULD do so by publishing multiple distributions, such as:
* `https://www.example.com/data/en/system_information.json`
* `https://www.example.com/data/fr/system_information.json`

### Output Format

Every JSON file presented in this specification contains the same common header information at the top level of the JSON response object:

Field Name | Presence | Type | Description
---|---|---|---
`last_updated` | REQUIRED | Timestamp | Indicates the last time data in the feed was updated. This timestamp represents the publisher's knowledge of the current state of the system at this point in time.
`ttl` | REQUIRED | Non-negative integer | Number of seconds before the data in the feed will be updated again. If the data should always be refreshed, the value SHOULD be `0`.
`version`  | REQUIRED | String | GOFS-lite version number to which the feed confirms, according to the versioning framework.
`data` | REQUIRED | Object | Response data in the form of name:value pairs.

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 3600,
  "version": "1.0",
  "data": {
    "language": "en",
    "timezone": "US/Central",
    "name": "Example MicroTransit"
  }
}
```

## Field Definitions

### gofs.json

The `gofs.json` discovery file SHOULD represent a single system or geographic area in which vehicles are operated. The location (URL) of the `gofs.json` file SHOULD be made available to the public using the specification's [auto-discovery](#auto-discovery) function.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`language` | REQUIRED | Language | The language that will be used throughout the rest of the files. It MUST match the value in the [system_information.json](#system_informationjson) file.
\-&nbsp;`feeds` | REQUIRED | Array | An array of all of the feeds that are published by this auto-discovery file. Each element in the array is an object with the keys below.
&emsp;\-&nbsp;`name` | REQUIRED | String | Key identifying the type of feed this is. The key MUST be the base file name defined in the spec for the corresponding feed type (`zones` for `zones.json` file, `operating_rules` for `operating_rules.json` file).
&emsp;\-&nbsp;`url` | REQUIRED | URL | URL for the feed. Note that the actual feed endpoints (urls) MAY NOT be defined in the `file_name.json` format. For example, a valid feed endpoint could end with `zones` instead of `zones.json`.

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "en": {
      "feeds": [
        {
          "name": "system_information",
          "url": "https://www.example.com/gofs/1/en/system_information"
        },
        {
          "name": "zones",
          "url": "https://www.example.com/gofs/1/en/zones"
        }
      ]
    },
    "fr" : {
      "feeds": [
        {
          "name": "system_information",
          "url": "https://www.example.com/gofs/1/fr/system_information"
        },
        {
          "name": "zones",
          "url": "https://www.example.com/gofs/1/fr/zones"
        }
      ]
    }
  }
}
```
### gofs_versions.json

The `gofs_versions.json` SHOULD include all the versions available for the same GOFS-lite feed representing the on-demand service system.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence| Type | Defines
---|---|---|---
`versions` | REQUIRED | Array | Contains one object, as defined below, for each of the available versions of a feed. The array MUST be sorted by increasing version numbers.
\-&nbsp;`version` | REQUIRED | String | Version number of the feed.
\-&nbsp;`url` | REQUIRED | URL | URL of the corresponding `gofs.json` endpoint.

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "versions": [
      {
        "version": "1.0",
        "url": "https://www.example.com/gofs/2/gofs"
      },
      {
        "version": "X.Y",
        "url": "https://www.example.com/gofs/3/gofs"
      }
    ]
  }
}
```

### system_information.json

This file defines the attributes of the on-demand service system.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`language` | REQUIRED | Language | Language used throughout the rest of the files.
`timezone` | REQUIRED | Timezone | Timezone where the on-demand service system is located.
`name` | REQUIRED | String | Name of the on-demand service system to be displayed to the riders.
`short_name` | OPTIONAL | String | Abbreviation commonly used to name the on-demand service system.
`operator` | OPTIONAL | String | Name of the on-demand service operator. The operator name MAY be the same as the system name.
`url` | OPTIONAL | URL | URL of the on-demand service system.
`subscribe_url` | OPTIONAL | URL | URL where riders can subscribe to the on-demand services.
`start_date` | OPTIONAL | Date | Date that the system began operations.
`phone_number` | OPTIONAL | Phone Number | Voice telephone number for the specified system's customer service department.
`email` | OPTIONAL | Email | Contact email address actively monitored by the operator's customer service department. This email address SHOULD be a direct contact point where riders can reach a customer service representative.
`feed_contact_email` | OPTIONAL | Email | Contact email for feed consumers to report technical issues with the feed.


##### Example:

```jsonc
{
  "last_updated": 1611598155,
  "ttl": 1800,
  "version": "1.0",
  "data": {
    "language": "en",
    "timezone": "Canada/Toronto",
    "name": "Example MicroTransit",
    "short_name": "Micro",
    "operator": "MicroTransit, Inc",
    "url": "https://www.example.com",
    "purchase_url": "https://www.example.com",
    "start_date": "20100610",
    "phone_number": "+18005551234",
    "email": "customerservice@example.com",
    "feed_contact_email": "datafeed@example.com"
  }
}
```

### service_brands.json

This file defines the on-demand service brands available to the riders. One feed MAY contain multiple service brands with different features and amenities (e.g. A ridehail service system MAY offer the 'Regular Ride', 'Large Ride' and 'Shared Ride' services).

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`service_brands` | REQUIRED | Array | Array that contains one object per service brand as defined below.
\-&nbsp;`brand_id` | REQUIRED | ID | Unique identifier of the service brand. This value SHOULD remain the same over the availability of the service.
\-&nbsp;`brand_name` | REQUIRED | String | Name of the service brand to be displayed to the riders.
\-&nbsp;`brand_color` | OPTIONAL | Color | Color identifying the service brand to be displayed to the riders
\-&nbsp;`brand_text_color` | OPTIONAL | Color | Color used for displaying text over the `brand_color`. For visual-accessibility reasons, the `brand_text_color` MUST highly contrast with the `brand_color` (e.g. a dark `brand_color` SHOULD be paired with a white `brand_text_color`; a light `brand_color` SHOULD be paired with a black `brand_text_color`).

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "service_brands": [
      {
        "brand_id": "regular_ride",
        "brand_name": "Regular Ride",
        "brand_color": "1C7F49",
        "brand_text_color": "FFFFFF",
      },
      {
        "brand_id": "large_ride",
        "brand_name": "Large Ride",
        "brand_color": "1C7F49",
        "brand_text_color": "FFFFFF",
      },
      {
        "brand_id": "shared_ride",
        "brand_name": "Large Ride",
        "brand_color": "1C7F49",
        "brand_text_color": "FFFFFF",
      }
    ]
  }
}
```

### vehicle_types.json

This file defines the vehicle types used for operating the on-demand services. This file is REQUIRED if any vehicle types are referenced in `operating_rules.json`.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`vehicle_types` | REQUIRED | Array | Array that contains one object per vehicle type as defined below.
\-&nbsp; `vehicle_type_id` | REQUIRED | ID | Unique identifier of the vehicle type.
\-&nbsp; `max_capacity` | OPTIONAL | Non-Negative Integer | Maximum number of riders that the vehicle can legally carry.
\-&nbsp; `wheelchair_boarding` | OPTIONAL | Enum | Possibility for riders with a wheelchair to board the vehicle. Valid options are:<br /><ul><li>`boarding_accessible`</li><li>`boarding_inaccessible`</li><li>`boarding_accessible_with_assistance`</li></ul>

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "vehicle_types": [
      {
        "vehicle_type_id": "large_van",
        "max_capacity": "7",
        "wheelchair_boarding": "boarding_accessible",
      }
    ]
  }
}
```

### zones.json

This file geographically defines the zones where the on-demand services are available to the riders. The zones are delineated with a "FeatureCollection" GeoJSON file, in accordance with [RFC 7946](https://tools.ietf.org/html/rfc7946). At least one zone MUST be defined.

All zones defined in this file are public information (i.e. all zones can be displayed on a map available to anyone).

Geolocalization operates in two dimensions: if the pickup or drop off is allowed on an overpass or bridge, it will also be allowed to the roadway or path beneath. Location data from GPS, cellular and Wi-Fi signals are subject to interference resulting in accuracy levels in the tens of meters or greater.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
 `zones` | REQUIRED | GeoJSON FeatureCollection | Object as per [RFC 7946](https://tools.ietf.org/html/rfc7946).|
 -&nbsp;`type` | REQUIRED | String | `FeatureCollection` as per [RFC 7946](https://tools.ietf.org/html/rfc7946). |
 -&nbsp;`features` | REQUIRED | Array | Array of objects where each object represent a zone, as defined below. |
 -&nbsp;\-&nbsp;`type` | REQUIRED | String | `Feature` as per [RFC 7946](https://tools.ietf.org/html/rfc7946). |
 -&nbsp;\-&nbsp;`zone_id` | REQUIRED | ID | Unique identifier of the zone. |
 -&nbsp;\-&nbsp;`geometry` | REQUIRED | GeoJSON MultiPolygon | A polygon that describes where riders can be picked up or dropped off. <p> Following the [right-hand rule](https://tools.ietf.org/html/rfc7946#section-3.1.6), a clockwise arrangement of points defines the area enclosed by the multipolygon, where pickup and drop off MAY occur; while a counterclockwise order defines the area outside the multipolygon, where pickup and drop off MAY NOT occur. |
 -&nbsp;\-&nbsp;`properties` | REQUIRED | Object | Location property keys. |
 -&nbsp;\-&nbsp;\-&nbsp;`name` | OPTIONAL | String | Indicates the name of the zone as displayed to the riders. |


##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "zones": {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "zone_id": "zoneA",
          "geometry": {
            "type": "MultiPolygon",
            "coordinates": [
              [
                [
                  -74.10,
                  45.35
                ],
                [
                  -73.30,
                  45.35
                ],
                [
                  -73.30,
                  45.75
                ],
                [
                  -74.10,
                  45.75
                ],
                [
                  -74.10,
                  45.35
                ]
              ],
              [
                [
                  -73.60,
                  45.55
                ],
                [
                  -73.60,
                  45.65
                ],
                [
                 -73.50,
                  45.65
                ],
                [
                  -73.50,
                  45.55
                ],
                [
                  -73.60,
                  45.55
                ]
              ]
            ],
            "properties": {
              "name": "Montréal Area"
            }
          }
        }
      ]
    }
  }
}
```

### operating_rules.json

This file contains operating rules enabling on-demand services between zones or within the same zone, according to time windows and calendars. At least one operating rule MUST be defined. If `start_pickup_window`, `end_pickup_window`, and `end_dropoff_window` are not provided, it is assumed that the on-demand service is available at any hours of the day.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`operating_rules` | REQUIRED | Array | Array that contains one object per operating rule as defined below.
\-&nbsp; `from_zone_id` | REQUIRED | ID | ID from a zone defined in `zones.json` representing the boarding zone for the current rule.
\-&nbsp; `to_zone_id` | REQUIRED | ID | ID from a zone defined in `zones.json` representing the alighting zone for the current rule. `from_zone_id` and `to_zone_id` MAY reference the same zone.
\-&nbsp; `start_pickup_window` | conditionally REQUIRED | Time | Time at which the pickup starts being available in `from_zone_id` defined in this array. If `start_pickup_window` is provided, either `end_pickup_window` or `end_dropoff_window` MUST also be provided.
\-&nbsp; `end_pickup_window` | conditionally REQUIRED | Time | Time at which the pickup stops being available in `from_zone_id` defined in this array. If `end_pickup_window` is provided, `start_pickup_window` MUST be provided.
\-&nbsp; `end_dropoff_window` | conditionally REQUIRED | Time | Time at which the drop off stops being available in `to_zone_id` defined in this array. Some services differ the end of the pickup time and the end of the drop off time (e.g.: The pickup time ends at 10PM in the origin zone but it is still possible to be dropped off in the destination zone until 10:30PM). If `end_dropoff_window` is provided, `start_pickup_window` MUST be provided.
\-&nbsp; `calendars` | REQUIRED | Array | Array of calendar IDs from `calendars.json` defining the dates and days when the pickup and drop off occur.
\-&nbsp; `brand_id` | OPTIONAL | ID | ID from a service brand defined in `service_brands.json`. If this field is not provided, the operating rule applies to every service brand defined in `service_brands.json`.
\-&nbsp; `vehicle_type_id` | REQUIRED | Array | Array of vehicle types used for delivering the on-demand service.
\-&nbsp; `fare_id` | OPTIONAL | ID | Unique identifier of a fare. Used to determine the price of the on-demand service. 


##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "operating_rules" : [
      {
        "from_zone_id": "zoneA",
        "to_zone_id": "zoneA",
        "start_pickup_window" : "06:00:00",
        "end_pickup_window": "09:00:00",
        "end_dropoff_window": "09:30:00",
        "calendars": ["weekend", "labor_day"],
        "brand_id": "large_ride",
        "vehicle_type_id": "large_van",
        "fare_id": "RegularPrice"
      }
   ]
  }
}
```

### calendars.json

This file defines the dates and days when on-demand services are available to the riders.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`calendars` | REQUIRED | Array | Array that contains one object per calendar as defined below.
\-&nbsp;`calendar_id` | REQUIRED | ID | Unique identifier of the calendar.
\-&nbsp;`days` | OPTIONAL | Array | Array of abbreviations (first 3 letters) of English names of the days of the week for which this object applies (e.g. `["mon", "tue", "wed", "thu", "fri", "sat, "sun"]`). If days are not defined, it is assumed that the on-demand service is available all days of the week.
\-&nbsp;`start_date` | REQUIRED | Date | Start date for the calendar.
\-&nbsp;`end_date` | REQUIRED | Date | End date for the calendar. The end date MUST be subsequent or equal to the start date.
\-&nbsp;`excepted_dates` | OPTIONAL | Array | Array of dates removing service availability from the calendar.

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "calendars": [
        {
          "calendar_id": "weekday",
          "days": [
            "mon",
            "tue",
            "wed",
            "thu",
            "fri"
          ],
          "start_date": "20210901",
          "end_date": "20211031",
          "excepted_dates": [
            "20210906"
          ]
        },
        {
          "calendar_id": "weekend",
          "days": [
            "sat",
            "sun"
          ],
          "start_date": "20210901",
          "end_date": "20211031"
        },
        {
          "calendar_id": "labor_day"
          "start_date": "20210906",
          "end_date": "20210906"
        }
      ]
    }
  }
}
```

### fares.json

This file defines fare calculations for a system.

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`fares` | REQUIRED | Array | Array that contains one object per fare defintion as defined below.
\-&nbsp;`fare_id` | REQUIRED | ID | Unique identifier of the fare.
\-&nbsp;`currency` | REQUIRED | Currency code | The currency of the fare.
\-&nbsp;`kilometer` | OPTIONAL | Array | Array of Fare objects defining the price of the service per kilometer. Total cost per rider is the base cost defined in `rider`, plus the addition of all segments in `kilometer`, `minute`, `active_minute`, and `idle_minute`.
\-&nbsp;`minute` | OPTIONAL | Array | Array of Fare objects defining the price of the service per minute, regardless of whether the vehicle is moving or not. Total cost per rider is the base cost defined in `rider`, plus the addition of all relevant segments in `kilometer`, `minute`, `active_minute`, and `idle_minute`.
\-&nbsp;`active_minute` | OPTIONAL | Array | Array of Fare objects defining the price of the service per minute, while the vehicle is actively moving. Total cost per rider is the base cost defined in `rider`, plus the addition of all relevant segments in `kilometer`, `minute`, `active_minute`, and `idle_minute`.
\-&nbsp;`idle_minute` | OPTIONAL | Array | Array of Fare objects defining the price of the service per minute, while the vehicle is stopped or not moving. Total cost per rider is the base cost defined in `rider`, plus the addition of all relevant segments in `kilometer`, `minute`, `active_minute`, and `idle_minute`.
\-&nbsp;`rider` | OPTIONAL | Array | Array of Fare objects defining the base cost per rider.
\-&nbsp;`luggage` | OPTIONAL | Array | Array of Fare objects defining the cost of luggage as a surcharge per piece of luggage.

The following is the structure of the "Fares" object. 

Field Name | Presence | Type | Description
---|---|---|---
`interval` | OPTIONAL | Float | Interval, in units of the parent key, at which the `amount` of the row is applied, from start to end.
`start` | OPTIONAL | Non-negative Integer | The value, in units of the parent key, at which the `amount` defined in the object starts being charged.
`end` | OPTIONAL | Non-negative Integer | The value, in units of the parent key, at which the `amount` defined in the object stops being charged. 
`amount` | OPTIONAL | Non-negative currency amount | The fare cost per each unit of the parent key.

##### Example 1: Distance-based fare

The first 10 kilometers cost 3.30 CAD per kilometer, and are charged every 250 meters. All other kilometers cost 4.30 CAD, and are charged every 500 meters.

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "fares": [
      {
        "fare_id": "RegularPrice",
        "currency": "CAD",
        "kilometer": [
          {
            "interval": 0.25,
            "end": 10,
            "amount": 3.30,
          },
          {
            "interval": 0.5,
            "start": 10,
            "amount": 4.30,
          }
        ]
      }
    ]
  }
}
```

##### Example 2: Time-based fare

The first 20 minutes cost $1.00 CAD per minute, and are charged every minute. After 20 minutes, the user pays $1.50 per minute, charged every 30 seconds.

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "fares": [
      {
        "fare_id": "RegularPrice",
        "currency": "CAD",
        "minute": [
          {
            "interval": 1,
            "end": 20,
            "amount": 1.0,
          },
          {
            "interval": 0.5,
            "start": 20,
            "amount": 1.5,
          }
        ]
      }
    ]
  }
}
```

##### Example 3: Mixed pricing

The user does not pay more than the base price of $2.50 CAD for the first 10km. After 10km, the user pays $1.00 CAD per km. After 25km, the user pays an additional extension price of $3.00 CAD per 5km on top of the $1.00/km. Users are allowed 2 pieces of free luggage, and are charged $5 per additional piece of luggage.

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "fares": [
      {
        "fare_id": "RegularPrice",
        "currency": "CAD",
        "kilometer": [
          {
            "interval": 1.0,
            "start": 10,
            "amount": 1.0
          },
          {
            "interval": 5,
            "start": 25,
            "amount": 3.0,
          }
        ],
        "rider": [
          {
            "amount": 2.5
          }
        ],
        "luggage": [
          {
            "start": 3,
            "amount": 5
          }
        ]
      }
    ]
  }
}
```

### wait_times.json

This file defines wait times for the entire system via either zones in `zones.json` or S2 cells. To provide wait times to consumers, either this method or `wait_time` method can be used. `wait_times.json` allows lower server load on on demand system's servers at the cost of potentially lower precision. 

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`wait_times` | REQUIRED | Array | Array that contains one object per wait time as defined below.
\-&nbsp;`from_s2_cells` | Conditionally REQUIRED | Array | The reference to one or many S2CellID that cover the area of the wait time update. Information on S2 cells can be found here https://s2geometry.io/. Required if `from_zone_ids` field is not populated. FORBIDDEN otherwise.
\-&nbsp;`to_s2_cells` | OPTIONAL | Array | The reference to one or many S2CellID that cover the area of the destination. Information on S2 cells can be found here https://s2geometry.io/. Optional if `from_s2_cells` field is populated. FORBIDDEN otherwise.
\-&nbsp;`from_zone_ids` | Conditionally REQUIRED | Array | One or many ID from a zone defined in `zones.json`  that cover the area of the wait time update. Required if `from_s2_cells` field is not populated. FORBIDDEN otherwise.
\-&nbsp;`to_zone_ids` | OPTIONAL | Array | One or many ID from a zone defined in `zones.json`  that cover the area of the destination. Optional if `from_zone_ids` field is populated. FORBIDDEN otherwise.
\-&nbsp;`wait_time` | REQUIRED | Non-negative Integer | Time in seconds the rider will need to wait at the requested pickup location for being picked up, after completion of the service request.
\-&nbsp;`brand_id` | OPTIONAL | Non-negative Integer | Brand ID from `service_brands.json` to which the wait time applies to which brand. If not specified, the updated `wait_time` is applied to every brand. 

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "wait_times": [
      {
        "from_s2_cells": ["89c25998b" , "89c25998d"],
        "to_s2_cells": null,
        "from_zone_ids": null,
        "to_zone_ids": null,
        "wait_time": 300,
      },
      {
        "from_s2_cells": null,
        "to_s2_cells": null,
        "from_zone_ids": ["zoneA"],
        "to_zone_ids": null,
        "wait_time": 300,
      },
      {
        "from_s2_cells": null,
        "to_s2_cells": null,
        "from_zone_ids": ["zoneA"],
        "to_zone_ids": ["zoneB"],
        "wait_time": 200,
      }
    ]
  }
}
```

### wait_time

This dynamic query provides wait time for specific location. To provide wait times to consumers, either this method or `wait_times.json` method can be used. `wait_time` allows more precise queries but requires a call on every interaction by users. 

The request must have the following query parameters. 

Field Name | Presence | Type | Description
---|---|---|---
`pickup_lat` | REQUIRED | Latitude | Latitude of the location where the user will be picked-up. 
`pickup_lon` | REQUIRED | Longitude | Longitude of the location where the user will be picked-up. 
`drop_off_lat` | Conditionally REQUIRED | Latitude | Latitude of the location where the user will be dropped off. Required if `drop_off_lon` is provided. FORBIDDEN otherwise.
`drop_off_lon` | Conditionally REQUIRED | Longitude | Longitude of the location where the user will be dropped off. Required if `drop_off_lat` is provided. FORBIDDEN otherwise.
`brand_id` | Conditionally REQUIRED | ID | Brand ID from `service_brands.json` to define the wait time is requested for which brand. REQUIRED if more than one service brand is available.  

The following fields are all attributes within the main "data" object for this query response.

Field Name | Presence | Type | Description
---|---|---|---
`wait_times` | REQUIRED | Array | An array that contains one object per `brand_id`
\-&nbsp; `brand_id` | REQUIRED | ID | ID from a service brand defined in `service_brands.json`
\-&nbsp; `wait_time` | REQUIRED | Non-negative Integer | Wait time in seconds the rider will need to wait in the location before pickup. 

##### Examples:

###### Query: 

`https://www.example.com/gofs/1/en/wait_time?pickup_lat=45.60&pickup_lon=-73.30&brand_id=regular`

###### Response: 

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "wait_times": [
      {
        "brand_id": "regular_ride",
        "wait_time": 300
      },
      {
        "brand_id": "large_ride",
        "wait_time": 600
      }
    ]
  }
}
```

### booking_rules.json

This file defines rules about how to book a ride. If available, users can either book a ride in real-time, for the same day with an advance notice or for a future day. 

The following fields are all attributes within the main "data" object for this feed.


| Field Name | Presence | Type |Description |
 ---------- | ---- | -------- | ----------- |
 `booking_rules` |  REQUIRED  | Array | Array that contains one object per booking rules as defined below. |
\-&nbsp;`from_s2_cells` | Conditionally REQUIRED | Array | The reference to one or many S2CellID that cover the area of the wait time update. Information on S2 cells can be found here https://s2geometry.io/. Required if `from_zone_ids` field is not populated. FORBIDDEN otherwise.
\-&nbsp;`to_s2_cells` | OPTIONAL | Array | The reference to one or many S2CellID that cover the area of the destination. Information on S2 cells can be found here https://s2geometry.io/. Optional if `from_s2_cells` field is populated. FORBIDDEN otherwise.
\-&nbsp;`from_zone_ids` | Conditionally REQUIRED | Array | One or many ID from a zone defined in `zones.json`  that cover the area of the wait time update. Required if `from_s2_cells` field is not populated. FORBIDDEN otherwise.
\-&nbsp;`to_zone_ids` | OPTIONAL | Array | One or many ID from a zone defined in `zones.json`  that cover the area of the destination. Optional if `from_zone_ids` field is populated. FORBIDDEN otherwise.
 \-&nbsp; `booking_type` | REQUIRED | Enum | Indicates how far in advance booking can be made. <br><br>Valid options are:<br>`0` - Real-time booking. To be use with `wait_times.json` or `wait_time`. <br>`1` - Up to same-day booking with advance notice.<br>`2` - Up to prior day(s) booking. ||
 \-&nbsp; `prior_notice_duration_min` | Conditionally REQUIRED | Integer | Minimum number of minutes before travel to make the request. REQUIRED for `booking_type=1`. FORBIDDEN otherwise. |
 \-&nbsp; `prior_notice_duration_max` | OPTIONAL | Integer | Maximum number of minutes before travel to make the booking request.  OPTIONAL for `booking_type=1`. FORBIDDEN otherwise.|
 \-&nbsp; `prior_notice_last_day` | Conditionally REQUIRED | Integer | Last day before travel to make the booking request (e.g. “Ride must be booked 1 day in advance before 5PM” will be encoded as `prior_notice_last_day=1`). REQUIRED for `booking_type=2`. FORBIDDEN otherwise.|
 \-&nbsp; `prior_notice_last_time` | Conditionally REQUIRED | Time | Last time on the last day before travel to make the booking request (e.g. “Ride must be booked 1 day in advance before 5PM” will be encoded as `prior_notice_last_time=17:00:00`). REQUIRED if `prior_notice_last_day` is defined. FORBIDDEN otherwise. |
 \-&nbsp; `prior_notice_start_day` | OPTIONAL | Integer | Earliest day before travel to make the booking request (e.g.: “Ride can be booked at the earliest one week in advance at midnight” will be encoded as `prior_notice_start_day=7`). FORBIDDEN for `booking_type=0`. FORBIDDEN for `booking_type=1` if `prior_notice_duration_max` is defined. OPTIONAL otherwise. |
 \-&nbsp; `prior_notice_start_time` | Conditionally REQUIRED | Time | Earliest time on the earliest day before travel to make the booking request (e.g. : “Ride can be booked at the earliest one week in advance at midnight” will be encoded as `prior_notice_start_time=00:00:00`). FORBIDDEN for `booking_type=0`. REQUIRED if `prior_notice_start_day` is defined. FORBIDDEN otherwise. |
 \-&nbsp; `prior_notice_calendar_id` | OPTIONAL | ID referencing a `calendar_id` from `calendars.json` | Indicates the service days on which `prior_notice_last_day` or `prior_notice_start_day` are counted (e.g. : If empty, `prior_notice_start_day=2` will be two calendar days in advance. If defined as a `calendar_id` containing only business days (weekdays without holidays), `prior_notice_start_day=2` will be two business days in advance). OPTIONAL if `booking_type=2`. FORBIDDEN otherwise. |
 \-&nbsp; `message` | OPTIONAL | String | Message to riders utilizing service inside a zone when booking on-demand pickup and drop off. Meant to provide minimal information to be transmitted within a user interface about the action a rider must take in order to utilize the service. |
 \-&nbsp; `pickup_message` | OPTIONAL| String  | Functions in the same way as `message` but used when riders have on-demand pickup only. |
 \-&nbsp; `drop_off_message` | OPTIONAL| String | Functions in the same way as `message` but used when riders have on-demand drop off only. |
 \-&nbsp; `phone_number` | OPTIONAL| Phone number | Phone number to call to make the booking request. |
 \-&nbsp; `info_url` | OPTIONAL| URL | URL providing information about the booking rule. |
 \-&nbsp; `booking_url` | OPTIONAL| URL  | URL to an online interface or app where the booking request can be made. |

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "booking_rules": [
        {
          "from_s2_cells": ["89c25998b" , "89c25998d"],
          "to_s2_cells": null,
          "from_zone_ids": null,
          "to_zone_ids": null,
          "booking_type": 1,
          "prior_notice_duration_min": 120
        },
        {
          "from_s2_cells": null,
          "to_s2_cells": null,
          "from_zone_ids": ["zoneA"],
          "to_zone_ids": null,
          "booking_type": 1,
          "prior_notice_duration_min": 30,
          "prior_notice_duration_max": 180,
        },
        {
          "from_s2_cells": null,
          "to_s2_cells": null,
          "from_zone_ids": ["zoneA"],
          "to_zone_ids": ["zoneB"],
          "booking_type": 2,
          "prior_notice_start_day": 2,
          "prior_notice_last_time": "17:00:00",  
          }
      ]
    }
  }
}
```

### realtime_booking

This dynamic query provides real time booking details, when static booking details can't be provided or are missing detail. 

The request must have the following query parameters. 

Field Name | Presence | Type | Description
---|---|---|---
`pickup_lat` | REQUIRED | Latitude | Latitude of the location where the user will be picked-up. 
`pickup_lon` | REQUIRED | Longitude | Longitude of the location where the user will be picked-up. 
`drop_off_lat` | Conditionally REQUIRED | Latitude | Latitude of the location where the user will be dropped off. Required if `drop_off_lon` is provided. FORBIDDEN otherwise.
`drop_off_lon` | Conditionally REQUIRED | Longitude | Longitude of the location where the user will be dropped off. Required if `drop_off_lat` is provided. FORBIDDEN otherwise.
`brand_id` | Conditionally REQUIRED | ID | Brand ID from `service_brands.json` to define the wait time is requested for which brand. REQUIRED if more than one service brand is available.  

The following fields are all attributes within the main "data" object for this query response.

Field Name | Presence | Type | Description
---|---|---|---
`realtime_booking` | REQUIRED | Object | An array that contains one object per `brand_id`
\-&nbsp; `error` | Conditionally REQUIRED | String | If booking is impossible, human readable string explaning why booking is not possible. REQUIRED if `booking_detail` is not provided. 
\-&nbsp; `booking_detail` | Conditionally REQUIRED | Object | Object with detail on how booking is possible. REQUIRED if `error` is not provided. 
-&nbsp;\-&nbsp; `service_name` | OPTIONAL | String | If the service name needs to change due to real time booking changes, `service_name` can be provided to update the name of the on-demand service system to be displayed to the riders.
-&nbsp;\-&nbsp; `android_url` | Conditionally REQUIRED | URL | Android App Links that can open the booking app on Android. At least of one `android_url`, `ios_url`, `web_url`, or `phone_number` needs to be provided.  
-&nbsp;\-&nbsp; `ios_url` | Conditionally REQUIRED | URL | iOS Universal Links that can open the booking app on iOS. At least of one `android_url`, `ios_url`, `web_url`, or `phone_number` needs to be provided.  
-&nbsp;\-&nbsp; `web_url` | Conditionally REQUIRED | URL | Phone number to call to make the booking request. At least of one `android_url`, `ios_url`, `web_url`, or `phone_number` needs to be provided.  
-&nbsp;\-&nbsp; `phone_number` | Conditionally REQUIRED | Phone | If the service name needs to change due to real time booking changes, `service_name` can be provided to update the name of the on-demand service system to be displayed to the riders.

##### Examples:

###### Query: 

`https://www.example.com/gofs/1/en/realtime_booking?pickup_lat=45.60&pickup_lon=-73.30&brand_id=regular`

###### Response: 

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "realtime_booking": {
      "error": null,
      "booking_detail" {
        "service_name" : "Taxi",
        "android_uri" : "https://www.example.com/app?service_type=XL&platform=android",
        "ios_uri" : "https://www.example.com/app?service_type=XL&platform=ios",
        "web_uri" : "https://www.example.com/app?service_type=XL",
        "phone_number": "+18005551234",
      }
    }
  }
}
```