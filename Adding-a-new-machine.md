# Add to keybox

Log into the new machine and put the AWX public key into ~root/.ssh/authorized_keys - the other option is to let keybox prompt you for the password when it first connects. Either method works - it depends what access you already have on the target system.

Log into keybox.adoptopenjdk.net with the credentials (from the secrets repository) and go to Manage -> Systems -> Add System. Fill in the fields (`Host` should be the systems IP address) and click `[Submit]`. If all goes well, keybox will log in and update the authorized_keys files on the machine so everything that needs to can access it.

# Add to AWX (ansible.adoptopenjdk.net)

You will need to submit a git pull request to update https://github.com/AdoptOpenJDK/openjdk-infrastructure/blob/master/ansible/inventory.yml with the new machine's details. Note that when the file is imported into AWX the machine name will have the machine type (e.g. `build`, `test` etc.) and also the provider `azure`, `osuosl` etc. prepended to the name so it will appear in ansible as e.g. `build-osuosl-centos74-ppc64le-2`)

Once you've done this and your PR has been accepted log into AWX (Using the right git icon on ansible.adoptopenjdk.net) and you'll need to allow two jobs to complete. The first is in `Projects -> AdoptOpenJDK GIT Infrastructure` - click the cloud button ("Start an SCM update") to run it. This mirrors the latest code from git onto the filesystem on the AWX server. The second job runs every half an hour and reads from the repository into the ansible inventory. You can see this one in Schedules as `30 minute inventory sync`. Once this runs after the `AdoptOpenJDK GIT Infrastructure` your machine should show up in the inventory (`Inventories -> Dynamic Hosts from Github -> Hosts`)

# Run the ansible setup scripts

From `Templates` on the AWX UI Select `AdoptOpenJDK Deploy Unix PlayBook` (assuming it's a UNIX-based machine you're working on. If you only want to run it on one machine you'll need to edit the job and click the `prompt on launch` checkbox next to `LIMIT`. This will give you the option to select a pattern into which you can type your host when you deploy the template, which you do by clicking the rocket ship icon to the right of the template name.