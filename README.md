# Ansible Role: pve_i915_sriov_dkms

This Ansible role manages the i915 SR-IOV DKMS module on Proxmox VE, ensuring the proper setup and configuration for enabling Intel GPU virtualization.

## Requirements

- Ansible version 2.9 or higher
- Proxmox VE environment
- Kernel version is between 6.1 and 6.5

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `pve_i915_sriov_dkms_kern_ver` | `{{ ansible_kernel }}` | Kernel version |
| `pve_i915_sriov_dkms_force` | `false` | Force various actions |
| `pve_i915_sriov_dkms_force_git_clone` | `true` | Force git clone |
| `pve_i915_sriov_dkms_force_dkms_install` | `{{ pve_i915_sriov_dkms_force }}` | Force DKMS install |
| `pve_i915_sriov_dkms_force_download_adlp_dmc` | `{{ pve_i915_sriov_dkms_force }}` | Force download ADLP DMC |
| `pve_i915_sriov_dkms_assert_kernel_ver` | `{{ pve_i915_sriov_dkms_kern_ver.split('.')[0:2] | join('.') }}` | Kernel version assertion |
| `pve_i915_sriov_dkms_assert_min_kernel_ver` | `"6.1"` | Minimum kernel version |
| `pve_i915_sriov_dkms_assert_max_kernel_ver` | `"6.5"` | Maximum kernel version |
| `pve_i915_sriov_dkms_sysfs_file` | `"/etc/sysfs.d/i915-sriov-dkms.conf"` | Sysfs file path |
| `pve_i915_sriov_dkms_sysfs_device_sriov_numvfs` | `{{ pve_i915_sriov_dkms_sysfs_device_path }}/sriov_numvfs` | Sysfs device path for SR-IOV VFs |
| `pve_i915_sriov_dkms_max_vfs` | `"7"` | Maximum VFs |
| `pve_i915_sriov_dkms_pkg_ver` | `{{ pve_i915_sriov_dkms_kern_ver.split('.')[0:2] | join('.') }}` | Package version |
| `pve_i915_sriov_dkms_git_repo` | `"https://github.com/strongtz/i915-sriov-dkms.git"` | Git repository URL |
| `pve_i915_sriov_dkms_git_version` | `"d2b3b6e374c2f1799fb5bc07aad1f4ed5d058919"` | Git version |
| `pve_i915_sriov_dkms_dest_dir` | `/usr/src/i915-sriov-dkms-{{ pve_i915_sriov_dkms_pkg_ver }}` | Destination directory |
| `pve_i915_sriov_dkms_conf_file` | `{{ pve_i915_sriov_dkms_dest_dir }}/dkms.conf` | Configuration file path |
| `pve_i915_sriov_dkms_patch_dir` | `{{ pve_i915_sriov_dkms_dest_dir }}/patches` | Patch directory |
| `pve_i915_sriov_dkms_repo_non_free_firmware_url` | `"http://deb.debian.org/debian"` | Non-free firmware repository URL |
| `pve_i915_sriov_dkms_repo_non_free_firmware` | `deb {{ pve_i915_sriov_dkms_repo_non_free_firmware_url }} {{ ansible_distribution_release }} non-free-firmware` | Non-free firmware repository |
| `pve_i915_sriov_dkms_repo_non_free_driver_url` | `"http://deb.debian.org/debian"` | Non-free driver repository URL |
| `pve_i915_sriov_dkms_repo_non_free_driver` | `deb {{ pve_i915_sriov_dkms_repo_non_free_driver_url }} {{ ansible_distribution_release }} non-free` | Non-free driver repository |
| `pve_i915_sriov_dkms_repo_filename` | `"non-free"` | Repository filename |
| `pve_i915_sriov_dkms_adlp_dmc_url` | `"https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/plain/i915/adlp_dmc.bin"` | ADLP DMC URL |
| `pve_i915_sriov_dkms_adlp_dmc_dest` | `"/lib/firmware/i915/adlp_dmc.bin"` | ADLP DMC destination |
| `pve_i915_sriov_dkms_grub_config` | `"/etc/default/grub"` | Grub configuration file path |
| `pve_i915_sriov_dkms_grub_params` | list of parameters | Grub parameters |

## Dependencies

- None

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: ktooi.pve_i915_sriov_dkms
      vars:
        pve_i915_sriov_dkms_force: true
```

## Installation

```sh
ansible-galaxy install ktooi.pve_i915_sriov_dkms
```

## Usage

Ensure that you have met all requirements and then include this role in your playbook as shown in the example. Customize variables as needed.

## Authors

* **Kodai Tooi** [GitHub](https://github.com/ktooi), [Qiita](https://qiita.com/ktooi)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

## Changelog

### v1.0.0
- Initial release

## Contribution Guidelines

Please open issues or submit pull requests for any bugs or feature requests. Contributions are welcome!

This `README.md` provides a comprehensive overview of the Ansible role, its variables, usage, and other essential information for users.
