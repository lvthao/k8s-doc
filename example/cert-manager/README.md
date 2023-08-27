# Cert-manager

Document: <https://cert-manager.io/docs/release-notes/release-notes-1.10/>

## Install

Use this version for k8s 1.23

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.10.1/cert-manager.yaml
```

## Usage

### selfsigned certificate config

Some note below:

- commonName: name of website we need to generate certifciate. EX:example.com
- secretName: name of secret tls

#### Apply config

```
kubectl apply -f selfsigned.yaml
```

#### Get certificate

```
kubectl get secret -n cert-demo root-secret -o json | jq -r '.data."tls.crt"' | base64 -d
```

#### Verify certificate

```
kubectl get secret -n cert-demo root-secret -o json | jq -r '.data."tls.crt"' | base64 -d | openssl x509 -noout -text 
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            ac:09:ee:8f:5d:3b:db:a0:bf:74:40:6e:b1:a8:71:2c
        Signature Algorithm: ecdsa-with-SHA256
        Issuer: CN = example.com
        Validity
            Not Before: Dec  3 08:42:23 2022 GMT
            Not After : Mar  3 08:42:23 2023 GMT
        Subject: CN = example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:70:2e:1a:ff:2a:9b:e8:fa:0c:09:24:95:80:72:
                    4f:5b:03:16:1f:c0:4b:e0:0e:a2:f1:51:ff:f0:77:
                    82:68:3d:54:de:f6:17:76:30:b2:7a:ce:8b:ca:98:
                    90:ca:8a:ce:30:ae:4e:63:d0:14:6e:ba:2a:91:10:
                    e9:bf:62:6f:66
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Certificate Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier: 
                FF:AB:56:8A:49:F6:B5:0E:A9:82:86:46:CA:94:76:5E:E4:72:87:0E
    Signature Algorithm: ecdsa-with-SHA256
         30:45:02:20:55:e1:1c:97:3b:47:32:2c:4b:2b:8b:95:d8:02:
         00:fd:1d:6f:53:ef:f0:83:6f:7f:cb:ce:5f:85:b1:ce:9f:a8:
         02:21:00:ba:6c:8a:50:28:32:7b:8e:44:f2:3c:65:2d:cc:11:
         ee:1b:21:d7:ad:e7:88:0f:d0:97:0f:cb:ef:9c:b7:53:0e
```

### ACME certificate config
