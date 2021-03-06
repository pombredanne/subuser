Packaging programs to run with subuser
======================================

Subuser allows anyone to create and share repositories of subuser images.  This allows anyone to become a packager and maintainer of subuser images.  There is no need for any bureaucracy, the process is entirely distributed.

Packaging programs for use with subuser is much simpler than packaging for say, Debian.  Furthermore, if your program is already packaged for a linux distribution then almost all of your work is already done.

A subuser repository is a git repository.  Repositories have the following file structure::

  image-name/
    docker-image/
      SubuserImagefile
      docker-build-context...
    permissions.json

Quick packaging tutorial
------------------------

1. Create a git repository for your own personal subuser repository:

::
  
  $ mkdir my-subuser-programs
  $ cd my-subuser-programs
  $ git init
  
2. Add subuser images:

 a. Create a folder in your subuser repository for your new subuser image and add a ``docker-image`` subdirectory:

::

  $ mkdir my-subuser-image
  $ cd my-subuser-image
  $ mkdir docker-image

Create an `permissions.json` file.  Here is an example::

  {
    "description"                : "Simple universal text editor."
    ,"maintainer"                : "Timothy Hobbs <timothyhobbs (at) seznam dot cz>"
    // Path to executable within the docker image.
    ,"executable"                : "/usr/bin/vim"
    // A list of directories the program should have Read/Write access to.
    // Paths are relative to your home. Ex: "Downloads" will access "$HOME/Downloads".
    ,"user-dirs"                 : [ 'Downloads', 'Documents' ]  // Default: []
    // Allowed the program to display x11 windows.
    ,"x11"                       : true        // Default: false
    // Allow the program access to your sound playing and recording.
    ,"sound-card"                : true        // Default: false
    // Allow the program access to Read/Write access to the directory from which it was initialized.
    ,"access-working-directory" : true        // Default: false
    // Allow the program access to the internet.
    ,"allow-network-access"      : true        // Default: false
  }

**Note**: Listing every permission is not necessary.

You can find a full specification for the `permissions.json` file format :doc:`here <subuser-standard/permissions-dot-json-file-format>`.

 b. Create a directory called `docker-image` and add a `SubuserImagefile` to that directory.  This is the same as a Dockerfile except for the adition of a special `FROM-SUBUSER-IMAGE` command which takes :doc:`the identifier of a subuser image source <subuser-standard/image-source-identifiers>` as it's argument. For information on creating a Dockerfile, please see the `official documentation for writting Dockerfiles <https://docs.docker.com/reference/builder/>`_.

Example::

  FROM debian
  RUN apt-get update && apt-get install -yyq vim

Example2::

  FROM-SUBUSER-IMAGE libx11
  RUN apt-get update && apt-get install -yyq iceweasel

Example3::

  FROM debian
  RUN apt-get update && apt-get install -yyq iceweasel

.. note :: Examples 2 and 3 do the **SAME** thing, it's just that Example3 takes a little longer to build and uses more space on disk.  There is **no magic** in the ``libx11`` image and never will be(we hope).

