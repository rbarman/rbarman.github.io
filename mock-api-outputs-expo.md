Title:Creating mock API outputs in an Expo React Native app
Date: 2023-09-09
Tags: Expo, ReactNative

I am currently working on an Expo React Native app and need to call different paid APIs to get data. There are times when I want to update how the data is rendered in the application or the application logic flow. These develepment tasks, especially UI updates, can be a very iterative process and ultimately very expensive if I need to constantly call APIs to get the data. One work around I have found is to create flags in the application to specifiy if the API should be mocked.

For example, suppose my application needs to call an AWS Lambda function to sport score related data. 
In `app.json` I create a new field called 'local_mock' with a boolean field called 'sports_score'.

```json
{
    "expo":{
        ...

       "local_mock":{
            "sports_score":true
       } 
    }
}

```

If `local_mock.sports_score` is true, then the application knows that whenever it is required to get sports score data, it should return mocked output. 

Suppose `api.js` has a function called `get_sports_score` which calls an AWS Lambda function.

```javascript
import Constants from 'expo-constants';

const get_sports_score = async(context) => {
    if (Constants.manifest.local_mock.sports_score) {
        local_mock = {
            "score":"..."
        }
        return local_mock
    }
    else{
        return call_lambda_function(context)
    }
}
```

This approach only works if the data structure of the local mock matches with the data structure of the output from the actual API output. 

I am still learning Expo and React Native development, so there may better ways to implement local mocks of API outputs.
