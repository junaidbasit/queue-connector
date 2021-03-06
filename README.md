# queue-connector
Provides an abstraction layer for various Cloud providers' queue implementation, like AWS SQS, Azure Service bus, etc.

## Installation
```sh
$ npm install queue-connector
```

## Usage
Using the queue with AWS SQS
```js
const QueueConnector = require('queue-connector');
const sqs = new QueueConnector.AwsProvider({
  accessKeyId: awsConfig.accessKeyId,
  secretAccessKey: awsConfig.secretAccessKey,
  region: awsConfig.sqs.region
}, sqsQueueUrl);
const queue = new QueueConnector.Queue(sqs);

// send a message
const msg = {a: '1', b: '2'}; // whatever data you want to send
queue.sendMessage(JSON.stringify(msg), (err, data) => {
  // handle callback
});

// receive a message
queue.receiveMessage((err, msg) => {
  console.log(msg.body); // msg as string '{"a": "1", "b": "2"}'
  console.log(msg.bodyAsJSON); // msg as JSON {a: '1', b: '2'}
  
  // delete a message, when processing done
  msg.remove((err, info) => {
    // removes the message from the queue
  });
});
```

The queue can also be configured to connect to Azure Service Bus with the same API.
```js
const QueueConnector = require('queue-connector');
const serviceBusConnectionString; // get this from Azure portal
const serviceBus = new QueueConnector.AzureProvider(serviceBusConnectionString, queueName);
const queue = new QueueConnector.Queue(serviceBus);

// use the queue object with the same API as the above example
```

## API

### new AwsProvider(awsCredentials, sqsQueueUrl, [options])
Creates a new AWS SQS queue provider.
* **awsCredentials** : the same awsCredentials that aws-sdk takes
* **sqsQueueUrl** : the queue url provided by AWS
* **[options]** :
    * **WaitTimeSeconds**: The request timeout for long polling

### new AzureProvider(connStr, queueName, [options])
Creates a new Azure Service Bus queue provider
* **connStr** : Connection string to the Service Bus
* **queueName** : Name of the queue
* **[options]** :
    * **timeoutIntervalInS**: The request timeout for long polling

## Adding more providers
Currently this lib has only AWS SQS and Azure Service Bus provider, if you need any more providers, please feel free to send in a pull request :)

## TODO
- [ ] Add Tests
- [ ] Use Flow for type checking
- [ ] Create TypeScript definition file (for autoCompletion in webstorm, don't know if required after flow)

