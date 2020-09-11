#LAB: Creating Virtual Machines

#Objectives:

In this lab, you explore the available options for VMs and see the differences between locations.
In this lab, you learn how to perform the following tasks:

  - Create several standard VMs
  - Create advanced VMs

#Task 1: Create a utility virtual machine
  \\Create a Virtual Machine within the us-central1 region and the us-central1-c zone.
  \\The machine type should be n1-standard-1 type with 1 vCPU and 3.75 GB memory.
  \\To create a virtual machine, run the following commands;  
      gcloud beta compute instances create my-new-instance \
          --zone=us-central1-c \
          --machine-type=n1-standard-1 \
          --subnet=default \
          --no-address \
          --maintenance-policy=MIGRATE \
          --image=debian-10-buster-v20200910 \
          --image-project=debian-cloud \
          --boot-disk-size=10GB --boot-disk-type=pd-standard \
          --boot-disk-device-name=my-new-instance \
          --no-shielded-secure-boot \
          --no-shielded-vtpm \
          --no-shielded-integrity-monitoring \
          --reservation-affinity=any


#Task 2: Create a Windows virtual machine
  \\Create a Windows virtual machine within the europe-west2 region and the europe-west2-a zone
  \\The  machine type should be n1-standard-2 type with 2 vCPUs, 7.5 GB memory
  \\The machine is to allow HTTP and HTTPS firewall rules
  \\To create a the windows virtual machine, run the following commands;
      gcloud beta compute instances create new-instance-2 \
          --zone=europe-west2-a \
          --machine-type=n1-standard-2 \
          --subnet=default \
          --network-tier=PREMIUM \
          --maintenance-policy=MIGRATE \
          --image=windows-server-2016-dc-core-v20200908 \
          --image-project=windows-cloud \
          --boot-disk-size=100GB \
          --boot-disk-type=pd-ssd \
          --boot-disk-device-name=new-instance-2 \
          --no-shielded-secure-boot --shielded-vtpm \
          --shielded-integrity-monitoring \
          --reservation-affinity=any

  \\For the firewall rules to allow HTTP, run the following codes;
      gcloud compute firewall-rules create default-allow-http \
          --direction=INGRESS \
          --priority=1000 \
          --network=default \
          --action=ALLOW \
          --rules=tcp:80 \
          --source-ranges=0.0.0.0/0 \
          --target-tags=http-server

  \\For the firewall rules to allow https, run the following codes;
      gcloud compute firewall-rules create default-allow-https \
          --direction=INGRESS \
          --priority=1000 \
          --network=default \
          --action=ALLOW \
          --rules=tcp:443 \
          --source-ranges=0.0.0.0/0 \
          --target-tags=https-server


#Set password for the VM
\\password can only be set in from the cloud console or via REST
\\To set a password for the VM using REST use the following commands;

{
  "canIpForward": false,
  "confidentialInstanceConfig": {
    "enableConfidentialCompute": false
  },
  "cpuPlatform": "Intel Broadwell",
  "creationTimestamp": "2020-09-11T08:32:30.642-07:00",
  "deletionProtection": false,
  "description": "",
  "disks": [
    {
      "autoDelete": true,
      "boot": true,
      "deviceName": "new-instance-2",
      "diskSizeGb": "100",
      "guestOsFeatures": [
        {
          "type": "MULTI_IP_SUBNET"
        },
        {
          "type": "UEFI_COMPATIBLE"
        },
        {
          "type": "VIRTIO_SCSI_MULTIQUEUE"
        },
        {
          "type": "WINDOWS"
        }
      ],
      "index": 0,
      "interface": "SCSI",
      "kind": "compute#attachedDisk",
      "licenses": [
        "projects/windows-cloud/global/licenses/windows-server-2016-dc",
        "projects/windows-cloud/global/licenses/windows-server-core"
      ],
      "mode": "READ_WRITE",
      "source": "projects/qwiklabs-gcp-03-71de7022df6e/zones/europe-west2-a/disks/new-instance-2",
      "type": "PERSISTENT"
    }
  ],
  "displayDevice": {
    "enableDisplay": false
  },
  "id": "376180042854966883",
  "kind": "compute#instance",
  "labelFingerprint": "42WmSpB8rSM=",
  "machineType": "projects/qwiklabs-gcp-03-71de7022df6e/zones/europe-west2-a/machineTypes/n1-standard-2",
  "metadata": {
    "fingerprint": "nAU5TL3M7io=",
    "kind": "compute#metadata"
  },
  "name": "new-instance-2",
  "networkInterfaces": [
    {
      "accessConfigs": [
        {
          "kind": "compute#accessConfig",
          "name": "External NAT",
          "natIP": "34.105.227.26",
          "networkTier": "PREMIUM",
          "type": "ONE_TO_ONE_NAT"
        }
      ],
      "fingerprint": "63nJ63uvx3g=",
      "kind": "compute#networkInterface",
      "name": "nic0",
      "network": "projects/qwiklabs-gcp-03-71de7022df6e/global/networks/default",
      "networkIP": "10.154.0.2",
      "subnetwork": "projects/qwiklabs-gcp-03-71de7022df6e/regions/europe-west2/subnetworks/default"
    }
  ],
  "reservationAffinity": {
    "consumeReservationType": "ANY_RESERVATION"
  },
  "scheduling": {
    "automaticRestart": true,
    "onHostMaintenance": "MIGRATE",
    "preemptible": false
  },
  "selfLink": "projects/qwiklabs-gcp-03-71de7022df6e/zones/europe-west2-a/instances/new-instance-2",
  "serviceAccounts": [
    {
      "email": "173683386471-compute@developer.gserviceaccount.com",
      "scopes": [
        "https://www.googleapis.com/auth/devstorage.read_only",
        "https://www.googleapis.com/auth/logging.write",
        "https://www.googleapis.com/auth/monitoring.write",
        "https://www.googleapis.com/auth/servicecontrol",
        "https://www.googleapis.com/auth/service.management.readonly",
        "https://www.googleapis.com/auth/trace.append"
      ]
    }
  ],
  "shieldedInstanceConfig": {
    "enableIntegrityMonitoring": true,
    "enableSecureBoot": false,
    "enableVtpm": true
  },
  "startRestricted": false,
  "status": "RUNNING",
  "tags": {
    "fingerprint": "6smc4R4d39I=",
    "items": [
      "http-server",
      "https-server"
    ]
  },
  "zone": "projects/qwiklabs-gcp-03-71de7022df6e/zones/europe-west2-a"
}





#Task 3: Create a custom virtual machine
  \\Create a Virtual Machine within the us-west1 region and the us-west1-b zone.
  \\The machine type is CUSTOM type with 6 vCPU and 32 GB memory.
  \\To create a custom virtual machine, run the following commands;
        gcloud beta compute instances create new-instance-custom \
            --zone=us-west1-b \
            --machine-type=e2-custom-6-32768 \
            --subnet=default \
            --network-tier=PREMIUM \
            --maintenance-policy=MIGRATE \
            --image=debian-10-buster-v20200910 \
            --image-project=debian-cloud \
            --boot-disk-size=10GB \
            --boot-disk-type=pd-standard \
            --boot-disk-device-name=new-instance-custom \
            --no-shielded-secure-boot \
            --no-shielded-vtpm \
            --no-shielded-integrity-monitoring \
            --reservation-affinity=any

#Connect via SSH to your custom VM
  \\Start SSH session from the cloud shell with this command
      gcloud alpha cloud-shell ssh
  \\To see information about unused and used memory and swap space on your custom VM, run the following command:
      free
  \\To see details about the RAM installed on your VM, run the following command:
      sudo dmidecode -t 17
  \\To verify the number of processors, run the following command:
      nproc
  \\To see details about the CPUs installed on your VM, run the following command:
      lscpu
  \\To exit the SSH terminal, run the following command:
      exit
