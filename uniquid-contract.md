# UniquID Contract

Create a new file called `contract.json` with the following content. Be sure to replace the placeholders with your own information.  
To get the provider and user public key you can run the command

{% code title="contract.json" %}
```text
[
    {
        "provider" : "tpubDBsq9shVfqNypSXYb5BaS4fpJ8fNXwNANbGQgPfRTgczWyPKwMhcqHMkPZ2bRLsD33im6wcgYSpFpPJui5aa7PWAe9LA4B1JmiMa7wZdsUj" ,
        "user" : "tpubDCHFveNmkUmTN3nEyg8UyEUJvtv3kj5oLfuHZ8psz95tkFxZSVyRvsf2oMZdTkKQ5Hp6cphXD9ZeyML1Xgk4JYVRKPGEuPqcLP35JtXBmwJ" ,
        "functions" : [34]
    }
]
```
{% endcode %}

{% tabs %}
{% tab title="Command" %}
```text
uniquid list-devices -o json
```
{% endtab %}

{% tab title="Response" %}
```text
[
    {
        "name": "Meter6c20405068d8",
        "xpub": "tpubDAm45GEsmbDkBbYacMToiNcUVwWsuLpe4dqpFRtQdgKBhiWBtnnkJzJnedummDpHXfXRvvN5qzC468GJfgWxebTGY6pDAXi8C9ARBFyGCZ6",
        "recipe": "Test",
        "address": null,
        "contracts": 1,
        "timestamp": 1555515390257
    },
    {
        "name": "ENI Utility OBXXGTXTF7I6",
        "xpub": "tpubDBNAe5Uh4rKSiU6k7DpgxJRfPxDeu9uAkerCQKLxjm6QDfviTkRvEJn55FNG3ZxpnrvrM3mNn1Vk4bwiasFubi8Ea1cpp2VVCH2e94tMQna",
        "recipe": "Test",
        "address": null,
        "contracts": 1,
        "timestamp": 1555517031551
    }
]
```
{% endtab %}
{% endtabs %}

and copy and past the _xpub_ of the devices you are interested in.   
Replace "functions" with the permissions you want to allow.  
Below table summarises the contracts you have to deploy in this demo.

| User | Provider | Functions | Meaning |
| :--- | :--- | :--- | :--- |
| Tech | ENI Utility | 34 | Capability |
| ENI Utility | Meter | 29 | Ownership |

### Create and deploy contract

After the file creation you are ready to create and deploy the contract.

```bash
uniquid create-contracts --input-json-file contract.json
```

Check contract list

```text
uniquid list-contracts -o json
```

