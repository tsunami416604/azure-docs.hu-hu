---
title: Fenyegetésfelderítés & biztonsági riasztási térképe – Azure Security Center
description: Ismerje meg, hogyan használhatja a biztonsági riasztási térképet és a fenyegetésfelderítési képességet az Azure Security Centerben a virtuális gépekés számítógépek potenciális fenyegetéseinek azonosításához.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603431"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Biztonsági riasztások térképe és fenyegetések felderítése
Ez a cikk segítséget nyújt az Azure Security Center biztonsági riasztások leképezésének és a biztonsági eseményalapú fenyegetésfelderítési térkép nek a biztonsággal kapcsolatos problémák megoldásához.

> [!NOTE]
> 2019. július 31-én megszüntették a Biztonsági *események* térképgombot. További információ és alternatív szolgáltatások: [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>A biztonsági riasztások térképének működése
A Security Center egy térképet biztosít, amely segít azonosítani a környezetet fenyegető biztonsági fenyegetéseket. Például azonosíthatja, hogy egy adott számítógép egy botnet része-e, és hogy honnan származik a fenyegetés. A számítógépek csomóponttá válhatnak a botnetben, ha a támadók illegálisan telepítenek olyan rosszindulatú programokat, amelyek titokban kommunikálnak a botnetet kezelő parancsokkal és vezérléssel. 

A térkép létrehozásához a Security Center a Microsofton belül több forrásból származó adatokat használ. A Security Center ezeket az adatokat a környezetet fenyegető lehetséges fenyegetések leképezésére használja. 

A [biztonsági incidensek megoldási folyamatának](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) egyik lépése a már nem biztonságos rendszer(eke)t ért kár súlyosságának beazonosítása. Ebben a szakaszban a következő feladatokat kell elvégeznie:

- A támadás természetének megállapítása.
- Határozza meg a támadás kiindulópontját.
- A támadás szándékának azonosítása. A támadás célzottan az Ön szervezete ellen irányult, adott információk megszerzése céljából, vagy nem célzott támadásról van szó?
- A sérült rendszerek azonosítása.
- A támadók által elért fájlok azonosítása, és azok bizalmasságának felmérése.

A Biztonsági központ Biztonsági riasztások térképe segítségével segítséget kaphat ezekhez a feladatokhoz.

## <a name="access-the-security-alerts-map"></a>A biztonsági riasztások térképének elérése
A környezetben lévő aktuális fenyegetések megjelenítéséhez nyissa meg a Biztonsági riasztások térképet:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali ablaktáblában a **Veszélyforrások elleni védelem** csoportban válassza a **Biztonsági riasztások leképezése lehetőséget.** Megnyílik a térkép.
3. Ha további információt szeretne kapni a riasztásról, és javítási lépéseket szeretne kapni, kattintson a térképen lévő Riasztási elemre, és kövesse az utasításokat. 
 
A biztonsági riasztások térképe riasztásokon alapul. Ezek a riasztások olyan tevékenységeken alapulnak, amelyekhez a hálózati kommunikáció egy sikeresen feloldott IP-címhez volt társítva, függetlenül attól, hogy az IP-cím ismert kockázatos IP-cím (például ismert kriptográfiai bányász) vagy egy nem felismert IP-cím-e. korábban kockázatosnak minősült. A térkép riasztásokat biztosít az Azure-ban korábban kiválasztott előfizetések között. 

A térképen lévő riasztások a földrajzi helynek megfelelően jelennek meg, ahonnan a rendszer észleli őket, és súlyosságszerint vannak kódolva. 
    ![Fenyegetésfelderítési információk](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedett a fenyegetésfelderítés a Security Centerben a gyanús tevékenységek azonosítására történő használatával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [A biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben.](security-center-monitoring.md) Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Az Azure Security Center hibaelhárítási útmutatója](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.