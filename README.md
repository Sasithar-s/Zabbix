# Zabbix Host Management

## Overview
This repository contains scripts and tools for managing Zabbix monitoring infrastructure, with a focus on bulk host configuration and management.

## Scripts

### zabbix host add.sh

A bash script that automates the bulk addition of hosts to Zabbix by generating XML export configurations from CSV input.

#### Purpose
- Streamline the addition of multiple hosts to Zabbix without manual UI interaction
- Generate Zabbix-compatible XML export files for batch host imports
- Configure SNMP monitoring interfaces automatically

#### Prerequisites
- Bash shell
- Standard Unix utilities: `cut`, `echo`, `cat`
- A CSV file with host information
- Zabbix 5.0+ (compatible with the XML schema used)

#### Input Format (CSV)
The script reads from `sample.csv` with the following comma-separated format:

```
hostname,ip_address,host_group,template
```

**Example:**
```
server1.example.com,192.168.1.10,Linux servers,Linux Server
server2.example.com,192.168.1.11,Hypervisors,Hypervisor
```

**Field Descriptions:**
- `hostname`: Fully qualified domain name of the host
- `ip_address`: IP address for SNMP monitoring
- `host_group`: Zabbix host group to assign the host to
- `template`: Zabbix template to link to the host

#### Output
The script generates an XML file compatible with Zabbix's import functionality containing:
- Host definitions with FQDN and short names
- SNMP interface configuration on port 161
- Template and host group assignments
- Inventory mode set to DISABLED

#### Usage

1. **Prepare your CSV file:**
   Create a `sample.csv` file in the same directory as the script with your host information.

2. **Run the script:**
   ```bash
   chmod +x "zabbix host add.sh"
   ./add.sh > zabbix_hosts.xml
   ```

3. **Import into Zabbix:**
   - Log in to Zabbix web interface
   - Navigate to: Configuration → Hosts
   - Click "Import" button
   - Upload the generated XML file
   - Confirm the import

#### Configuration Details

**SNMP Community Variable:**
The script uses `{
$SNMP_COMMUNITY}` macro which should be defined in your Zabbix configuration as a global macro or host macro.

**Host Properties:**
- Interface Type: SNMP
- Port: 161 (standard SNMP port)
- Inventory Mode: DISABLED

#### Example Workflow

```bash
# 1. Create your CSV file
echo "web-server.example.com,10.0.0.50,Linux servers,Linux Server" >> sample.csv
echo "db-server.example.com,10.0.0.51,Linux servers,MySQL Server" >> sample.csv

# 2. Generate XML
./add.sh > zabbix_export.xml

# 3. Import in Zabbix UI
# Use the XML file for bulk import
```

#### Notes
- Ensure the CSV file exists in the same directory as the script
- Empty lines in the CSV are automatically skipped
- The XML output is printed to stdout, redirect to a file for import
- SNMP community string must be configured in Zabbix before importing
- Validate CSV format before running to prevent import errors

#### Troubleshooting

**No output generated:**
- Verify `sample.csv` exists and is in the correct format
- Check for empty lines in the CSV file

**Import fails in Zabbix:**
- Ensure the host groups and templates exist in Zabbix
- Verify SNMP community macro is configured globally or on the template
- Check Zabbix server logs for detailed error messages

## Repository Structure
```
Zabbix/
├── zabbix host add.sh    # Main script for bulk host addition
├── sample.csv            # Example CSV file (create this)
└── README.md            # This file
```

## Requirements
- Zabbix Server 5.0 or higher
- Bash 3.0+
- Network connectivity to hosts for SNMP monitoring

## License
Released under the MIT license.

## Contributing
Feel free to submit issues or pull requests to improve the scripts.
