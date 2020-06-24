---
title: Azure IoT Hub TLS-támogatás
description: Ajánlott eljárások a biztonságos TLS-kapcsolatok használatához a IoT Hub kommunikáló eszközökhöz és szolgáltatásokhoz
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: jlian
ms.openlocfilehash: 8c52037684215d1672ed813389d0bbace9a03e42
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080618"
---
# <a name="tls-support-in-iot-hub"></a>TLS-támogatás a IoT Hub

A IoT Hub Transport Layer Security (TLS) protokollt használ a IoT-eszközök és-szolgáltatások közötti kapcsolatok biztonságossá tételéhez. A TLS protokoll három verziója jelenleg támogatott, azaz a 1,0, 1,1 és 1,2 verziókat.

A TLS 1,0 és a 1,1 örökölt, és elavultnak számít. További információ: [a TLS 1,0 és a 1,1 elavult a IoT hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Javasoljuk, hogy a TLS 1,2-et használja elsődleges TLS-verzióként IoT Hubhoz való csatlakozáskor.

## <a name="tls-12-enforcement-available-in-select-regions"></a>A TLS 1,2 kényszerítés a kiválasztott régiókban érhető el

A további biztonság érdekében konfigurálja a IoT hubokat úgy, hogy *csak* a 1,2-es TLS-verziót használó ügyfélkapcsolatokat engedélyezze, és kényszerítse az [ajánlott titkosítási algoritmusok](#recommended-ciphers)használatát. Ez a funkció csak a következő régiókban támogatott:

* USA keleti régiója
* USA déli középső régiója
* USA nyugati régiója, 2.
* USA-beli államigazgatás – Arizona
* USA-beli államigazgatás – Virginia

Erre a célra hozzon létre egy új IoT Hub a bármelyik támogatott régióban, és állítsa be a `minTlsVersion` tulajdonságot a `1.2` Azure Resource Manager sablon IoT hub erőforrás-specifikációjában:

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
> A `minTlsVersion` tulajdonság csak olvasható, és a IoT hub-erőforrás létrehozása után nem módosítható. Ezért fontos, hogy megfelelően tesztelje és ellenőrizze *, hogy a* IoT-eszközök és-szolgáltatások kompatibilisek-e a TLS 1,2-mel és az [ajánlott titkosítási](#recommended-ciphers) megoldásokkal.
> 
> Feladatátvétel esetén a `minTlsVersion` IoT hub tulajdonsága továbbra is érvényben marad a helyszíni, a feladatátvételt követő régióban.

## <a name="recommended-ciphers"></a>Ajánlott titkosítási algoritmusok

A csak a TLS 1,2 használatára konfigurált IoT hubok a következő ajánlott titkosítási algoritmusok használatát is kikényszerítik:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

A TLS 1,2-kényszerítéshez nem konfigurált IoT-hubok esetében a TLS 1,2 továbbra is a következő titkosítási megoldásokkal működik:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>A TLS 1,2 használata a IoT Hub SDK-ban

Az alábbi hivatkozásokkal konfigurálhatja a TLS 1,2 és az engedélyezett titkosítási algoritmusokat IoT Hub ügyféloldali SDK-k használatával.

| Nyelv | A TLS 1,2-et támogató verziók | Dokumentáció |
|----------|------------------------------------|---------------|
| C        | 2019-12-11 vagy újabb címke            | [Hivatkozás](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 2.0.0 vagy újabb verzió             | [Hivatkozás](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 1.21.4 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 1.19.0 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 1.12.2 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>A TLS 1,2 használata a IoT Edge-telepítőben

A IoT Edge-eszközök úgy konfigurálhatók, hogy a TLS 1,2-et használják a IoT Hubval való kommunikáció során. Erre a célra használja a [IoT Edge dokumentációs oldalát](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).