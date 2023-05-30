# Background
Simple load testing demo with [K6](https://github.com/grafana/k6).
<br>APIs used are the Public APIs of [DataGovSG](https://github.com/datagovsg/datagovsg-datasets/blob/master/documentation/swagger.yml)

<br>
Test Case:

1. Ramp up user traffic slowly from 1 -> 10 users
2. Assert request duration should be less than 100ms. According to the [Nielsen Norman Group](https://www.nngroup.com/articles/website-response-times/), 100ms is the threshold for responding "instantaenously"

 


# Prerequisites

1. Install **k6**
    ```
    $ brew install k6
    ```

2. Install node modules
    ```
    $ npm install
    ```
<br>

# Generate k6 load tests from postman collection
1. Download collection and environments from postman 

2. Convert the collection to load tests
    ```
    $ npx postman-to-k6 <postman_collection>.json --output <output>/k6-script.js 
    
    ## Example:
    $ npx postman-to-k6 data/tech_immersion.postman_collection.json --output tests/k6-script.js
    ```

3. Run k6 load tests!
    ```
    k6 run tests/k6-script.js
    ```

4. Improve load test to simulate real traffic
    ```js
    export let options = {
      maxRedirects: 4,
      stages: [
        { duration: '0.25m', target: 1 },    // start at 1 user
        { duration: '0.25m', target: 5 },    // stay at 5 users for 15 seconds
        { duration: '0.25m', target: 10 },   // ramp-up to 10 users
        { duration: '0.25m', target: 0 },    // go-down to 0 users
      ],
      thresholds: {
        http_req_duration: ['p(95)<100'], // 95% of requests should be below 100ms
      },
    };
    ```