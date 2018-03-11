# svnperms
This is a fork from a SVN hook script provided by the Apache Software Foundation ( http://svn.apache.org/repos/asf/subversion/trunk/tools/hook-scripts/ at revision 1707324 ).

"svnperms" is a script written in Python to enforce advanced permissions check. Comparatively to the usual "path-based permission file (authz)" it offers the possibility to check paths against regular expressions.
This script is very useful when it's integrated in a pre-commit-hook for example, but **can't do anything to restrict read access**.

All the configuration is done in a dedicated file, by default './conf/svnperms.conf' (relative to the repository root) but can be edited with the '-f' modifier. In the configuration there must be a section named like the repository name, or a section named like the one passed with the '-s' modifier.

When used with a [post-commit delegation script](https://github.com/justinmassiot/svnhooks/blob/master/post-commit.99.svnperms-delegation.sh) you can even leave your SVN users manage the access rights themselves, right from the repository!


```
Usage: svnperms.py OPTIONS

Options:
    -r PATH    Use repository at PATH to check transactions
    -t TXN     Query transaction TXN for commit information
    -f PATH    Use PATH as configuration file (default is repository
               path + /conf/svnperms.conf)
    -s NAME    Use section NAME as permission section (default is
               repository name, extracted from repository path)
    -R REV     Query revision REV for commit information (for tests)
    -A AUTHOR  Check commit as if AUTHOR had committed it (for tests)
    -h         Show this message
```


The following code is an example pre-commit hook script on a Linux OS, with several configuration files:
```shell
#!/bin/sh

# This script uses svnperms to define advanced path-based user rights.

# List of parameters:
# [1] REPOS-PATH   (the path to this repository)
# [2] TXN-NAME     (the name of the txn about to be committed)
python "$1"/hooks/svnperms.py -f /var/svn/authz_groups_ldap.conf -f "$1"/conf/svnperms.conf -r "$1" -t "$2" || exit 1;
# the svnperms configuration is defined in ./conf/svnperms.conf by default
# also valid in the most common use case: python "$1"/hooks/svnperms.py -r "$1" -t "$2" || exit 1;

# If we are here, it means that all checks passed, so we can allow the operation to be done.
exit 0;
```
