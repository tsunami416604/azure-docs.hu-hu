---
title: Korlátok és kvóták IoT Plug and Play előzetes verzió | Microsoft Docs
description: Ismerje meg a IoT Plug and Play előzetes verziójának használatakor alkalmazandó korlátokat, kvótákat és szabályozásokat.
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80518166"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT Plug and Play előzetes verziójának korlátai, kvótái és szabályozásai

Ez a cikk ismerteti a nyilvános előzetes verzióban alkalmazandó, Plug and Play-specifikus korlátokat, kvótákat és IoT. Meglévő [IoT hub kvóták és szabályozás](../iot-hub/iot-hub-devguide-quotas-throttling.md) is érvényes.

## <a name="iot-hub"></a>IoT Hub

A nyilvános előzetes verzió esetében a következő korlátozások és kvóták érvényesek egy IoT hub-ra:

| Korlátok, korlátozások és szabályozások | Érték | Megjegyzések |
|-----|-----|-----|
| Az eszköz kapacitási modelljeinek (DCMs) vagy a központ által regisztrálható felületek száma | 1500 ||
| Az Eszközönként regisztrálható felületek maximális száma | 40 ||
| Az Eszközönként regisztrálható DCMs maximális száma | 1 ||
| Illesztőfelület/DCM-fájl maximális mérete | 512 KB ||
| Illesztőfelület nevének maximális mérete | 256 karakter ||
| Tulajdonságnév maximális mérete  | 64 bájt, 7 szint mélységben (és az első szint a számára van fenntartva `$iotin` ) | Engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter) és az aláhúzás. |
| Tulajdonság értékének maximális mérete | 512 bájt ||
| Parancs nevének maximális mérete | 100 bájt ||
| Eszköz kettős mérete | Ugyanaz, mint a [IoT hub korlátok](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| API-hívások feloldása az SKU-ban (az egységektől függetlenül) | 100 kérelem/másodperc ||

## <a name="model-repository"></a>Modell tárháza

A nyilvános előzetes verzió esetében a következő korlátozások és kvóták érvényesek a modell adattárára:

| Korlátok, korlátozások és szabályozások | Érték |
|-----|-----|
| A vállalati modell adattárainak száma Azure Active Directory bérlőn | 1 |
| Engedélyezési kulcsok száma egy modell adattárában | 10  |
| Modellek száma (DCMs vagy Interface) a vállalati modell adattárában| 1500  |
| Modellek száma (DCMs vagy Interface) a nyilvános modell adattárában Azure Active Directory bérlő| 1500  |
| A vállalati modell adattárában törölt DCMs vagy felületek száma | másodpercenként 10 lekérdezés (QPS)|
| A bérlő által létrehozott/frissített modell-Tárházak száma| 1 QPS |
| A modell adattárában létrehozott/frissített/törölt engedélyezési kulcsok száma | 1 QPS|
| A vállalati modell adattárában létrehozott DCMs száma | 10 QPS |
| A vállalati modell adattárában létrehozott felületek száma | 10 QPS|
| A nyilvános modell adattárában létrehozott DCMs száma | 10 QPS|
| A nyilvános modell adattárában létrehozott felületek száma | 10 QPS|

## <a name="parser-library"></a>Elemző könyvtár

Az elemző könyvtár a [digitális kettős definíciós nyelvre](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)érvényes korlátokat követi.

## <a name="next-steps"></a>További lépések

A következő lépés az, hogy megtudja, hogyan [csatlakozhat egy IoT Plug and Play-eszközhöz, és hogyan dolgozhat velük](./howto-develop-solution.md).
