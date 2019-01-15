---
title: Az Azure Security Center pontszám biztonságos |} A Microsoft Docs
description: " Rangsorolja a biztonsági javaslatok az Azure Security Centerben a biztonságos pontszám használatával. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 4bc426e291a47ae42e7628ad911e2960e654819f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261298"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Az Azure Security Centerben a biztonságos pontszám javítása


Igen sok szolgáltatással biztonsági előnyöket kínál meglehetősen gyakran nehéz tudni, hogy milyen lépéseket kell tennie, hogy az első biztonságát, és a számítási feladat megerősítése. Az Azure biztonságos pontszám ellenőrzi a biztonsági javaslatok és, rangsorolja őket, így megtudhatja, mely ajánlások végrehajtásához először. Ezáltal könnyebben megtalálhat a legsúlyosabb biztonsági réseket, így rangsorolhatja a vizsgálatot. Biztonságos pontszám egy olyan eszköz, felmérheti a számítási feladatok biztonsági helyzetét.

## <a name="secure-score-calculation"></a>Pontszám számítási biztonságossá tétele

A Security Center a biztonsági elemzőknek szét, a biztonsági javaslatok áttekintéséhez, és fejlett algoritmusok alkalmazásával annak megállapításához, hogy kulcsfontosságú javaslatot munkáját utánozza.
Az Azure Security center folyamatosan ellenőrzi, hogy aktív javaslatok és a biztonságos pontszám, ezek alapján számítja ki, az ajánlás a pontszám a súlyosság és az ajánlott biztonsági eljárások, amelyek hatással lesznek a leginkább a számítási feladatok biztonsági van származtatva.

A Security Center is biztosít egy **teljes biztonságos pontszám**. 

**A teljes biztonságos pontszám** van a javaslat-pontszámok felgyűlnek. Az előfizetések vagy a felügyeleti csoportokhoz, attól függően, hogy válassza ki a teljes biztonságos pontszám tekintheti meg. A pontszám a kiválasztott előfizetésben és ezen előfizetés az aktív javaslatok alapján változhat.

 
Ellenőrizze, hogy mely ajánlások legtöbb befolyásolhatja a biztonságos pontszámát, megtekintheti az első három tevékenységét leginkább befolyásoló javaslatokat a Security Center irányítópultján, vagy a javaslatok a javaslatok listája panel használatával rendezheti a **biztonságos pontszám hatás** oszlop.


A teljes biztonságos pontszám megtekintése:

1. Az Azure irányítópultján kattintson **a Security Center** majd **biztonságos pontszám**.
2. Tetején látható biztonságos pontozása emeli ki:
   - A **teljes biztonságos pontszám** jelenti. a szabályzatok kiválasztott előfizetésenként kiszolgálónként pontszám
   - **Pontszám biztonságos kategória szerint** azt mutatja be, mely erőforrásokat a legtöbb figyelmet igénylő
   - **Leggyakoribb ajánlásokat, biztonságos pontszám hatás** biztosít, amely a biztonságos pontszám javulni fog a legtöbb, ha őket kijavítani a javaslatok listája.
 
   ![biztonságos pontszám](./media/security-center-secure-score/secure-score-dashboard.png)

3. Az alábbi táblázat az egyes láthatja minden egyes előfizetés és a teljes biztonságos pontszámot.

   > [!NOTE]
   > A biztonságos pontszám előfizetések összegét nem egyenlő a teljes biztonságos pontszámot. A biztonságos pontszám, a kifogástalan állapotú erőforrások és a teljes erőforrások száma javaslat, nem biztonságos pontszámokat több előfizetéshez összege aránya alapján. 
   >
4. Kattintson a **ajánlás megtekintése** , tekintse meg ezt az előfizetést, amely akkor javíthatja a biztonságos pontszám javítására vonatkozó javaslatok láthatók.
4. A javaslatok listája, láthatja, hogy az egyes javaslatok van egy oszlop, amely jelöli az **pontszám hatás biztonságos**. A szám jelöli, mennyi a teljes biztonságos pontszám javítja, ha követi az ajánlások. Például az a képernyő, ha az alábbi, **tároló biztonsági konfigurációk a biztonsági rések**, a biztonságos pontszám 35 pontok nő.

   ![biztonságos pontszám](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Az egyes biztonságos pontszám

Emellett egyes biztonságos pontszámok megtekintéséhez annak belül az egyes javaslatok panel.  

A **javaslat biztonságos pontszám** a számítás, a kifogástalan állapotú erőforrások és az összes erőforrás aránya alapján. Kifogástalan állapotú erőforrások száma megegyezik az erőforrások teljes száma, ha az ajánlott érték az 50 maximális biztonságos pontszámot kap. Próbálja a biztonságos pontszám közelebb a maximális pontszámot, javítsa ki a nem megfelelő állapotú erőforrások a javaslatok követésével.

A **javaslatok hatás** lehetővé teszi, hogy tudja, milyen mértékben javítja a a biztonságos pontszám Ha alkalmazza a javasolt következő lépésre. Például, ha a biztonságos pontszám: 42 és a **javaslatok hatás** + 3, akkor a pontszám 45 válik a javaslat lépéseinek végrehajtása javítása.

A javaslat azt mutatja, hogy milyen fenyegetéseket a számítási feladat közvetlenül csatlakozik, ha a rendszer nem hajtja végre a hibaelhárítási lépéseket.

![az egyes javaslatok biztonságos pontszám](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta fejlesztéséhez, a biztonsági rendszer kialakításához használatával **biztonságos pontszám** az Azure Security Centerben. A Security Centerrel kapcsolatos további tudnivalókért lásd:

* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.


