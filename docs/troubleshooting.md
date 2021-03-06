# Troubleshooting

### Device status will not move on from 'Deployment in progress'

If your device never moves to a `Device status` of online, and instead seems to be stuck on `Deployment in progress`, first be sure to click the refresh button on your web browser, as that status does not always update on its own.  If you are still stuck on `Deployment in progress`, de-register the device, and restart the registration steps.

### Update is available

If your device shows a status of `Update is available`, scroll down and click on `Update` to get the latest set of software updates for the DeepLens.  You will need to wait several minutes for this process to complete.

### 'mplayer' command does not successfully show a project viewer window

There are occasional times when `mplayer` issues errors but does not completely fail.  There are other times when the window displayed is the wrong aspect ratio.  In either of these cases, simply `ctrl-C` the `mplayer` command, and try again.

### Project successfully deployed, but top light never turns on

Be sure you have waited at least a minute after the successful deployment to give it a chance to run.

Now check the lambda logs for the project.  Click on the project.  Click on lambda logs.  Pick the `deeplens-object-detection` log group.  Pick the most recent log stream.  Search for `error`.

If the Lambda function is failing immediately when the DeepLens tries to load it, you may get errors in the `python_runtime` log.  From the `Device` page of the DeepLens console, navigate to the Greengrass logs (link near the bottom of the page).  From CloudWatch, choose the log group called `/aws/greengrass/GreengrassSystem/python_runtime`.  Open the log stream in this log group that has the latest event time.  If you had forgotten to add the environment variables to your Lambda function, your function would be failing to load.  The `python_runtime` log would show an error about its inability to find the `BUCKET_NAME` environment variable.

#### Other DeepLens remediation steps

Try restarting greengrass.  

```
sudo systemctl restart greengrassd.service —no-block
```

If that does not work, try rebooting the DeepLens device.

If that does not work, navigate to the device in the DeepLens console and click `Remove project` to remove the project.  This will take a couple of minutes to complete.  You may need to refresh the console to see that it has been removed.  Deploy the project once again, and ensure this time the top light gets lit.

If that does not work, click on `Deregister the device`.  Then go back and repeat device registration and deploying the project.

### S3 access denied for Lab 5

You have not given permission to the Lambda function to create objects in s3.

In IAM, find the `AWSDeepLensGreengrassGroupRole` and extend it by attaching the `AmazonS3FullAccess` policy.

### DeepLens device registration hangs and times out

Ensure you are not on VPN.

If `deeplens.amazon.net` hangs when trying to connect with the device over its softAP SSID, try `deepLens.config` instead.

If neither of those work, try using the ip address directly (e.g., `http://10.105.168.217/`).  You can find this by opening a terminal window using the directly connected keyboard and monitor.  From the terminal window, enter `ifconfig`.  In the output, look for `inet addr:` and you should be able to find the ip address of the device.

### Lambda function editor spins endlessly

On occasion, the Lambda function editor does not take you to the function code.  A simple browser refresh click normally solves this.

### Lab 2 model training fails with 'Multiple RecordIO files found in the train channel'

If you receive this error, you most likely incorrectly named the S3 locations for your data channels.  If you left off the folder names, SageMaker would find multiple `.rec` files when it was only expecting one per channel.

### Lab 3 Endpoint not found

When you try to test your trained model, you may get an error like the following:

```
botocore.errorfactory.ValidationError: An error occurred (ValidationError) when calling the InvokeEndpoint operation: Endpoint nabirds-species-identifier of account 355151823911 not found.
```

There are a couple of possible root causes for this error.  One is that you may not have named your SageMaker endpoint properly (should be `nabirds-species-identifier`).  If so, you can delete the endpoint and recreate it.

A second possible cause is that your endpoint has not yet moved to the `InService` state.  See the Lab 3 documentation for more details.

## Navigation

[Home](../README.md) - [Lab 1](lab1-image-prep.md) - [Lab 2](lab2-train-model.md) - [Lab 3](lab3-host-model.md) - [Lab 4](lab4-trigger-inference-from-s3.md) - [Lab 5](lab5-deeplens-detect-and-classify.md) - [Lab 6](lab6-text-notification.md) - [Troubleshooting](troubleshooting.md)
