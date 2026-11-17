
# RESTful API Planning: Vehicle Safety Dashboard

## 1. API Selection

For this project, I chose the National Highway Traffic Safety Administration (NHTSA) Vehicle Product Information Catalog (vPIC) API. This public API provides vehicle information such as manufacturer, model, model year, and vehicle specifications.

I chose this API because vehicle information is useful to everyday drivers, and I have already worked with NHTSA data in a previous assignment. My goal is to design a simple dashboard where users can search for vehicles, review available specifications, and save vehicles they want to reference later.

The API follows REST-style principles by identifying vehicle resources through URLs and using HTTP GET requests to retrieve information. Each request is stateless, meaning the server does not need to remember previous requests to process a new one.

Public API documentation:
https://vpic.nhtsa.dot.gov/api/

## 2. Endpoint Mapping

The application will use the following endpoints.

### External NHTSA API

**GET /api/vehicles/GetMakesForVehicleType/car?format=json**

- Retrieves vehicle manufacturers associated with passenger cars.
- Returns JSON containing manufacturer information.
- Populates the manufacturer dropdown.

**GET /api/vehicles/GetModelsForMakeYear/make/{make}/modelyear/{year}?format=json**

- Retrieves available vehicle models for a selected manufacturer and model year.
- Returns JSON containing model information.
- Populates the model dropdown.

**GET /api/vehicles/DecodeVinValues/{vin}?format=json**

- Decodes a Vehicle Identification Number (VIN).
- Returns available vehicle details and specifications.
- Provides an alternative way to search for a vehicle.

These endpoints use GET because they retrieve existing resources without modifying server data.

### Proposed Python Backend

The following endpoints will be designed for a future Flask backend.

**GET /api/saved-vehicles**

Returns the user's saved vehicle records.

**POST /api/saved-vehicles**

Accepts a JSON object containing vehicle information and creates a saved record.

**DELETE /api/saved-vehicles/{id}**

Deletes a saved vehicle by its unique identifier.

These routes use resource-based URIs and standard HTTP methods. Requests will remain stateless, with any required identification or authentication provided independently in each request.

The backend is only being planned for this assignment, not implemented.

## 3. Data Flow Diagram (Textual)

The planned application will follow this sequence:

User opens dashboard
    |
    v
JavaScript displays loading indicator
    |
    v
Fetch API sends asynchronous GET request
    |
    v
NHTSA API returns JSON response
    |
    v
JavaScript checks response.ok
    |
    +-- Request failed --> Display error message
    |
    +-- Request succeeded --> Parse JSON
                                |
                                v
                       Validate response data
                                |
                                v
                       Render vehicle information
                                |
                                v
                       Apply conditional CSS styling

JavaScript's fetch() function will retrieve data asynchronously without requiring the entire page to reload.

Example request:

```javascript
async function getVehicleMakes() {
    const status = document.getElementById("status");
    status.textContent = "Loading vehicles...";

    try {
        const response = await fetch(
            "https://vpic.nhtsa.dot.gov/api/vehicles/GetMakesForVehicleType/car?format=json",
            {
                method: "GET",
                headers: {
                    "Accept": "application/json"
                }
            }
        );

        if (!response.ok) {
            throw new Error("Unable to retrieve vehicle data.");
        }

        const data = await response.json();
        renderMakes(data.Results);
        status.textContent = "";

    } catch (error) {
        status.textContent = error.message;
    }
}
```

### Dynamic Rendering

The dashboard will use JavaScript to map JSON response objects into HTML elements.

Manufacturer names will populate a dropdown, while selected vehicle information will appear in a table.

For example, a decoded VIN might display:

| Field | Value |
|---|---|
| Manufacturer | Toyota |
| Model | Camry |
| Model Year | 2024 |
| Vehicle Type | Passenger Car |

### Conditional Styling

The dashboard will apply different CSS classes depending on the returned data.

- Valid VIN results: green success styling.
- Missing vehicle information: yellow warning styling.
- Failed requests or invalid VIN results: red error styling.
- Loading requests: neutral loading indicator.

The application will inspect the NHTSA response for missing fields and decoding error indicators rather than assuming every successful HTTP response contains valid vehicle information.

## 4. Form Handling

The dashboard will include two forms.

### Vehicle Search Form

**Manufacturer**
- Required dropdown.
- Must match a manufacturer returned by the API.

**Model Year**
- Required numeric input.
- Must contain four digits.
- Must fall within a reasonable supported year range.

**Model**
- Required dropdown.
- Must match a model returned for the selected manufacturer and year.

**VIN Search**
- Optional alternative search method.
- Must contain exactly 17 characters when provided.
- Only valid VIN characters will be accepted.

The application will validate user input before sending requests and display helpful messages when information is missing or incorrectly formatted.

### Save Vehicle Form

Users will be able to save a selected vehicle using these fields:

- Manufacturer: required string.
- Model: required string.
- Model year: required integer.
- VIN: optional 17-character string.
- Notes: optional text, maximum 250 characters.

JavaScript will collect the form values and convert them into JSON using JSON.stringify().

Example JSON payload:

```json
{
    "manufacturer": "Toyota",
    "model": "Camry",
    "model_year": 2024,
    "vin": "",
    "notes": "Vehicle for future reference"
}
```

The client will submit this payload using POST /api/saved-vehicles with the Content-Type: application/json header.

The proposed Flask backend will validate the JSON again before saving it. A successful creation will return HTTP 201, while invalid input will return HTTP 400.

The backend and database will not be built during this planning assignment.

## 5. Technology Stack

The planned application will use:

**Frontend**
- HTML5 for page structure.
- CSS3 for layout and conditional styling.
- JavaScript for user interaction.
- Fetch API for asynchronous HTTP requests.

**Backend**
- Python and Flask for proposed REST endpoints.
- SQLite for future saved-vehicle storage.

**Development Tools**
- Visual Studio Code for development.
- Git and GitHub for version control.
- Browser Developer Tools for debugging.
- Postman for API testing.

**Testing**

I will test valid and invalid VIN values, missing form fields, unsuccessful HTTP responses, and empty API results.

I will also verify that loading indicators appear during requests and disappear after success or failure.

## 6. Implementation Steps

### Phase 1: Project Setup

1. Create a new GitHub repository.
2. Organize the proposed frontend and backend file structure.
3. Review the NHTSA API documentation.
4. Identify required endpoints and response fields.

### Phase 2: Fetch GET Integration

1. Create the dashboard's basic HTML layout.
2. Write JavaScript fetch() functions.
3. Request vehicle data from NHTSA.
4. Add loading indicators and error handling.
5. Confirm successful JSON parsing.

### Phase 3: Dynamic Rendering

1. Populate manufacturer and model dropdowns.
2. Display vehicle information in a table.
3. Handle missing or incomplete data.
4. Add conditional styling for success, warning, and error states.

### Phase 4: Forms and Validation

1. Build the vehicle search form.
2. Add VIN input validation.
3. Create the save vehicle form.
4. Convert submitted values into JSON.
5. Define the POST contract for the future Flask backend.

### Phase 5: Integration and Review

1. Test the application using multiple vehicle searches.
2. Verify loading and error messages.
3. Check form validation behavior.
4. Review REST principles and endpoint naming.
5. Document setup instructions and expected functionality.

### Phase 6: Deployment

The planned frontend will be hosted using GitHub Pages or another static hosting platform.

If the proposed Flask backend is implemented later, it will require a separate Python-compatible hosting environment.

Before deployment, I will verify that external API requests work from the hosted frontend and that any cross-origin restrictions are handled appropriately.

## Conclusion

This project demonstrates how a RESTful client can retrieve and display information from an independent public API.

The design emphasizes stateless requests, resource-based endpoints, JSON data exchange, asynchronous fetching, and user-friendly error handling.

Although the backend is not being implemented for this assignment, the planning document establishes a clear foundation for building and testing the complete application.
