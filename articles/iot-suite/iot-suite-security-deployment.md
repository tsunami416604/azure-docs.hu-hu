---
title: Az eszközök internetes hálózatát üzembe |} Microsoft Docs
description: Ez a cikk részletesen biztonságossá tétele az IoT-telepítés
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 95c23341-16b0-4954-b3f2-d2e82ab7b367
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 995759cf4831deedc642c0850603947b22ee1222
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Az IoT-megoldás gyorsító titkosítócsomagok

Az IoT-megoldás gyorsítók támogatja a következő titkosító csomag használatát, az itt megadott sorrendben.

| Titkosítási csomagok | Hossz |
| --- | --- |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA-256 (0xc027) ECDH secp256r1 (eq. FS 3072 bits RSA) |128 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (eq. FS 3072 bits RSA) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA-256 (0x9c) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA-256 (0x3d) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA-256 (0x3c) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35 hiba) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| A TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Lásd még
Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A prediktív karbantartási megoldás gyorsító – áttekintés][lnk-predictive-overview]
* [Gyakran ismételt kérdések az IoT-megoldásgyorsítókról][lnk-faq]

Az IoT-központ biztonsági olvashat [IoT-központ való hozzáférés szabályozása] [ lnk-devguide-security] az IoT Hub fejlesztői útmutató.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]:../iot-accelerators/iot-accelerators-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
