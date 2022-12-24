# Virtualisation Related Stuff

---

### Table of Contents
- [QEMU](#qemu)
  * [Convert Between Image Formats](#convert-between-image-formats)
- [VMWare](#vmware)
  * [Missing Manifest](#missing-manifest)
  * [Export VMs From ESX Without vCenter](#export-vms-from-esx-without-vcenter)
- [Proxmox](#proxmox)
  * [Import OVA or VMDK](#import-ova-or-vmdk)

---

## QEMU
Stuff related to `qemu`.

### Convert Between Image Formats
- Using `qemu-img convert` is a good choice

```bash
# -f ... define input format
# -O ... define output format
#
# Common formats: raw, vdi, vhd, vmdk, qcow2, qed
qemu-img convert -f raw -O qcow2 input_image.img output_image.qcow2
```

## VMWare
Stuff related to `vmware`.

### Missing Manifest
- If exporting a VM as `ovf` leaves you with no manfifest file, re-create it

```bash
openssl sha256 exported_vm.vmdk exported_vm.nvram exported_vm.ovf > exported_vm.mf
```

### Export VMs From ESX Without vCenter
- If you need to export a VM from an ESX host directly (maybe you have no vCenter or no needed lics)

```bash
# Best is to load them from VMWare directly ---> https://customerconnect.vmware.com/downloads/#all_products
chmod +x VMware-ovftool-4.4.0-15722219-lin.x86_64.bundle
./VMware-ovftool-4.4.0-15722219-lin.x86_64.bundle --extract vmware-ovftool
scp -r vmware-ovftool root@A.B.C.D:/vmfs/volumes/someDatastore/

# Connect to host before
sed -i 's/bash/sh/' /vmfs/volumes/someDatastore/vmware-ovftool/ovftool
chmod +x /vmfs/volumes/someDatastore/vmware-ovftool/ovftool
./ovftool --noSSLVerify vi://A.B.C.D/dir_of_vm_to_export /vmfs/volumes/someDatastore/exported_vm.ova
```

## Proxmox
Stuff related to `proxmox`.

### Import OVA or VMDK
- If you need to import an ova or vmdk and be able to take snapshots

```bash
# Create vm in proxmox without harddisk before and take a note of the vmid (= target_vm_id)
# Transfer vm to porxmox and extract 
tar xvf vm_to_import.ova

# Import and convert
qm importdisk target_vm_id vm_to_import_disk.vmdk local -format qcow2
```


