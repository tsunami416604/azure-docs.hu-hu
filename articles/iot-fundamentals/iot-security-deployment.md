---
title: Az Azure Internet of Things (IoT) üzemelő példány biztonságos |} A Microsoft Docs
description: Ez a cikk részletesen bemutatja az Azure IoT-környezet biztonságossá tétele
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: b2d69f6ee24ced392da09585c9e934f647efbf14
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286877"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>IoT-megoldás gyorsító titkosítócsomagok

Az IoT-megoldásgyorsítók ebben a sorrendben az alábbi titkosító csomagok támogatja.

| A titkosítási csomagok | Hossz |
| --- | --- |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (eq. 3072-bites RSA) FS |128 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (eq. 3072-bites RSA) FS |128 |
| A TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35 hiba) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| A TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Lásd még

További információ az IoT Hub biztonsági [férhet hozzá az IoT Hub](../iot-hub/iot-hub-devguide-security.md) az IoT Hub fejlesztői útmutatójában található. 
