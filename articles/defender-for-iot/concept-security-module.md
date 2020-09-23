---
title: Biztonsági modul és eszköz ikrek
description: Ismerje meg az ikrek biztonsági moduljának fogalmát, valamint azt, hogy azok hogyan használhatók a Defenderben a IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: a5382313c837482f116f498f3a05c36447062b0a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940944"
---
# <a name="security-module"></a>Biztonsági modul

Ez a cikk azt ismerteti, hogyan használja a Defender for IoT az eszköz-ikreket és modulokat.

## <a name="device-twins"></a>Eszköz ikrek

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is.

A IoT Defender teljes körű integrációt biztosít a meglévő IoT-eszközkezelés platformmal, lehetővé téve az eszköz biztonsági állapotának kezelését, valamint a meglévő eszközök vezérlési képességeinek kihasználását. Az integráció a IoT Hub Twin mechanizmus használatával érhető el.

Tudjon meg többet az Azure IoT Hub [eszközön található ikrek](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) koncepciójának megismeréséről.

## <a name="security-module-twins"></a>Biztonsági modul ikrek

A IoT Defender a szolgáltatás minden eszközéhez külön biztonsági modult tart fenn.
A biztonsági modul Twin tartalmazza az eszköz biztonságával kapcsolatos összes információt a megoldás minden egyes eszközén.
Az eszköz biztonsági tulajdonságait egy külön biztonsági modulban, a biztonságosabb kommunikációhoz, valamint a kevesebb erőforrást igénylő frissítések és karbantartás engedélyezéséhez kell megőrizni.

Lásd: a [biztonsági modul létrehozása](quickstart-create-security-twin.md) és a [biztonsági ügynökök konfigurálása](how-to-agent-configuration.md) , amelyekből megtudhatja, hogyan hozhatja létre, testreszabhatja és konfigurálhatja a Twin-et. Tekintse meg az ikrek [modul ismertetése](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) című témakört, amely részletesebben ismerteti a IoT hub-ben az ikrek koncepcióját.

## <a name="see-also"></a>Lásd még

- [Defender for IoT – áttekintés](overview.md)
- [Biztonsági ügynökök üzembe helyezése](how-to-deploy-agent.md)
- [Biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md)
