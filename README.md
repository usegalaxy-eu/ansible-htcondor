# Ansible role: HTCondor

Ansible role to install and configure HTCondor on a RHEL system.

## Requirements

None.

## Role variables

Default values are listed in [defaults/main.yml](defaults/main.yml).

```
condor_release_channel: "10.0"
```

This role installs HTCondor using its
[official installation script](https://get.htcondor.org). Such script does not
directly install HTCondor, but rather configures a repository in the system's
package manager from which then HTCondor packages are downloaded, installed and
updated by the package manager itself.

HTCondor developers offer several
[release channels](https://research.cs.wisc.edu/htcondor/repo/) from which
HTCondor can be sourced. Those channels correspond to major
versions of HTCondor (e.g. 9.x.x), minor versions (e.g. 9.1.x) and the special
["current" and "release" channels](https://htcondor.org/manual/quickstart.html).
HTCondor's
[versioning scheme](https://htcondor.readthedocs.io/en/latest/version-history/introduction-version-history.html)
is quite similar to [Semantic Versioning](https://semver.org/). This means that
this script can pin HTCondor to a minor version, but not to a patch version. A
system update through the package manager can thus increase HTCondor's patch
version number.

⚠ This role is **not compatible** with the **stable** and **current** release
channels. **Do not** set `condor_release_channel` to "stable" neither
"current".

<details>
<summary>Where does this limitation arise from?</summary>
<br>

The actual release channel configured when `condor_release_channel` is set to
"stable" is hardcoded in the installation script. For example, at the moment of
writing, "stable" is hardcoded as being equal to "10.0". The "current" release
channel, on the other hand, is updated on HTCondor's repository, meaning that
the link https://research.cs.wisc.edu/htcondor/repo/current/ points to 
different release channels (e.g. "9.x" or "10.x) at different points in time.

Moreover, the installation script not always installs HTCondor nor changes the
configured repositories. If HTCondor is already installed, it will exit and ask
you to use the system's package manager to either update or remove HTCondor.
This implies that even when using the "current" release channel, re-running
the script seems not to update HTCondor's release channel, which is rather
counterintuitive.

In addition, the role does not always attempt to run the installation script.
It will attempt to run the installation script only when:
- HTCondor is not installed.
- The configured repository does not match the specified release channel. This
  will always happen if `condor_release_channel` is set to "stable" or
  "current".
- The variable `condor_role` has changed.

All of this implies that using the "stable" and "current" release channles
would trigger unintended HTCondor updates, thus we have decided not to support
those release channels

</details>

```
condor_host: localhost
```

Hostname where the Condor Central Manager daemon is expected to be running.

```
condor_password: set_your_password
```

This password is shared among all machines in the HTCondor pool. The password
is used to generate IDTOKENS which are used to authenticate communication
between the machines in the pool. See
[HTCondor's documentation](https://htcondor.readthedocs.io/en/v10_0/man-pages/get_htcondor.html)
for more details.

```
condor_role: all
```

This variable can take one of `all`, `execute`, `submit` or `central-manager`
as value. Different configuration files are written to `/etc/condor/config.d/`
depending on the chosen option. `all` corresponds to a so-called
"single-machine" installation. Read
[HTCondor's administrative quick start guide](https://htcondor.readthedocs.io/en/v10_0/getting-htcondor/admin-quick-start.html#administrative-quick-start-guide)
for more details.

```
condor_systemd_enable: true
```

Whether to enable `condor.service` each time the role is invoked.

```
condor_systemd_start: true
```

Whether to start `condor.service` each time the role is invoked.

```
condor_installation_script: "https://raw.githubusercontent.com/htcondor/htcondor/main/src/condor_scripts/get_htcondor"
```

Changing this option allows to source an installation script different from the
official one, or to freeze it in time (i.e. using a URL pointing to a specific 
Git commit).

## Dependencies

None.

## Example Playbook

```yaml
- name: Install and configure HTCondor
  hosts: all
  vars:
    condor_release_channel: "10.x"
    condor_role: execute
    condor_host: condor.example.org
    condor_password: example_password
  roles:
    - usegalaxy_eu.htcondor
```

## License

[GPLv3](LICENSE.md)

## Author information

This role was created by [Galaxy Europe](https://galaxyproject.eu). Check the
[contributors page](https://github.com/usegalaxy-eu/ansible-htcondor/graphs/contributors)
for detailed information.
