# Ansible Role: pve_i915_sriov_dkms

This Ansible role manages the i915 SR-IOV DKMS module on Proxmox VE, ensuring the proper setup and configuration for enabling Intel GPU virtualization.

## Requirements

- Intel Core processor 11th Gen (Tiger Lake only, not including Rocket Lake) through 13th Gen: See also [Graphics Virtualization Technologies Support for Each Intel®...](https://www.intel.com/content/www/us/en/support/articles/000093216/graphics/processor-graphics.html)
- Ansible version 2.9 or higher
- Proxmox VE environment
- Kernel version is between 6.17 and 7.0 for the latest i915-sriov-dkms release. For older kernels, use an older upstream release or branch as documented by strongtz/i915-sriov-dkms.

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `pve_i915_sriov_dkms_kern_ver` | `{{ ansible_kernel }}` | Kernel version |
| `pve_i915_sriov_dkms_force` | `false` | Force various actions |
| `pve_i915_sriov_dkms_force_git_clone` | `true` | Force git clone |
| `pve_i915_sriov_dkms_force_dkms_install` | `{{ pve_i915_sriov_dkms_force }}` | Force DKMS install |
| `pve_i915_sriov_dkms_force_download_adlp_dmc` | `{{ pve_i915_sriov_dkms_force }}` | Force download ADLP DMC |
| `pve_i915_sriov_dkms_assert_kernel_ver` | `{{ pve_i915_sriov_dkms_kern_ver.split('.')[0:2] \| join('.') }}` | Kernel version assertion |
| `pve_i915_sriov_dkms_assert_min_kernel_ver` | `"6.17"` | Minimum kernel version |
| `pve_i915_sriov_dkms_assert_max_kernel_ver` | `"7.0"` | Maximum kernel version |
| `pve_i915_sriov_dkms_sysfs_file` | `"/etc/sysfs.d/i915-sriov-dkms.conf"` | Sysfs file path |
| `pve_i915_sriov_dkms_sysfs_device_sriov_numvfs` | `{{ pve_i915_sriov_dkms_sysfs_device_path }}/sriov_numvfs` | Sysfs device path for SR-IOV VFs |
| `pve_i915_sriov_dkms_max_vfs` | `"7"` | Maximum VFs |
| `pve_i915_sriov_dkms_block_vfs` | `false` | Bind i915 VFs to `vfio-pci` on the host so only guests use them |
| `pve_i915_sriov_dkms_vfio_modules_load_file` | `"/etc/modules-load.d/vfio.conf"` | vfio-pci module-load configuration path |
| `pve_i915_sriov_dkms_vfio_udev_rules_file` | `"/etc/udev/rules.d/99-i915-vf-vfio.rules"` | udev rule path for binding i915 VFs to `vfio-pci` |
| `pve_i915_sriov_dkms_vf_vendor_id` | `"0x8086"` | PCI vendor ID matched by the VF udev rule |
| `pve_i915_sriov_dkms_vf_device_id` | detected from PF sysfs `device` | PCI device ID matched by the VF udev rule |
| `pve_i915_sriov_dkms_pf_pci_addr` | `{{ pve_i915_sriov_dkms_sysfs_device_path | basename }}` | iGPU Physical Function PCI address |
| `pve_i915_sriov_dkms_vf_pci_kernel_pattern` | derived from PF PCI address and `pve_i915_sriov_dkms_max_vfs` | udev `KERNEL` pattern for i915 VFs |
| `pve_i915_sriov_dkms_pkg_ver` | `"2026.05.06"` | Package version |
| `pve_i915_sriov_dkms_git_repo` | `"https://github.com/strongtz/i915-sriov-dkms.git"` | Git repository URL |
| `pve_i915_sriov_dkms_git_version` | `"b79661e"` | Git version |
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
| `pve_i915_sriov_dkms_grub_params` | list of parameters including `intel_iommu=on`, `iommu=pt`, `i915.enable_guc=3`, `i915.max_vfs=7`, and `module_blacklist=xe` | Grub parameters |

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


### Optional: Block VFs on the host

The upstream i915-sriov-dkms documentation recommends binding VFs to `vfio-pci` when the host should not use them directly. This can improve host stability and avoids host media or monitoring tools accidentally using VFs intended for guests. Enable this role feature with:

```yaml
pve_i915_sriov_dkms_block_vfs: true
```

When enabled, the role loads `vfio-pci`, creates a udev rule for the i915 VF PCI functions, regenerates initramfs, and reboots via the existing handlers.

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
