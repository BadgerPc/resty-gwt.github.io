---
layout: default
title: RestyGWT / Source
slogan : Get the latest source code
---


## Source Repository

The *{{site.project_name}}* project stores its source code in a [Git](http://git-scm.com/) repository.
If you are new to Git you might like to try the 
[Git guide for subversion users](http://git.or.cz/course/svn.html) or have a look at the 
[Git community book](http://book.git-scm.com/).
<br/><br/>

The Git repository is here:

* **[{{site.github_page}}]({{site.github_page}})**

You can [browse the repository online]({{site.github_page}}) if you prefer.

## How to Checkout

You must first install a [Git client](http://git-scm.com/download). Then 
you clone the repository using the Git URL:

    git clone {{site.github_page}}
    cd {{site.project_id}}

Now you probably want to try [build the {{site.project_name}} code](building.html) 

## Contributing patches

If you are not yet a committer but want to contribute some 
patch (we love [contributions!](/community/contributing.html)) here's 
how you can submit patches
<br/><br/>

We gladly accept patches if you can find ways to improve, tune or fix
{{site.project_name}} in some way.
<br/><br/>

Most IDEs can create nice patches now very easily. e.g. in Eclipse just
right click on a file/directory and select `Team -> Create Patch`. Then
just save the patch as a file and then submit it. (You may have to click
on `Team -> Share...` first to enable the Subversion options).
Incidentally if you are an Eclipse user you should install the
[subclipse](http://subclipse.tigris.org/) plugin.
<br/><br/>

If you're a command line person try the following to create the patch

    diff -u Main.java.orig Main.java >> patchfile.txt

or

    svn diff Main.java >> patchfile.txt

## Submitting patches

The easiest way to submit a patch is to create a new issue at our [Issue
Tracker]({{site.issue_tracker}}), attach the patch, tick the Patch
Attached button on the issue then fire off an email to the mailing
lists.

## Next steps

Once you have checked out the code try following 

* [instructions to build {{site.project_name}}](building.html) 
* read the [documentation](../documentation/index.html)
* browse the [developer links and maven reports](developers.html)