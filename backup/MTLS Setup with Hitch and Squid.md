# Install Squid

Use Linux machine to install squid
```
sudo apt update
sudo apt install squid
```

Config squid, change "/etc/squid/squid.conf" and allow all traffic

```
# And finally deny all other access to this proxy
#http_access deny all
http_access allow all
```

Restart squid:

```
sudo systemctl restart squid
```

Verify squid works (10.231.249.222 is the ip address of the machine):

```
curl -x http://10.231.249.222:3128 https://myip.ipip.net
```

# Install Hitch

Then install Hitch, old version may not support MTLS, so just grab the latest one:

```
wget https://hitch-tls.org/source/hitch-1.8.0.tar.gz
tar zxvf hitch-1.8.0.tar.gz
sudo apt-get install libev-dev libssl-dev automake python3-docutils flex bison pkg-config make -y
cd hitch-1.8.0
./configure;make;sudo make install
```

Create Hitch Config "/etc/hitch/hitch.conf" as below:

```
frontend = {
    host = "*"
    port = "8443"
}
backend = "[127.0.0.1]:3128"    # 6086 is the default Varnish PROXY port.
workers = 4                     # number of CPU cores

daemon = off

# We strongly recommend you create a separate non-privileged hitch
# user and group
user = "hitch"
group = "hitch"

# Enable to let clients negotiate HTTP/2 with ALPN. (default off)
# alpn-protos = "h2, http/1.1"

# run Varnish as backend over PROXY; varnishd -a :80 -a localhost:6086,PROXY ..
# write-proxy-v2 = on               # Write PROXY header

# Only support strong ciphers and TLSv1.2, TLSv1.3
tls-protos = TLSv1.2 TLSv1.3
ciphers = "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:!aNULL:!eNULL:!EXPORT:!LOW:!MD5:!DES:!3DES:!RC2:!RC4:!PSK:!kDH"

syslog = on
log-level = 2

# The path to the combined server certificate and its private key.
pem-file = "/etc/hitch/cert/server.pem"
```

Create user "hitch":
sudo useradd -r -U -s /bin/false hitch

# Test TLS

Create server certificate and private key:

```
mkdir cert
cd cert
openssl req -newkey rsa:2048 -nodes -keyout server.key -x509 -days 365 -out server.crt -subj "/CN=yourdomain.com" -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "\n[SAN]\nsubjectAltName=DNS:yourdomain.com,DNS:www.yourdomain.com"))
cat server.key server.crt > server.pem
sudo mkdir -p /etc/hitch/cert
sudo cp server.pem /etc/hitch/cert/
```

Add domain name in "/etc/hosts":

```
10.231.249.222 www.yourdomain.com
```

Add the Self-Signed Certificate to the Trust Store:

```
sudo cp server.crt /usr/local/share/ca-certificates/my-self-signed.crt
sudo update-ca-certificates
```

Run Hitch:

```
/usr/local/sbin/hitch --test --config /etc/hitch/hitch.conf # validate conf first
/usr/local/sbin/hitch --user hitch --group hitch --config /etc/hitch/hitch.conf --daemon
```

Test with curl:

```
curl -x https://www.yourdomain.com:8443 https://myip.ipip.net
```

# Test MTLS

Create the "client.ext" file with the following content:

```
# client.ext
basicConstraints=CA:FALSE
nsCertType=client
nsComment="OpenSSL Generated Client Certificate"
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer:always
keyUsage=critical,digitalSignature,keyEncipherment
extendedKeyUsage=clientAuth
```

Create client certificate and private key:

```
openssl genrsa -out ca.key 2048
openssl req -new -x509 -days 3650 -key ca.key -out ca.crt

openssl genrsa -out client.key 2048
openssl req -new -key client.key -out client.csr
openssl x509 -req -days 365 -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -extfile client.ext -out client.crt

sudo cp ca.crt /etc/hitch/cert/
```

Now add the following content in "/etc/hitch/hitch.conf" to enable MTLS on Hitch

```
# This forces clients to present a valid client certificate.
client-verify = required

# Client certificate authority file used to verify client certificates for mTLS.
client-verify-ca = "/etc/hitch/cert/ca.crt"
```

Now run Hitch

```
/usr/local/sbin/hitch --test --config /etc/hitch/hitch.conf # validate conf first
/usr/local/sbin/hitch --user hitch --group hitch --config /etc/hitch/hitch.conf --daemon
```

Verify MTLS setup with curl

```
      MTLS           TLS           TLS
curl -------> Hitch ------> Squid -----> Dst
```

```
curl --cacert ca.crt --proxy-cert client.crt --proxy-key client.key -x https://www.yourdomain.com:8443  https://myip.ipip.net
```

> [!NOTE]
> Should be noticed that currently only tls3 can be used in this case, https://github.com/curl/curl/issues/12286, so it's hard to verify from wireshark, since all certificate exchanges are encrypted after the TLS handshake. But we have Hitch configured to require MTLS, so if it succeeds, it should be using MTLS.

# Enable Proxy Protocol

The PROXY protocol was designed to help carry connection information from the source requesting side through a proxy server to the destination, especially when network address translation (NAT) is involved. It provides a way to preserve the original end-user IP addresses and TCP/UDP port numbers when traffic is routed through proxies and load balancers. The information is typically added to the header of the request forwarded to the destination server.

There are two versions of the PROXY protocol: V1 and V2.
- V1 is human-readable ASCII, V2 is binary.
- V1 headers are simpler but more verbose, V2 headers are more flexible and can carry additional information using TLV (Type-Length-Value) vectors..
- V2 can support protocols other than TCP, e.g., UDP.
It needs to be enabled at both Squid and Hitch.
Add the following in "/etc/hitch/hitch.conf":

```
proxy-proxy = on
```

Change "http_port 3128" in "/etc/squid/squid.conf":

```
http_port 3128 require-proxy-header
proxy_protocol_access allow localnet

# following one is needed when testing on the same machine
acl localnet src 127.0.0.1
```

Verify MTLS setup with curl

```
curl --cacert ca.crt --proxy-cert client.crt --proxy-key client.key -x https://www.yourdomain.com:8443 --haproxy-protocol https://myip.ipip.net
```

# Cert and Key

Here's the step-by-step process for creating a set of certificates to be used for MTLS, including the CA certificate and key, server certificate and key, and client certificate and key. Please replace "Your CA Name", "yourdomain.com", and "Your Server Name" with your actual CA, domain, and server names.

## Step 1: Generate the CA's Private Key

```
openssl genrsa -out ca.key 4096
```

This creates a new 4096-bit RSA private key for your CA. The key will be stored in ca.key.

## Step 2: Generate the Self-Signed Root CA Certificate

```
openssl req -new -x509 -days 3650 -key ca.key -out ca.crt -subj "/CN=Your CA Name"
```

This command generates a new self-signed CA certificate based on the private key (ca.key) you created. The -days 3650 makes the certificate valid for 10 years. The certificate will be stored in ca.crt.

## Step 3: Generate the Server's Private Key

```
openssl genrsa -out server.key 2048
```

Generate a new 2048-bit RSA private key for your server. The key will be stored in server.key.

## Step 4: Create a Certificate Signing Request (CSR) for the Server

```
openssl req -new -key server.key -out server.csr -subj "/CN=www.yourdomain.com"
```

This creates a new CSR (server.csr) for your server certificate using the server's private key (server.key).

## Step 5: Generate the Server Certificate Signed by Your CA

```
openssl x509 -req -days 365 -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt
```

This signs the server CSR with your CA key, resulting in a signed server certificate (server.crt) valid for 1 year.

## Step 6: Generate the Client's Private Key

```
openssl genrsa -out client.key 2048
```

Generate a new 2048-bit RSA private key for the client. This key will be stored in client.key.

## Step 7: Create a CSR for the Client

```
openssl req -new -key client.key -out client.csr -subj "/CN=Your Client Name"
```

Create a CSR (client.csr) for the client using the newly created client key (client.key).

## Step 8: Generate the Client Certificate Signed by Your CA

```
openssl x509 -req -days 365 -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt
```

This signs the client CSR with your CA key, creating a signed client certificate (client.crt) valid for 1 year. This certificate is to be used for mutual TLS authentication by the client.

## Step 9 (optional): Verify the Certificates

To verify the server and client certificates were signed by your CA:

```
openssl verify -CAfile ca.crt server.crt
openssl verify -CAfile ca.crt client.crt
```

Both commands should return OK if everything went well.

Now you have:
- CA certificate (ca.crt) installed and trusted on the server and clients.
- Server certificate (server.crt) and private key (server.key) are installed on the server.
- Client certificate (client.crt) and private key (client.key) are installed on the client.

Make sure you protect your private keys (ca.key, server.key, client.key) by setting appropriate file permissions. Using these instructions, your server and client can mutually authenticate themselves over a TLS connection, with your own CA acting as the trust anchor.
This content is only supported in a Feishu Docs

Reference
https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt
https://seriousben.com/posts/2020-02-exploring-the-proxy-protocol/