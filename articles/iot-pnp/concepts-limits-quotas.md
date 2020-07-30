---
title: Korlátok és kvóták IoT Plug and Play előzetes verzió | Microsoft Docs
description: Ismerje meg a IoT Plug and Play előzetes verziójának használatakor alkalmazandó korlátokat, kvótákat és szabályozásokat.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337398"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT Plug and Play előzetes verziójának korlátai, kvótái és szabályozásai

Ez a cikk ismerteti a nyilvános előzetes verzióban alkalmazandó, Plug and Play-specifikus korlátokat, kvótákat és IoT. Meglévő [IoT hub kvóták és szabályozás](../iot-hub/iot-hub-devguide-quotas-throttling.md) is érvényes.

## <a name="iot-hub"></a>IoT Hub

A nyilvános előzetes verzió esetében a következő korlátozások és kvóták érvényesek egy IoT hub-ra:

| Korlátok, korlátozások és szabályozások | Érték | Megjegyzések |
|-----|-----|-----|
| A központ által regisztrálható felületek száma | 1500 ||
| Az összetevő nevének maximális mérete | 1-64 karakter | Engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter), és az aláhúzás (nem az első vagy az utolsó karakter). |
| Tulajdonságnév maximális mérete | 1-64 karakter | Engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter), és az aláhúzás (nem az első vagy az utolsó karakter). |
| Tulajdonság értékének maximális mérete | Ugyanaz, mint a digitális Twins Definition Language [tulajdonsága](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) | 5 szint mélységben, és nem lehet tömb vagy olyan összetett séma, amely egy tömböt tartalmaz |
| Parancs nevének maximális mérete | 1-64 karakter | Engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter), és az aláhúzás (nem az első vagy az utolsó karakter).|
| Eszköz kettős mérete | Ugyanaz, mint a [IoT hub korlátok](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Elemző könyvtár

Az elemző könyvtár a [digitális Twins-definíciós nyelvre](https://github.com/Azure/opendigitaltwins-dtdl)vonatkozó korlátokat követi.

## <a name="next-steps"></a>Következő lépések

A javasolt következő lépés a [IoT Plug and Play architektúra](concepts-architecture.md)áttekintése.
