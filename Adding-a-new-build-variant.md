
There are several steps required to add a new variant to build in ci.adoptopenjdk.net and be published on the website:


### Add new repositories for storing releases

You need to create two new repositories for storing nightly builds and release builds

1. Create a releases repo using this [link](https://github.com/organizations/AdoptOpenJDK/repositories/new) naming it `openjdk{version}-{variant}-releases` (e.g `openjdk8-openj9-releases`).
2. Create a nightly repo using this [link](https://github.com/organizations/AdoptOpenJDK/repositories/new) naming it `openjdk{version}-{variant}-nightly` (e.g `openjdk8-openj9-nightly`).
3. On both of these newly created repos navigate to Settings, Collaborators and teams and add the GetOpenJDK team as Admin.

### Modify the OpenJDK-Website-Backend Job to start polling the repo to check for new releases

1. Add a new block of code with your release details [here](https://github.com/AdoptOpenJDK/openjdk-website-backend/blob/master/sbin/checkApi.sh#L25-L31).

### Add the variant to the website

1. Add a variant to the website config.json file [here](https://github.com/AdoptOpenJDK/openjdk-website/blob/master/src/json/config.json#L4-L7).

### Modify the release job

1. Navigate to the job config [here](https://ci.adoptopenjdk.net/job/openjdk_release_tool/configure). If you don't have access ensure that you are logged in. You may also need to request access from an org owner.
2. Modify the VERSION Choice parameter to add your new variant
3. Add a new Conditional Step (single) for your variant. (See other variants for the correct values)

### Modify the Release counter job
1. Add a new line to the job [here](https://ci.adoptopenjdk.net/job/release_counter/configure) to gather download statistics for your variant.

### Create a nightly and release pipeline for your variant

TODO add details here. Contact [gdams](https://github.com/gdams) for information.

