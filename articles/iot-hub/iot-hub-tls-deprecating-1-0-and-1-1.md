---
title: A TLS 1,0 és a 1,1 elavult a IoT Hubban | Microsoft Docs
description: Irányelvek a TLS 1,0 és 1,1, valamint a IoT Hub támogatott titkosítási algoritmusok elavulása tekintetében.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83849518"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>A TLS 1,0-es és 1,1-es verziójának elavult változata IoT Hub

A legjobb titkosítás biztosításához IoT Hub a Transport Layer Security (TLS) 1,2-as verzióra kerül, mint a IoT-eszközök és-szolgáltatások titkosítási mechanizmusa. 

## <a name="timeline"></a>Idővonal

A IoT Hub továbbra is támogatja a TLS 1.0/1.1-et további értesítésig. Azt javasoljuk azonban, hogy az összes ügyfél a lehető leghamarabb telepítse át a TLS 1,2-re.

## <a name="deprecating-tls-11-ciphers"></a>TLS 1,1-Rejtjelek elavultak

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>TLS 1,0-Rejtjelek elavultak

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>TLS 1,2-Rejtjelek

Lásd: [IOT hub TLS 1,2 ajánlott titkosítási algoritmus](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Felhasználói visszajelzés

Míg a TLS 1,2-kényszerítés az iparági szintű legjobb titkosítási megoldás, és a tervezett módon lesz engedélyezve, továbbra is hallani kell az ügyfelektől az adott üzembe helyezésekkel és a TLS 1,2 bevezetésével kapcsolatos nehézségekkel. Erre a célra a megjegyzéseit elküldheti a következőnek: [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) .
