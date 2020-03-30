---
title: A TLS 1.0 és 1.1 elavulttá importálása az IoT Hub és az eszközkiépítési szolgáltatásban (DPS) | Microsoft dokumentumok
description: A TLS 1.0 és 1.1, valamint az IoT Hub és a DPS által támogatott titkosítások e-deprecációjára vonatkozó irányelvek.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402790"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>A TLS 1.0 és 1.1 evesztője az IoT Hub és az Eszközkiépítési szolgáltatásban

A kategóriájában legjobb titkosítás biztosítása érdekében az IoT Hub és az Eszközlétesítési szolgáltatás (DPS) a Transport Layer Security (TLS) 1.2-re kerül, mint az IoT-eszközök és -szolgáltatások titkosítási mechanizmusa. 

## <a name="supported-ciphers"></a>Támogatott rejtjelek

A TLS kézfogásban használt különböző rejtjelek elérhetőségének idővonala a következő:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (jelenleg támogatott)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (2020 második felében támogatott)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (2020 második felében támogatott)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (2020 második felében támogatott)


## <a name="customer-feedback"></a>Vásárlói visszajelzések

Bár a TLS 1.2 kényszerítése az egész iparágra kiterjedő legjobb titkosítási választás, és a tervek nek megfelelően lesz engedélyezve, továbbra is szeretnénk hallani az ügyfelektől a konkrét telepítésekről és a TLS 1.2 elfogadásával kapcsolatos nehézségekről. Ebből a célból elküldheti [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)észrevételeit a alkalmazásba.
