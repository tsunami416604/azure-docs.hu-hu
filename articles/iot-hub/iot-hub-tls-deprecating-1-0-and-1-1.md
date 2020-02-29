---
title: A TLS 1,0 és a 1,1 elavult a IoT Hub és a Device kiépítési szolgáltatásban (DPS) | Microsoft Docs
description: Irányelvek a TLS 1,0 és 1,1, valamint a IoT Hub és DPS által támogatott titkosítási algoritmusok elavulása tekintetében.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912154"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>A TLS 1,0 és a 1,1 elavult a IoT Hub és az eszközök kiépítési szolgáltatásában

A legjobb titkosítás biztosítása érdekében a IoT Hub és az eszközök kiépítési szolgáltatása (DPS) a Transport Layer Security (TLS) 1,2-as verzióra kerül, mint a IoT eszközök és szolgáltatások számára választott titkosítási mechanizmus. Így a TLS 1,0 és a TLS 1,1 korábbi támogatása, valamint a nem ajánlott örökölt titkosítási rendszerek a **2020. július 1-jén**elavulttá válik.


## <a name="impact"></a>Hatás
Az ügyfelek konkrét körülményei és konfigurációi alapján a TLS 1,0 és 1,1, valamint a nem ajánlott örökölt titkosítási rendszerek elavult változást okozhatnak a IoT-eszközök és-szolgáltatások IoT Hub vagy DPS-vel való kommunikációjában. Bizonyos esetekben az ezekkel a módosításokkal nem kompatibilis eszközök és szolgáltatások nem tudnak majd csatlakozni a IoT Hubhoz vagy a DPS-hez a fent említett lejárati dátum után.


## <a name="supported-ciphers"></a>Támogatott titkosítási algoritmusok

A TLS-kézfogásban használt különböző titkosítási módok elérhetőségének ütemterve a következő:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (jelenleg támogatott)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (a 2020-as második félévben lesz támogatva)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (a 2020-as második félévben lesz támogatva)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (a 2020-as második félévben lesz támogatva)


## <a name="customer-feedback"></a>Felhasználói visszajelzés

Míg a TLS 1,2-kényszerítés az iparági szintű legjobb titkosítási megoldás, és a tervezett módon lesz engedélyezve, továbbra is hallani kell az ügyfelektől az adott üzembe helyezésekkel és a TLS 1,2 bevezetésével kapcsolatos nehézségekkel. Erre a célra a megjegyzéseit [iot_tls1_deprecation@microsoft.comba ](mailto:iot_tls1_deprecation@microsoft.com)küldheti.
