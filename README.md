jenkins-post-receive
====================

This is an executable bash script that accepts info regarding your Jenkins server, your job name, your target branch, and the last committer to the branch (can be used in post-build notifications if the Jenkins build doesn't find culprits to blame for a build failure), and fires off a Jenkins build when included in your git repo's post-receive hook script.

This will also check the commit message for a "--skip-ci" flag, which will bypass a Jenkins build unless it's an integration or master branch being pushed.

Running this in the context of a git post-receive hook would look something like this:

```bash
#!/bin/bash
#
# An example hook script for the "post-receive" event.
#
# The "post-receive" script is run after receive-pack has accepted a pack
# and the repository has been updated.  It is passed arguments in through
# stdin in the form
#  <oldrev> <newrev> <refname>
# For example:
#  aa453216d1b3e49e7f6f98441fa56946ddcd6a20 68f7abf4e6f922807889f52bc043ecd31b79f814 refs/heads/master
#
# see contrib/hooks/ for an sample, or uncomment the next line and
# rename the file to "post-receive".

read oldrev newrev refname

# Define varables that the various scripts below will need
JENKINS_SERVER="your_jenkins_server" #no http:// necessary
JENKINS_JOB_NAME="your_jenkins_job_name"
JENKINS_BUILD_TOKEN="your_configured_build_token"
LAST_COMMITTER=$(git log --pretty="format: %cN" -1 $newrev | sed -e 's/^ *//g' -e 's/ *$//g')
TARGET_BRANCH=${refname#refs/heads/}

# Fire off a jenkins build
. [wherever you cloned this on your git server]/jenkins-post-receive
```
