---
title: A TLS 1,0 és a 1,1 elavult a IoT Hubban | Microsoft Docs
description: Irányelvek a TLS 1,0 és 1,1, valamint a IoT Hub támogatott titkosítási algoritmusok elavulása tekintetében.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381298"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>A TLS 1,0-es és 1,1-es verziójának elavult változata IoT Hub

A legjobb titkosítás biztosításához IoT Hub a Transport Layer Security (TLS) 1,2-as verzióra kerül, mint a IoT-eszközök és-szolgáltatások titkosítási mechanizmusa. 

## <a name="timeline"></a>Idővonal

A IoT Hub továbbra is támogatja a TLS 1.0/1.1-et további értesítésig. Azt javasoljuk azonban, hogy az összes ügyfél a lehető leghamarabb telepítse át a TLS 1,2-re.

## <a name="supported-ciphers"></a>Támogatott titkosítási algoritmusok

A TLS-kézfogásban használt különböző titkosítási módok elérhetőségének ütemterve a következő:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (jelenleg támogatott)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (a 2020-as második félévben lesz támogatva)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (a 2020-as második félévben lesz támogatva)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (a 2020-as második félévben lesz támogatva)

## <a name="customer-feedback"></a>Felhasználói visszajelzés

Míg a TLS 1,2-kényszerítés az iparági szintű legjobb titkosítási megoldás, és a tervezett módon lesz engedélyezve, továbbra is hallani kell az ügyfelektől az adott üzembe helyezésekkel és a TLS 1,2 bevezetésével kapcsolatos nehézségekkel. Erre a célra a megjegyzéseit elküldheti a [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)következőnek:.
