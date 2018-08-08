Authentication
===============================================

The Agave API uses OAuth 2 for managing authentication and authorization. Luckily for this tutorial our JupyterHub already has a client that we authenticated earlier when we logged and clicked "approve" also we have the ability to fetch and store an authentication token in our session.

Open a new bash notebook so that we can start to use the Agave CLI tools.

Run the following
```
auth-check
```

If our token is expired we can run:

```
auth-tokens-refresh
```
And this will fetch a new auth token that we can use to authenticate our call to the Agave API

Creating a client and getting a set of API Keys
===============================================
The Agave API uses OAuth 2 for managing authentication and authorization. Before you work with Agave, you must create an OAuth client application and record the API keys that are returned. This is a one-time action, so if you already have a set of API keys, skip to the next tutorial.

Create a set of API keys using the Agave clients service as follows:

```clients-create -S -v -N my_api_client -D "Client used for app development" ```

*Note:* The -N flag allows you to specify a machine-readable name for your application and -D provides the description. The -S option stores your API keys for future use, so you will not need to manually enter them when you authenticate later. Please do not forget to include the -S option when creating a client.

You will be prompted for your University of Hawai'i username and password. Enter them when asked, then wait for a response that resembles this one:

```json
{
    "_links": {
        "self": {
            "href": "https://uhhpctenant.its.hawaii.edu/clients/v2/my_client"
        },
        "subscriber": {
            "href": "https://uhhpctenant.its.hawaii.edu/profiles/v2/vaughn"
        },
        "subscriptions": {
            "href": "https://uhhpctenant.its.hawaii.edu/clients/v2/my_client/subscriptions/"
        }
    },
    "callbackUrl": "",
    "consumerKey": "fMYfhijQE8fauxkeyGH4D5sngh",
    "consumerSecret": "8FZ6K9Qwfauxsecretcyi4oaEkMa",
    "description": "Client used for app development",
    "name": "my_client",
    "tier": "Unlimited"
}
```

You will need access to the ```consumerKey``` and ```consumerSecret``` values when setting up the SDK on other hosts. So, please take a moment and record *client_name*, *consumerKey*, and *consumerSecret* somewhere safe. If you lose these values, you can create new instance of the client by deleting the old client (clients-delete CLIENT_NAME) and creating it again.

Obtaining an OAuth 2 authentication token
=========================================

Tokens are a form of short-lived, temporary authenticiation and authorization used in place of your username and password. To interact Agave, you will need to acquire one. Each Agave token, typically, expires after 4 hours, but can easily be refreshed.

On a host where you have configured an OAuth2 client already, the command to get a new token is:

```auth-tokens-create -S -v```

You will then be prompted to enter your *API password*. **Type your University of Hawai'i password**.  At this point, you should receive an affirmation of success in your terminal that resembles this one:

```
Token successfully refreshed and cached for 14400 seconds
{
    "access_token": "abc1236eb2c917a4c5796eb1cc2c6f",
    "expires_in": 14400,
    "refresh_token": "abc12315b26ebc1aec5d6e232bb455b",
    "token_type": "bearer"
}
```

If you have installed the SDK on a new host and are creating a token for the first time on that host, you will need to also include the key and secret from your Oauth2 client. In the future, the key and secret will be cached on the host and you will not need to pass them in the command line.

```auth-tokens-create -S -v --apisecret CONSUMER_SECRET --apikey CONSUMER_KEY```

## Refreshing your token

This tutorial won't take very long, but if you are interrupted and come back later, you might find your token has expired. You can always refresh a token as follows:

```auth-tokens-refresh -S -v```

A successful refresh should appear:

```
Token for uhhpctenant.its.hawaii.edu:seanbc successfully refreshed and cached for 3600 seconds
{
    "access_token": "abc1235418ffce0da7fbdcb193d0ef",
    "expires_in": 3600,
    "refresh_token": "abc123524638e9d7ed0c3adcd3e99d5",
    "scope": "default",
    "token_type": "bearer"
}
```

This topic is covered in great detail at the Agave [Authorization Guide](http://developer.agaveapi.co/#authorization)
