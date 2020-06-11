# ENI Utility

Is the service that provide the AirGap Policy.

Before to start the application you have to create the configuration file you will pass to the run command. 

> {% code title="appconfig.properties" %}
> ```text
> organisation=your_organisation
> mqtt.broker=tcp://your_broker:1883
> registry.url=http://your_registry_url:port
> checkpoint.url=http://checkpoints-ltc-testnet.uniquid.co:3001
> ```
> {% endcode %}

Replace "your\_organisation" with the name you used during the registration and the URLs with those related to your organisation instance.

Once you created the configuration file, you can start the service

```bash
java -jar Eni-Utility-x.y.z.jar appconfig.properties
```

{% hint style="info" %}
_x.y.z_ is the version of the application, refer to the last Release Note to get the right one.
{% endhint %}

Wait for the ENI-Utility device to appear in the list of devices. You can check it with this command:

```text
uniquid list-devices -o json
```

You can view events and actions from the UniquID IAM system in realtime by using the log command

```bash
uniquid log
```

### Contract information

The ENI Utility will be the '**user**' in the contract with the Meter with permission **29**.

The ENI Utility will be the '**provider**' in the contract with the ENI Tech with permission **34**.

