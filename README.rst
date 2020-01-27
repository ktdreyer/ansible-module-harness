ansible-module-harness
======================

A script to run and debug Ansible modules within playbooks.

The ``harness`` script reads and executes an Ansible playbook file on the
local host. If any of the modules crash, it drops the user into a PDB
interactive shell.

This allows you to quickly triage the cause of the failure without adding a
lot of "debug" log messages to your modules.

```
./harness my-playbook.yml
```

Motivation
----------

There are several documents about "how to develop Ansible modules." These
documents are good starting points, but there are `many manual steps
<https://docs.ansible.com/ansible/latest/dev_guide/debugging.html>`_
associated with getting all the way into PDB. You must convert your task's
YAML arguments to JSON, write that JSON to a file, fix up imports for
``ansible.module_utils``, exec the module with ``python3 -m pdb``, and so on.

As I wrote more complex suites of Ansible modules, I needed to go faster.
I needed a tool that allows me to get into PDB quickly and explore the
complexities of the data and APIs with which I'm interacting. I'm also writing
modules with *many* settings, so I needed a way to operate on real playbook
files that I can apply with ``ansible-playbook`` or (``ansible-playbook
--check``).

The ``pytest`` tool has an excellent ``--pdb`` feature. When you run ``pytest
--pdb``, it will completely stay out of your way if the entire test suite
passes, but it will drop you neatly into the interactive debugger if a test
happens to fail. I wrote this tool to do the same thing for
``ansible-playbook``.


Limitations
-----------

This is a rough developer tool, so there are several limitations:

* You must have a local ``library`` directory where all your modules reside.
  This is typical if you are developing a new module. (This tool does not
  respect the "``ANSIBLE_LIBRARY``" environment variable.)
* You cannot use any built-in Ansible modules in your playbook. This script
  will only load the modules from your local ``library`` directory.
* Your modules must execute on localhost, not on any remote host. This is
  typical if you are developing modules that will use a web API.

License
-------

This project is licensed under the GPLv3 to match Ansible's license.
