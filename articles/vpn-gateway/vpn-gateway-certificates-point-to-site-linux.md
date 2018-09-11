---
title: 'Tanúsítványok létrehozása és exportálása pont – hely számára: Linux: parancssori felület: Azure |} A Microsoft Docs'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot és a Linux (strongSwan) parancssori felület használatával ügyféltanúsítványokat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305741"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Tanúsítványok létrehozása és exportálása a pont – hely Linux strongSwan parancssori felület használatával

Pont – hely kapcsolatok tanúsítványok segítségével hitelesíti. Ez a cikk bemutatja, hogyan hozhat létre egy önaláírt főtanúsítványt, és ügyféltanúsítványokat strongSwan és Linux parancssori felület használatával. Ha ugyanazt a tanúsítványt utasításokat keres, tekintse meg a [Powershell](vpn-gateway-certificates-point-to-site.md) vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) cikkeket.

> [!NOTE]
> A jelen cikkben ismertetett lépések strongSwan szükséges.
>

Ez a cikk lépései használt az érintett számítógép konfigurációja a következő volt:

| | |
|---|---|
|**Számítógép**| Ubuntu Server 16.04<br>ID_LIKE = debian<br>PRETTY_NAME = "Ubuntu 16.04.4 LTS"<br>VERSION_ID = "16.04" |
|**Függőségek**| Apt-get install strongswan-ikev2 strongswan-beépülő modul – eap-tls<br>Apt-get libstrongswan – standard – beépülő modulok telepítése |

## <a name="install-strongswan"></a>StrongSwan telepítése

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

A grafikus felhasználói felülettel strongSwan telepítésével kapcsolatos információkért lásd: a lépések a [ügyfél-konfiguráció](point-to-site-vpn-client-configuration-azure-cert.md#install) cikk.

## <a name="generate-keys-and-certificate"></a>Kulcsok és tanúsítványok

1. A CA-tanúsítvány előállításához.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. Nyomtassa ki a Hitelesítésszolgáltatói tanúsítvány base64 formátumban. Ez az Azure által támogatott formátumban. Akkor lesz később fel kell töltenie azt az Azure a P2S konfiguráció részeként.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. A felhasználói tanúsítvány előállításához.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. Hozzon létre egy felhasználói tanúsítványt tartalmazó p12-csomagot. A csomag a következő lépésben használandó, az használatakor a [ügyfél konfigurációs fájljainak](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>További lépések

A pont – hely konfigurációt, folytassa [létrehozása és telepítése VPN-ügyfél konfigurációs fájljainak](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).