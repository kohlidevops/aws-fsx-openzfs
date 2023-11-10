# Amazon FSx for OpenZFS 

Amazon FSx for OpenZFS provides simple, cost-effective, high-performance file storage built on the OpenZFS file system. 

Broadly accessible from Linux, Windows, and macOS compute instances and containers via the industry-standard NFS protocol (v3, v4.0, v4.1, and v4.2). 

Provides powerful OpenZFS data management capabilities including Z-Standard compression, instant point-in-time snapshots, and data cloning. 

Delivers up to 1 million IOPS with latencies of just a few hundreds of microseconds, and up to 12.5 GB/s of throughput. 

Offers highly available and highly durable single-AZ SSD storage with built-in, fully managed backups. 

Enables cost-efficient shared file systems across multiple users and applications with multiple volumes per file system, thin provisioning, and user/group quotas. 

## To Create OpenZFS file system 

Navigate to AWS Management console - Select - FSx 

Select - Amazon FSx for OpenZFS - Standard Create 

File system name - <meaningful-name> 

SSD storage capacity - 64 GB <Min - 64GB and Max - 512 TB> 

Provisioned IOPS - Automatic (3 IOPS per GB of SSD storage) 

Throughput capacity - 64 MB/s 

or 

Specify throughput capacity - 64, 128, 256, 512,.. 

### Network & Security 

Virtual Private Cloud - <Your VPC> 

VPC Security group - <Your Security group> 

Subnet - <Specify the subnet in which your file system's network interface resides. Both FS subnet and EC2 subnet should be same for better AZ performance> 

Encryption - Enter your KMS 

### Root volume configuration 

Data compression type - Z-Standard or LZ4 or No compression 

Copy tags to snapshot - Enabled or Disabled 

### NFS exports 

Client addresses - * 

NFS option - rw,crossmnt 

Record size - Default (128 KB) 

User and group quotas - If need 

Backup and maintenance - If need 

Next - to create a file system. 

  
## OpenZFS with Windows EC2 Instance 

Launch AWS EC2 windows server with same file system subnet. 

### RDP to server. 

#### Install NFS client 

Open Powershell As a Administrator and execute below command 

    >Install-WindowsFeature -Name NFS-Client 

#### Mount a Drive (Try this as local user since volume mount will not be visible for Administrator) 

Open Powershell and execute below command 

    >New-PSDrive -Name U -Root \\fs-04f99707580a5f491.fsx.us-east-1.amazonaws.com\fsx\ -Persist -PSProvider "FileSystem" 
    <U - it's a drive letter. It should not be used for any purpose before mounting.> 

      Refer: (https://stackoverflow.com/questions/51640983/powershell-map-network-drive-if-does-not-exists) 
        Or 
    Open Command prompt and execute below command 
    > mount \\fs-0450d2517febff803.fsx.us-east-1.amazonaws.com\fsx\ A: 
    A is a drive letter 

#### To Create a one or multiple volume with parent filesystem 

Select Volume - Create Volume 

File system - <select - Parent filesystem> 

Parent volume id - <Parent volume id> 

Volume name - <vol1> 

Storage capacity quota - <10 GB> //defines a maximum storage 

Storage capacity reservation - <10 GB> //Guarntee a minimum storage 

Data compression type - <No compression> 

Copy tags to snapshot - <Enabled> //if need 

NFS exports - Client address (*) NFS Options (rw,crossmnt)	//default 

Record size - Default (128 KiB) or User-configured //if need 

Confirm to create 

//You can create a multiple volume from Parent volume. So, All child volumes are derived from Parent volume. 

//Sum of all child volumes should not exceed or equal to Parent total volume size 

#### To Mount a child volume in Windows Server 

Open a Windows command prompt 

    >mount \\fs-0450d2517febff803.fsx.us-east-1.amazonaws.com\fsx\vol3\ R: 

#### To custom a mapped drive 

    >Powershell.exe 
    >$rename = new-object -ComObject Shell.Application 
    >$rename.NameSpace("R:\").Self.Name = "Shareddrive" 

#### To Back up a parent filesystem 

Navigate to OpenZFS filesystem 

Filesystems - select your parent filesystem 

Actions - create backup 

Backup name - <zfs-backup> //meaningfull name 

Create backup 

#### To restore a Backup 

//After created backup, it should be available in Backups option 

Backups - Select your backup - Restore backup 

File system name - <RestoredFilesystem> 

Throughput capacity - 64 MB 

Network and Security - VPC - <Select your VPC> 

VPC Security group - <Select your Security group> 

Sunet - <Select your subnet> 

Encryption - <Encryption key> 

Backup and maintenance - <Enabled> //if need 

Weekly maintenance window - No preference //if need enable 

Next to restore a filesystem from backup 

//once created a restored volumes should be available in Volumes. 

//You can mount this volume and ensure that restored volumes are sliced as original volume and data's are available. 

 
#### Not worked once, 

    https://stackoverflow.com/questions/70388601/amazon-fsx-for-openzfs-mount-connection-time-out/71672086#71672086 

 
