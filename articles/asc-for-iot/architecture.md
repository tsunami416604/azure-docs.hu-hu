---
title: A IoT-megoldás architektúrájának Azure Security Center ismertetése | Microsoft Docs
description: Ismerje meg a IoT szolgáltatás Azure Security Centerban található információk áramlását.
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
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922166"
---
# <a name="azure-security-center-for-iot-architecture"></a>Az Azure Security Center for IoT architektúrája

Ez a cikk a IoT-megoldás Azure Security Centerának funkcionális rendszerarchitektúráját ismerteti. 

## <a name="azure-security-center-for-iot-components"></a>IoT-összetevők Azure Security Center

A IoT Azure Security Center a következő összetevőkből áll:
- IoT Hub integráció
- Eszközök ügynökei (nem kötelező)
- Biztonsági üzenetsor-SDK küldése
- Elemzési folyamat
 
### <a name="azure-security-center-for-iot-workflows"></a>IoT-munkafolyamatok Azure Security Center

A IoT Azure Security Center a következő két szolgáltatás munkafolyamatában működik: beépített és továbbfejlesztett  

### <a name="built-in"></a>Beépített
A **beépített** módban a IoT Azure Security Center engedélyezve van, ha úgy dönt, hogy bekapcsolja a **biztonsági** beállítást a IoT hub. A valós idejű monitorozást, ajánlásokat és riasztásokat kínáló beépített üzemmód egylépéses eszköz-láthatóságot és páratlan biztonságot nyújt. A beépített módban nem szükséges az ügynök telepítése semmilyen eszközön, és a naplózott tevékenységeken a speciális elemzések segítségével elemezheti és védetté teheti a mező eszközét. 

### <a name="enhanced"></a>Továbbfejlesztett 
**Bővített** módban a IoT hub **biztonsági** beállításának bekapcsolása és az eszközökön lévő IoT telepítése után az ügynökök a nyers biztonsági események gyűjtését, összesítését és elemzését Azure Security Center végzik el az eszközökön. A nyers biztonsági események közé tartozhatnak az IP-kapcsolatok, a folyamatok létrehozása, a felhasználói bejelentkezések és egyéb fontos információk. A IoT-ügynökök Azure Security Center az események összesítését is kezelik a nagy hálózati átviteli sebesség elkerülése érdekében. Az ügynökök nagy mértékben testreszabhatók, így adott feladatokhoz is használhatók, például csak a leggyorsabb SLA-ban, vagy a kiterjedt biztonsági információk és környezetek nagyobb szegmensekre történő elküldésével, a magasabb szolgáltatási költségek elkerülésével.

![Az Azure Security Center for IoT architektúrája](./media/architecture/azure-iot-security-architecture.png)
 
Az eszközök ügynökei és más alkalmazásai az **Azure Security Message SDK** használatával biztonsági információkat küldhetnek az Azure IoT hubba. IoT Hub felveszi ezt az információt, és továbbítja azt a IoT szolgáltatás Azure Security Center.

Ha a IoT szolgáltatáshoz tartozó Azure Security Center engedélyezve van, a továbbított adatokon kívül IoT Hub a belső adatokat is elküldheti elemzésre a IoT Azure Security Center. Ezek az adategységek a Felhőbeli műveleti naplókat, az eszköz identitásait és a hub konfigurációját tartalmazzák. Ezen információk mindegyike segíti a IoT Analytics-folyamat Azure Security Center létrehozását.
 
A IoT Analytics-folyamat Azure Security Center a Microsofton és a Microsoft partnerein belüli különböző forrásokból származó további veszélyforrások elleni intelligenciát is megkapja. A teljes elemzési folyamat IoT Azure Security Center a szolgáltatásban végzett minden ügyfél-konfigurációval működik (például egyéni riasztások és a biztonsági üzenet küldése SDK használata).
 
Az elemzési folyamat használatával a IoT Azure Security Center az összes adatfolyamot ötvözi a gyakorlatban alkalmazható ajánlások és riasztások létrehozásához. A folyamat a biztonsági kutatók és szakértők által létrehozott egyéni szabályokat, valamint a szabványos eszköz viselkedésével és a kockázatelemzéstől való eltérést kereső gépi tanulási modelleket is tartalmaz.
 
A IoT ajánlásainak és értesítéseinek Azure Security Center (az elemzési folyamat kimenete) az egyes ügyfelek Log Analytics munkaterületére íródik. A munkaterületen található nyers események, valamint a riasztások és javaslatok segítségével a gyanús tevékenységek pontos részleteivel részletesen elmerülhetnek a felderítéssel kapcsolatos vizsgálatok és lekérdezések.  

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megismerheti a IoT-megoldás Azure Security Centerának alapszintű architektúráját és munkafolyamatát. Ha többet szeretne megtudni az előfeltételekről, az első lépésekről és a biztonsági megoldás engedélyezéséről IoT Hubban, tekintse meg a következő cikkeket:

- [A szolgáltatás előfeltételei](service-prerequisites.md)
- [Bevezetés](getting-started.md)
- [A megoldás konfigurálása](quickstart-configure-your-solution.md)
- [A IoT Hub biztonságának engedélyezése](quickstart-onboard-iot-hub.md)
- [Azure Security Center IoT – gyakori kérdések](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT – biztonsági riasztások](concept-security-alerts.md)
