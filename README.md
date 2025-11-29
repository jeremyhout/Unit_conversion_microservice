# Unit Conversion Microservice

The Unit Conversion Microservice is a standalone HTTP service that converts values between various units of measurement. It supports temperature, distance, speed, and pressure conversions with high precision.

## 1. Overview

**Workflow:**

1. A client app calls **GET `/convert`** with a value and source/target units.
2. The service performs the conversion using precise formulas.
3. The service returns the converted value.

**Supported Categories:**
- **Temperature:** Celsius, Fahrenheit, Kelvin
- **Distance:** Meters, Kilometers, Miles, Feet, Inches, Centimeters
- **Speed:** MPH, KPH, Knots, Meters/second
- **Pressure:** hPa, mbar, inHg, mmHg, Pascals

**Use Cases:**
- Convert weather data between units (°F ↔ °C)
- Convert distances for mapping applications
- Convert wind speeds for weather apps
- Convert pressure readings for meteorological data

---

## 2. Running the Microservice

### Install Requirements
```bash
pip install fastapi uvicorn
```

### Start the Unit Conversion Service
```bash
python unit_conversion_service.py
```

By default, it runs on port **6001**. You can specify a custom port:
```bash
python unit_conversion_service.py 6002
```

The service will be available at:
```text
http://127.0.0.1:6001
```

---

## 3. Authentication

This service does **not require authentication** and is open for any client to use.

For production deployments, consider adding API key authentication.

---

## 4. Data Model – Communication Contract

### 4.1. Conversion Request (Query Parameters)
```
GET /convert?value={number}&from={unit}&to={unit}
```

**Parameters:**
- `value` (float, required): The numeric value to convert
- `from` (string, required): Source unit (e.g., "celsius", "miles", "mph")
- `to` (string, required): Target unit (e.g., "fahrenheit", "kilometers", "kph")

### 4.2. Conversion Response
```json
{
  "value": 32.0,
  "from": "fahrenheit",
  "to": "celsius",
  "result": 0.0,
  "category": "temperature"
}
```

**Fields:**
- `value` (float): The original input value
- `from` (string): Source unit (as provided)
- `to` (string): Target unit (as provided)
- `result` (float): Converted value (rounded to 6 decimal places)
- `category` (string): Category of conversion ("temperature", "distance", "speed", "pressure")

---

## 5. Endpoints

### 5.1. Health Check
```http
GET /healthz
```

**Response:**
```json
{
  "status": "healthy",
  "service": "Unit Conversion Microservice",
  "timestamp": "2025-11-27T12:34:56.789012"
}
```

---

### 5.2. Convert Units
```http
GET /convert?value={number}&from={unit}&to={unit}
```

**Example Request (Temperature):**
```http
GET /convert?value=32&from=fahrenheit&to=celsius
```

**Response:**
```json
{
  "value": 32.0,
  "from": "fahrenheit",
  "to": "celsius",
  "result": 0.0,
  "category": "temperature"
}
```

**Example Request (Distance):**
```http
GET /convert?value=100&from=miles&to=kilometers
```

**Response:**
```json
{
  "value": 100.0,
  "from": "miles",
  "to": "kilometers",
  "result": 160.9344,
  "category": "distance"
}
```

**Example Request (Speed):**
```http
GET /convert?value=60&from=mph&to=kph
```

**Response:**
```json
{
  "value": 60.0,
  "from": "mph",
  "to": "kph",
  "result": 96.56064,
  "category": "speed"
}
```

---

### 5.3. List Supported Units
```http
GET /units
```

**Response:**
```json
{
  "categories": ["temperature", "distance", "speed", "pressure"],
  "temperature": ["celsius", "c", "fahrenheit", "f", "kelvin", "k"],
  "distance": ["meters", "m", "kilometers", "km", "miles", "mi", "feet", "ft", "inches", "in", "centimeters", "cm"],
  "speed": ["meters_per_second", "mps", "kilometers_per_hour", "kph", "kmh", "miles_per_hour", "mph", "knots", "kt"],
  "pressure": ["hectopascals", "hpa", "millibars", "mbar", "inches_mercury", "inhg", "millimeters_mercury", "mmhg", "pascals", "pa"]
}
```

**Use:** Get a list of all supported units organized by category.

---

### 5.4. Batch Convert
```http
GET /batch-convert?values={csv}&from={unit}&to={unit}
```

Convert multiple values at once using comma-separated values.

**Example Request:**
```http
GET /batch-convert?values=32,50,100&from=fahrenheit&to=celsius
```

**Response:**
```json
{
  "from": "fahrenheit",
  "to": "celsius",
  "count": 3,
  "conversions": [
    {"value": 32.0, "result": 0.0},
    {"value": 50.0, "result": 10.0},
    {"value": 100.0, "result": 37.777778}
  ]
}
```

---

## 6. Supported Units Reference

### 6.1. Temperature

| Unit | Aliases | Notes |
|------|---------|-------|
| Celsius | celsius, c | Standard metric |
| Fahrenheit | fahrenheit, f | US standard |
| Kelvin | kelvin, k | Absolute scale |

**Examples:**
```
32°F = 0°C = 273.15K
100°C = 212°F = 373.15K
```

---

### 6.2. Distance

| Unit | Aliases | Notes |
|------|---------|-------|
| Meters | meters, m | SI base unit |
| Kilometers | kilometers, km | 1000 meters |
| Miles | miles, mi | 1609.344 meters |
| Feet | feet, ft | 0.3048 meters |
| Inches | inches, in | 0.0254 meters |
| Centimeters | centimeters, cm | 0.01 meters |

**Examples:**
```
1 mile = 1.609 km = 5,280 feet
1 meter = 3.281 feet = 39.37 inches
```

---

### 6.3. Speed

| Unit | Aliases | Notes |
|------|---------|-------|
| Meters/second | meters_per_second, mps | SI unit |
| Kilometers/hour | kilometers_per_hour, kph, kmh | Metric standard |
| Miles/hour | miles_per_hour, mph | US standard |
| Knots | knots, kt | Nautical/aviation |

**Examples:**
```
60 mph = 96.56 kph = 26.82 m/s = 52.14 knots
100 kph = 62.14 mph = 27.78 m/s
```

---

### 6.4. Pressure

| Unit | Aliases | Notes |
|------|---------|-------|
| Hectopascals | hectopascals, hpa | Meteorology standard |
| Millibars | millibars, mbar | Same as hPa |
| Inches Mercury | inches_mercury, inhg | US weather reports |
| Millimeters Mercury | millimeters_mercury, mmhg | Medical use |
| Pascals | pascals, pa | SI unit |

**Examples:**
```
1013.25 hPa = 1013.25 mbar = 29.92 inHg = 760 mmHg
1 inHg = 33.86 hPa
```

---

## 7. Example Client Code

### Python Example (using httpx)
```python
import httpx

CONVERSION_SERVICE = "http://127.0.0.1:6001"

def convert_units(value: float, from_unit: str, to_unit: str):
    """Convert a value between units."""
    try:
        response = httpx.get(
            f"{CONVERSION_SERVICE}/convert",
            params={
                "value": value,
                "from": from_unit,
                "to": to_unit
            },
            timeout=2.0
        )
        response.raise_for_status()
        data = response.json()
        
        print(f"{value} {from_unit} = {data['result']} {to_unit}")
        return data['result']
    
    except httpx.HTTPStatusError as e:
        print(f"Error: {e.response.json()}")
        return None

# Example usage
if __name__ == "__main__":
    # Temperature conversion
    convert_units(32, "fahrenheit", "celsius")
    
    # Distance conversion
    convert_units(100, "miles", "kilometers")
    
    # Speed conversion
    convert_units(60, "mph", "kph")
    
    # Batch conversion
    response = httpx.get(
        f"{CONVERSION_SERVICE}/batch-convert",
        params={
            "values": "32,50,100",
            "from": "fahrenheit",
            "to": "celsius"
        }
    )
    print(response.json())
```

### JavaScript Example (using fetch)
```javascript
async function convertUnits(value, fromUnit, toUnit) {
  try {
    const url = `http://127.0.0.1:6001/convert?value=${value}&from=${fromUnit}&to=${toUnit}`;
    const response = await fetch(url);
    
    if (!response.ok) {
      const error = await response.json();
      throw new Error(error.detail);
    }
    
    const data = await response.json();
    console.log(`${value} ${fromUnit} = ${data.result} ${toUnit}`);
    return data.result;
  } catch (error) {
    console.error('Conversion failed:', error);
    return null;
  }
}

// Example usage
convertUnits(32, 'fahrenheit', 'celsius');  // 0°C
convertUnits(100, 'miles', 'kilometers');   // 160.9344 km
convertUnits(60, 'mph', 'kph');             // 96.56 kph
```

### cURL Examples
```bash
# Temperature conversion
curl "http://127.0.0.1:6001/convert?value=32&from=fahrenheit&to=celsius"

# Distance conversion
curl "http://127.0.0.1:6001/convert?value=100&from=miles&to=kilometers"

# Speed conversion
curl "http://127.0.0.1:6001/convert?value=60&from=mph&to=kph"

# Batch conversion
curl "http://127.0.0.1:6001/batch-convert?values=32,50,100&from=fahrenheit&to=celsius"

# List all units
curl "http://127.0.0.1:6001/units"

# Health check
curl "http://127.0.0.1:6001/healthz"
```

---

## 8. Client Library (Optional)

For easier integration, you can use the included `unit_conversion_client.py`:
```python
from unit_conversion_client import get_conversion_client

# Initialize client
client = get_conversion_client()

# Single conversion
result = client.convert(32, 'fahrenheit', 'celsius')
print(f"Result: {result}°C")  # 0.0°C

# Batch conversion
results = client.batch_convert([32, 50, 100], 'fahrenheit', 'celsius')
print(f"Results: {results}")  # [0.0, 10.0, 37.78]

# Get supported units
units = client.get_supported_units()
print(f"Temperature units: {units['temperature']}")

# Health check
is_healthy = client.health_check()
print(f"Service healthy: {is_healthy}")
```

---

## 9. Integration Guide

### Step 1: Start the Service
```bash
python unit_conversion_service.py
```

### Step 2: Make Conversion Requests
```http
GET /convert?value=YOUR_VALUE&from=SOURCE_UNIT&to=TARGET_UNIT
```

### Step 3: Handle Responses
```python
response = requests.get(url, params={
    "value": 32,
    "from": "fahrenheit",
    "to": "celsius"
})

if response.status_code == 200:
    data = response.json()
    converted_value = data['result']
else:
    error = response.json()
    print(f"Error: {error['detail']}")
```

### Step 4: Use Converted Values
```javascript
// Weather app example
const tempC = 20;  // From API
const tempF = await convertUnits(tempC, 'celsius', 'fahrenheit');
displayTemperature(`${tempF}°F`);
```

---

## 10. Error Handling

### Common Errors

**400 Bad Request - Unknown Unit:**
```json
{
  "detail": "Unknown unit: celcius"
}
```
- Typo in unit name
- Solution: Check `/units` endpoint for valid unit names

**400 Bad Request - Incompatible Units:**
```json
{
  "detail": "Cannot convert celsius to miles - different categories"
}
```
- Trying to convert between different categories
- Solution: Only convert within same category (temp↔temp, distance↔distance, etc.)

**422 Unprocessable Entity - Missing Parameters:**
```json
{
  "detail": [
    {
      "loc": ["query", "value"],
      "msg": "field required"
    }
  ]
}
```
- Missing required parameters
- Solution: Include all required query parameters

---

## 11. Conversion Formulas

### Temperature
```
Celsius to Fahrenheit: (C × 9/5) + 32
Fahrenheit to Celsius: (F - 32) × 5/9
Celsius to Kelvin: C + 273.15
Kelvin to Celsius: K - 273.15
```

### Distance
All conversions go through meters as base:
```
Miles to Meters: mi × 1609.344
Feet to Meters: ft × 0.3048
Inches to Meters: in × 0.0254
```

### Speed
All conversions go through m/s as base:
```
MPH to m/s: mph × 0.44704
KPH to m/s: kph / 3.6
Knots to m/s: kt × 0.514444
```

### Pressure
All conversions go through hPa as base:
```
inHg to hPa: inHg × 33.8639
mmHg to hPa: mmHg × 1.33322
Pa to hPa: Pa / 100
```

---

## 12. Performance Notes

- **Response Time:** < 10ms for single conversions
- **Precision:** Results rounded to 6 decimal places
- **Batch Limit:** Recommended max 100 values per batch request
- **Caching:** Client-side caching recommended for repeated conversions
- **Timeout:** Default client timeout is 2 seconds

---

## 13. Unit Aliases

The service accepts multiple aliases for convenience:

| Category | Full Name | Short Aliases |
|----------|-----------|---------------|
| Temperature | celsius | c |
| Temperature | fahrenheit | f |
| Temperature | kelvin | k |
| Distance | meters | m |
| Distance | kilometers | km |
| Distance | miles | mi |
| Distance | feet | ft |
| Distance | inches | in |
| Distance | centimeters | cm |
| Speed | kilometers_per_hour | kph, kmh |
| Speed | miles_per_hour | mph |
| Speed | meters_per_second | mps |
| Speed | knots | kt |
| Pressure | hectopascals | hpa |
| Pressure | millibars | mbar |
| Pressure | inches_mercury | inhg |
| Pressure | millimeters_mercury | mmhg |
| Pressure | pascals | pa |

**Examples:**
```
/convert?value=100&from=f&to=c        ✅ Works
/convert?value=100&from=km&to=mi      ✅ Works
/convert?value=100&from=kph&to=mph    ✅ Works
```

---

## 14. Production Deployment Checklist

- [ ] Add API key authentication
- [ ] Implement rate limiting (prevent abuse)
- [ ] Add request logging
- [ ] Set up monitoring/health checks
- [ ] Use HTTPS (not HTTP)
- [ ] Configure proper CORS origins
- [ ] Set up error alerting
- [ ] Document your specific port/URL
- [ ] Add input validation limits (max value size)

---

## 15. Troubleshooting

### Service won't start

**Error:** `Address already in use`

**Solution:**
```bash
# Use a different port
python unit_conversion_service.py 6002
```

### Incorrect conversion results

**Possible Causes:**
- Using incompatible units
- Typo in unit name

**Solution:**
- Check `/units` endpoint for valid unit names
- Verify units are in same category
- Use unit aliases if needed

### Batch conversion fails

**Error:** 400 Bad Request

**Possible Causes:**
- Invalid CSV format
- Non-numeric values
- Too many values

**Solution:**
- Ensure values are comma-separated numbers: "1,2,3"
- No spaces: "1, 2, 3" ❌ → "1,2,3" ✅
- Limit to reasonable batch sizes (< 100)

---

## 16. API Specification Summary

| Endpoint | Method | Auth | Purpose |
|----------|--------|------|---------|
| `/healthz` | GET | No | Health check |
| `/convert` | GET | No | Convert single value |
| `/batch-convert` | GET | No | Convert multiple values |
| `/units` | GET | No | List supported units |

**Base URL:** `http://127.0.0.1:6001` (default)

**Query Parameters:**
- `value` (float, required for /convert)
- `values` (string, required for /batch-convert)
- `from` (string, required): Source unit
- `to` (string, required): Target unit

**Response Format:** JSON

**Content-Type:** `application/json`

---

## 17. Example Use Cases

### Weather Application
```python
# Convert temperatures from API (Celsius) to user preference (Fahrenheit)
temp_c = 20  # From weather API
temp_f = convert(temp_c, 'celsius', 'fahrenheit')
display(f"{temp_f}°F")
```

### Fitness Tracker
```python
# Convert distance from miles to kilometers
distance_mi = 5.2  # User ran 5.2 miles
distance_km = convert(distance_mi, 'miles', 'kilometers')
display(f"You ran {distance_km:.2f} km")
```

### Aviation App
```python
# Convert wind speed from knots to mph
wind_kt = 15  # Wind speed in knots
wind_mph = convert(wind_kt, 'knots', 'mph')
display(f"Wind: {wind_mph:.1f} mph")
```

### Weather Station
```python
# Convert pressure from inHg to hPa
pressure_inhg = 29.92
pressure_hpa = convert(pressure_inhg, 'inhg', 'hpa')
display(f"Pressure: {pressure_hpa:.1f} hPa")
```

---

**End of README**
