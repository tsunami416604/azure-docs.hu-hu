---
title: Elavult TLS 1,0 és 1,1 a IoT Hub és a Device kiépítési szolgáltatásban (DPS) | Microsoft Docs
description: Irányelvek a TLS 1,0 és 1,1, valamint a IoT Hub és DPS által támogatott titkosítási algoritmusok elavulása tekintetében.
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f1ee3156e5639ae47d5bb323cf992586580668f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485909"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>A TLS 1,0 és a 1,1 elavult a IoT Hub és az eszközök kiépítési szolgáltatásában

A legjobb titkosítás biztosítása érdekében a IoT Hub és az eszközök kiépítési szolgáltatása (DPS) a Transport Layer Security (TLS) 1,2-as verzióra kerül, mint a IoT eszközök és szolgáltatások számára választott titkosítási mechanizmus. Így a TLS 1,0 és a TLS 1,1 korábbi támogatása, valamint a nem ajánlott örökölt titkosítási rendszerek a **2020. július 1-jén**elavulttá válik.


## <a name="impact"></a>Hatás
Az ügyfelek konkrét körülményei és konfigurációi alapján a TLS 1,0 és 1,1, valamint a nem ajánlott örökölt titkosítási rendszerek elavult változást okozhatnak a IoT-eszközök és-szolgáltatások IoT Hub vagy DPS-vel való kommunikációjában. Bizonyos esetekben az ezekkel a módosításokkal nem kompatibilis eszközök és szolgáltatások nem tudnak majd csatlakozni a IoT Hubhoz vagy a DPS-hez a fent említett lejárati dátum után.


## <a name="supported-ciphers"></a>Támogatott titkosítási algoritmusok

A TLS-kézfogás során csak a következő titkosítási műveleteket engedélyezi a rendszer:

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Felhasználói visszajelzés

Noha a TLS 1.2 kötelezővé tétele a teljes iparágra jellemző, és célja a legmagasabb szintű titkosítási megoldás használata, és ezért azt a tervezett módon megvalósítjuk, szeretnénk megtudni, hogy ügyfeleink milyen üzemelő példányokkal milyen nehézségekkel találkoznak a TLS 1.2 bevezetése során. Erre a célra a megjegyzéseit [iot_tls1_deprecation@microsoft.comba ](mailto:iot_tls1_deprecation@microsoft.com)küldheti.