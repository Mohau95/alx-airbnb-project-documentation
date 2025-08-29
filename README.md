# alx-airbnb-project-documentation
#!/bin/bash

set -e

# Prompt for PNG file paths
read -p "Path to features-and-functionalities.png: " FEATURES_PNG
read -p "Path to use-case-diagram.png: " USECASE_PNG
read -p "Path to data-flow.png: " DATAFLOW_PNG
read -p "Path to flowchart.png: " FLOWCHART_PNG

# Validate PNG files
for file in "$FEATURES_PNG" "$USECASE_PNG" "$DATAFLOW_PNG" "$FLOWCHART_PNG"; do
    if [ ! -f "$file" ]; then
        echo "Error: File $file not found."
        exit 1
    fi
done

# Create directories
mkdir -p features-and-functionalities use-case-diagram user-stories data-flow-diagram flowcharts

# Copy PNG files
cp "$FEATURES_PNG" features-and-functionalities/features-and-functionalities.png
cp "$USECASE_PNG" use-case-diagram/use-case-diagram.png
cp "$DATAFLOW_PNG" data-flow-diagram/data-flow.png
cp "$FLOWCHART_PNG" flowcharts/flowchart.png

# 0. Documenting Project Features and Functionalities
echo "# Features and Functionalities

This directory contains documentation for the Airbnb Clone backend features and functionalities.

## Features
- **User Authentication**:
  - Register new users with email, password, name, role (guest/host/admin).
  - Login with email/password, return JWT token.
  - Password reset via email.
  - Profile management (update name, photo, contact info).
- **Property Management**:
  - Hosts create/edit/delete property listings (title, description, location, price, amenities, availability).
  - Search properties by location, price, or amenities.
  - View property details, including availability calendar.
- **Booking System**:
  - Guests book properties for specific dates.
  - Validate availability (prevent overlapping bookings).
  - Cancel bookings (with refund rules).
  - View booking history.
- **Payments**:
  - Process payments for bookings (via Stripe/PayPal).
  - Support multiple currencies (e.g., USD, ZAR).
  - Handle refunds for cancellations.
  - Generate payment receipts.
- **Reviews**:
  - Guests submit ratings (1-5) and comments after bookings.
  - Hosts view reviews for their properties.
  - Publicly display average ratings.

See [features-and-functionalities.png](features-and-functionalities.png) for a visual overview." > features-and-functionalities/README.md

# 1. Design the Use Case Diagram of the Features and Functionalities
echo "# Use Case Diagram

This directory contains the use case diagram for the Airbnb Clone backend.

## Description
The diagram captures interactions between actors (Guest, Host, Admin) and the system for key functionalities:
- **Actors**: Guest, Host, Admin.
- **Use Cases**:
  - Guest: Register, Login, Search Properties, Book Property, Pay for Booking, Write Review.
  - Host: Register, Login, Manage Properties, View Bookings, View Reviews.
  - Admin: Manage Users, Moderate Properties, Process Refunds.

See [use-case-diagram.png](use-case-diagram.png) for the visual diagram." > use-case-diagram/README.md

# 2. Convert Your Use Case Diagram to User Stories
echo "# User Stories

This directory contains user stories derived from the use case diagram.

## User Stories
1. **As a Guest**, I want to register an account with my email and password so that I can access booking features.
2. **As a Guest**, I want to search properties by location and price so that I can find a suitable place to stay.
3. **As a Guest**, I want to book a property for specific dates so that I can secure my accommodation.
4. **As a Guest**, I want to pay for my booking securely so that I can confirm my reservation.
5. **As a Host**, I want to create and manage property listings so that I can attract guests.
6. **As a Host**, I want to view bookings for my properties so that I can manage my schedule.
7. **As an Admin**, I want to moderate property listings so that they meet platform standards." > user-stories/user-stories.md

echo "# User Stories Directory
See [user-stories.md](user-stories.md) for the list of user stories." > user-stories/README.md

# 3. Design a Data Flow Diagram for Your Features and Functionalities
echo "# Data Flow Diagram

This directory contains the data flow diagram (DFD) for the Airbnb Clone backend.

## Description
The DFD illustrates data movement:
- **Entities**: Users, Properties, Bookings, Payments, Reviews.
- **Processes**: Register User, Search Properties, Create Booking, Process Payment, Submit Review.
- **Data Stores**: User DB, Property DB, Booking DB, Payment DB, Review DB.
- **Data Flows**: User inputs (email, password), property details, booking requests, payment transactions, review submissions.

See [data-flow.png](data-flow.png) for the visual DFD." > data-flow-diagram/README.md

# 4. Design a Flowchart for System Processes
echo "# Flowchart

This directory contains a flowchart for the booking process.

## Description
The flowchart maps the booking process:
- Guest searches properties.
- Selects property and dates.
- System checks availability (no overlapping bookings).
- Guest confirms booking and makes payment.
- System updates booking status and sends confirmation.

See [flowchart.png](flowchart.png) for the visual flowchart." > flowcharts/README.md

# 5. Write Requirement Specifications for Backend Features
echo "# Requirement Specifications for Backend Features

## 1. User Authentication
- **API Endpoints**:
  - POST /api/auth/register: Create user (email, password, name, role).
    - Input: JSON { "email": string, "password": string, "name": string, "role": enum(guest,host,admin) }
    - Output: JSON { "user_id": uuid, "token": string }
    - Validation: Email unique, password >8 chars, role valid.
  - POST /api/auth/login: Authenticate user.
    - Input: JSON { "email": string, "password": string }
    - Output: JSON { "token": string }
    - Validation: Email/password match.
  - PUT /api/auth/profile: Update profile.
    - Input: JSON { "name": string, "photo": url, "contact_info": string }
    - Output: JSON { "user_id": uuid, "updated": boolean }
- **Performance**: <500ms response time, 99.9% uptime.
- **Security**: JWT tokens, hashed passwords (bcrypt).

## 2. Property Management
- **API Endpoints**:
  - POST /api/properties: Create property (host only).
    - Input: JSON { "title": string, "description": text, "location": string, "price": decimal, "amenities": json, "availability": json }
    - Output: JSON { "property_id": uuid }
    - Validation: Price >0, valid JSON for amenities/availability.
  - GET /api/properties/search: Search properties.
    - Input: Query params (location, price_min, price_max, amenities)
    - Output: JSON array of properties.
  - PUT /api/properties/{id}: Update property (host only).
  - DELETE /api/properties/{id}: Delete property (host only).
- **Performance**: Search <1s for 1000 properties.
- **Security**: Host-only access for create/update/delete.

## 3. Booking System
- **API Endpoints**:
  - POST /api/bookings: Create booking (guest only).
    - Input: JSON { "property_id": uuid, "guest_id": uuid, "start_date": date, "end_date": date }
    - Output: JSON { "booking_id": uuid }
    - Validation: End_date > start_date, no overlapping bookings.
  - GET /api/bookings/{id}: View booking details.
  - PUT /api/bookings/{id}/cancel: Cancel booking.
    - Output: JSON { "booking_id": uuid, "status": "canceled" }
- **Performance**: Booking creation <300ms.
- **Constraints**: Prevent double bookings via database checks." > requirements.md

echo "Success: All files created in alx-airbnb-project-documentation."
