# Step 1: Find your backup file (adjust path as needed)
ls -la /var/lib/vz/dump/

# Step 2: Create a working directory
mkdir -p ~/vm-export
cd ~/vm-export

# Step 3: Copy or link the backup file to your working directory
cp /var/lib/vz/dump/name_of_thevm.vma.zst ~/vm-export/

# Step 4: Decompress the zstd-compressed backup file
zstd -d the_youve_moved.zst -o backup.vma

# Step 5: Create extraction directory
mkdir -p extracted

# Step 6: Extract the VMA archive
vma extract -v backup.vma extracted

# Step 7: Create a new logical volume for storing the VMDK
sudo lvcreate -V 50G -T pve/data -n vmdk_export

# Step 8: Format the new logical volume
sudo mkfs.ext4 /dev/pve/vmdk_export

# Step 9: Mount the logical volume
sudo mkdir -p /mnt/vmdk_export
sudo mount /dev/pve/vmdk_export /mnt/vmdk_export

# Step 10: Convert the raw disk to VMDK format
sudo qemu-img convert -f raw -O vmdk ~/vm-export/extracted/tmp-disk-drive-scsi0.raw /mnt/vmdk_export/debian-full-pack.vmdk

# Step 11: Verify the conversion was successful
ls -lah /mnt/vmdk_export/
