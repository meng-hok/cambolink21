### Step 1: Get Available Locations
```sh
curl --location 'https://hok-proxy.acetoz.com/mobile/api/v1/locations'
```
**Response:**
- `_id`: Unique identifier for the document.
- `name`: Name of the location in Khmer.
- `coordinate`: Object containing coordinates for the location, with `lon` (longitude) and `lat` (latitude) values.
- `en_name`: English name of the location.
- `km_name`: Khmer name of the location.
- `address`: Branch address.

### Step 2: Get Available Trips
```sh
curl --location 'https://hok-proxy.acetoz.com/mobile/api/v2/trips?travel_date=2024-8-23&to_location=Phnom%20Penh&from_location=Poi%20Pet&intf=nham24'
```
**Request Parameters:**
- `from_location`: Departure location.
- `to_location`: Arrival location.
- `travel_date`: Departure date.
- `intf`: Interface identifier (set to "nham24" for vendor nham24).

**Response:**
- `_id`: Unique identifier for the document.
- `master_id`: Trip parent ID.
- `travel_info`: Contains information about the travel:
  - `departure`: Departure location (e.g., "Battombong").
  - `departure_time`: Time of departure (e.g., "06:30").
  - `arrival`: Arrival location (e.g., "Phnom Penh-HQ").
  - `arrival_time`: Time of arrival (e.g., "14:30").
  - `seat_left`: The number of available seats left (e.g., 40).
  - `number_of_seat`: The total number of seats (e.g., 36).
  - `pricing`: Pricing information:
    - `value`: Price value (e.g., 16).
    - `currency`: Currency of the price (e.g., "USD").
  - `vehicle`: Details about the vehicle:
    - `name`: Name of the vehicle (e.g., "Hyundai_BUS").
    - `stories`: Contains transportation layout:
      - `rows`: Number of rows (e.g., 4).
      - `column`: Number of columns (e.g., 2).
      - `X`: No Seat.
      - `B`: Booked/Reserved.
      - `A1, A2...`: Available for booking.

### Step 2.1 (Optional): Get Trip Detail
```sh
curl --location 'https://hok-proxy.acetoz.com/mobile/api/v2/trips/66975e901bbcb152fd71e2bf?from_location=Phnom%20Penh&to_location=Poi%20Pet'
```
**Request Parameters:**
- `from_location`: Departure location.
- `to_location`: Arrival location.
- `trip_id`: Trip Id.

**Response:**
- `_id`: Unique identifier for the document.
- `master_id`: Trip parent ID.
- `travel_info`: Contains information about the travel:
  - `departure`: Departure location (e.g., "Battombong").
  - `departure_time`: Time of departure (e.g., "06:30").
  - `arrival`: Arrival location (e.g., "Phnom Penh-HQ").
  - `arrival_time`: Time of arrival (e.g., "14:30").
  - `seat_left`: The number of available seats left (e.g., 40).
  - `number_of_seat`: The total number of seats (e.g., 36).
  - `pricing`: Pricing information:
    - `value`: Price value (e.g., 16).
    - `currency`: Currency of the price (e.g., "USD").
  - `vehicle`: Details about the vehicle:
    - `name`: Name of the vehicle (e.g., "Hyundai_BUS").
    - `stories`: Contains transportation layout:
      - `rows`: Number of rows (e.g., 4).
      - `column`: Number of columns (e.g., 2).
      - `X`: No Seat.
      - `B`: Booked/Reserved.
      - `A1, A2...`: Available for booking.

### Step 3: Pre-Checkout
```sh
curl --location 'https://hok-proxy.acetoz.com/mobile/api/v2/tickets/checkout' \
--header 'Content-Type: application/json' \
--data-raw '{
    "trip_id": "64bb46a0bdcbe9b07865f9e1",
    "to_location": "Phnom Penh",
    "from_location": "Poi Pet",
    "price": {"currency": "USD", "value": 2},
    "total_price": {"currency": "USD", "value": 4},
    "seat_no": ["A15", "A16"],
    "coupon": "",
    "email": "info@cambotra.com",
    "phone_number": "85560123456",
    "client_name": "Daniel Isaac",
    "gender" : "Male"
}'
```
**Request Parameters:**
- `coupon`: Coupon code provided by Cambolink.
- `price`: Ticket gross price.
- `total_price`: Total ticket net price (e.g., ticket price 2 USD * seat 2 = total_price 4 USD).
- `trip_id`: Target trip ID for travelling.
- `from_location`: Departure location.
- `to_location`: Arrival location.
- `email`: Client email.
- `phone_number`: Client phone number.
- `client_name`: Client name.
- `gender`: Male or Female.


**Response:**
- `data`: List of bought tickets.
- `checkout_transaction`: Object containing checkout information:
  - `_id`: Store as checkout_id to trigger in the next step.

### Step 4: Confirm from Response Checkout ID
```sh
curl --location 'https://hok-proxy.acetoz.com/mobile/api/v2/tickets/trigger' \
--header 'Content-Type: application/json' \
--data '{
    "status": "CANCEL", // or "APPROVE"
    "checkout_id": "645137385582e93d1e2c2ba3"
}'
```
**Request Parameters:**
- `status`: 
  - `APPROVE`: In case user has completed payment.
  - `CANCEL`: In case payment was not successful.
- `checkout_id`: ID obtained from STEP 3 (`checkout_transaction._id`).

### Step 5: Get Ticket Following Checkout ID
```sh
curl 'https://hok-proxy.acetoz.com/mobile/api/v1/tickets?checkout_id='
```
**Request Parameters:**
- `checkout_id`: ID obtained from STEP 3 (`checkout_transaction._id`).

### Check Coupon
```sh
curl 'https://hok-proxy.acetoz.com/mobile/api/v1/coupons/check?coupon_code=hahaha'
```
**Request Parameters:**
- `coupon_code`: Coupon code to check (e.g., "hahaha").
