---
title: Biztonságos pontszám a Azure Security Centerban | Microsoft Docs
description: " A biztonsági javaslatok rangsorolása a Azure Security Center biztonságos pontszámának használatával. "
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
ms.openlocfilehash: 15a94a9724fac29d36f6bb88ee4810b3bc7ca607
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394654"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Javítsa a biztonságos pontszámot Azure Security Center

> [!NOTE]
> Az előzetes verzióban elérhető egy bővített biztonsági pontszám. A fokozottan biztonságos pontszám végül lecseréli a meglévő biztonságos pontszámot, egy darabig pedig párhuzamosan futnak, hogy megkönnyítsék az áttérést.
>
> A fokozottan biztonságos pontszám előnyeiről [itt](secure-score-security-controls.md)talál további információt.
>
> Az előzetes verzióban való részvételhez nyissa meg az Azure Portalt, indítsa el Azure Security Center, és válassza a biztonságos pontszám lehetőséget. Innen egy szalagcím jelenik meg az oldal tetején, amely az új biztonságos pontszám élményét kínálja. Másik lehetőségként kattintson [ide](https://aka.ms/ascnewscore).

Számos olyan szolgáltatás, amely biztonsági előnyöket kínál, gyakran nehéz megismerni, hogy milyen lépéseket kell elvégeznie a számítási feladatok biztonságossá tételéhez és megerősítéséhez. A biztonságos pontszám áttekinti a biztonsági javaslatokat, és rangsorolja őket az Ön számára, így biztos lehet benne, hogy mely ajánlásokat kell elvégeznie. Ez segít megtalálni a legsúlyosabb biztonsági réseket, hogy rangsorolni lehessen a vizsgálatot. A biztonságos pontszám egy olyan eszköz, amely segít felmérni a munkaterhelés biztonsági helyzetét.

## <a name="secure-score-calculation"></a>Biztonságos pontszám kiszámítása

A Security Center a biztonsági elemzők munkáját, a biztonsági javaslatok áttekintését és a speciális algoritmusok alkalmazásával határozza meg, hogy az egyes javaslatok mennyire fontosak.
Az Azure Security Center folyamatosan áttekinti az aktív javaslataikat, és kiszámítja a biztonsági pontszámát, és az ajánlat pontszáma a legfontosabb súlyossági és biztonsági gyakorlatokból származik, amelyekkel a lehető leghatékonyabban befolyásolhatja a munkaterhelés-biztonságot.

A Security Center egy **teljes körű biztonságos pontszámot**is biztosít. 

A **teljes körű biztonsági pontszám** a javaslat összes pontszámának felhalmozódása. A kiválasztott beállításoktól függően megtekintheti az előfizetések és a felügyeleti csoportok teljes biztonsági pontszámát. A pontszám a kiválasztott előfizetés és az előfizetések aktív javaslatai alapján változhat.

Ha szeretné megtekinteni, hogy mely javaslatok befolyásolják a legtöbbet a biztonságos pontszámot, megtekintheti az első három leghatékonyabb javaslatot a Security Center irányítópulton, vagy rendezheti a javaslatok listáját a **biztonságos pontszám hatása** oszlop használatával.

A teljes biztonsági pontszám megtekintése:

1. Az Azure-irányítópulton kattintson a **Security Center** , majd a **biztonságos pontszám**elemre.

2. A felső részen a biztonságos pontszám Kiemelt jellemzői láthatók:
   - A **teljes biztonsági pontszám** a pontszám/szabályzatok alapján kiválasztott előfizetésre vonatkozik
   - A **biztonságos pontszám kategóriánként** megmutatja, hogy mely erőforrásokra van szükség a legnagyobb figyelmet
   - A **biztonságos pontszám hatásával kapcsolatos leggyakoribb javaslatok alapján** megtekintheti azokat az ajánlásokat, amelyek a lehető leghatékonyabban javítják a biztonságos pontszámot.
 
   ![Biztonságos pontszám](./media/security-center-secure-score/secure-score-dashboard.png)

3. Az alábbi táblázatban megtekintheti az egyes előfizetéseket, valamint a teljes biztonságos pontszámot.

   > [!NOTE]
   > Az egyes előfizetések biztonságos pontszámának összege nem egyenlő a teljes biztonsági pontszámmal. A biztonságos pontszám kiszámítása az egészséges erőforrások és a teljes erőforrások közötti arány alapján történik, nem pedig az előfizetések összes biztonságos pontszáma. 
   >
4. Kattintson a **javaslatok megtekintése** lehetőségre, és tekintse meg az előfizetésre vonatkozó javaslatokat, amelyekkel javíthatja a biztonságos pontszámot.
4. A javaslatok listájában láthatja, hogy minden javaslathoz van egy oszlop, amely a **biztonságos pontszám hatását**jelöli. Ez a szám azt jelzi, hogy a teljes biztonsági pontszám milyen mértékben javul, ha követi a javaslatokat. Például az alábbi képernyőn, ha elhárítja a biztonsági **réseket a tároló biztonsági konfigurációjában**, akkor a biztonságos pontszám 35 ponttal nő.

   ![Biztonságos pontszám](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Egyéni biztonsági pontszám

Emellett az egyes biztonsági pontszámok megtekintéséhez az egyes javaslatok panelen is megtalálhatja ezeket.  

A **biztonságos pontszám** kiszámítása az egészséges erőforrások és a teljes erőforrások aránya alapján történik. Ha a kifogástalan erőforrások száma megegyezik az erőforrások teljes számával, akkor a 50-es javaslat maximális biztonságos pontszámát kapja meg. Ha szeretné, hogy a biztonsági pontszáma közelebb kerüljön a maximális pontszámhoz, javítsa ki a nem megfelelő állapotú erőforrásokat a javaslatok követésével.

Az **ajánlási hatás** azt mutatja, hogy a biztonsági pontszám milyen mértékben javul, ha alkalmazza a javaslat lépéseit. Ha például a biztonságos pontszám 42, és a **javaslat hatása** a + 3, a javaslatban ismertetett lépések végrehajtásával javítsa a pontszámot, hogy 45 váljon.

A javaslat azt mutatja, hogy a számítási feladatok milyen veszélyforrásokkal vannak kitéve, ha a Szervizelési lépések nem teljesülnek.

![egyedi javaslat – biztonságos pontszám](./media/security-center-secure-score/indiv-recommendation-secure-score.png)




## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan javíthatja biztonsági állapotát a Azure Security Center **biztonságos pontszámának** használatával. A Security Centerról további információt a következő témakörben talál:

* [Azure Security Center GYIK](faq-general.md)– gyakori kérdések a szolgáltatással és a biztonságos pontszámmal kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.
* [Biztonságos pontszám – továbbfejlesztett](secure-score-security-controls.md)– megismerheti, hogy jelenleg előzetes verzióban érhető el a fokozott biztonságú pontszám előnye.