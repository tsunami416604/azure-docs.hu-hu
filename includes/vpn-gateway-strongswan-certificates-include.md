---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c6f9065786879749eee6187e93283f4c026b7fff
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568804"
---
A következő számítógép konfigurációja használták az alábbi lépéseket:

  | | |
  |---|---|
  |Computer| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |Függőségek| strongSwan |

#### <a name="1-install-strongswan"></a>1. StrongSwan telepítése

A következő parancsok használatával telepítse a szükséges strongSwan konfigurációt:

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```

#### <a name="2-generate-keys-and-certificate"></a>2. Kulcsok és tanúsítványok

A CA-tanúsítvány előállításához.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Nyomtassa ki a Hitelesítésszolgáltatói tanúsítvány base64 formátumban. Ez az Azure által támogatott formátumban. Akkor lesz később fel kell töltenie azt az Azure a P2S konfiguráció részeként.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

A felhasználói tanúsítvány előállításához.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Hozzon létre egy felhasználói tanúsítványt tartalmazó p12-csomagot. A csomag a következő lépésben lesz, az ügyfél-konfigurációs fájlok használata során.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```