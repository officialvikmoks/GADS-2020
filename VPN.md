#LAB: Virtual Private Networks (VPN)

#Objectives:
In this lab, you learn how to perform the following tasks:
  - Create VPN gateways in each network
  - Create VPN tunnels between the gateways
  - Verify VPN connectivity


#Task 1: Explore the networks and instances
#Explore the networks
  \\To view the available networks, run the following commands:
      gcloud compute networks list

#Explore the firewall rules
  \\To view the firewall rules, run the following commands:
      gcloud compute firewall-rules list

#Explore the instances and their connectivity
  \\To view the compute instances, run the following commands:
      gcloud compute instances list


\\Note the external and internal IP addresses for server-2
  **External 104.199.10.212
  **Internal 10.1.3.2
#Launch SSH session for server-1
    gcloud beta compute ssh --zone "us-central1-b" "server-1"

    \\To test connectivity to server-2's external IP address, run the following command, replacing server-2's external IP address with the value noted earlier:
        ping -c 3 104.199.10.212
    \\To test connectivity to server-2's internal IP address, run the following command, replacing server-2's internal IP address with the value noted earlier:
        ping -c 3 10.1.3.2
    \\Exit the SSH session using the following command:
        exit


\\Note the external and internal IP addresses for server-1.
  **External 34.121.15.92
  **Internal 10.5.4.2
#Launch SSH session for server-2
    gcloud beta compute ssh --zone "europe-west1-b" "server-2"

    \\To test connectivity to server-1's external IP address, run the following command, replacing server-1's external IP address with the value noted earlier:
        ping -c 3 34.121.15.92
    \\To test connectivity to server-1's internal IP address, run the following command, replacing server 1's internal IP address with the value noted earlier:
        ping -c 3 10.5.4.2
    \\Exit the SSH session using the following command:
        exit




#Task 2: Create the VPN gateways and tunnels
\\Reserve static address for vpn-1 using the following command:
      gcloud compute addresses create vpn-1-static-ip \
          --project=qwiklabs-gcp-03-02093c3983fe \
          --region=us-central1

\\Reserve static address for vpn-2 using the following command:
      gcloud compute addresses create vpn-2-static-ip \
          --project=qwiklabs-gcp-03-02093c3983fe \
          --region=europe-west1



#Create the vpn-1 gateway and tunnel1to2
      gcloud compute target-vpn-gateways create "vpn-1" \
          --region "us-central1" \
          --network "vpn-network-1"

      gcloud compute forwarding-rules create "vpn-1-rule-esp" \
          --region "us-central1" \
          --address "35.239.196.152" \
          --ip-protocol "ESP" \
          --target-vpn-gateway "vpn-1"

      gcloud compute forwarding-rules create "vpn-1-rule-udp500" \
          --region "us-central1" \
          --address "35.239.196.152" \
          --ip-protocol "UDP" \
          --ports "500" \
          --target-vpn-gateway "vpn-1"

      gcloud compute forwarding-rules create "vpn-1-rule-udp4500" \
          --region "us-central1" \
          --address "35.239.196.152" \
          --ip-protocol "UDP" \
          --ports "4500" \
          --target-vpn-gateway "vpn-1"

      gcloud compute vpn-tunnels create "tunnel1to2" \
          --region "us-central1" \
          --peer-address "35.187.1.212" \
          --shared-secret "gcprocks" \
          --ike-version "2" \
          --local-traffic-selector "0.0.0.0/0" \
          --target-vpn-gateway "vpn-1"

      gcloud compute routes create "tunnel1to2-route-1" \
          --network "vpn-network-1" \
          --next-hop-vpn-tunnel "tunnel1to2" \
          --next-hop-vpn-tunnel-region "us-central1" \
          --destination-range "10.1.3.0/24"


#Create the vpn-2 gateway and tunnel2to1
      gcloud compute target-vpn-gateways create "vpn-2" \
          --region "europe-west1" \
          --network "vpn-network-2"

      gcloud compute forwarding-rules create "vpn-2-rule-esp" \
          --region "europe-west1" \
          --address "35.187.1.212" \
          --ip-protocol "ESP" \
          --target-vpn-gateway "vpn-2"

      gcloud compute forwarding-rules create "vpn-2-rule-udp500" \
          --region "europe-west1" \
          --address "35.187.1.212" \
          --ip-protocol "UDP" \
          --ports "500" \
          --target-vpn-gateway "vpn-2"

      gcloud compute forwarding-rules create "vpn-2-rule-udp4500" \
          --region "europe-west1" \
          --address "35.187.1.212" \
          --ip-protocol "UDP" \
          --ports "4500" \
          --target-vpn-gateway "vpn-2"

      gcloud compute vpn-tunnels create "tunnel2to1" \
          --region "europe-west1" \
          --peer-address "35.239.196.152" \
          --shared-secret "gcprocks" \
          --ike-version "2" \
          --local-traffic-selector "0.0.0.0/0" \
          --target-vpn-gateway "vpn-2"

      gcloud compute routes create "tunnel2to1-route-1" \
          --network "vpn-network-2" \
          --next-hop-vpn-tunnel "tunnel2to1" \
          --next-hop-vpn-tunnel-region "europe-west1" \
          --destination-range "10.5.4.0/24"




#Task 3: Verify VPN connectivity
  \\Verify server-1 to server-2 connectivity
  \\Note the internal IP address for server-1 and server-2
    **server-1 35.239.196.152
    **server-2 35.187.1.212

  \\Launch SSH terminal for server-1
        gcloud beta compute ssh --zone "us-central1-b" "server-1"
      \\To test connectivity to server-2's internal IP address, run the following command:
          ping -c 3 35.187.1.212

  \\Launch SSH terminal for server-2
        gcloud beta compute ssh --zone "europe-west1-b" "server-2"
      \\To test connectivity to server-1's internal IP address, run the following command:
          ping -c 3 35.239.196.152


#Remove the external IP addresses
  \\stop the server-1 instance
      gcloud compute instances stop server-1
  \\change external IP address to none
      gcloud compute addresses delete server-1
  \\start the server-1 instance again
      gcloud compute instances start server-1 --zone=us-central1-b
