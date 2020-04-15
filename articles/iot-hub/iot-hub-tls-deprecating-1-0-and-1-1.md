---
title: A TLS 1.0 és 1.1 elavulttá importálása az IoT Hubban | Microsoft dokumentumok
description: A TLS 1.0 és 1.1 és a támogatott titkosítások idomai az IoT Hubban történő e-deprecációjára vonatkozó irányelvek.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381298"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>A TLS 1.0 és 1.1 evesztője az IoT Hubban

A kategóriájában legjobb titkosítás biztosítása érdekében az IoT Hub áthelyezi a Transport Layer Security (TLS) 1.2-es átviteli rétegre (TLS) az IoT-eszközök és -szolgáltatások által választott titkosítási mechanizmust. 

## <a name="timeline"></a>Idővonal

Az IoT Hub további értesítésig továbbra is támogatja a TLS 1.0/1.1-et. Javasoljuk azonban, hogy minden ügyfél a lehető leghamarabb térjen át a TLS 1.2-re.

## <a name="supported-ciphers"></a>Támogatott rejtjelek

A TLS kézfogásban használt különböző rejtjelek elérhetőségének idővonala a következő:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (jelenleg támogatott)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020 második felében támogatott)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020 második felében támogatott)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020 második felében támogatott)

## <a name="customer-feedback"></a>Vásárlói visszajelzések

Bár a TLS 1.2 kényszerítése az egész iparágra kiterjedő legjobb titkosítási választás, és a tervek nek megfelelően lesz engedélyezve, továbbra is szeretnénk hallani az ügyfelektől a konkrét telepítésekről és a TLS 1.2 elfogadásával kapcsolatos nehézségekről. Ebből a célból elküldheti [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)észrevételeit a alkalmazásba.
