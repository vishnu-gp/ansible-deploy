# Use Debian 11 ARM64 as the base image
FROM debian:bullseye-slim

# Install OpenVPN and other dependencies
RUN apt-get update && apt-get install -y openvpn iptables

# Install EasyRSA
RUN apt-get update && apt-get install -y easy-rsa

# Copy OpenVPN server configuration files
COPY server.conf /etc/openvpn/

# Expose OpenVPN port
EXPOSE 1194/udp

# Create Easy RSA dir if not exists
RUN mkdir -p /etc/openvpn/easy-rsa

# Initialize PKI and build CA
RUN cd /etc/openvpn/easy-rsa  && \
    /usr/share/easy-rsa/easyrsa init-pki && \
    printf '%s\n' \
    "IN" \
    "Kerala" \
    "Kozhikode" \
    "Vannucherum" \
    "hello@vannucherum.com" \
    "IT Department" | \
    /usr/share/easy-rsa/easyrsa build-ca nopass

# Build the server certificate and key
RUN cd /etc/openvpn/easy-rsa && \
    /usr/share/easy-rsa/easyrsa build-server-full server nopass

# Generate Diffie-Hellman parameters
RUN cd /etc/openvpn/easy-rsa && \
    /usr/share/easy-rsa/easyrsa gen-dh

# Generate TLS Auth key
RUN openvpn --genkey --secret /etc/openvpn/easy-rsa/pki/ta.key

# Start OpenVPN server
CMD ["openvpn", "--config", "/etc/openvpn/server.conf"]