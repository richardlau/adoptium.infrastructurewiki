# Principles

The infrastructure supported directly by the AdoptOpenJDK project comes from a variety of sources, many made available by the generous support of our users.  To protect those users, and our project at large, we will ensure that the infrastructure is managed in a manner that ensures it cannot be misappropriated or damaged.  We will do this by following a set of project guidelines and procedures.

The following information is provided to be as open as possible about the administration of the project's resources.  We publish our scripts and processes in the `openjdk-build` repository and associated wiki documentation, so that anyone can review and comment upon them.  Naturally, the ability to execute these procedures on behalf of the project will be protected by access control backed by individual user authentication, and an individual's details will necessarily be kept private.

> Principle : We will keep the project resources safe, secure, and fulfilling our rightful obligation to others.

# Infrastructure

## Password Management

> Principle : We will use industry tools and techniques for password management

We use LastPass to hold shared passwords.  If you need access to a particular password then you can ask on the [Slack Channel](https://adoptopenjdk.slack.com) or [Mailing List](http://mail.openjdk.java.net/mailman/listinfo/adoption-discuss).

## Github

Our code, tests, build pipeline, and website are backed by Github.  Therefore a large number of resources are protected by a user's Github identity, and their role in the [AdoptOpenJDK organization](https://github.com/AdoptOpenJDK).

We have a number of [Github teams](https://github.com/orgs/AdoptOpenJDK/teams) associated with the association, and membership in those teams grants additional rights and responsibilities.

> Principle : We only grant the minimum number of rights to a project member for them to fulfil their role in the project.  This includes revoking rights for project members whose role changes.

The teams page includes a description of the various activities conducted by members of each team.

Some teams, such as the [Jenkins administrators](https://github.com/orgs/AdoptOpenJDK/teams/jenkins-admins) and [Release team](https://github.com/orgs/AdoptOpenJDK/teams/release) have broad rights, and correspondingly heavy responsibilities!  Members of such groups are expected to protect their Github account access with a secure password and two-factor authentication.

TODO: Who decides on team membership changes?  Suggestion, nomination by existing team member, lazy consensus voting to accept.

## Secure shell access to machines

We have a reasonably large number of Linux-based machines whose administration may require direct shell access.  Shell access to these machines via password login is disabled, and we only allow remote secure shell access via public/private key.

To ensure that these systems remain stable and secure we prefer to grant limited time access to people (and bots/processes) who need machine access for administering them, and keep the master secrets to a very small group of owners. The 'ownership' group tends to be people who are ultimately responsible for activity on those machines, i.e. through legal contract with their service provider / employer.

Also see [[[WIP] Temporary admin access to build machines]]

## Other resources

Other resources managed by the project, such as our domain name management, Slack channel accounts, etc. may have to be managed by password as they don't have a Github authentication integration.

In these cases the passwords are kept in a secure password safe (we are using LastPass) and shared with those who need to know them.  The passwords may be changed and the list of members who have access to the password safe is reviewed as required.