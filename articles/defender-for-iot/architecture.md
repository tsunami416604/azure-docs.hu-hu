---
title: Megoldásarchitektúra
description: Ismerje meg az Azure Defender for IoT szolgáltatás információinak áramlását.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d26d9e3d686ad7c34e7493dc1413b7a9e7a2f6b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940980"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender a IoT architektúrához

Ez a cikk a Defender IoT-megoldás működési rendszerarchitektúráját ismerteti.

## <a name="defender-for-iot-components"></a>Defender IoT-összetevőkhöz

A IoT Defender a következő összetevőkből áll:

- IoT Hub integráció
- Eszközök ügynökei (nem kötelező)
- Biztonsági üzenetsor-SDK küldése
- Elemzési folyamat

### <a name="defender-for-iot-workflows"></a>Defender a IoT-munkafolyamatokhoz

A IoT Defender a következő két szolgáltatás-munkafolyamatban működik: beépített és továbbfejlesztett

### <a name="built-in"></a>Beépített

A **beépített** módban a Defender for IoT engedélyezve van, ha úgy dönt, hogy bekapcsolja a **biztonsági** beállítást a IoT hub. A valós idejű monitorozást, ajánlásokat és riasztásokat kínáló beépített üzemmód egylépéses eszköz-láthatóságot és páratlan biztonságot nyújt. A beépített módban nem szükséges az ügynök telepítése semmilyen eszközön, és a naplózott tevékenységeken a speciális elemzések segítségével elemezheti és védetté teheti a mező eszközét.

### <a name="enhanced"></a>Továbbfejlesztett

**Továbbfejlesztett** módban, miután bekapcsolta a **biztonsági** beállítást a IoT hub és a Defender telepítését a IoT az eszközökön, az ügynökök gyűjtik, összesítik és elemzik a nyers biztonsági eseményeket az eszközökről. A nyers biztonsági események közé tartozhatnak az IP-kapcsolatok, a folyamatok létrehozása, a felhasználói bejelentkezések és egyéb fontos információk. A IoT-ügynökök védelmezője az események összesítését is kezeli, hogy elkerülje a hálózat magas átviteli sebességét. Az ügynökök nagy mértékben testreszabhatók, így adott feladatokhoz is használhatók, például csak a leggyorsabb SLA-ban, vagy a kiterjedt biztonsági információk és környezetek nagyobb szegmensekre történő elküldésével, a magasabb szolgáltatási költségek elkerülésével.

![Defender a IoT architektúrához](./media/architecture/azure-iot-security-architecture.png)

Az eszközök ügynökei és más alkalmazásai az **Azure Security Message SDK** használatával biztonsági információkat küldhetnek az Azure IoT hubba. IoT Hub felveszi ezt az információt, és továbbítja azt a Defender for IoT szolgáltatásnak.

Ha a Defender for IoT szolgáltatás engedélyezve van, a továbbított adatokon felül a IoT Hub a Defender által az IoT-hoz történő elemzéshez is küldi a belső adatokat. Ezek az adategységek a Felhőbeli műveleti naplókat, az eszköz identitásait és a hub konfigurációját tartalmazzák. Mindezen információk segítenek létrehozni a Defendert a IoT Analytics-folyamathoz.

A IoT Analytics-folyamat Defender a Microsoft és a Microsoft partnerei által használt különböző forrásokból származó további veszélyforrások elleni intelligenciát is megkapja. A IoT teljes elemzési folyamatának védelmezője a szolgáltatásban végzett összes ügyfél-konfigurációval működik (például az egyéni riasztások és a biztonsági üzenet küldése SDK használata).

Az elemzési folyamat használatával a Defender for IoT az összes adatfolyamot ötvözi a gyakorlatban használható javaslatok és riasztások létrehozásához. A folyamat a biztonsági kutatók és szakértők által létrehozott egyéni szabályokat, valamint a szabványos eszköz viselkedésével és a kockázatelemzéstől való eltérést kereső gépi tanulási modelleket is tartalmaz.

A IoT ajánlásai és értesítései (analitikai folyamat kimenete) az egyes ügyfelek Log Analytics munkaterületére íródnak. A munkaterületen található nyers események, valamint a riasztások és javaslatok segítségével a gyanús tevékenységek pontos részleteivel részletesen elmerülhetnek a felderítéssel kapcsolatos vizsgálatok és lekérdezések.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan hozhatja ki a Defender IoT-megoldás alapszintű architektúráját és munkafolyamatát. Ha többet szeretne megtudni az előfeltételekről, az első lépésekről és a biztonsági megoldás engedélyezéséről IoT Hubban, tekintse meg a következő cikkeket:

- [A szolgáltatás előfeltételei](service-prerequisites.md)
- [Bevezetés](getting-started.md)
- [A megoldás konfigurálása](quickstart-configure-your-solution.md)
- [A IoT Hub biztonságának engedélyezése](quickstart-onboard-iot-hub.md)
- [Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)
- [IoT biztonsági riasztások védelmezője](concept-security-alerts.md)
