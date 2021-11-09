## General On-Demand Format Specification Reference

This document defines the format and structure of the files that comprise a GOFS dataset. 

## Table of Contents

1. [Document Conventions](#document-conventions)
   - [Term Definitions](#term-definitions)
   - [Presence](#presence)
   - [Field Types](#field-types)
   - [Field Signs](#field-signs)
3. [Dataset Files](#file-definitions)
2. [File Requirements](#file-definitions)
   - [Localization](#localization)
   - [Output Format](#output-format)
4. [Field Definitions](#field-definitions)
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
- **Date** - Service day in the YYYYMMDD format. Since time within a service day may be above 24:00:00, a service day may contain information for the subsequent day(s). <br> *Example: `20180913` for September 13th, 2018.*
- **Email** - An email address. <br> *Example: `example@example.com`*
- **Enum** - An option from a set of predefined constants defined in the "Description" column. <br> *Example: The `route_type` field contains a `0` for tram, a `1` for subway...*
- **ID** - Should be represented as a string that identifies that particular entity. An ID:
    * MUST be unique within like fields (e.g. `zone_id` MUST be unique among zones)
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
vehicle_types.json | Conditionally REQUIRED | Describes the types of vehicles that System operator has available the service. REQUIRED if any vehicle types are references in zones_transfer_rules.json
zones.json | REQUIRED | Defines zones available for the services.
zones_transfer_rules.json | REQUIRED | Defines rules for intra-zones and inter-zone travel and operating hours.
calendar.json | REQUIRED | Defines dates where service is active.

## File Requirements

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
  "version": "3.0",
  "data": {
    "name": "Example Bike Rental",
    "system_id": "example_cityname",
    "timezone": "US/Central",
    "language": "en"
  }
}
```

*****

## Field Definitions

#### gofs.json

The `gofs.json` discovery file SHOULD represent a single system or geographic area in which vehicles are operated. The location (URL) of the `gbfs.json` file SHOULD be made available to the public using the specification’s [auto-discovery](#auto-discovery) function.

Field Name | Presence | Type | Description
---|---|---|---
`language` | REQUIRED | Language | The language that will be used throughout the rest of the files. It MUST match the value in the [system_information.json](#system_informationjson) file.
\-&nbsp;`feeds` | REQUIRED | Array | An array of all of the feeds that are published by this auto-discovery file. Each element in the array is an object with the keys below.
&emsp;\-&nbsp;`name` | REQUIRED | String | Key identifying the type of feed this is. The key MUST be the base file name defined in the spec for the corresponding feed type (`zones` for `zones.json` file, `zones_transfer_rules` for `zones_transfer_rules.json` file).
&emsp;\-&nbsp;`url` | REQUIRED | URL | URL for the feed. Note that the actual feed endpoints (urls) MAY NOT be defined in the `file_name.json` format. For example, a valid feed endpoint could end with `station_info` instead of `station_information.json`.

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "3.0",
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

The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`system_id` | REQUIRED | ID | This is a globally unique identifier for the vehicle share system.  It is up to the publisher of the feed to guarantee uniqueness and MUST be checked against existing `system_id` fields in [systems.csv](https://github.com/NABSA/gbfs/blob/master/systems.csv) to ensure this. This value is intended to remain the same over the life of the system. <br><br>Each distinct system or geographic area in which vehicles are operated SHOULD have its own `system_id`. Systems IDs SHOULD be recognizable as belonging to a particular system as opposed to random strings - for example, `bcycle_austin` or `biketown_pdx`.
`language` | REQUIRED | Language | The language that will be used throughout the rest of the files. It MUST match the value in the [gbfs.json](#gbfsjson) file.
`name` | REQUIRED | String | Name of the system to be displayed to customers.
`short_name` | OPTIONAL | String | OPTIONAL abbreviation for a system.
`operator` | OPTIONAL | String | Name of the system operator.
`url` | OPTIONAL | URL | The URL of the vehicle share system.
`purchase_url` | OPTIONAL | URL | URL where a customer can purchase a membership.
`start_date` | OPTIONAL | Date | Date that the system began operations.
`phone_number` | OPTIONAL | Phone Number | This OPTIONAL field SHOULD contain a single voice telephone number for the specified system’s customer service department. It can and SHOULD contain punctuation marks to group the digits of the number. Dialable text (for example, Capital Bikeshare’s "877-430-BIKE") is permitted, but the field MUST NOT contain any other descriptive text.
`email` | OPTIONAL | Email | This OPTIONAL field SHOULD contain a single contact email address actively monitored by the operator’s customer service department. This email address SHOULD be a direct contact point where riders can reach a customer service representative.
`feed_contact_email` | OPTIONAL | Email | This OPTIONAL field SHOULD contain a single contact email for feed consumers to report technical issues with the feed.
`timezone` | REQUIRED | Timezone | The time zone where the system is located.

##### Example:

```jsonc
{
  "last_updated": 1611598155,
  "ttl": 1800,
  "version": "3.0",
  "data": {
    "system_id": "example_cityname",
    "language": "en",
    "name": "Example MicroTransit",
    "short_name": "Micro Example",
    "operator": "Micro, Inc",
    "url": "https://www.example.com",
    "purchase_url": "https://www.example.com",
    "start_date": "2010-06-10",
    "phone_number": "1-800-555-1234",
    "email": "customerservice@example.com",
    "feed_contact_email": "datafeed@example.com",
    "timezone": "US/Central"
  }
}
```

### service_information.json

Defining the services available in the feed. One feed MAY contain multiple service where services have different features. Ex : Uber's feed MAY contains the 'uberX' and 'uberPool' services.

Field Name | Presence | Type | Description
---|---|---|---
`services` | REQUIRED | Array | Array of all the services available in the feed.
\-&nbsp;`service_name` | REQUIRED| String | Name of the service

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "3.0",
  "data": {
   
  }
}
```

### vehicle_types.json

REQUIRED if any vehicle types are references in zones_transfer_rules.json. <br/>The following fields are all attributes within the main "data" object for this feed.

Field Name | Presence | Type | Description
---|---|---|---
`vehicle_types` | REQUIRED | Array | Array that contains one object per vehicle type in the system as defined below.
\- `vehicle_type_id` | REQUIRED | ID | Unique identifier of a vehicle type. See [Field Types](#field-types) above for ID field requirements.
\- `max_capacity` | OPTIONAL | Non-Negative Integer | This number denotes the maximum number of riders that the vehicle can carry.
\- `wheelchair_boarding` | OPTIONAL | Enum | This value denotes the possibility of boarding the vehicle with a wheelchair. Valid options are:<br /><ul><li>`boarding_accessible`</li><li>`boarding_inaccessible`</li><li>`boarding_accessible_with_assistance`</li></ul>

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "3.0",
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

The following fields are all attributes within the main "data" object for this feed. The zone data should be a "FeatureCollection" GeoJSON file. 

Field Name | Presence | Type | Description
---|---|---|---
| -&nbsp;`type` | REQUIRED | String | `"FeatureCollection"` of locations. |
| -&nbsp;`features` | REQUIRED | Array | Collection of `"Feature"` objects describing the locations. |
| &emsp;\-&nbsp;`type` | REQUIRED | String | `"Feature"` |
| &emsp;\-&nbsp;`id` | REQUIRED | ID | ID representing the zone. |
| &emsp;\-&nbsp;`properties` | REQUIRED | Object | Location property keys. |
| &emsp;&emsp;\-&nbsp;`name` | OPTIONAL | String | Indicates the name of the zone as displayed to riders. |
| &emsp;\-&nbsp;`geometry` | REQUIRED | Object | Geometry of the location as defined by GeoJSON. |

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "3.0",
  "data": {
    
  }
}
```

### zones_transfer_rules.json

Zone transfer rules contains rules that allows service between zones, including intra-zone service. If service between two zones is not defined, then the service is not possible. 

The following fields are all attributes within the main "data" object for this feed. 

Field Name | Presence | Type | Description
---|---|---|---
`zone_tranfers_rules` | REQUIRED | Array | Array that contains one object per transfer rule as defined below. 
\- `service_id` | OPTIONAL | ID | ID from a service defined in service_information.json. If not provided, the rule applies to every service in the GOFS feed. 
\- `from_zone_id` | REQUIRED | ID | ID from a zone defined in zone.json representing the boarding zone for the current rule. 
\- `to_zone_id` | REQUIRED | ID | ID from a zone defined in zone.json representing the alighting zone for the current rule. 
\- `start_pickup_window` | OPTIONAL | Time | Time at which the pickup is available in `from_zone_id`.
\- `end_pickup_window` | OPTIONAL | Time | Time at which the pickup stops being available in `from_zone_id`.
\- `end_dropoff_window` | OPTIONAL | Time | The last time dropoff is available in `to_zone_id`. Some service allow pickup at a certain hour and can drop off after that time. Ex : Pickup ends at 10PM and the service will be able to drop you off anywhere in the destination zone as long as the drop off happens before 10:30PM. 
\- `calendars` | REQUIRED | Array | Array of IDs from a calendar in calendar.json


##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 0,
  "version": "3.0",
  "data": {
     "zone_tranfers_rules" : [
       {
        "service_id": "route",
        "from_zone_id": "zoneA",
        "to_zone_id": "zoneA",
        "start_pickup_window" : "06:00:00",
        "end_pickup_window": "09:00:00",
        "end_dropoff_window": "09:30:00",
        "calendars": "weekday",
      }
     ]
  }
}
```

### calendar.json

This REQUIRED file is used to describe hours and days of operation when vehicles are available for rental. If `system_hours.json` is not published, it indicates that vehicles are available for rental 24 hours a day, 7 days a week.

Field Name | Presence | Type | Description
---|---|---|---
`calendars` | REQUIRED | Array | Array of objects as defined below. 
\-&nbsp;`id` | REQUIRED | ID | ID for that calendar
\-&nbsp;`days` | OPTIONAL | Array | An array of abbreviations (first 3 letters) of English names of the days of the week for which this object applies (e.g. `["mon", "tue", "wed", "thu", "fri", "sat, "sun"]`). Rental hours MUST NOT be defined more than once for each day and user type. If not defined, it is assumed that the service is active on all days. 
\-&nbsp;`start_date` | Conditionally REQUIRED | Date | Start date for the calendar. Required if `days` is defined. 
\-&nbsp;`end_date` | Conditionally REQUIRED | Date | End date for the calendar. Required if `days` is defined. 
\-&nbsp;`excepted_dates` | OPTIONAL | Array | Array of Date removing service on those dates for the current calendar. 

##### Example:

```jsonc
{
  "last_updated": 1609866247,
  "ttl": 86400,
  "version": "3.0",
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
          "end_date": "20211231",
          "excepted_dates": [
            "20210906",
            "20211225",
          ],
          "additional_dates": [
            "20210906",
            "20211225",
          ],
        },
        {
          "id": "weekends_and_labour_day",
          "days": [
            "sat",
            "sun"
          ],
          "start_date": "20210901",
          "end_date": "20211231",
          "additional_dates": [
            "20210906",
          ],
        }
      ]
    }
  }
}
```