---
title: Korlátok és kvóták IoT Plug and Play | Microsoft Docs
description: Ismerje meg a IoT-Plug and Play használatakor alkalmazandó korlátokat, kvótákat és szabályozásokat.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577985"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>IoT Plug and Play korlátok, kvóták és szabályozások

Ez a cikk a IoT Plug and Play-specifikus korlátait, kvótáit és szabályozását ismerteti. Meglévő [IoT hub kvóták és szabályozás](../iot-hub/iot-hub-devguide-quotas-throttling.md) is érvényes.

## <a name="iot-hub"></a>IoT Hub

A következő korlátozások és kvóták érvényesek egy IoT hubhoz:

| Korlátok, korlátozások és szabályozások | Érték | Jegyzetek |
|-----|-----|-----|
| A központ által regisztrálható felületek száma | 1500 ||
| Az összetevő nevének maximális mérete | 1-64 karakter | Engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter), és az aláhúzás (nem az első vagy az utolsó karakter). |
| Tulajdonságnév maximális mérete | 1-64 karakter | Engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter), és az aláhúzás (nem az első vagy az utolsó karakter). |
| Tulajdonság értékének maximális mérete | Ugyanaz, mint a digitális Twins Definition Language [tulajdonsága](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) | 5 szint mélységben, és nem lehet tömb vagy olyan összetett séma, amely egy tömböt tartalmaz |
| Parancs nevének maximális mérete | 1-64 karakter | Engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter), és az aláhúzás (nem az első vagy az utolsó karakter).|
| Eszköz kettős mérete | Ugyanaz, mint a [IoT hub korlátok](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Elemző könyvtár

Az elemző könyvtár a [digitális Twins-definíciós nyelvre](https://github.com/Azure/opendigitaltwins-dtdl)vonatkozó korlátokat követi.

## <a name="next-steps"></a>További lépések

A javasolt következő lépés a [IoT Plug and Play architektúra](concepts-architecture.md)áttekintése.
