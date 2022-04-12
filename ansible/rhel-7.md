# Ansible on RHEL/OEL/CentOS 7

## Using Python 3

The package `ansible-python3` is available from [EPEL] and [Oracle EPEL].

**Problem:** it will install all binaries as `ansible-3` and `ansible-3.x`

We can correct this with `alternatives`:

```bash
alternatives --install /usr/bin/ansible-playbook ansible-playbook /usr/bin/ansible-playbook-3 1
alternatives --install /usr/bin/ansible ansible /usr/bin/ansible-3 1
````

To get all commands installed properly:

```bash
for cmd in "" -playbook -vault -config -galaxy -connection -console -doc -test -pull -inventory
do
    alternatives --install "/usr/bin/ansible${cmd}" "ansible${cmd}" "/usr/bin/ansible${cmd}-3" 1
done
```

[EPEL]: https://docs.fedoraproject.org/en-US/epel/
[Oracle EPEL]: https://yum.oracle.com/repo/OracleLinux/OL7/developer_EPEL/x86_64/index.html
