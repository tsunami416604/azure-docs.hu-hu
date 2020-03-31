---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c2525b352c25f470814ce909a8d10ff821d9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70961599"
---
A hitelesítésszolgáltatói tanúsítvány létrehozása.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

A hitelesítésszolgáltatói tanúsítvány nyomtatása base64 formátumban. Ezt a formátumot támogatja az Azure. Ezt a tanúsítványt a [P2S-konfigurációs lépések](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)részeként tölti fel az Azure-ba.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Hozza létre a felhasználói tanúsítványt.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Hozzon létre egy p12-csomagot, amely tartalmazza a felhasználói tanúsítványt. Ez a csomag lesz használva a következő lépésekben, amikor az ügyfél konfigurációs fájlokkal dolgozik.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```