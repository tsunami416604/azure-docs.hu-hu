---
title: Biztonságos pontszám az Azure Security Centerben | Microsoft dokumentumok
description: " Az Azure Security Center biztonságos pontszámával rangsorolja a biztonsági javaslatokat. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415773"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Javítsa biztonságos pontszámát az Azure Security Centerben

> [!NOTE]
> Ez a cikk a biztonságos pontszám előző verziójáról szól. Ez a biztonságos pontszám tapasztalat továbbra is elérhető a felhasználói felületen, de fokozatosan megszűnik az idő múlásával. A két biztonságos pontszám élménye egymás mellett fut, így zökkenőmentesebb az átállás.
>
> Az újabb biztonságos pontszám részleteiről [itt](secure-score-security-controls.md)olvashat.
>

A számos biztonsági előnyt kínáló szolgáltatással gyakran nehéz tudni, hogy milyen lépéseket kell tennie először a munkaterhelés védelme és megkeményítése érdekében. A biztonságos pontszám áttekinti a biztonsági javaslatokat, és rangsorolja őket az Ön számára, így tudja, hogy mely ajánlásokat kell végrehajtania először. Ez segít megtalálni a legsúlyosabb biztonsági réseket, így rangsorolhatja a vizsgálatot. A Biztonságos pontszám egy olyan eszköz, amely segít felmérni a számítási feladatok biztonsági állapotát.

## <a name="secure-score-calculation"></a>Biztonságos pontszám számítása

A Security Center egy biztonsági elemző munkáját utánozza, áttekinti a biztonsági javaslatokat, és speciális algoritmusokat alkalmaz annak meghatározására, hogy az egyes javaslatok mennyire fontosak.
Az Azure Security Center folyamatosan áttekinti az aktív javaslatokat, és kiszámítja a biztonságos pontszám alapján őket, a pontszám egy javaslat származik annak súlyossága és a biztonsági ajánlott eljárások, amelyek befolyásolják a számítási feladatok biztonságát a leginkább.

A Security Center **általános biztonsági pontszámot**is biztosít. 

**Összességében biztonságos pontszám** felhalmozódása az összes ajánlás pontszámok. A teljes biztonságos pontszám az előfizetések vagy felügyeleti csoportok között, attól függően, hogy mit választ. A pontszám a kiválasztott előfizetéstől és az előfizetések aktív javaslataitól függően változik.

Annak ellenőrzéséhez, hogy mely javaslatok befolyásolják a legnagyobb mértékben a biztonságos pontszámot, megtekintheti az első három leghatásosabb javaslatot a Security Center irányítópultján, vagy rendezheti a javaslatokat a javaslatok listájában a **Biztonságos pontszám hatás** oszlop használatával.

A teljes biztonságos pontszám megtekintése:

1. Az Azure-irányítópulton kattintson a **Biztonsági központ** elemre, majd a Biztonságos **pontszám parancsra.**

2. A tetején látható Secure Score kiemeli:
   - A **teljes biztonságos pontszám** a szabályzatok szerint, a kiválasztott előfizetésenkénti pontszámot jelöli.
   - **A biztonságos pontszám kategória szerint** megmutatja, hogy mely erőforrásokigényelnek a legnagyobb figyelmet
   - **A Biztonságos pontszám hatás legfontosabb ajánlásai** azoknak a javaslatoknak a listáját nyújtják, amelyek a leginkább javítják a biztonságos pontszámot, ha megvalósítja őket.
 
   ![Biztonsági pontszám](./media/security-center-secure-score/secure-score-dashboard.png)

3. Az alábbi táblázatban láthatja az egyes előfizetések és a teljes biztonságos pontszám az egyes.

   > [!NOTE]
   > Az egyes előfizetések biztonságos pontszámának összege nem egyezik meg a teljes biztonságos pontszámmal. A biztonságos pontszám egy olyan számítás, amely a kifogástalan állapotú erőforrások és az összes erőforrás ajánlásonkénti aránya alapján történik, nem pedig az előfizetések biztonságos pontszámainak összege. 
   >
4. Kattintson **a Javaslatok megtekintése** gombra az adott előfizetésre vonatkozó javaslatok megtekintéséhez, amelyeket a biztonságos pontszám javítása érdekében javíthat.
4. A javaslatok listájában láthatja, hogy minden egyes ajánláshoz tartozik egy oszlop, amely a **biztonságos pontszám hatását**jelöli. Ez a szám azt jelzi, hogy a teljes biztonságos pontszám mennyit fog javulni, ha követi a javaslatokat. Ha például az alábbi képernyőn **a tárolóbiztonsági konfigurációk biztonsági réseit szünteti**meg, a biztonságos pontszám 35 ponttal nő.

   ![Biztonsági pontszám](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Egyéni biztonságos pontszám

Emellett az egyes biztonságos pontszámok megtekintéséhez ezeket az egyes ajánlási panelen találja.  

Az **ajánlott biztonságos pontszám** egy számítás a kifogástalan állapotú erőforrások és az összes erőforrás közötti arány alapján. Ha a kifogástalan erőforrások száma megegyezik az erőforrások teljes száma, akkor a maximális biztonságos pontszám a javaslat 50. Ha meg szeretné próbálni, hogy a biztonságos pontszám közelebb a maximális pontszámot, javítsa ki a nem megfelelő erőforrásokat a javaslatok követésével.

A **javaslat hatása** lehetővé teszi, hogy tudja, mennyit javít a biztonságos pontszám, ha alkalmazza a javaslat lépéseket. Ha például a biztonságos pontszám 42, és a **javaslat hatása** +3, a javaslatban ismertetett lépések végrehajtása javítja a pontszám 45 lesz.

A javaslat megmutatja, hogy a számítási feladatok milyen fenyegetéseknek vannak kitéve, ha a javítási lépések nem történtek meg.

![egyéni ajánlás biztonságos pontszám](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>További lépések
Ez a cikk bemutatja, hogyan javíthatja a biztonsági testtartást az Azure Security Center **biztonságos pontszám** használatával. Ha többet szeretne megtudni a Biztonsági központról, olvassa el a következő témakört:

* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
