---
title: Azure IoT Hub TLS-támogatás
description: Ajánlott eljárások a biztonságos TLS-kapcsolatok használatához a IoT Hub kommunikáló eszközökhöz és szolgáltatásokhoz
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722888"
---
# <a name="tls-support-in-iot-hub"></a>TLS-támogatás a IoT Hub

A IoT Hub Transport Layer Security (TLS) protokollt használ a IoT-eszközök és-szolgáltatások közötti kapcsolatok biztonságossá tételéhez. A TLS protokoll három verziója jelenleg támogatott, azaz a 1,0, 1,1 és 1,2 verziókat.

A TLS 1,0 és a 1,1 örökölt, és elavultnak számít. További információ: [a TLS 1,0 és a 1,1 elavult a IoT hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Javasoljuk, hogy a TLS 1,2-et használja elsődleges TLS-verzióként IoT Hubhoz való csatlakozáskor.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>A TLS 1,2 kapcsolatának korlátozása a IoT Hub erőforrásban

A további biztonság érdekében javasoljuk, hogy a IoT-hubokat úgy konfigurálja, hogy *csak* a 1,2-es TLS-verziót használó ügyfélkapcsolatokat engedélyezze, és kényszerítse ki az [ajánlott titkosítási algoritmusok](#recommended-ciphers)használatát.

Erre a célra hozzon létre egy új IoT Hub a bármelyik [támogatott régióban](#supported-regions) , és állítsa a `minTlsVersion` tulajdonságot az Azure Resource Manager-sablon IoT hub erőforrás-specifikációjának `1.2`ára:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Az ezzel a konfigurációval létrehozott IoT Hub erőforrás elutasítja az eszköz-és szolgáltatás-ügyfeleket, amelyek a 1,0-es és a 1,1-es TLS-verzióval próbálnak csatlakozni. Hasonlóképpen, a TLS-kézfogás visszautasítva lesz, ha az ügyfél HELLO üzenete nem sorolja fel a [javasolt titkosítási algoritmusokat](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` tulajdonság csak olvasható, és a IoT Hub erőforrás létrehozása után nem módosítható. Ezért fontos, hogy megfelelően tesztelje és ellenőrizze *, hogy a* IoT-eszközök és-szolgáltatások kompatibilisek-e a TLS 1,2-mel és az [ajánlott titkosítási](#recommended-ciphers) megoldásokkal.

### <a name="supported-regions"></a>Támogatott régiók

A TLS 1,2 használatát igénylő IoT-hubok a következő régiókban hozhatók létre:

* USA keleti régiója
* USA déli középső régiója
* USA nyugati régiója, 2.

> [!NOTE]
> Feladatátvétel esetén a IoT Hub `minTlsVersion` tulajdonsága továbbra is érvényben marad a feladatátvételt követően a Geo-párosítási régióban.

### <a name="recommended-ciphers"></a>Ajánlott titkosítási algoritmusok

A csak a TLS 1,2 használatára konfigurált IoT hubok a következő ajánlott titkosítási algoritmusok használatát is kikényszerítik:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>A TLS 1,2 használata a IoT Hub SDK-ban

Az alábbi hivatkozásokkal konfigurálhatja a TLS 1,2 és az engedélyezett titkosítási algoritmusokat IoT Hub ügyféloldali SDK-k használatával.

| Nyelv | TLS 1,2 támogatott | Dokumentáció |
|----------|-------------------|---------------|
| Ü        | Igen               | [Hivatkozás](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Igen               | [Hivatkozás](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Igen               | [Hivatkozás](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Igen               | [Hivatkozás](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Igen               | [Hivatkozás](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>A TLS 1,2 használata a IoT Edge-telepítőben

A IoT Edge-eszközök úgy konfigurálhatók, hogy a TLS 1,2-et használják a IoT Hubval való kommunikáció során. Erre a célra használja a [IoT Edge dokumentációs oldalát](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).