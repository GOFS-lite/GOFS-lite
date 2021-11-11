## General On-Demand Format Specification Reference

This document defines the format and structure of the files that comprise a GOFS dataset. 

## Table of Contents

1. [Document Conventions](#document-conventions)
   - [Term Definitions](#term-definitions)
   - [Presence](#presence)
   - [Field Types](#field-types)
   - [Field Signs](#field-signs)
2. [Dataset Files](#fdataset-files)
3. [File Requirements](#file-requirements)
   - [Auto-discovery](#auto-discovery)
   - [Localization](#localization)
   - [Output Format](#output-format)
5. [Field Definitions](#field-definitions)
   - [gofs.json](#gofsjson)
   - [system_information.json](#system_informationjson)
   - [service_information.json](#service_informationjson)
   - [vehicle_types](#vehicle_typesjson)
   - [zones.json](#zonesjson)
   - [zones_transfer_rules.json](#zones_transfer_rulesjson)
   - [calendar.json](#calendarjson)


## Document Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", “SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

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
- **Date** - Service day in the YYYYMMDD format. Since time within a service day may be above 24:00:00, a service day may contain information for the subsequent day(s). <br> *Example: `20211109` for November 9th, 2021.*
- **Email** - An email address. <br> *Example: `example@example.com`*
- **Enum** - An option from a set of predefined constants listed in the "Description" column. <br> *Example: If provided, the `wheelchair_boarding` field MUST indicate either `boarding_accessible`, `boarding_inacessible`, or any other options available in the list.*
- **ID** - Should be represented as a string that identifies that particular entity. An ID:
    * MUST be unique within like fields (e.g. `id` MUST be unique among zones)
    * does not have to be globally unique, unless otherwise specified
    * MUST NOT contain spaces
    * MUST be persistent for a given entity (zone, plan, etc).
- **Language** - An IETF BCP 47 language code. For an introduction to IETF BCP 47, refer to [http://www.rfc-editor.org/rfc/bcp/bcp47.txt](http://www.rfc-editor.org/rfc/bcp/bcp47.txt) and [http://www.w3.org/International/articles/language-tags/](http://www.w3.org/International/articles/language-tags/). <br> *Example: `en` for English, `en-US` for American English or `de` for German.*
- **Non-negative Integer** - An integer greater than or equal to 0.
- **Object** - A JSON element consisting of key-value pairs (fields).
- **Phone number** - A phone number.
- **String** - A text string of UTF-8 characters, which is aimed to be displayed and which must therefore be human readable.
- **Time** - Time in the HH:MM:SS format (H:MM:SS is also accepted). The time is measured from "noon minus 12h" of the service day (effectively midnight except for days on which daylight savings time changes occur). For times occurring after midnight, enter the time as a value greater than 24:00:00 in HH:MM:SS local time for the day on which the trip schedule begins. <br> *Example: `14:30:00` for 2:30PM or `25:35:00` for 1:35AM on the next day.*
- **Timezone** - TZ timezone from the [https://www.iana.org/time-zones](https://www.iana.org/time-zones). Timezone names never contain the space character but may contain an underscore. Refer to [http://en.wikipedia.org/wiki/List\_of\_tz\_zones](http://en.wikipedia.org/wiki/List\_of\_tz\_zones) for a list of valid values. <br> *Example: `Asia/Tokyo`, `America/Los_Angeles` or `Africa/Cairo`.*
- **URL** - A fully qualified URL that includes http:// or https://, and any special characters in the URL must be correctly escaped. See the following [http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html](http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html) for a description of how to create fully qualified URL values.

## Dataset Files

File Name | Presence | Description
---|---|---
gofs.json | REQUIRED | Auto-discovery file that links to all of the other files published by the system.
system_information.json | REQUIRED | Details including system operator, system location, year implemented, URL, contact info, timezone, etc.
service_information.json | REQUIRED | Details including service name, branding, etc.
vehicle_types.json | Conditionally REQUIRED | Describes the types of vehicles available in the on-demand service system. REQUIRED if any vehicle types are references in zones_transfer_rules.json
zones.json | REQUIRED | Defines zones available for the services.
zones_transfer_rules.json | REQUIRED | Defines rules for intra-zones and inter-zone travel and operating hours.
calendar.json | REQUIRED | Defines dates where service is active.

## File Requirements

### Auto-Discovery

Publishers SHOULD implement auto-discovery of GOFS feeds by linking to the location of the `gofs.json` auto-discovery endpoint.

- The location of the auto-discovery file SHOULD be provided in the HTML area of the on-demand service's landing page hosted at the URL specified in the URL field of the `system_infomation.json` file.
- This is referenced via a _link_ tag with the following format:
  - `<link rel="gbfs" type="application/json" href="https://www.example.com/data/gbfs.json" />`
  - References:
    - https://microformats.org/wiki/existing-rel-values
    - https://microformats.org/wiki/rel-faq#How_is_rel_used
- An on-demand service's landing page MAY contain links to auto-discovery files for multiple systems.

### Localization

* Each set of data files SHOULD be distributed in a single language as defined in system_information.json.
* A system that wants to publish feeds in multiple languages SHOULD do so by publishing multiple distributions, such as:
    * `https://www.example.com/data/en/system_information.json`
    * `https://www.example.com/data/fr/system_information.json`

### Output Format

Every JSON file presented in this specification contains the same common header information at the top level of the JSON response object:

Field Name | Presence | Type | Description
---|---|---|---
`last_updated` | REQUIRED | Timestamp | Indicates the last time data in the feed was updated. This timestamp represents the publisher's knowledge of the current state of the system at this point in time.
`ttl` | REQUIRED | Non-negative integer | Number of seconds before the data in the feed will be updated again (0 if the data should always be refreshed).
`version`  | REQUIRED | String | GBFS version number to which the feed confirms, according to the versioning framework.
`data` | REQUIRED | Object | Response data in the form of name:value pairs.

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 3600,
  "version": "1.0",
  "data": {
    "system_id": "example_cityname",
    "language": "en",
    "timezone": "US/Central",
    "name": "Example MicroTransit"
  }
}
```

## Field Definitions

#### gofs.json

The `gofs.json` discovery file SHOULD represent a single system or geographic area in which vehicles are operated. The location (URL) of the `gbfs.json` file SHOULD be made available to the public using the specification’s [auto-discovery](#auto-discovery) function.
The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`language` | REQUIRED | Language | The language that will be used throughout the rest of the files. It MUST match the value in the [system_information.json](#system_informationjson) file.
\-&nbsp;`feeds` | REQUIRED | Array | An array of all of the feeds that are published by this auto-discovery file. Each element in the array is an object with the keys below.
&emsp;\-&nbsp;`name` | REQUIRED | String | Key identifying the type of feed this is. The key MUST be the base file name defined in the spec for the corresponding feed type (`zones` for `zones.json` file, `zones_transfer_rules` for `zones_transfer_rules.json` file).
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
          "url": "https://www.example.com/gbfs/1/en/system_information"
        },
        {
          "name": "zones",
          "url": "https://www.example.com/gbfs/1/en/zones"
        }
      ]
    },
    "fr" : {
      "feeds": [
        {
          "name": "system_information",
          "url": "https://www.example.com/gbfs/1/fr/system_information"
        },
        {
          "name": "zones",
          "url": "https://www.example.com/gbfs/1/fr/zones"
        }
      ]
    }
  }
}
```

### system_information.json

This file defines the attributes of the on-demand service system.
The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`system_id` | REQUIRED | ID | Unique identifier of the on-demand service system. This value SHOULD remain the same over the life of the system.
`language` | REQUIRED | Language | Language used throughout the rest of the files.
`timezone` | REQUIRED | Timezone | Timezone where the on-demand service system is located.
`name` | REQUIRED | String | Name of the on-demand service system to be displayed to customers.
`short_name` | OPTIONAL | String | Abbreviation commonly used to name the on-demand service system.
`operator` | OPTIONAL | String | Name of the on-demand service operator.
`url` | OPTIONAL | URL | URL of the on-demand service system.
`purchase_url` | OPTIONAL | URL | URL where a customer can subscribe to the on-demand services.
`start_date` | OPTIONAL | Date | Date that the system began operations.
`phone_number` | OPTIONAL | Phone Number | Voice telephone number for the specified system’s customer service department. It can and SHOULD contain punctuation marks to group the digits of the number. Dialable text (for example, Capital Bikeshare’s "877-430-BIKE") is permitted, but the field MUST NOT contain any other descriptive text.
`email` | OPTIONAL | Email | Contact email address actively monitored by the operator’s customer service department. This email address SHOULD be a direct contact point where riders can reach a customer service representative.
`feed_contact_email` | OPTIONAL | Email | Contact email for feed consumers to report technical issues with the feed.


##### Example:

```jsonc
{
  "last_updated": 1611598155,
  "ttl": 1800,
  "version": "1.0",
  "data": {
    "system_id": "example_cityname",
    "language": "en",
    "timezone": "US/Central",
    "name": "Example MicroTransit",
    "short_name": "Micro",
    "operator": "MicroTransit, Inc",
    "url": "https://www.example.com",
    "purchase_url": "https://www.example.com",
    "start_date": "20100610",
    "phone_number": "1-800-555-1234",
    "email": "customerservice@example.com",
    "feed_contact_email": "datafeed@example.com"
  }
}
```

### service_information.json

This files defines the on-demand services available to the riders. One feed MAY contain multiple services with different features and amenities (e.g. A ridehail service system MAY offer the 'Regular Ride', 'Large Ride' and 'Shared Ride' services).
The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`services` | REQUIRED | Array | Array of all the services available in the feed.
\-&nbsp;`service_id` | REQUIRED| ID | Unique identifier of the on-demand service. This value SHOULD remain the same over the availability of the service.
\-&nbsp;`service_name` | REQUIRED| String | Name of the service to be displayed to customers.

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "services": [
      {
        "service_id": "regular_ride",
        "service_name": "Regular Ride",
      },
      {
        "service_id": "large_ride",
        "service_name": "Large Ride",
      },
      {
        "service_id": "shared_ride",
        "service_name": "Large Ride",
      }
    ]
  }
}
```

### vehicle_types.json

This file defines the vehicle types used for operating the on-demand services. This file is REQUIRED if any vehicle types are referenced in `zones_transfer_rules.json`.
The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`vehicle_types` | REQUIRED | Array | Array that contains one object per vehicle type in the system as defined below.
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
        "vehicle_type_id": "regular_van",
        "max_capacity": "5",
        "wheelchair_boarding": "boarding_accessible",
      }
    ]
  }
}
```

### zones.json

This file geographically defines the zones where the on-demand services are available to riders. The zone data should be a "FeatureCollection" GeoJSON file.
The following fields are all attributes within the main "data" object for this feed. 

Field Name | Presence | Type | Description
---|---|---|---
| `zones` | REQUIRED | Array | Each zone is defined as an object within the array of features, as follows. |
| -&nbsp;`type` | REQUIRED | String | `"FeatureCollection"` of locations. |
| -&nbsp;`features` | REQUIRED | Array | Collection of `"Feature"` objects describing many zones. |
| -&nbsp;\-&nbsp;`type` | REQUIRED | String | `"Feature"` |
| -&nbsp;\-&nbsp;`id` | REQUIRED | ID | Unique identifier of the zone. |
| -&nbsp;\-&nbsp;`geometry` | REQUIRED | Object | Geometry of the location as defined by GeoJSON. |
| -&nbsp;\-&nbsp;`properties` | REQUIRED | Object | Location property keys. |
| -&nbsp;\-&nbsp;\-&nbsp;`name` | OPTIONAL | String | Indicates the name of the zone as displayed to riders. |


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
          "id": "zoneA",
          "geometry": {
            "type": "MultiPolygon",
            "coordinates": [
              [
                [
                  -74.168701171875,
                  45.22848059584359
                ],
                [
                  -73.245849609375,
                  45.24008561090264
                ],
                [
                  -73.0645751953125,
                  45.59482210127054
                ],
                [
                  -73.40240478515625,
                  45.87088761346192
                ],
                [
                  -74.0313720703125,
                  45.805828539928356
                ],
                [
                  -74.24285888671875,
                  45.515970517182474
                ],
                [
                  -74.168701171875,
                  45.22848059584359
                ]
              ]
            ],
            "properties": {
              "name": "Montréal Region"
            }
          }
        }
      ]
    }
  }
}
```

### zones_transfer_rules.json

This file contains rules that enable on-demand services between zones, including intra-zone service, and that provide operating hours. At least one service between two zones MUST be defined. 
The following fields are all attributes within the main "data" object for this feed. 

Field Name | Presence | Type | Description
---|---|---|---
`zone_tranfers_rules` | REQUIRED | Array | Array that contains one object per transfer rule as defined below. 
\-&nbsp; `service_id` | OPTIONAL | ID | ID from a service defined in `service_information.json`. If not provided, the rule applies to every service in the GOFS feed. 
\-&nbsp; `from_zone_id` | REQUIRED | ID | ID from a zone defined in `zones.json` representing the boarding zone for the current rule. 
\-&nbsp; `to_zone_id` | REQUIRED | ID | ID from a zone defined in `zones.json` representing the alighting zone for the current rule. `from_zone_id` and `to_zone_id` MAY reference the same zone. 
\-&nbsp; `start_pickup_window` | OPTIONAL | Time | Time at which the pickup starts being available in `from_zone_id` defined in this array.
\-&nbsp; `end_pickup_window` | OPTIONAL | Time | Time at which the pickup stops being available in `from_zone_id` defined in this array.
\-&nbsp; `end_dropoff_window` | OPTIONAL | Time | Time at which the drop off stops being available in `to_zone_id` defined in this array. Some services differ the end of the pickup time and the end of the drop off time (e.g.: The pickup time ends at 10PM in the origin zone but it is still possible to be dropped off in the destination zone until 10:30PM). 
\-&nbsp; `calendars` | REQUIRED | Array | Array of IDs from a calendar in `calendar.json`.


##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "1.0",
  "data": {
    "zone_tranfers_rules" : [
      {
        "service_id": "regular_ride",
        "from_zone_id": "zoneA",
        "to_zone_id": "zoneA",
        "start_pickup_window" : "06:00:00",
        "end_pickup_window": "09:00:00",
        "end_dropoff_window": "09:30:00",
        "calendars": ["weekend", "labor_day"]
      }
   ]
  }
}
```

### calendar.json

This file defines the dates and days when on-demand services are available to riders.
The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`calendars` | REQUIRED | Array | Array of objects as defined below. 
\-&nbsp;`id` | REQUIRED | ID | Unique identifier of the calendar.
\-&nbsp;`days` | OPTIONAL | Array | Array of abbreviations (first 3 letters) of English names of the days of the week for which this object applies (e.g. `["mon", "tue", "wed", "thu", "fri", "sat, "sun"]`). If days are not defined, it is assumed that the service is active all days of the week. 
\-&nbsp;`start_date` | Conditionally REQUIRED | Date | Start date for the calendar. 
\-&nbsp;`end_date` | Conditionally REQUIRED | Date | End date for the calendar. The start date and the end date may be the same.
\-&nbsp;`excepted_dates` | OPTIONAL | Array | Array of dates removing service on these specific dates from the calendar defined. 

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "1.0",
  "data": {
    "calendars": [
        {
          "id": "weekday",
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
          "id": "weekend",
          "days": [
            "sat",
            "sun"
          ],
          "start_date": "20210901",
          "end_date": "20211031"
        },
        {
          "id": "labor_day"
          "start_date": "20210906",
          "end_date": "20210906"
        }
      ]
    }
  }
}
```