---
description: Interact with your favorite models without managing the pods yourself.
---

# RunPod APIs

### Prerequisites

* You will need a RunPod API key which can be generated under your [user settings](https://www.runpod.io/console/user/settings). This API key will identify you for billing purposes, so guard it well!

### Overview

Our initial API implementation works in an asynchronous manner. This means you fire an API request to our endpoint with your input parameters, and you immediately get a response with a unique job ID. What do I do with this useless response, you say? Well, you can then query the status endpoint and pass it your job ID. The status endpoint will give you the job results when your job is completed.

Let's take the Stable Diffusion v1 inference endpoint, for example.

#### Start your job

You would first make a request like the following (remember to replace the "xxxxxx"s with your real API key:

```bash
curl -X POST https://api.runpod.ai/v1/stable-diffusion-v1/run \
-H 'Content-Type: application/json'                             \
-H 'Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'    \
-d '{"input": {"prompt": "a cute magical flying dog, fantasy art drawn by disney concept artists"}}'
```

You would get an immediate response that looks like this:

```json
{
    "id": "c80ffee4-f315-4e25-a146-0f3d98cf024b",
    "status": "IN_QUEUE"
}
```

In this example, your job ID would be "c80ffee4-f315-4e25-a146-0f3d98cf024b". You get a new one for each job and it is a unique identifier for your job.&#x20;

#### Check the status of your job

You haven't gotten any output at this point, so you must make an additional call to the status endpoint after some time. Your status endpoint uses the job ID to route to the correct job status. In this case, the status endpoint is

```
https://api.runpod.ai/v1/stable-diffusion-v1/status/c80ffee4-f315-4e25-a146-0f3d98cf024b
```

Note how the last part of the URL is your job ID. You could request that endpoint like so. Remember to use your API key for this request too!

```bash
curl https://api.runpod.ai/v1/stable-diffusion-v1/status/c80ffee4-f315-4e25-a146-0f3d98cf024b \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'  
```

If your job hasn't been completed, you may get something that looks like this back:

```
{
    "delayTime": 2624,
    "id": "c80ffee4-f315-4e25-a146-0f3d98cf024b",
    "input": {
        "prompt": "a cute magical flying dog, fantasy art drawn by disney concept artists"
    },
    "status": "IN_PROGRESS"
}
```

This just means to wait a bit longer before you query the status endpoint again.&#x20;

#### Get completed job status

Eventually, you will get the final results of your job. They would look something like this:

```
{
  "delayTime": 123456, // (milliseconds) time in queue
  "executionTime": 1234, // (milliseconds) time it took to complete the job
  "gpu": "24", // gpu type used to run the job
  "id": "c80ffee4-f315-4e25-a146-0f3d98cf024b",
  "input": {
    "prompt": "a cute magical flying dog, fantasy art drawn by disney concept artists"
  },
  "output": [
    {
      "image": "https://job.results1",
      "seed": 1
    },
    {
      "image": "https://job.results2",
      "seed": 2
    }
  ],
  "status": "COMPLETED"
}
```

#### Get your stuff

Note how you don't get the images directly in the output. The output simply contains the URLs to the cloud storage that will let you download each image.

You've successfully generated your first images with our stable diffusion API! :tada::tada:

If you're interested in descriptions for all parameters and code examples past curl, read on!
