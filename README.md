📝 Project Summary: The "Infrastructure as Code" Journey
This project simulates a real-world enterprise network migration from manual configuration to Ansible-driven automation. The core challenge was not just pushing configurations, but ensuring the multi-vendor environment (Cisco & Fortinet) maintained a seamless control plane via OSPF.

Key Technical Achievements

High-Availability Firewalling: Configured a FortiGate HA Cluster with automated policy generation and NAT.

Dynamic Route Propagation: Solved the "Internet Black Hole" issue by automating OSPF default-route injection (default-information-originate) from the perimeter to the access layer.

Hierarchical Scalability: Used Ansible loops to manage a 6-switch access layer, standardizing VLANs, SVIs, and default gateways across the fabric.

Security-First Automation: Sanitized all playbooks by migrating hardcoded credentials into an encrypted/variable-based structure to prevent sensitive data leakage.

# ansible-fortinet-cisco-enterprise-lab
Automated Multi-Tier Network Deployment (Ansible & FortiOS)
This project automates the deployment of a robust, three-tier enterprise network architecture. It features a high-availability FortiGate firewall cluster, Cisco IOS Distribution switches, and a scaled Access layer.

🚀 Project Overview
The goal was to move away from manual CLI configuration and implement a fully automated "Push-to-Production" workflow.

Core/Firewall: FortiGate HA Cluster (OSPF & NAT)

Distribution Layer: Layer 3 Cisco Switches (Inter-VLAN Routing & OSPF)

Access Layer: Layer 2 Cisco Switches (VLANs & Port Security)

🛠️ The Troubleshooting "War Room" (Key Challenges)
The most valuable part of this project was the deep-dive troubleshooting required to achieve end-to-end connectivity.

1. The OSPF "Gateway of Last Resort" Issue
Problem: Even after OSPF neighbors reached FULL state, the internal switches couldn't reach the internet.

Discovery: The switches lacked a "Default Route" because OSPF doesn't share the internet path by default.

Solution: We updated the FortiGate Ansible task to include default_information_originate: "always". This forced the firewalls to announce themselves as the gateway to the Distro layer.

2. FortiOS Case Sensitivity & Object Dependencies
Problem: Playbook failed with Error in repo or node_check_object fail.

Discovery: We found two issues: FortiOS interface names are case-sensitive (e.g., port1 vs Port1), and address groups cannot be created until the individual address objects exist.

Solution: Refactored the playbook to use loops for address objects, ensuring they were fully committed to the database before the group or policy tasks were executed.

3. Asymmetric Routing & NAT
Problem: Internal pings reached the FortiGate but never received a reply.

Discovery: The "Net" cloud did not have a return route to our private 192.168.x.x subnets.

Solution: Enabled Source NAT (SNAT) on the FortiGate firewall policy. This masked internal traffic behind the firewall's WAN IP, allowing the internet to route the replies back correctly.

📂 Repository Structure
deploy_forti.yml: Configures OSPF, NAT Policies, and Address Objects.

deploy_distro.yml: Configures SVIs, L3 Routing, and OSPF Adjacency.

deploy_access.yml: Configures L2 VLANs and default gateways.

hosts.yml: Inventory file defining device groups.

🏁 How to Run
Bash
# To deploy core routers and distro switches
ansible-playbook -i inventory/hosts.yml playbooks/deploy_network.yml --limit core_routers,distro_switches

# To deploy the full Firewall
ansible-playbook -i inventory/hosts.yml deploy_forti.ymL
