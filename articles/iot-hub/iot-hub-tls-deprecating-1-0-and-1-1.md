---
title: A TLS 1,0 és a 1,1 elavult a IoT Hub és a Device kiépítési szolgáltatásban (DPS) | Microsoft Docs
description: Irányelvek a TLS 1,0 és 1,1, valamint a IoT Hub és DPS által támogatott titkosítási algoritmusok elavulása tekintetében.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 95733f579740f2928cda917eec0023bf00d53076
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722784"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>A TLS 1,0 és a 1,1 elavult a IoT Hub és az eszközök kiépítési szolgáltatásában

A legjobb titkosítás biztosítása érdekében a IoT Hub és az eszközök kiépítési szolgáltatása (DPS) a Transport Layer Security (TLS) 1,2-as verzióra kerül, mint a IoT eszközök és szolgáltatások számára választott titkosítási mechanizmus. Így a TLS 1,0 és a TLS 1,1 korábbi támogatása, valamint a nem ajánlott örökölt titkosítási rendszerek a **2020. július 1-jén**elavulttá válik.


## <a name="impact"></a>Hatás
Az ügyfelek konkrét körülményei és konfigurációi alapján a TLS 1,0 és 1,1, valamint a nem ajánlott örökölt titkosítási rendszerek elavult változást okozhatnak a IoT-eszközök és-szolgáltatások IoT Hub vagy DPS-vel való kommunikációjában. Bizonyos esetekben az ezekkel a módosításokkal nem kompatibilis eszközök és szolgáltatások nem tudnak majd csatlakozni a IoT Hubhoz vagy a DPS-hez a fent említett lejárati dátum után.


## <a name="supported-ciphers"></a>Támogatott titkosítási algoritmusok

A TLS-kézfogás során csak a következő titkosítások engedélyezettek:

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>Felhasználói visszajelzés

Noha a TLS 1.2 kötelezővé tétele a teljes iparágra jellemző, és célja a legmagasabb szintű titkosítási megoldás használata, és ezért azt a tervezett módon megvalósítjuk, szeretnénk megtudni, hogy ügyfeleink milyen üzemelő példányokkal milyen nehézségekkel találkoznak a TLS 1.2 bevezetése során. Erre a célra a megjegyzéseit [iot_tls1_deprecation@microsoft.comba ](mailto:iot_tls1_deprecation@microsoft.com)küldheti.