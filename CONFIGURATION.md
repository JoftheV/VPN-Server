# OpenVPN Server Configuration Guide

This guide provides step-by-step instructions on configuring your OpenVPN server and client devices. It aims to ensure a secure and efficient VPN setup.

## Server Configuration

### Initial Setup

1. **Install OpenVPN**: Ensure OpenVPN is installed on your server. Refer to `INSTALLATION.md` for detailed instructions.

2. **Configure Easy-RSA for Certificate Management**:
   - Navigate to the Easy-RSA directory:
     ```bash
     cd /etc/openvpn/easy-rsa/
     ```
   - Source the vars file to set environmental variables:
     ```bash
     source vars
     ```
   - Clean the environment (optional) and build the CA:
     ```bash
     ./clean-all
     ./build-ca
     ```

3. **Generate Server Certificate and Keys**:
   - Generate the server key pair and certificate signing request (CSR):
     ```bash
     ./build-key-server server
     ```
   - Sign the server certificate using your CA:
     ```bash
     ./sign-req server
     ```
   - Generate Diffie-Hellman parameters for key exchange:
     ```bash
     ./build-dh
     ```

### Configuring the Server

1. **Edit the OpenVPN Configuration**:
   - Copy the example server configuration file and edit it:
     ```bash
     cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz /etc/openvpn/
     gzip -d /etc/openvpn/server.conf.gz
     nano /etc/openvpn/server.conf
     ```
   - Modify the configuration to fit your network setup, paying close attention to the following directives:
     - `ca`, `cert`, `key`, and `dh`: Point these to your generated CA certificate, server certificate, server key, and DH parameters.
     - `server`: Define the IP address pool used for VPN clients.
     - `push "redirect-gateway def1 bypass-dhcp"`: Uncomment to route all client traffic through the VPN.
     - `push "dhcp-option DNS ..."`: Set DNS servers to be pushed to clients.

2. **Firewall and Routing Configuration**:
   - Enable IP forwarding and configure `ufw` or `iptables` to allow VPN traffic. See `SECURITY.md` for secure firewall configurations.

3. **Start the OpenVPN Service**:
   - Enable and start the OpenVPN service:
     ```bash
     systemctl enable openvpn@server
     systemctl start openvpn@server
     ```

## Client Configuration

1. **Generate Client Certificates**:
   - Each client needs a unique certificate. Generate them similarly to the server certificate, signing each with your CA.

2. **Prepare Client Configuration Files (.ovpn)**:
   - Use the sample client configuration as a base. Include the CA certificate, client certificate, and client key within the file or reference them as external files.
   - Ensure the client configuration matches the server settings, especially the server IP address and port.

3. **Connect to the VPN**:
   - Use the OpenVPN client software to import the `.ovpn` file and establish a connection to your VPN server.

## Advanced Configuration Options

- **Security Enhancements**: Implement additional security measures, such as TLS auth and cipher adjustments, to strengthen your VPN's security posture. Refer to the OpenVPN manual for comprehensive security options.

- **Performance Tuning**: Adjust `sndbuf` and `rcvbuf` settings to optimize network performance based on your server's capabilities and expected load.

- **Logging and Monitoring**: Set up detailed logging for troubleshooting and monitoring. `log-append` and `status` directives in the server config can be used for this purpose.

For further customization and advanced configurations, consult the [OpenVPN 2.4 Manual](https://openvpn.net/community-resources/reference-manual-for-openvpn-2-4/).

## Troubleshooting

Refer to `TROUBLESHOOTING.md` for guidance on common issues and their resolutions.

---

For additional help and resources, the OpenVPN community and forums are valuable sources of information and support.
