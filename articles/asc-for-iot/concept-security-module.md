---
title: Biztonsági modul és eszköz ikrek
description: Ismerje meg az ikrek biztonsági moduljának fogalmát, valamint azt, hogy azok hogyan használhatók a IoT Azure Security Center.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311447"
---
# <a name="security-module"></a>Biztonsági modul

Ez a cikk azt ismerteti, hogy a IoT Azure Security Center hogyan használja az eszközökhöz készült ikreket és modulokat.

## <a name="device-twins"></a>Eszköz ikrek

Az Azure-ban beépített IoT-megoldások esetében az eszközök az ikrek kulcsszerepet játszanak az eszközkezelés és a folyamatok automatizálása terén is.

Azure Security Center for IoT teljes körű integrációt biztosít a meglévő IoT-eszközkezelés platformmal, így kezelheti az eszköz biztonsági állapotát, és használhatja a meglévő eszköz-vezérlési képességeket. Az integráció a IoT Hub Twin mechanizmus használatával érhető el.

Tudjon meg többet az Azure IoT Hub [eszközön található ikrek](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) koncepciójának megismeréséről.

## <a name="security-module-twins"></a>Biztonsági modul ikrek

A IoT Azure Security Center a szolgáltatás minden eszközéhez külön biztonsági modult tart fenn.
A biztonsági modul Twin tartalmazza az eszköz biztonságával kapcsolatos összes információt a megoldás minden egyes eszközén.
Az eszköz biztonsági tulajdonságait egy külön biztonsági modulban, a biztonságosabb kommunikációhoz, valamint a kevesebb erőforrást igénylő frissítések és karbantartás engedélyezéséhez kell megőrizni.

Lásd: a [biztonsági modul létrehozása](quickstart-create-security-twin.md) és a [biztonsági ügynökök konfigurálása](how-to-agent-configuration.md) , amelyekből megtudhatja, hogyan hozhatja létre, testreszabhatja és konfigurálhatja a Twin-et. Tekintse meg az ikrek [modul ismertetése](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) című témakört, amely részletesebben ismerteti a IoT hub-ben az ikrek koncepcióját.

## <a name="see-also"></a>Lásd még

- [A IoT Azure Security Center áttekintése](overview.md)
- [Biztonsági ügynökök üzembe helyezése](how-to-deploy-agent.md)
- [Biztonsági ügynök hitelesítési módszerei](concept-security-agent-authentication-methods.md)
