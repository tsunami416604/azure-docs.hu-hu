---
title: Tudnivalók az Azure Security Center az IoT-megoldásarchitektúra előzetes verzió |} A Microsoft Docs
description: Ismerje meg az Azure Security Center az IoT-szolgáltatás az adatok áramlása látható.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: a0eb459391da65f8d0e2ae251809805924d07ad1
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862365"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center az IoT-architektúra

Ez a cikk ismerteti a működési rendszer-architektúra, az Azure Security Center (ASC) az IoT-megoldás. 

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>ASC IoT-összetevők

Az IoT ASC az alábbi összetevőkből áll:
- Eszköz-ügynökök
- SDK-t biztonsági üzenet küldése
- IoT Hub-integrációval
- Analytics-folyamat
 
### <a name="asc-for-iot-workflow"></a>ASC IoT-munkafolyamat

IoT-eszköz ügynökök ASC lehetővé teszi, hogy egyszerűen az eszközök a nyers biztonsági események gyűjtését. Nyers biztonsági események IP kapcsolatok, folyamat-létrehozás, felhasználói bejelentkezések és egyéb biztonsági kapcsolódó információkat tartalmazhatnak. IoT-eszköz ügynökök ASC is kezelni, így elkerülhető a magas hálózati kapacitása esemény összesítést. Az ügynökök olyan nagymértékben testre szabható, így azok használatát, bizonyos feladatok, például a leggyorsabb SLA csak fontos információkat küldhet vagy nagyobb méretű részekre kiterjedt biztonságiadat- és környezeti összesítéséhez szükséges magasabb szolgáltatás költségek elkerülése.
 
Eszköz-ügynökök és más alkalmazások használatra a **Azure ASC küldése biztonsági SDK** Azure IoT Hub biztonsági adatok küldése. Az IoT Hub szerzi be ezt az információt, és továbbítja azokat az ASC IoT-szolgáltatás.

A továbbított adatok mellett az ASC IoT szolgáltatás engedélyezése után az IoT Hub is elküldi a belső adatok elemzéséhez az ASC az IoT. Az adatok között szerepel a műveletnaplók eszközről a felhőbe, eszközidentitások és Hub konfigurációját. Összes Ez az információ segít az ASC IoT-elemzési folyamat létrehozása.
 
IoT-elemzési folyamat ASC is fogad további threat intelligence Streamek különböző forrásokból belül a Microsoft és a Microsoft partnerei. Az ASC IoT teljes elemzési folyamat minden ügyfél-konfigurációval (például egyéni riasztások és a küldési biztonsági közlemény SDK használata) a szolgáltatásban végzett működik.
 
Az elemzési folyamatok használata esetén ASC az IoT egyesíti az összes a Streamek információit gyakorlatban hasznosítható javaslatokat és riasztásokat hozhat létre. A folyamat hozta létre biztonsági kutatói és a szakértők, valamint a gépi tanulási modelleket standard-eszközök viselkedését és a kockázati elemzés eltérés keresése egyéni szabályokat is tartalmazza.
 
A Log Analytics-munkaterületet, az egyes ügyfelek írt ASC IoT javaslatokat és riasztásokat (elemzési folyamatok elvégzésére kimeneti). Beleértve a nyers események a munkaterülethez, valamint a riasztások és javaslatok lehetővé teszi a deep dive vizsgálatok és a lekérdezések a pontos az észlelt gyanús tevékenységek részleteit.  

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az alapvető architektúráját és IoT-megoldás az ASC-munkafolyamatot. További információ az Előfeltételek, hogyan lehet a kezdéshez, és engedélyezze az IoT Hub biztonsági megoldását, tekintse meg a következő cikkeket:

- [A szolgáltatás előfeltételei](service-prerequisites.md)
- [Első lépések](getting-started.md)
- [A megoldás konfigurálása](quickstart-configure-your-solution.md)
- [Az IoT Hub-biztonság engedélyezése](quickstart-onboard-iot-hub.md)
- [ASC IOT – gyakori kérdések](resources-frequently-asked-questions.md)
- [Az IoT biztonsági riasztásokhoz ASC](concept-security-alerts.md)

