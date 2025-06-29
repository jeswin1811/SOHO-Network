# SOHO Network Design and Implementation

## Project Overview

### Problem Statement
XYZ company is a fast-growing company in Eastern Australia with more than 2 million customers globally. The company deals with selling and buying food items, which are basically operated from the headquarters. The company is intending to open a branch near the local village Bonalbo. Thus, the company requires young IT graduates to design the network for the branch. The network is intended to operate separately from the HQ network.

### Requirements
Being a small network, the company has the following requirements during implementation:
- One router and one switch to be used (all CISCO products)
- 3 departments (Admin/IT, Finance/HR and Customer service/Reception)
- Each department is required to be in different VLANs
- Each department is required to have wireless network for the users
- Host devices in the network are required to obtain IPv4 address automatically
- Devices in all the departments are required to communicate with each other

### Given Parameters
- **Base Network**: 192.168.1.0/24
- **ISP Assignment**: 192.168.1.0 network for branch operations

## Network Design

### Network Architecture
- **Topology**: Star topology with centralized switching
- **VLAN Implementation**: 802.1Q VLAN tagging
- **Routing Method**: Router-on-a-stick configuration
- **IP Assignment**: DHCP for automatic IP allocation

### Subnetting Design

#### Subnet Calculation
- **Base Network**: 192.168.1.0/24
- **Number of Subnets Required**: 3
- **Subnet Mask**: 255.255.255.192 (/26)
- **Hosts per Subnet**: 62 usable addresses

#### VLAN and Subnet Allocation

| Department | VLAN ID | Network Address | Subnet Mask | Usable Range | Gateway | Broadcast |
|------------|---------|-----------------|-------------|--------------|---------|-----------|
| Admin/IT | 10 | 192.168.1.0 | 255.255.255.192 | 192.168.1.1 - 192.168.1.62 | 192.168.1.62 | 192.168.1.63 |
| Finance/HR | 20 | 192.168.1.64 | 255.255.255.192 | 192.168.1.65 - 192.168.1.126 | 192.168.1.126 | 192.168.1.127 |
| Customer Service | 30 | 192.168.1.128 | 255.255.255.192 | 192.168.1.129 - 192.168.1.190 | 192.168.1.190 | 192.168.1.191 |

## Hardware Configuration

### Equipment Used
- **Router**: Cisco 2911 (2900 Series)
- **Switch**: Cisco Catalyst 2960 Series
- **Wireless Access Points**: Cisco Access Points (3 units)
- **End Devices**: PCs, Laptops, Smartphones, Tablets, Printers

### Physical Connections
- Router GigabitEthernet0/0 connected to Switch FastEthernet0/1 (Trunk Port)
- Access Points connected to respective VLAN access ports
- End devices connected to switch access ports and wireless access points

## Configuration Details

### Router Configuration (Cisco 2911)

#### Interface Configuration
```cisco
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 no shutdown

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.1.62 255.255.255.192

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.1.126 255.255.255.192

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.1.190 255.255.255.192
```

#### DHCP Pool Configuration
```cisco
ip dhcp pool Admin-Pool
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.62
 dns-server 192.168.1.62
 domain-name Admin.com

ip dhcp pool Finance-Pool
 network 192.168.1.64 255.255.255.192
 default-router 192.168.1.126
 dns-server 192.168.1.126
 domain-name Finance.com

ip dhcp pool CS-Pool
 network 192.168.1.128 255.255.255.192
 default-router 192.168.1.190
 dns-server 192.168.1.190
 domain-name CS.com
```

### Switch Configuration (Cisco Catalyst 2960)

#### Trunk Port Configuration
```cisco
interface FastEthernet0/1
 switchport mode trunk
```

#### VLAN Access Port Configuration
```cisco
! Admin/IT VLAN 10
interface FastEthernet0/2
 switchport access vlan 10
 switchport mode access

interface FastEthernet0/5
 switchport access vlan 10
 switchport mode access

interface FastEthernet0/6
 switchport access vlan 10
 switchport mode access

! Finance/HR VLAN 20
interface FastEthernet0/4
 switchport access vlan 20
 switchport mode access

interface FastEthernet0/7
 switchport access vlan 20
 switchport mode access

interface FastEthernet0/8
 switchport access vlan 20
 switchport mode access

! Customer Service VLAN 30
interface FastEthernet0/3
 switchport access vlan 30
 switchport mode access

interface FastEthernet0/9
 switchport access vlan 30
 switchport mode access

interface FastEthernet0/10
 switchport access vlan 30
 switchport mode access
```

### Wireless Configuration

#### Access Point Settings
- **Admin/IT Access Point**
  - SSID: Admin-WIFI
  - Security: WPA2-PSK
  - VLAN: 10

- **Finance/HR Access Point**
  - SSID: Finance-WIFI
  - Security: WPA2-PSK
  - VLAN: 20

- **Customer Service Access Point**
  - SSID: CS-WIFI
  - Security: WPA2-PSK
  - VLAN: 30

## Implementation Process

### Step 1: Network Planning
1. Analyzed requirements and calculated subnet requirements
2. Designed VLAN structure and IP addressing scheme
3. Selected appropriate Cisco hardware

### Step 2: Physical Setup
1. Connected router to switch via trunk port
2. Connected access points to designated VLAN ports
3. Connected end devices to appropriate switch ports

### Step 3: Router Configuration
1. Configured router subinterfaces with 802.1Q encapsulation
2. Assigned gateway IP addresses (last usable address of each subnet)
3. Configured DHCP pools for automatic IP assignment
4. Enabled inter-VLAN routing

### Step 4: Switch Configuration
1. Configured trunk port for router connection
2. Assigned switch ports to appropriate VLANs
3. Configured access ports for end devices

### Step 5: Wireless Configuration
1. Configured access point SSIDs for each department
2. Implemented WPA2-PSK security
3. Associated access points with respective VLANs

## Key Technical Decisions

### Router-on-a-Stick Implementation
- **Rationale**: Cost-effective solution using single router interface
- **Method**: 802.1Q VLAN tagging with subinterfaces
- **Benefit**: Enables inter-VLAN communication while maintaining VLAN isolation

### Subnetting Strategy
- **Challenge**: Efficiently utilizing 192.168.1.0/24 network for 3 departments
- **Solution**: /26 subnetting providing 62 hosts per department
- **Result**: Adequate addressing space with room for growth

### DHCP Configuration
- **Gateway Assignment**: Used last usable IP address of each subnet
- **DNS Server**: Router interfaces serve as DNS servers
- **Domain Names**: Separate domains for each department

## Testing and Verification

### Connectivity Testing
1. **Intra-VLAN Communication**: Verified devices within same VLAN can communicate
2. **Inter-VLAN Communication**: Confirmed cross-VLAN connectivity through router
3. **DHCP Functionality**: Verified automatic IP assignment for all devices
4. **Wireless Connectivity**: Tested wireless access for all departments

### Ping Tests Performed
- Admin devices to Finance devices ✓
- Finance devices to Customer Service devices ✓
- Customer Service devices to Admin devices ✓
- Wireless devices to wired devices ✓
- Gateway connectivity from all VLANs ✓

## Challenges and Solutions

### Primary Challenge: Subnetting
- **Issue**: Proper calculation and implementation of subnet addressing
- **Solution**: Calculated /26 subnetting to provide adequate hosts per VLAN
- **Result**: Efficient IP address utilization with 62 hosts per department

### Configuration Complexity
- **Issue**: Router-on-a-stick configuration with multiple VLANs
- **Solution**: Systematic approach to subinterface configuration and VLAN tagging
- **Result**: Successful inter-VLAN routing implementation

## Network Scalability

### Current Capacity
- **Total Addressable Hosts**: 186 (62 per VLAN)
- **Current Utilization**: Approximately 30% per department
- **Growth Potential**: Adequate for small office expansion

### Future Expansion Options
- Additional VLANs can be created within existing addressing scheme
- Switch port density allows for more end devices
- Wireless infrastructure supports additional access points

## Conclusion

The SOHO network implementation successfully meets all specified requirements:
- ✅ Separate VLANs for three departments
- ✅ Wireless connectivity for all departments
- ✅ Automatic IP address assignment via DHCP
- ✅ Inter-departmental communication capability
- ✅ Cost-effective single router/switch solution

The network provides a solid foundation for XYZ company's Bonalbo branch operations with room for future growth and expansion.
