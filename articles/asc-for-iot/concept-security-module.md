---
title: Az ikrek IoT biztonsági moduljának Azure Security Center ismertetése | Microsoft Docs
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
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596498"
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