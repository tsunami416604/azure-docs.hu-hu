---
title: Az Azure Security Center pontszám biztonságos |} A Microsoft Docs
description: " Rangsorolja a biztonsági javaslatok az Azure Security Centerben a biztonságos pontszám használatával. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: v-mohabe
ms.openlocfilehash: c23c9a2b9af1947c397b96431ae3c3bcd0e30aaa
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968300"
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
   - A **teljes biztonsági pontszám** a szabályzatonkénti és kiválasztott előfizetésenkénti pontszámot jelöli.
   - **Pontszám biztonságos kategória szerint** azt mutatja be, mely erőforrásokat a legtöbb figyelmet igénylő
   - **Leggyakoribb ajánlásokat, biztonságos pontszám hatás** biztosít, amely a biztonságos pontszám javulni fog a legtöbb, ha azok végrehajtása a javaslatok listája.
 
   ![biztonságos pontszám](./media/security-center-secure-score/secure-score-dashboard.png)

3. Az alábbi táblázat az egyes láthatja minden egyes előfizetés és a teljes biztonságos pontszámot.

   > [!NOTE]
   > Az egyes előfizetések biztonsági pontszámának összege nem egyenlő a teljes biztonsági pontszámmal. A biztonsági pontszám a kifogástalan állapotú erőforrások és a teljes erőforrások javaslatonkénti arányán alapuló számítás, nem pedig az előfizetések biztonsági pontszámainak összege. 
   >
4. Kattintson a **ajánlás megtekintése** , tekintse meg ezt az előfizetést, amely akkor javíthatja a biztonságos pontszám javítására vonatkozó javaslatok láthatók.
4. A javaslatok listája, láthatja, hogy az egyes javaslatok van egy oszlop, amely jelöli az **pontszám hatás biztonságos**. A szám jelöli, mennyi a teljes biztonságos pontszám javítja, ha követi az ajánlások. Például az a képernyő, ha az alábbi, **tároló biztonsági konfigurációk a biztonsági rések**, a biztonságos pontszám 35 pontok nő.

   ![biztonságos pontszám](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Az egyes biztonságos pontszám

Emellett egyes biztonságos pontszámok megtekintéséhez annak belül az egyes javaslatok panel.  

A **javaslat biztonsági pontszáma** a kifogástalan állapotú erőforrások és a teljes erőforrások arányán alapuló számítás. Ha a kifogástalan állapotú erőforrások száma megegyezik az erőforrások teljes számával, a javaslat az 50 pontot, a maximális biztonsági pontszámot kapja. A biztonsági pontszám maximális pontszámhoz való közelítése érdekében javítsa ki a sérült erőforrásokat az alábbi javaslatok szerint.

A **Javaslat hatása** megmutatja, milyen mértékben javítja a javaslat lépéseinek alkalmazása a biztonsági pontszámot. Például ha a biztonsági pontszám 42, és a **Javaslat hatása** +3, akkor a pontszám a javaslat lépéseinek végrehajtása után 45-re emelkedik.

A javaslat azt mutatja, hogy milyen fenyegetéseket a számítási feladat közvetlenül csatlakozik, ha a rendszer nem hajtja végre a hibaelhárítási lépéseket.

![az egyes javaslatok biztonságos pontszám](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta fejlesztéséhez, a biztonsági rendszer kialakításához használatával **biztonságos pontszám** az Azure Security Centerben. A Security Centerrel kapcsolatos további tudnivalókért lásd:

* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.


