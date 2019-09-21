##Pareto Efficient DevOps techniques

### Good tools for the team: good tools for yourself
It's great if there is a uniform workflow that everyone in your team follows, if that workflow makes you productive. Frequently you can do better than what's in place, and sometimes it's just personal, like when your team is ordained to use vi but you prefer emacs. This dubious tip is to get yourself whatever workflow you want - you want somewhere to type, and a way or two to test your changes as you develop before they get official. When it's just for yourself it can be as nonreproducible and flaky as you want. The honorary mention for applying this tip goes to a colleague from my job at the bank who was tired of the official IDE for developing in a Scala, and spent many hours tinkering to replace it with a tool he liked more- despite the project having a few hundred developers, and three people dedicated solely to providing tooling, with compiler plugins and what else.


### RAM: swap space
I couldn't install a Haskell package on my 5$/month 500MB RAM VM that I got for experimenting, because GHC would run out of memory. I asked on IRC and got the above pro tip, apparently the initial install from a vendor will typically not come with any swap space to trick you into upgrading. I've added myself some swap but the install was taking long so I've temporarily made my VM bigger, provisioned everything I needed, and downsized it back.

### Continuous deployment: `scp code-folder remote:/var/tmp/code-folder && ssh remote /var/tmp/code-folder/run.sh`
A typical continuous deployment scheme is for a team to keep code in a repository, and have a build server that listens for pushes to that repository, and for every push checks the code out and runs a deployment, or a GUI where you log in, press a button, and do a deploy.
You can have most of the value of that scheme for your personal project - which is that the code runs somewhere else than your laptop, so that your webpage won't be brought down by a Windows Update or your parents switching off the router to make you go to sleep.
Make yourself a command that sends over the code to a remote server and executes it with ssh. If stuff doesn't work, just make the command longer.

### Deployment tasks: deployment notes
The npm ecosystem encourages you to alias common tasks- it's expected what `npm build` or `npm test` will do, and you can work them into the package update lifecycle as e.g. predeploy tasks. Some projects like jhthorsen's (git ship)[https://github.com/jhthorsen/app-git-ship] attempt to give you a whole workflow for a specific kind of project (here, a Perl project that hosts code on git and tarballs on CPAN).
I say, if you don't have that then make yourself a file with notes. Make it include random commands you needed in the past, like starting a dev server. When you come back to a project after a few months, you'll know what to do from there.

### Provisioning: keep notes of stuff you installed
In similar spirit to the above, if you're using a clean cloud VM to host your project, note down what architecture and OS it was (say x64 Ubuntu) and then make notes as you install things in it. It's not quite Terraform+Ansible but it's one step before that.
