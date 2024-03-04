# Lambda

### 源function 结果没问题
![image](https://github.com/Jssice/Lambda/assets/33156021/afdfeaa5-7ec3-4507-b6d0-4d4eb9ef1b33)
![image](https://github.com/Jssice/Lambda/assets/33156021/fbbe17eb-f39b-4a50-aeba-ca30329f64a8)


### 1 index.mjs 
```
'use strict';
console.log('Loading hello world function');

import { DynamoDBClient, PutItemCommand } from "@aws-sdk/client-dynamodb";

const client = new DynamoDBClient({
  region: "ap-southeast-2"
});

export const handler = async (event) => {
    let name;
    let city;
    let time;
    let day;
    let responseCode = 200;
    
    console.log("request: " + JSON.stringify(event));

    // Parse input from the API call body
    if (event.body) {
        try {
            const body = JSON.parse(event.body);
            name = body.name;
            city = body.city;

            // Validate required parameters
            if (!name || !city) {
                responseCode = 400; // Bad Request
                throw new Error("Missing required parameters. Please provide 'name' and 'city' in the request body.");
            }

            // Use default values or dynamically lookup day and time
            time = body.time || getCurrentTimeInAEST();
            day = body.day || getCurrentDayInAEST();
        } catch (error) {
            responseCode = 400; // Bad Request
            return {
                statusCode: responseCode,
                body: JSON.stringify({ error: error.message })
            };
        }
    } else {
        responseCode = 400; // Bad Request
        return {
            statusCode: responseCode,
            body: JSON.stringify({ error: "Missing request body. Please provide 'name' and 'city' in the request body." })
        };
    }

    let greeting = `Good ${time}, ${name} of ${city}.`;
    if (day) greeting += ` Happy ${day}!`;

    let responseBody = {
        message: greeting,
        input: event
    };

    let response = {
        statusCode: responseCode,
        headers: {
            "x-custom-header" : "my custom header value"
        },
        body: JSON.stringify(responseBody)
    };
    
    let params = {
        TableName: 'HelloWorldTable',
        Item: {
            'name' : {S: name},
            'city' : {S: city}
        }
    };

    const putItemCommand = new PutItemCommand(params); 

    try {
        const data = await client.send(putItemCommand);
        console.log("Item added successfully:", JSON.stringify(data, null, 2));
    } catch (err) {
        console.error("Error adding item:", err);
    }

    return response;
};

// Function to get the current time in AEST
function getCurrentTimeInAEST() {
    // Add logic to calculate the current time in AEST
    // For example, you can use a library like Luxon or Moment.js
    // and convert the time to AEST timezone.
    return "AEST_time"; // Replace with actual AEST time
}

// Function to get the current day in AEST
function getCurrentDayInAEST() {
    // Add logic to calculate the current day in AEST
    // For example, you can use a library like Luxon or Moment.js
    // and convert the day to AEST timezone.
    return "AEST_day"; // Replace with actual AEST day
}

```





### 2Test result

Test Event Name
test

Response
{
  "statusCode": 400,
  "body": "{\"error\":\"Missing request body. Please provide 'name' and 'city' in the request body.\"}"
}

Function Logs
2024-03-04T05:32:27.096Z	undefined	INFO	Loading hello world function
START RequestId: 124707fe-f96e-469f-8ede-9c6325b4a9d2 Version: $LATEST
2024-03-04T05:32:27.130Z	124707fe-f96e-469f-8ede-9c6325b4a9d2	INFO	request: {"key1":"value1","key2":"value2","key3":"value3"}
END RequestId: 124707fe-f96e-469f-8ede-9c6325b4a9d2
REPORT RequestId: 124707fe-f96e-469f-8ede-9c6325b4a9d2	Duration: 14.84 ms	Billed Duration: 15 ms	Memory Size: 128 MB	Max Memory Used: 91 MB	Init Duration: 507.68 ms

Request ID
124707fe-f96e-469f-8ede-9c6325b4a9d2

### 浏览器json数据
![image](https://github.com/Jssice/Lambda/assets/33156021/b567e1e1-e829-4ebd-8a54-5da31bbae6b1)


![image](https://github.com/Jssice/Lambda/assets/33156021/2050ed66-15c0-4acc-8af3-ce8ad8e7273e)


