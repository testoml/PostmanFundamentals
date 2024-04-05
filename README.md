# Postman Fundamentals
Postman is a popular tool used for testing APIs (Application Programming Interfaces) and for building, sharing, and documenting APIs. It provides an intuitive interface for sending HTTP requests, organizing APIs, and automating testing workflows. Here are some fundamentals of Postman:

- **HTTP Requests**: Postman allows users to send various types of HTTP requests such as GET, POST, PUT, DELETE, PATCH, etc., to interact with APIs. Users can specify parameters, headers, and request bodies as needed.
- **Collections**: Collections are groups of saved requests that can be organized hierarchically. They help in organizing and managing API requests efficiently. Collections can also be exported and shared with others.
- **Request Builder**: Postman provides a user-friendly interface for building HTTP requests. Users can easily add headers, parameters, and request bodies to their requests.
- **Variables and Environments**: Postman allows users to define variables that can be used across requests. This enables dynamic data handling and parameterization. Environments provide a way to manage variables for different environments such as development, staging, and production.
- **Tests**: Postman allows users to write tests using JavaScript. These tests can be executed after sending a request to validate the response. Tests can check status codes, response body content, headers, etc.
- **Pre-request Scripts and Post-request Scripts**: Users can write scripts that run before sending a request (pre-request scripts) or after receiving a response (post-request scripts). These scripts can be used for tasks such as generating dynamic data, setting environment variables, or extracting data from responses.
- **Assertions**: Postman provides built-in assertions for validating responses. Users can add assertions to tests to ensure that the response meets certain criteria.
- **Documentation**: Postman allows users to generate documentation for their APIs. This documentation includes details such as request endpoints, parameters, headers, sample requests, and responses. It helps in sharing API documentation with team members or external developers.
- **Collaboration**: Postman provides features for team collaboration such as shared workspaces, version control, and commenting. This enables teams to work together on API development and testing.
- **Automation**: Postman can be integrated with continuous integration/continuous deployment (CI/CD) pipelines for automating API testing and monitoring. It supports integration with tools like Jenkins, Newman, and others.

## API TESTING IMPLEMENTATION
### Strategy
> Automation testing based on: [Open Weather](https://openweathermap.org/) (Create user and key.)
- Check all scenarios within [link](https://openweathermap.org/triggers)
  - Create a trigger - Unauthorized (401)
  - Create a trigger - Not Found (404)
  - Create a trigger - Bad Request (400)
  - Successful - Create a trigger (201)
  - Successful - Get all triggers (200)
  - Successful - Update Trigger
  - Successful - Delete Trigger
 
### Installation and Setup:
Download and install Postman from the official [website](https://www.postman.com/downloads/).
Sign up for a free account to access additional features and cloud syncing capabilities.

### Create Collection and Request
Creating Collections:
1. Open Postman: Launch the Postman application on your computer.
2. Navigate to Collections: In the left sidebar, click on the "Collections" tab.
3. Create New Collection:
   - Click on the "New Collection" button.
   - Enter a name for your collection.
   - Optionally, describe the collection.
   - Click "Create" to create the collection.
4. Add Requests to Collection:
   - Once the collection is created, you can start adding requests to it.
   - Click on the collection name to open it.
   - Click on the "Add Request" button.
   - Enter a name for the request.
     
### Set variables and Environment
Regarding the trigger endpoint, the variables are required: 
- baseURL : https://api.openweathermap.org/data
- id: id of trigger created
- key: appid 
- value: key value

#### Create a trigger - Unauthorized (401)
- Set Url : {{baseURL}}/triggers
- Method: POST
- Header: Content-Type: application/json
- Expected Response:
```sh
{
    "cod": 401,
    "message": "Invalid API key. Please see https://openweathermap.org/faq#error401 for more info."
}
```
- TESTS - Create a trigger - Unauthorized (401)
```sh
pm.test("Status code is 401", function () {
    pm.response.to.have.status(401);
});

pm.test("Validate Message", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.message).to.eql("Invalid API key. Please see https://openweathermap.org/faq#error401 for more info.");
});
```
#### Create a trigger - Bad Request (400)
- Set Url : {{baseURL}}/3.0/triggers&{{key}}={{value}}
- Method: POST
- Header: Content-Type: application/json
- Expected Response:
```sh
{
Not found
}
```
- TESTS - Create a trigger - Unauthorized (401)
```sh
pm.test("Status code is 404", function () {
    pm.response.to.have.status(404);
});
```
> Common configuration for response status equal to 404, 201
- Authorization: APIKey
- Params:
  - Key: appid
  - Value: {your api key}
- Header: Content-Type: application/json    

#### Create a trigger - Not Found (404)
- url: {{baseURL}}/3.0/trigger
- Method: POST
- Expected Response:
```sh
{
    "cod": "404",
    "message": "Internal error"
}
```
- TESTS - Create a trigger - Not Found (404)
```sh
//Test
pm.test("Status code is 404", function () {
    pm.response.to.have.status(404);
});
pm.test("Your test name", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.message).to.eql("Internal error");
});
```
#### Create a trigger - Bad Request (400)
- Url: {{baseURL}}/3.0/triggers
- Method: POST
- Body:
```sh
{
   "time_period":{
      "start":{
         "expression":"",
         "amount":0
      },
      "end":{
         "expression":"",
         "amount":0
      }
   }
}
```
- Expected Response:
```sh
Time_period start should to have "expression" param.
```
- TESTS - Successful - Create a trigger (200)
```sh
//Variables
const response = pm.response.json();
const body = pm.environment.get('body');
const id = response["_id"];
//Test
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Validate payload", function () {
    pm.expect(body).to.contains(body);
});

pm.test("Validate id distinct 0", function () {
    pm.expect(id).to.not.eq(0);
});

//Save id created
pm.environment.set('id', id)
});
```

#### Successful - Create a trigger (201)
- Url: {{baseURL}}/3.0/triggers
- Method: POST
> You can set the payload below  or create a variable called "createPayload" in the environment to validate the container after the response
- Body:
```sh
{
   "time_period":{
      "start":{
         "expression":"after",
         "amount":132000000
      },
      "end":{
         "expression":"after",
         "amount":432000000
      }
   },
   "conditions":[
      {
         "name":"temp",
         "expression":"$gt",
         "amount":299
      }
   ],
   "area":[
      {
         "type":"Point",
         "coordinates":[
            53,
            37
         ]
      }
   ]
}
```
- Expected Response:
```sh
{
    "__v": 0,
    "_id": "660eca4de3b29f01ffbabba0",
    "alerts": {},
    "area": [
        {
            "type": "Point",
            "_id": "660eca4de3b29f01ffbabba1",
            "coordinates": [
                53,
                37
            ]
        }
    ],
    "conditions": [
        {
            "name": "temp",
            "expression": "$gt",
            "amount": 299,
            "_id": "660eca4de3b29f01ffbabba2"
        }
    ],
    "time_period": {
        "end": {
            "expression": "after",
            "amount": 432000000
        },
        "start": {
            "expression": "after",
            "amount": 132000000
        }
    }
}

```
- TESTS - Successful - Create a trigger (201)
```sh
//Variables
const response = pm.response.json();
const body = pm.environment.get('body');
const id = response["_id"];
//Test
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

pm.test("Validate payload", function () {
    pm.expect(body).to.contains(body);
});

pm.test("Validate id distinct 0", function () {
    pm.expect(id).to.not.eq(0);
});

//Save id created
pm.environment.set('id', id)
});
```
#### Successful - Get all triggers (200)
- Url: {{baseURL}}/3.0/triggers
- Method: GET
- Body:
- Expected Response:
```sh

```
- TESTS - Successful - Get all triggers (200)
```sh

```
#### Successful - Update Trigger
> A useful tool to compare the differences is [tool](https://www.jsondiff.com/)
- Url: {{baseURL}}/3.0/triggers/{{id}}
- Method: PUT
- Body: {{updatePayload}}
- Expected Response:
```sh
{
    "_id": "660efb9c0fec5504aa6e63bc",
    "__v": 11,
    "alerts": {},
    "area": [
        {
            "type": "Point",
            "_id": "660f02ba050a47048603a2d4",
            "coordinates": [
                53,
                37
            ]
        }
    ],
    "conditions": [
        {
            "name": "temp",
            "expression": "$lt",
            "amount": 270,
            "_id": "660f02ba050a47048603a2d3"
        }
    ],
    "time_period": {
        "start": {
            "expression": "after",
            "amount": 132000000
        },
        "end": {
            "expression": "after",
            "amount": 132000000
        }
    }
}
```
- TESTS - Successful - Update Trigger (200)
```sh
//Current response
const response = pm.response.json();

//Test
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Validate update payload with response", function () {
    const payload = JSON.parse(pm.environment.get('updatePayload'));  
    for(i=0; i<payload.area.length; i++){
        let difference = payload.area[i].coordinates.filter(x => !response.area[i].coordinates.includes(x));
        pm.expect(difference.length).to.be.eql(0);
    };
    let differenceConditions = payload.conditions.filter(x => !response.conditions.includes(x));
    pm.expect(differenceConditions.length).to.be.eql(1); //it's 1 because _id is a new field added
    pm.expect(payload.time_period).to.have.jsonSchema(response.time_period); //Validate the same Json Schema
});

pm.test("Validate update information with previous payload", function(){
    const responseCreatePayload = JSON.parse(pm.environment.get('responseCreatePayload'));
     pm.expect(responseCreatePayload).to.have.jsonSchema(response); //Validate the same Json Schema
     pm.expect(response.__v).to.not.eq(responseCreatePayload.__v);
     pm.expect(response.area[0]._id).to.not.be.eq(responseCreatePayload.area[0]._id, "Are id should be different");
     pm.expect(response.conditions[0]._id).to.not.be.eq(responseCreatePayload.conditions[0]._id, "Condition id in 0 position should be different");
     pm.expect(response.conditions[0].amount).to.not.be.eq(responseCreatePayload.conditions[0].amount, "Condition amount should be different");
     pm.expect(response.conditions[0].amount).to.below(responseCreatePayload.conditions[0].amount, "Condition amount after update should be bellow");
     pm.expect(response.conditions[0].expression).to.not.be.eq(responseCreatePayload.conditions[0].expression, "Condition expression after update shoul be bellow than create payload");
     pm.expect(response.time_period.end.amount).to.below(responseCreatePayload.time_period.end.amount, "Time period amount after update should be bellow than create payload");
});

```
#### Successful - Delete Trigger (200)
> A useful tool to compare the differences is [tool](https://www.jsondiff.com/)
- Url: {{baseURL}}/3.0/triggers/{{id}}
- Method: DELETE
- Expected Response:
```sh

```
- TESTS - Successful - Delete Trigger (200)
```sh

```
