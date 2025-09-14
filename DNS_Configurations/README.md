# Lab 03 - DNS Configuration Files
## Computer Networks Laboratory

This directory contains all the DNS configuration files and documentation for Laboratory 03.

## Directory Structure

```
DNS_Configurations/
├── README.md                    # This file
├── slackware_named.conf        # Slackware main DNS configuration
├── solaris_named.conf          # Solaris main DNS configuration
├── andersson.org.uk.zone       # Zone file for andersson.org.uk domain
├── cristian.com.it.zone        # Zone file for cristian.com.it domain
└── Testing_Guide.md            # Comprehensive testing procedures
```

## Implementation Overview

### Server Configuration
- **Slackware (10.2.77.176)**: Primary DNS for andersson.org.uk, Secondary for cristian.com.it
- **Solaris (10.2.77.178)**: Primary DNS for cristian.com.it, Secondary for andersson.org.uk

### Domain Structure
- **cristian.com.it**: Contains 4 A records, 2 AAAA records, 4 CNAME records
- **andersson.org.uk**: Contains 4 A records, 2 AAAA records, 3 CNAME records

## File Installation Instructions

### Slackware Server Setup
1. Copy `slackware_named.conf` to `/etc/named.conf`
2. Copy `andersson.org.uk.zone` to `/var/named/andersson.org.uk.zone`
3. Create SOA file as shown in the main LaTeX document
4. Start service: `/usr/sbin/named`

### Solaris Server Setup
1. Copy `solaris_named.conf` to `/etc/named.conf`
2. Copy `cristian.com.it.zone` to `/var/named/cristian.com.it.zone`
3. Create root servers file as shown in the main document
4. Create localhost zone files
5. Start service: `/usr/sbin/named`

## Testing Procedures

Follow the comprehensive testing guide in `Testing_Guide.md` to verify:
- DNS resolution functionality
- Zone transfers between servers
- Different record types (A, AAAA, CNAME, NS)
- External domain resolution
- Service reliability and performance

## Configuration Notes

### Important IP Addresses
- Network Range: 10.2.77.0/24
- Gateway: 10.2.65.1
- Slackware DNS: 10.2.77.176
- Solaris DNS: 10.2.77.178

### Security Considerations
- Zone transfers are restricted to authorized servers only
- Proper file permissions must be set on all configuration files
- Regular backup of zone files is recommended

### Performance Optimization
- TTL values are set to 86400 seconds (24 hours) for stability
- Refresh intervals configured for optimal zone synchronization
- Recursive queries enabled for external domain resolution

## Troubleshooting

Common issues and solutions:
1. **Service won't start**: Check configuration syntax with `named-checkconf`
2. **Zone transfer fails**: Verify allow-transfer and masters settings
3. **Resolution not working**: Check /etc/resolv.conf on client machines
4. **Permission errors**: Ensure proper ownership of /var/named directory

## Video Demonstration Requirements

Your 5-minute video should demonstrate:
1. DNS service startup on both servers
2. nslookup commands showing successful resolution
3. Zone transfer verification
4. External domain resolution testing
5. Brief explanation of the configuration

## Deliverables Checklist

- [ ] PDF document (generated from LaTeX) - Maximum 15 pages
- [ ] All DNS configuration files (this directory)
- [ ] 5-minute video demonstration
- [ ] Configuration files properly documented and commented

## Contact Information

For questions about this configuration, contact:
- Student 1: Cristian
- Student 2: Andersson
- Course: Computer Networks
- Lab: DNS Server Implementation

---
*This configuration was implemented as part of Laboratory 03 - DNS Server Configuration and Implementation*
