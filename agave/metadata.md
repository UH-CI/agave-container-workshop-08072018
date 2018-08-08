## Metadata to describe

Agave has a MongoDB store that works with the Agave APIs to store JSON that can be associated with any Agave object - files, systems, jobs etc.

We can create metadata objects to further capture information related to these objects - in particular files are something that can be enhanced with metadata.

In order to add metadata that links to an Agave Object we need to know the objects UUID.  To generate or find a files UUID we can use:
```
files-index -v -S seanbc-workshop-vm-storage my_file.txt
```
this will return something like:
```
[
  {
    "name": "my_file.txt",
    "path": "my_file.txt",
    "lastModified": "2018-08-08T13:01:45.000-05:00",
    "length": 0,
    "permissions": "READ_WRITE",
    "format": "raw",
    "mimeType": "text/plain",
    "type": "file",
    "system": "seanbc-workshop-vm-storage",
    "uuid": "4450908563165221352-242ac113-0001-002",
    "_links": {
      "self": {
        "href": "https://uhhpctenant.its.hawaii.edu/files/v2/media/system/seanbc-workshop-vm-storage//workshop/my_file.txt"
      },
      "system": {
        "href": "https://uhhpctenant.its.hawaii.edu/systems/v2/seanbc-workshop-vm-storage"
      },
      "profile": {
        "href": "https://uhhpctenant.its.hawaii.edu/profiles/v2/seanbc"
      },
      "metadata": {
        "href": "https://uhhpctenant.its.hawaii.edu/meta/v2/data?q=%7B%22associationIds%22%3A%224450908563165221352-242ac113-0001-002%22%7D"
      },
      "history": {
        "href": "https://uhhpctenant.its.hawaii.edu/files/v2/history/system/seanbc-workshop-vm-storage//workshop/my_file.txt"
      }
    }
  }
]
```
The Agave UUID for the file in our example is 4450908563165221352-242ac113-0001-002.

We can now add some metadata related to that UUID by creating a JSON file called metadata.json:
```
{
 "associationIds": ["4450908563165221352-242ac113-0001-002"],
 "name":"myfile",
 "value":[{"description":"This is a test file from the vm workshop"},
          {"author":"Sean Cleveland"},
          {"date-created":"2018-08-09"}]
}
```
To store the metadata we use:
```
metadata-addupdate -F metadata.json
```
Successfully submitted metadata object 2991688192166063640-242ac1111-0001-012

We can view the metadata object with:
```
metadata-list -v 2991688192166063640-242ac1111-0001-012
```
results look like:
```
{
  "uuid": "2991688192166063640-242ac1111-0001-012",
  "owner": "seanbc",
  "schemaId": null,
  "internalUsername": null,
  "associationIds": [
    "4450908563165221352-242ac113-0001-002"
  ],
  "lastUpdated": "2018-08-08T13:14:06.368-05:00",
  "name": "myfile",
  "value": [
    {
      "description": "This is a test file from the vm workshop"
    },
    {
      "author": "Sean Cleveland"
    },
    {
      "date-created": "2018-08-09"
    }
  ],
  "created": "2018-08-08T13:14:06.368-05:00",
  "_links": {
    "self": {
      "href": "https://uhhpctenant.its.hawaii.edu/meta/v2/data/2991688192166063640-242ac1111-0001-012"
    },
    "permissions": {
      "href": "https://uhhpctenant.its.hawaii.edu/meta/v2/data/2991688192166063640-242ac1111-0001-012/pems"
    },
    "owner": {
      "href": "https://uhhpctenant.its.hawaii.edu/profiles/v2/seanbc"
    },
    "associationIds": [
      {
        "rel": "4450908563165221352-242ac113-0001-002",
        "href": "https://uhhpctenant.its.hawaii.edu/files/v2/media/system/seanbc-workshop-vm-storage//workshop/my_file.txt",
        "title": "file"
      }
    ]
  }
}
```
We can also query metadata using MongoDB syntax. So we can query for all metadata that has our file uuid associated with it:

```
metadata-list -v -Q '{"associationIds":"4450908563165221352-242ac113-0001-002"}'
```

Or we could query for any metadata with a name field that matches "myfile":
```
metadata-list -v -Q '{"name":"myfile"}'
```

Or all the metadata objects that have an author Sean Cleveland:
```
metadata-list -v -Q '{"value.author":"Sean Cleveland"}'
```

## Metadata as Data


So for the loggers to push data into IkeWai in a similar way as chords   :  
here is an example test.json file
```
{"name":"site1","value":[{"date":"2018-07-31T16:07:56+00:00"},{"tempc":"30.5"},{"diss_ox":"0.2"}]}
```

and the call to push it into Agave with curl is:
```curl -sk -H "Authorization: Bearer 940203248123c1796c7316cd17Xe3" -X POST -F "fileToUpload=@test.json" 'https://uhhpctenant.its.hawaii.edu/meta/v2/data/?pretty=true'```

and the call to query looks like:
```curl -sk -H "Authorization: Bearer 940203248123c1796c7316cd17Xe3" 'https://uhhpctenant.its.hawaii.edu/meta/v2/data?q=%7b%27%6e%61%6d%65%27%3a%27%73%69%74%65%31%27%7d&pretty=true'```
