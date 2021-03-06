CaDuC
=================================
.. image:: https://travis-ci.org/tjamet/caduc.svg?branch=master
    :target: https://travis-ci.org/tjamet/caduc
Caduc watch changes on the docker engine to schedule unused image removal.
A gracetime is observed between the removal of a container and removal of the image

How To
======

Run in a container
------------------

As simple as running:
``docker run --rm -ti -v /var/run/docker.sock:/var/run/docker.sock tjamet/caduc``

Optionnally, you can provide a configuration file:
``docker run --rm -ti -v /var/run/docker.sock:/var/run/docker.sock -v $HOME/.caduc:/var/run/caduc:ro tjamet/caduc --config-file /var/run/caduc/config.yml``

Install
-------

Install the tool by launching ``python setup.py install`` as root or ``python setup.py install --user``
If you do not have root acces or do not want to bother your co-workers on the same host

Use
---

Setup your environment so that ``docker ps`` returns the list of running containers
To delete images one hour after removal of the last container running them, simply run ``caduc --image-gracetime="1h"``.

The default gracetime is of 1 day.

Customize
---------

You can customize image grace time, the delay between last container/children layer using the image is deleted
and the actual image removal. This customization can be done per image, matching their tagged name.
To do so, create ``~/.caduc`` directory with a ``config.yml`` file inside it. The matching will be done
by python's `fnmatch function <https://docs.python.org/2/library/fnmatch.html#fnmatch.fnmatch>`_.

This config file should look like:

    images:
        <match>:
            grace_time: <some_delay>

To keep base images stored on your local registry (my.repo.local) 2 days and never delete images pulled
from tjamet on dockerhub, create a config.yml file like this. The delay should be a string matching
`pytimeparse <https://pypi.python.org/pypi/pytimeparse/1.1.0>`_.

    images:
        my.repo.local/base/*:
            grace_time: 2d
        tjamet/*:
            grace_time: -1

