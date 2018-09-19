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
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: fc521db9ad753c4162b65abfd2f9f23c318fa994
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131321"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Az Azure Security Centerben a biztonságos pontszám javítása


Igen sok szolgáltatással biztonsági előnyöket kínál meglehetősen gyakran nehéz tudni, hogy milyen lépéseket kell tennie, hogy az első biztonságát, és a számítási feladat megerősítése. Az Azure Security Center biztonságos pontszám ellenőrzi a biztonsági javaslatok láthatók, és priorizálja őket, hogy tudja, melyik javaslatok végrehajtásához először segít megtalálni a legsúlyosabb biztonsági réseket, így rangsorolhatja a vizsgálatot. Biztonságos pontszám, egy mérték eszköz, amely segít felvértezni a biztonság, a munkaterhelés biztonságos eléréséhez.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]


![Biztonságos pontszám irányítópult](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Pontszám számítási biztonságossá tétele

A Security Center utánozza biztonsági elemző, a biztonsági javaslatok áttekintése és annak megállapításához, hogy kulcsfontosságú javaslatot speciális algoritmusok alkalmazásával a munkát.
Az Azure Security center folyamatosan ellenőrzi, hogy aktív javaslatok és a biztonságos pontszám, ezek alapján számítja ki, a pontszám, ajánlás, a súlyosság és a biztonság ajánlott eljárásokat, amelyek hatással lesznek a leginkább a számítási feladatok biztonsági van származtatva.

A **pontszám biztonságos** a számítás, a kifogástalan állapotú erőforrások és az összes erőforrás aránya alapján. Kifogástalan állapotú erőforrások száma megegyezik az erőforrások teljes száma, ha a maximális biztonságos pontszám 50 kap. Próbálja a biztonságos pontszám közelebb a maximális pontszámot, javítsa ki a nem megfelelő állapotú erőforrások a javaslatok követésével.

A Security Center emellett lehetőséget biztosít egy biztonságos Overall score. 

**A teljes biztonságos pontszám** összes javaslat felgyűlnek van. Az előfizetések vagy a felügyeleti csoportokhoz, attól függően, hogy válassza ki a teljes biztonságos pontszám tekintheti meg. A pontszám a kiválasztott előfizetésben és ezen előfizetés az aktív javaslatok alapján változhat.

 

Ellenőrizze, hogy melyik javaslatok láthatók, a legnagyobb hatással van a biztonságos pontszám, a Security Center irányítópultján tekintheti meg a 3 leggyakoribb tevékenységét leginkább befolyásoló javaslatok, vagy a javaslatok a javaslatok listája panel használatával rendezheti a **biztonságos pontszám hatás** oszlop.


A teljes biztonságos pontszám megtekintése:

1. Az Azure irányítópultján kattintson **a Security Center** majd **javaslatok**.
2. Tetején megjelenik a biztonságos pontszám, és a szabályzatok kiválasztott előfizetésenként kiszolgálónként pontszám jelöli. 
2. Az alábbi táblázat, amely felsorolja a javaslatok, láthatja, hogy az egyes javaslatok van egy oszlop, amely jelöli az **pontszám hatás biztonságos**. A szám jelöli, mennyi a teljes biztonságos pontszám javítja, ha követi az ajánlások. Például az a képernyő, ha az alábbi, **tároló biztonsági konfigurációk a biztonsági rések**, a biztonságos pontszám 35 pontok nő.

   ![biztonságos pontszám](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Az egyes biztonságos pontszám

Emellett egyes biztonságos pontszámok megtekintéséhez annak belül az egyes javaslatok panel.  

A **javaslat biztonságos pontszám** a számítás, a kifogástalan állapotú erőforrások és az összes erőforrás aránya alapján. Kifogástalan állapotú erőforrások száma megegyezik az erőforrások teljes száma, ha az ajánlás a maximális biztonságos pontszámot kap. Próbálja a biztonságos pontszám közelebb a maximális pontszámot, javítsa ki a nem megfelelő állapotú erőforrások az alábbi hibaelhárítási lépéseket.

A **javaslatok hatás** jelzi, hogy mekkora a biztonságos pontszám javulni fog, ha alkalmazza a javasolt következő lépésre. Például ha a biztonságos pontszám 42 és a **javaslatok hatás** + 3, ha a javaslat ismertetett lépéseket hajtja végre a biztonságos pontszám javul a 45 válik.

A javaslat azt mutatja, hogy milyen fenyegetéseket a számítási feladat közvetlenül csatlakozik, ha a rendszer nem hajtja végre a hibaelhárítási lépéseket.

![az egyes javaslatok biztonságos pontszám](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta fejlesztéséhez, a biztonsági rendszer kialakításához használatával **biztonságos pontszám** az Azure Security Centerben. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.


