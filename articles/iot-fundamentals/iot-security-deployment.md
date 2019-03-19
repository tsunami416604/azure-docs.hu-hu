---
title: Az Azure Internet of Things (IoT) üzemelő példány biztonságos |} A Microsoft Docs
description: Ez a cikk részletesen bemutatja az Azure IoT-környezet biztonságossá tétele
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: b6a444e64e58611e36208cfa615b4a148de3596c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182676"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>IoT-megoldás gyorsító titkosítócsomagok

Az IoT-megoldásgyorsítók ebben a sorrendben az alábbi titkosító csomagok támogatja.

| Cipher Suite | Hossz |
| --- | --- |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (eq. 3072-bites RSA) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (eq. 3072-bites RSA) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Lásd még

További információ az IoT Hub biztonsági [férhet hozzá az IoT Hub](../iot-hub/iot-hub-devguide-security.md) az IoT Hub fejlesztői útmutatójában található. 
