---
title: Korlátozások és kvóták IoT Plug and Play Preview | Microsoft dokumentumok
description: Ismerje meg az IoT Plug and Play előzetes verzió használatakor érvényes korlátokat, kvótákat és szabályozást.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531377"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT Plug and Play előzetes korlátozások, kvóták és szabályozások

Ez a cikk ismerteti az IoT Plug-és Play-specifikus korlátok, kvóták és a szabályozás, amelyek a nyilvános előzetes verzióban alkalmazandó. Vannak meglévő [IoT Hub-kvóták és szabályozás,](../iot-hub/iot-hub-devguide-quotas-throttling.md) amelyek szintén érvényesek.

## <a name="iot-hub"></a>IoT Hub

A nyilvános előzetes verzióhoz a következő korlátok és kvóták vonatkoznak egy IoT-központra:

| Korlátok, korlátozások és fojtószelepek | Érték | Megjegyzések |
|-----|-----|-----|
| A hubonként regisztrálható eszközképességi modellek (DCM-ek) vagy interfészek száma | 1500 ||
| Eszközönként regisztrálható összeköttetések maximális száma | 40 ||
| Eszközönként regisztrálható tartományvezérlők maximális száma | 1 ||
| Az összeköttetés/DCM fájl maximális mérete | 512 karakter ||
| Az összeköttetés nevének maximális mérete | 256 karakter ||
| Tulajdonságnév maximális mérete  | 64 bájt, 7 mélységi szint (és az `$iotin`első szint fenntartva) | Megengedett karakterek: a-z, A-Z, 0-9 (nem az első karakter), és aláhúzás. |
| Egy tulajdonságérték maximális mérete | 512 bájt ||
| A parancsnév maximális mérete | 100 bájt ||
| Az ikereszköz mérete | Ugyanaz, mint az [IoT Hub-korlátok](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Feloldási API-hívások a termékváltozaton keresztül (az egységektől függetlenül) | 100 kérelem/másodperc ||

## <a name="model-repository"></a>Modelltárház

A nyilvános előzetes verzióban a következő korlátok és kvóták vonatkoznak a modelltárházra:

| Korlátok, korlátozások és fojtószelepek| Érték |
|-----|-----|
| Vállalati modelltárolók száma Az Azure Active Directory-bérlőnként | 1 |
| Engedélyezési kulcsok száma modelltárházonként | 10  |
| Modellek (DCM-ek vagy interfészek) száma vállalati modelltárházonként| 1500  |
| Modellek (DcM-ek vagy összeköttetések) száma a nyilvános modelltárházban Azure Active Directory-bérlőnként| 1500  |
| A vállalati modelltárházban törölt tartományvezérlők vagy összeköttetések száma | 10 lekérdezés másodpercenként (QPS)|
| A bérlő által létrehozott/frissített modelladattárak száma| 1 QPS |
| A modelltárházban létrehozott/frissített/törölt engedélyezési kulcsok száma | 1 QPS|
| A vállalati modelltárházban létrehozott dcm-ek száma | 10 QPS |
| A vállalati modelltárházban létrehozott felületek száma | 10 QPS|
| A nyilvános modelltárházban létrehozott közös tartományvezérlők száma | 10 QPS|
| A nyilvános modelltárházban létrehozott összeköttetések száma | 10 QPS|

## <a name="parser-library"></a>Elemző könyvtár

Az elemzőkönyvtár a [digitális ikerdefiníciós nyelvre](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)vonatkozó korlátokat követi.

## <a name="next-steps"></a>További lépések

A javasolt következő lépés az [IoT Plug and Play eszközökcsatlakoztatásának és az azzal való együttműködésnek](./howto-develop-solution.md)a megismerése.
