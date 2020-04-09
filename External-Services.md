## Artifactory

* URL: https://adoptopenjdk.jfrog.io/
* Admins: [aahlenst](https://github.com/aahlenst), [gdams](https://github.com/gdams)
* Remarks: Sponsored and managed by JFrog

As of April 2020, Artifactory is primarily being used to host our Linux packages.

## Maven Central

* URL: https://central.sonatype.org/pages/ossrh-guide.html
* Admins: [karianna](https://github.com/karianna), [reinhapa](https://github.com/reinhapa)
* Remarks: none

Artifacts should use `net.adoptopenjdk` as the Group ID (not **org**.adoptopenjdk). Packages are being signed using personal GPG keys.
