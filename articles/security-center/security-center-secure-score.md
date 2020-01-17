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
ms.openlocfilehash: bd896cbee98bd61518c9a093f86e088f0b018d73
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156658"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Javítsa a biztonságos pontszámot Azure Security Center

> [!NOTE]
> Az előzetes verzióban elérhető egy bővített biztonsági pontszám. A fokozottan biztonságos pontszám végül lecseréli a meglévő biztonságos pontszámot, egy darabig pedig párhuzamosan futnak, hogy megkönnyítsék az áttérést.
>
> A fokozottan biztonságos pontszám előnyeiről [itt](secure-score-security-controls.md)talál további információt.
>
> Az előzetes verzióban való részvételhez nyissa meg az Azure Portalt, indítsa el Azure Security Center, és válassza a biztonságos pontszám lehetőséget. Innen egy szalagcím jelenik meg az oldal tetején, amely az új biztonságos pontszám élményét kínálja. Másik lehetőségként kattintson [ide](https://aka.ms/ascnewscore).

Számos olyan szolgáltatás, amely biztonsági előnyöket kínál, gyakran nehéz megismerni, hogy milyen lépéseket kell elvégeznie a számítási feladatok biztonságossá tételéhez és megerősítéséhez. Az Azure Secure pontszáma a biztonsági javaslatok áttekintését és rangsorolását végzi el Önnek, így biztos lehet benne, hogy mely ajánlásokat kell elvégeznie. Ez segít megtalálni a legsúlyosabb biztonsági réseket, hogy rangsorolni lehessen a vizsgálatot. A Biztonsági pontszám olyan eszköz, amely segít felmérni a számítási feladatok biztonsági helyzetét.

## <a name="secure-score-calculation"></a>Biztonságos pontszám kiszámítása

A Security Center a biztonsági elemzők munkáját, a biztonsági javaslatok áttekintését és a speciális algoritmusok alkalmazásával határozza meg, hogy az egyes javaslatok mennyire fontosak.
Az Azure Security Center folyamatosan áttekinti az aktív ajánlásokat, és kiszámítja a biztonsági pontszámát, és az ajánlat pontszáma a legfontosabb súlyossági és biztonsági gyakorlatból származik, amely a leghatékonyabban befolyásolja a munkaterhelés-biztonságot.

A Security Center egy **teljes körű biztonságos pontszámot**is biztosít. 

A **teljes körű biztonsági pontszám** a javaslat összes pontszámának felhalmozódása. A kiválasztott beállításoktól függően megtekintheti az előfizetések és a felügyeleti csoportok teljes biztonsági pontszámát. A pontszám a kiválasztott előfizetés és az előfizetések aktív javaslatai alapján változhat.

Ha szeretné megtekinteni, hogy mely javaslatok befolyásolják a legtöbbet a biztonságos pontszámot, megtekintheti az első három leghatékonyabb javaslatot a Security Center irányítópulton, vagy rendezheti a javaslatok listáját a **biztonságos pontszám hatása** oszlop használatával.

A teljes biztonsági pontszám megtekintése:

1. Az Azure-irányítópulton kattintson a **Security Center** , majd a **biztonságos pontszám**elemre.

2. A felső részen a biztonságos pontszám Kiemelt jellemzői láthatók:
   - A **teljes biztonsági pontszám** a szabályzatonkénti és kiválasztott előfizetésenkénti pontszámot jelöli.
   - A **biztonságos pontszám kategóriánként** megmutatja, hogy mely erőforrásokra van szükség a legnagyobb figyelmet
   - A **biztonságos pontszám hatásával kapcsolatos leggyakoribb javaslatok alapján** megtekintheti azokat az ajánlásokat, amelyek a lehető leghatékonyabban javítják a biztonságos pontszámot.
 
   ![biztonságos pontszám](./media/security-center-secure-score/secure-score-dashboard.png)

3. Az alábbi táblázatban megtekintheti az egyes előfizetéseket, valamint a teljes biztonságos pontszámot.

   > [!NOTE]
   > Az egyes előfizetések biztonsági pontszámának összege nem egyenlő a teljes biztonsági pontszámmal. A biztonsági pontszám a kifogástalan állapotú erőforrások és a teljes erőforrások javaslatonkénti arányán alapuló számítás, nem pedig az előfizetések biztonsági pontszámainak összege. 
   >
4. Kattintson a **javaslatok megtekintése** lehetőségre, és tekintse meg az előfizetésre vonatkozó javaslatokat, amelyekkel javíthatja a biztonságos pontszámot.
4. A javaslatok listájában láthatja, hogy minden javaslathoz van egy oszlop, amely a **biztonságos pontszám hatását**jelöli. Ez a szám azt jelzi, hogy a teljes biztonsági pontszám milyen mértékben javul, ha követi a javaslatokat. Például az alábbi képernyőn, ha elhárítja a biztonsági **réseket a tároló biztonsági konfigurációjában**, akkor a biztonságos pontszám 35 ponttal nő.

   ![biztonságos pontszám](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Egyéni biztonsági pontszám

Emellett az egyes biztonsági pontszámok megtekintéséhez az egyes javaslatok panelen is megtalálhatja ezeket.  

A **javaslat biztonsági pontszáma** a kifogástalan állapotú erőforrások és a teljes erőforrások arányán alapuló számítás. Ha a kifogástalan állapotú erőforrások száma megegyezik az erőforrások teljes számával, a javaslat az 50 pontot, a maximális biztonsági pontszámot kapja. A biztonsági pontszám maximális pontszámhoz való közelítése érdekében javítsa ki a sérült erőforrásokat az alábbi javaslatok szerint.

A **Javaslat hatása** megmutatja, milyen mértékben javítja a javaslat lépéseinek alkalmazása a biztonsági pontszámot. Például ha a biztonsági pontszám 42, és a **Javaslat hatása** +3, akkor a pontszám a javaslat lépéseinek végrehajtása után 45-re emelkedik.

A javaslat azt mutatja, hogy a számítási feladatok milyen veszélyforrásokkal vannak kitéve, ha a Szervizelési lépések nem teljesülnek.

![egyedi javaslat – biztonságos pontszám](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan javíthatja biztonsági állapotát a Azure Security Center **biztonságos pontszámának** használatával. A Security Centerról további információt a következő témakörben talál:

* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.


