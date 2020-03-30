---
title: Az Azure Security Center az IoT-megoldásarchitektúrához| Microsoft dokumentumok
description: Ismerje meg az információáramlást az Azure Security Center for IoT szolgáltatásban.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 2e55f98f8c7b6ddbc21f7ea8633467461ea5be29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922166"
---
# <a name="azure-security-center-for-iot-architecture"></a>Az Azure Security Center for IoT architektúrája

Ez a cikk ismerteti az Azure Security Center for IoT-megoldás funkcionális rendszerarchitektúráját. 

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center ioT-összetevőkhöz

Az Azure Security Center for IoT a következő összetevőkből áll:
- IoT Hub-integráció
- Eszközügynökök (nem kötelező)
- Biztonsági üzenet küldése SDK
- Elemzési folyamat
 
### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center IoT-munkafolyamatokhoz

Az Azure Security Center for IoT a következő két funkciómunkafolyamat egyikében működik: beépített és továbbfejlesztett  

### <a name="built-in"></a>Beépített
**Beépített** módban az Azure Security Center for IoT engedélyezve van, ha úgy dönt, hogy bekapcsolja a **biztonság** beállítást az IoT Hubban. A valós idejű figyelést, javaslatokat és riasztásokat kínáló beépített mód egylépéses eszközláthatóságot és páratlan biztonságot kínál. A beépített mód nem igényel ügynöktelepítést egyetlen eszközön sem, és a naplózott tevékenységek fejlett elemzésével elemzi és védi a mezőeszközt. 

### <a name="enhanced"></a>Továbbfejlesztett 
**Továbbfejlesztett** módban, miután **bekapcsolta** a Biztonság beállítást az IoT Hubban, és telepítette az Azure Security Center for IoT eszközügynököket az eszközökre, az ügynökök összegyűjtik, összesítik és elemzik a nyers biztonsági eseményeket az eszközökről. A nyers biztonsági események közé tartozhatnak az IP-kapcsolatok, a folyamatok létrehozása, a felhasználói bejelentkezések és egyéb, biztonsági szempontjából releváns információk. Az Azure Security Center for IoT eszközügynökök is kezeli az eseményösszesítés, hogy elkerüljék a nagy hálózati átviteli. Az ügynökök nagymértékben testreszabhatók, lehetővé téve, hogy bizonyos feladatokhoz is használhassa őket, például csak fontos információkat küldjön a leggyorsabb SLA-n, vagy hogy kiterjedt biztonsági információkat és környezeteket összesítsen nagyobb szegmensekbe, elkerülve a magasabb szolgáltatási költségeket.

![Az Azure Security Center for IoT architektúrája](./media/architecture/azure-iot-security-architecture.png)
 
Az eszközügynökök és más alkalmazások az Azure-ban küldik el az **SDK biztonsági üzenetet,** hogy biztonsági információkat küldjenek az Azure IoT Hubba. Az IoT Hub felveszi ezeket az információkat, és továbbítja azokat az Azure Security Center for IoT szolgáltatás.

Miután az Azure Security Center for IoT szolgáltatás engedélyezve van, a továbbított adatok mellett az IoT Hub is elküldi az összes belső adatok elemzésaz Azure Security Center for IoT. Ezek az adatok magukban tartalmazzák az eszközfelhő-üzemeltetési naplókat, az eszközidentitásokat és a Hub-konfigurációt. Mindezek az információk segítenek az Azure Security Center ioT-elemzési folyamat létrehozásában.
 
Az Azure Security Center for IoT-elemzési folyamat további fenyegetésfelderítési adatfolyamokat is kap a Microsoft és a Microsoft partnereitől különböző forrásokból. Az Azure Security Center for IoT teljes elemzési folyamat együttműködik a szolgáltatás on minden ügyfél konfiguráció (például az egyéni riasztások és a küldési biztonsági üzenet SDK használata).
 
Az elemzési folyamat használatával az Azure Security Center for IoT egyesíti az összes adatfolyamot, hogy végrehajtható javaslatokat és riasztásokat hozzon létre. A folyamat tartalmazza a biztonsági kutatók és szakértők által létrehozott egyéni szabályokat, valamint a gépi tanulási modelleket, amelyek a szabványos eszközviselkedéstől és a kockázatelemzéstől való eltérést keresik.
 
Az Azure Security Center az IoT-javaslatok és riasztások (elemzési folyamat kimenete) az egyes ügyfelek Log Analytics-munkaterületére kerül. A belső események a munkaterületen, valamint a riasztások és javaslatok lehetővé teszi a részletes merülés vizsgálatok és lekérdezések a gyanús észlelt tevékenységek pontos részleteit.  

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Security Center for IoT-megoldás alapvető architektúrájáról és munkafolyamatairól szerzett ismereteket. Ha többet szeretne megtudni az előfeltételekről, az első lépésekről és a biztonsági megoldás engedélyezéséről az IoT Hubban, olvassa el az alábbi cikkeket:

- [A szolgáltatás előfeltételei](service-prerequisites.md)
- [Kezdetekhez](getting-started.md)
- [A megoldás konfigurálása](quickstart-configure-your-solution.md)
- [A biztonság engedélyezése az IoT Hubban](quickstart-onboard-iot-hub.md)
- [Az Azure Security Center for IoT – gyakori kérdések](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT – biztonsági riasztások](concept-security-alerts.md)
