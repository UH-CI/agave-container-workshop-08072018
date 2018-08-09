---
layout: page
title: Share an Application with Others
tagline:
---

As a normal user of the UH tenant, you have permissions to build and deploy
private apps only. Private apps are, by default, only visible to you. To share
an app with your colleagues, follow the steps below.


<br>
#### Update permissions on an app

Assuming you have the private app `username-tensorflow-0.1` , you can check who has
permission to access the app with the following command:
```
% apps-pems-list username-tensorflow-0.1
username READ WRITE EXECUTE
```

(Replace `username` with your UH username). By default, as creator of the app
you are the only with with access, and you have `+rwx` permissions.

Next, identify the UH `username` of the user with whom you would like to share
your app. Given the username `my_collaborator`, grant that user permissions with:
```
% apps-pems-update -u my_collaborator -p ALL username-tensorflow-0.1
Successfully updated permission for my_collaborator

# list permissions again
% apps-pems-list username-tensorflow-0.1
username READ WRITE EXECUTE
my_collaborator READ WRITE EXECUTE
```

Ask your collaborator to perform the `apps-list` command, and they should now
be able to see your app.

##HANDS ON

Go ahead an share and app with the person next to you - each of you perform the above operations.


<br>
#### Update permissions on an execution system

Before your collaborator can run a job with your app, they must also have correct permissions on the app execution system. Since you built your app to use a private execution system you must also grant your collaborator the correct permissions on that system.

*Tip: The app execution system is listed in the `app.json` file, or can be foundby issuing: '`apps-list -v --filter executionSystem app-name`'.*

Use the `systems-roles-list` command to see who can access it:
```
% systems-roles-list username-workshop-storage
username OWNER
```

Grant your collaborator `USER` access with the following:
```
% systems-roles-addupdate -u my_collaborator -r USER username-workshop-storage
Successfully updated roles for user my_collaborator on username-workshop-storage

# list permissions again
% systems-roles-list username-workshop-storage
USERNAME OWNER
my_collaborator USER
```

Ask your collaborator to perform the `systems-list` command, and they should now
be able to see your private system. Now, your collaborator can run jobs against
your private app using the same `job.json` file and `jobs-submit` commands as you.

<br>
#### Sharing Data

The Agave CLI makes it possible to share data with other users on the UH
tenant, and import data from the web. Use your best judgement in deciding
whether to copy shared data or link against shared data with the understanding
that storage space is limited.


<br>
#### Share files with another user

File permissions are managed similar to Linux file permissions. To list the
permissions on an existing file on the STORAGE system, issue:
```
% files-pems-list -S username-workshop-storage workshop/my_file.txt
```

The output should look similar to:
```
username READ WRITE EXECUTE
```

To add permissions for another user (in this case with username '`collaborator`')
to view the file, use the `files-pems-update` command:
```
% files-pems-update -U collaborator -P ALL -S username-workshop-storage workshop/my_file.txt
% files-pems-list -S username-workshop-storage workshop/my_file.txt

collaborator READ WRITE EXECUTE
username READ WRITE EXECUTE
```

Now, a user with username `collaborator` has permissions to access the file.
Valid values for setting permission with the `-P` flag are READ, WRITE, EXECUTE,
READ_WRITE, READ_EXECUTE, WRITE_EXECUTE, ALL, and NONE. This same action can be
performed recursively on directories using the `-R` flag.

Next, the collaborator needs the correct permissions to access your STORAGE
system. To see who has access to your STORAGE system, perform:
```
% systems-roles-list username-workshop-storage
username OWNER
```

To add your collaborator to your system, use the `systems-roles-addupdate` command:
```
% systems-roles-addupdate -u collaborator -r GUEST username-workshop-storage
Successfully updated roles for user collaborator on username-workshop-storage

% systems-roles-list username-workshop-storage
collaborator GUEST
username OWNER
```

Now, a user with username `collaborator` can see files with the appropriate
permissions on your STORAGE system. Valid values for setting a role with the `-r`
flag are GUEST, USER, PUBLISHER, ADMIN, and OWNER.

Finally, ask your collaborator to download the file with the exact same command
you use to download the file:
```
% files-get -S username-workshop-storage workshop/my_file.txt
```
<br>
#### Share files using postits

Another convenient way to share data is the Agave postits service. Postits
generate a short URL with a user-specified lifetime and limited number of uses.
Anyone with the URL can paste it into a web browser, or curl against it on the
command line. To create a postit:
```
% postits-create -m 5 -l 3600 \
    https://uhhpctenant.its.hawaii.edu/files/v2/media/system/dusername-workshop-storage/workshop/my_file.txt
```

The json response from this command is a URL, e.g.:

```
https://uhhpctenant.its.hawaii.edu/postits/v2/866d55b36a459e8098173655e916fa15
```

This postit is only good for 5 downloads (`-m 5`) and only available for one hour (3600 seconds, `-l 3600`). The creator of the postit can list and delete their postits with the following commands:

```
% postits-list -V
% postits-delete 866d55b36a459e8098173655e916fa15
```

The long alphanumeric string is the postit UUID displayed by the verbose postits-list command.

<br>
