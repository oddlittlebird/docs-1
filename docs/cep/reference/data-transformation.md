# Transforming Data

## Math & Logical Operations

Provides examples on performing math or logical operations on events.

**Example:**

```
define stream TemperatureStream (sensorId string, temperature double);

@info(name = 'celciusTemperature')

-- Converts Celsius value into Fahrenheit.
select sensorId, (temperature * 9 / 5) + 32 as temperature
from TemperatureStream
insert into FahrenheitTemperatureStream;


@info(name = 'Overall-analysis')
-- Calculate approximated temperature to the first digit 
select sensorId, math:floor(temperature) as approximateTemp 
from FahrenheitTemperatureStream
insert all events into OverallTemperatureStream;

@info(name = 'RangeFilter') 
-- Filter out events where `-2 < approximateTemp < 40`
select *
from OverallTemperatureStream[ approximateTemp > -2 and approximateTemp < 40]
insert into NormalTemperatureStream;
```

**Input:**

Below event is sent to `TemperatureStream`,

[`'SensorId'`, `-17`]

**Output:**

After processing, the following events will be arriving at each stream:

* FahrenheitTemperatureStream: [`'SensorId'`, `1.4`]
* OverallTemperatureStream: [`'SensorId'`, `1.0`]
* NormalTemperatureStream: [`'SensorId'`, `1.0`]

## Transform JSON

Provides examples on transforming JSON object within Stream App.

**Example:**

```
define stream InputStream(jsonString string);

-- Transforms JSON string to JSON object which can then be manipulated
select json:toObject(jsonString) as jsonObj 
from InputStream 
insert into PersonalDetails;


select jsonObj, 
-- Get the `name` element(string) form the JSON
    json:getString(jsonObj,'$.name') as name,

-- Validate if `salary` element is available
    json:isExists(jsonObj, '$.salary') as isSalaryAvailable,

-- Stringify the JSON object
    json:toString(jsonObj) as jsonString
from PersonalDetails
insert into OutputStream;


-- Set `salary` element to `0` is not available 
select json:setElement(jsonObj, '$', 0f, 'salary') as jsonObj
from OutputStream[isSalaryAvailable == false]
insert into PreprocessedStream;
```

**Input:**

Below event is sent to `InputStream`,

```json
[
    {
        "name" : "streamapp.user",
        "address" : {
            "country": "USA"
        },
        "contact": "+9xxxxxxxx"
    }
]
```

**Output:**

After processing, the following events will be arriving:

* OutputStream:

```json
[ 
    {
        "address": {
            "country":"USA"
        },
        "contact":"+9xxxxxxxx",
        "name":"streamapp.user"
    }
]
```

* PreprocessedStream:

```json
[
    {
        "name" : "streamapp.user",
        "salary": 0.0,
        "address" : {
            "country": "USA"
        },
        "contact": "+9xxxxxxxx"
    }
]
```