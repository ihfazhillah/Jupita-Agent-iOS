# Jupita Agent Swift SDK

This SDK is developed for iOS using Swift 5.4, and utilizes `URLSession` in order to create the required API call requests. This iOS SDK fully supports the 3 APIs available for Jupita Agent. All API calls are made asynchronously, thus there are event callbacks available to handle the API results

## APIs

There are 3 APIs within the Juptia Agent product 
`dump` `rating` & `feed`. 

* Dump allows you to send the utterances you wish to send
* Rating allows you to retrieve the rating for the agent in question
* Feed provides you with some basic rating analytics. 

### Quickstart

#### Step 1

Drag and drop the SDK folder `Jupita.framework` into your project under ‘Frameworks', then select the framework from the `Targets` menu. Make sure you have selected 'Embed and sign'.

#### Step 2

Import the Jupita.framework into your class.

#### Step 3

Build Jupita Agent, in the example below `2` has been used to represent the `agentId`

```
let token:String = “authentication token”; 
let agent = Agent.init(token, "2")
```

#### Step 4

Call the `dump` API as a message from Agent by specifying the message and clientId – represented as '3' below;

```
agent.dump(text: "hello", clientId: "1", type:  Agent.AGENT) { (result) -> Void in
      switch result {
      case .success(let json):
        debugPrint(json)
        break
      case .failure(let error):
        debugPrint(error)
        break
      }
    }
```

Similarly, call the`dump` API whenever client responds back to the same agent by specifying the message and ID of the client;


```
agent.dump(text: "hi, how are you?", clientId: "1", type:  Agent.CLIENT) { (result) -> Void in
      switch result {
      case .success(let json):
        debugPrint(json)
        break
      case .failure(let error):
        debugPrint(error)
        break
      }
    }
```

#### Step 5

Call the `rating` API;

```
agent.rating { (result) -> Void in
      switch result {
      case .success(let json):
        debugPrint(json)
        break
      case .failure(let error):
        debugPrint(error)
        break
      }
}
```

#### Step 6

Call the `feed` API;

```
agent.feed { (result) -> Void in
      switch result {
      case .success(let json):
      debugPrint(json)
      break
      case .failure(let error):
        debugPrint(error)
      break
      }
    }
```


### Error handling

The SDK throws one error, which occurs if the user input is not JSON compatible. This can be incorrect usage of strings when passed on to the Agent methods. This error may also arise if the message type set in the dump method is not 1 or 0, or the model name in rating method is not set to `JupitaV1`.

### Error codes 

Error codes thrown are 401 when the token is incorrect and 400 when there is an attempt to dump gibberish content to the server, although the model does have an inbuilt gibberish detector. 

### Libraries

Use Step 1 and 2 so that the Jupita Agent iOS SDK is available within the scope of the project. Currently the Jupita Agent iOS SDK is dependent on `URLSession`.

### Classes

The available product under the iOS SDK is Jupita Agent. Jupita Agent can be constructed directly using the public constructor however it is highly recommended to use the `Agent.Init` class to build the product. This will ensure that mistakes are not made while building the iOS Jupita Agent SDK.

```
let token:String = “your-token”; 
let agent = Agent.init(token, "2")
```


This is needed for building the ‘URLSession’ request. Next the token and agentId needs to be set.
The built agent can now be used to call ‘dump’, `rating` and `feed` methods asynchronously. The definitions for the `dump` methods are as follows;

```
public func dump(text: String,  clientId: String, type: Int, isCall: Bool, completionHandler: @escaping(_ result: Result<Any,Error>) -> Void?)
 
public func dump(text: String, clientId: String, type: Int, completionHandler: @escaping(_ result: Result<Any,Error>) -> Void?)
 
public func dump(text: String, clientId: String, completionHandler: @escaping(_ result: Result<Any,Error>) -> Void?)
 
public func dump(text: String, clientId: String) 
```

If the values of `type` and `isCall` are not provided by default the values are considered `0` and `false`. Thus text and the clientId are essential when creating a dump request. To avoid illegal argument error use Agent.AGENT or Agent.CLIENT for type.

The definitions for the `rating` methods available are as follows;

```
public func rating(completionHandler: @escaping(result: Result<Any,Error>) -> Void?)

public func rating(modelName: String, completionHandler: @escaping(_ result: Result<Any,Error>) -> Void?) 

public func rating(modelName: String, completionHandler: @escaping(_ result: Result<Any,Error>) -> Void?) 
```

The second `rating` definition is created for future use when there will be multiple models to choose from. At the moment only 1 model (*JupitaV1*) is supported. To avoid illegal argument error use Agent.JupitaV1 for `modelName`.

The definition for the `feed` method is as follows;

```
public func feed(completionHandler: @escaping(_ result: Result<Any,Error>) -> Void?)
```

### Notes

`completionHandler` is a callback which needs to be implemented to listen to the results of the API request. It will return the following for the respective APIs;

1. The success event returns the rating as a double.
2. The success event returns the feed for the whole week as a JSONObject.
3. The success event returns the success message as well as the utterance rating as a double.
