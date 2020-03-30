---
title: Az Azure IoT Hub TLS támogatása
description: Gyakorlati tanácsok a biztonságos TLS-kapcsolatok használatával kapcsolatban az IoT Hubbal kommunikáló eszközök höz és szolgáltatásokhoz
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409496"
---
# <a name="tls-support-in-iot-hub"></a>TLS-támogatás az IoT Hubban

Az IoT Hub a Transport Layer Security (TLS) használatával biztosítja az IoT-eszközök és -szolgáltatások kapcsolatait. A TLS protokoll három verziója jelenleg támogatott, nevezetesen az 1.0, 1.1 és 1.2 verziók.

A TLS 1.0 és 1.1 örököltnek minősül, és evehetetlenségre van tervezve. További információ: [A TLS 1.0 és 1.1 elavultak az IoT Hub hoz.](iot-hub-tls-deprecating-1-0-and-1-1.md) Erősen ajánlott a TLS 1.2 használata az Előnyben részesített TLS-verzióként az IoT Hubhoz való csatlakozáskor.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Kapcsolatok korlátozása a TLS 1.2-re az IoT Hub-erőforrásban

A nagyobb biztonság érdekében azt javasoljuk, hogy az IoT Hubs konfigurálása *csak* a TLS 1.2-es verzióját használó ügyfélkapcsolatok engedélyezéséhez és az [ajánlott titkosítások](#recommended-ciphers)használatának érvényesítéséhez.

Ebből a célból hozzon létre egy új IoT `minTlsVersion` Hub `1.2` bármely [támogatott régióban,](#supported-regions) és állítsa be a tulajdonságot az Azure Resource Manager-sablon IoT hub erőforrás-specifikációjában:

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

A létrehozott IoT Hub-erőforrás ezzel a konfigurációval megfogja tagadni az 1.0-s és 1.1-es verziókkal csatlakozni próbáló eszköz- és szolgáltatásügyfeleket. Hasonlóképpen, a TLS kézfogás lesz elutasítva, ha az ügyfél HELLO üzenet nem sorolja fel az [ajánlott rejtjelek](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` tulajdonság írásvédett, és nem módosítható az IoT Hub-erőforrás létrehozása után. Ezért elengedhetetlen, hogy megfelelően tesztelje és ellenőrizze, hogy *az összes* IoT-eszköz és szolgáltatás kompatibilis-e a TLS 1.2-vel és az [ajánlott titkosításokkal.](#recommended-ciphers)

### <a name="supported-regions"></a>Támogatott régiók

A TLS 1.2 használatát igénylő IoT-központok a következő régiókban hozhatók létre:

* USA keleti régiója
* USA déli középső régiója
* USA nyugati régiója, 2.
* USA-beli államigazgatás – Arizona
* USA-beli államigazgatás – Virginia

> [!NOTE]
> Feladatátvételesetén az `minTlsVersion` IoT Hub tulajdonsága továbbra is hatékony marad a geo-párosított régióban a feladatátvétel után.

### <a name="recommended-ciphers"></a>Ajánlott rejtjelek

A csak a TLS 1.2-es érték fogadására konfigurált IoT-központok a következő ajánlott titkosítások használatát is kikényszerítik:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>A TLS 1.2 használata az IoT Hub SDK-kban

Az alábbi hivatkozások segítségével konfigurálhatja a TLS 1.2-es és engedélyezett titkosításokat az IoT Hub ügyfél SDK-kban.

| Nyelv | TLS 1.2-t támogató verziók | Dokumentáció |
|----------|------------------------------------|---------------|
| C#        | Címke 2019-12-11 vagy újabb            | [Hivatkozás](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 2.0.0-s vagy újabb verzió             | [Hivatkozás](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 1.21.4-es vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 1.19.0-s vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 1.12.2-es vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>A TLS 1.2 használata az IoT Edge beállításában

Az IoT Edge-eszközök konfigurálhatók a TLS 1.2 használatára az IoT Hubbal való kommunikáció során. Ehhez használja az [IoT Edge dokumentációs lapját.](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)