---
title: Fenyegetés intelligencia & biztonsági riasztások térképe – Azure Security Center
description: Megtudhatja, hogyan használhatja a biztonsági riasztások leképezése és a veszélyforrások felderítése funkciót a Azure Security Center a virtuális gépek és számítógépek potenciális fenyegetésének azonosítására.
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
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603431"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Biztonsági riasztások térképe és fenyegetések felderítése
Ebből a cikkből megtudhatja, hogy a biztonsággal kapcsolatos problémák megoldásához a Azure Security Center biztonsági riasztások leképezése és a biztonsági események alapján észlelt veszélyforrások felderítése a biztonsági problémákat.

> [!NOTE]
> A biztonsági *események* leképezése gomb 2019. július 31-én megszűnt. További információ és alternatív szolgáltatások: [Security Center szolgáltatások kivonása (július 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>A biztonsági riasztások térképének működése
Security Center olyan térképet biztosít, amely segít azonosítani a környezettel szembeni biztonsági fenyegetéseket. Meghatározhatja például, hogy egy adott számítógép egy botnet részét képezi-e, és a fenyegetés honnan származik. A számítógépek csomópontok lehetnek a botnetekben, ha a támadók a botnetet kezelő parancsokkal és vezérlőelemekkel titokban kommunikáló kártevő szoftvereket jogosulatlanul telepítenek. 

A Térkép létrehozásához Security Center a Microsofton belül több forrásból származó adatokkal működik. Security Center ezekkel az adataival leképezi a potenciális fenyegetéseket a környezettel szemben. 

A [biztonsági incidensek megoldási folyamatának](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) egyik lépése a már nem biztonságos rendszer(eke)t ért kár súlyosságának beazonosítása. Ebben a szakaszban a következő feladatokat kell elvégeznie:

- A támadás természetének megállapítása.
- Határozza meg a támadás kezdőpontját.
- A támadás szándékának azonosítása. A támadás célzottan az Ön szervezete ellen irányult, adott információk megszerzése céljából, vagy nem célzott támadásról van szó?
- A sérült rendszerek azonosítása.
- A támadók által elért fájlok azonosítása, és azok bizalmasságának felmérése.

Ezekkel a feladatokkal kapcsolatos segítségért használhatja a Security Center biztonsági riasztások leképezését.

## <a name="access-the-security-alerts-map"></a>Hozzáférés a biztonsági riasztások térképéhez
Az aktuális fenyegetések megjelenítéséhez a környezetében nyissa meg a biztonsági riasztások leképezését:

1. Nyissa meg a **Security Center** irányítópultját.
2. A bal oldali ablaktábla **veszélyforrások védelme** területén válassza a **biztonsági riasztások leképezése**elemet. Megnyílik a Térkép.
3. Ha további információt szeretne kapni a riasztásról és a Szervizelési lépésekről, kattintson a Térkép riasztás pontjára, és kövesse az utasításokat. 
 
A biztonsági riasztások leképezése riasztásokon alapul. Ezek a riasztások azokon a tevékenységeken alapulnak, amelyekhez a hálózati kommunikáció egy sikeresen megoldott IP-címmel lett társítva, függetlenül attól, hogy az IP-cím egy ismert kockázatos IP-cím (például ismert cryptominer) vagy egy ismeretlen IP-cím. korábban kockázatos. A Térkép riasztásokat biztosít az Azure-ban korábban kiválasztott előfizetések között. 

A térképen a riasztások a földrajzi hely alapján jelennek meg, ahol a rendszer a-ből származóként észleli őket, és súlyossági szint alapján kódolja őket. 
    ![veszélyforrások felderítésére vonatkozó információk](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedett a fenyegetésfelderítés a Security Centerben a gyanús tevékenységek azonosítására történő használatával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Az Azure Security Center hibaelhárítási útmutatója](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti.