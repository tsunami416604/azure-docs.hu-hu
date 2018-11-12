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
ms.date: 11/5/2018
ms.author: rkarlin
ms.openlocfilehash: 3a377441758fcd7dd91deefb5cae91579e881498
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007051"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Az Azure Security Centerben a biztonságos pontszám javítása


Igen sok szolgáltatással biztonsági előnyöket kínál meglehetősen gyakran nehéz tudni, hogy milyen lépéseket kell tennie, hogy az első biztonságát, és a számítási feladat megerősítése. Az Azure biztonságos pontszám ellenőrzi a biztonsági javaslatok és, rangsorolja őket, így megtudhatja, mely ajánlások végrehajtásához először. Ezáltal könnyebben megtalálhat a legsúlyosabb biztonsági réseket, így rangsorolhatja a vizsgálatot. Biztonságos pontszám egy olyan eszköz, felmérheti a számítási feladatok biztonsági helyzetét.

![Biztonságos pontszám irányítópult](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Pontszám számítási biztonságossá tétele

A Security Center a biztonsági elemzőknek szét, a biztonsági javaslatok áttekintéséhez, és fejlett algoritmusok alkalmazásával annak megállapításához, hogy kulcsfontosságú javaslatot munkáját utánozza.
Az Azure Security center folyamatosan ellenőrzi, hogy aktív javaslatok és a biztonságos pontszám, ezek alapján számítja ki, az ajánlás a pontszám a súlyosság és az ajánlott biztonsági eljárások, amelyek hatással lesznek a leginkább a számítási feladatok biztonsági van származtatva.

A Security Center is biztosít egy **teljes biztonságos pontszám**. 

**A teljes biztonságos pontszám** van a javaslat-pontszámok felgyűlnek. Az előfizetések vagy a felügyeleti csoportokhoz, attól függően, hogy válassza ki a teljes biztonságos pontszám tekintheti meg. A pontszám a kiválasztott előfizetésben és ezen előfizetés az aktív javaslatok alapján változhat.

 
Ellenőrizze, hogy mely ajánlások legtöbb befolyásolhatja a biztonságos pontszámát, megtekintheti az első három tevékenységét leginkább befolyásoló javaslatokat a Security Center irányítópultján, vagy a javaslatok a javaslatok listája panel használatával rendezheti a **biztonságos pontszám hatás** oszlop.


A teljes biztonságos pontszám megtekintése:

1. Az Azure irányítópultján kattintson **a Security Center** majd **javaslatok**.
2. Tetején megjelenik a biztonságos pontszám, és a szabályzatok kiválasztott előfizetésenként kiszolgálónként pontszám jelöli. 
2. Az alábbi táblázat, amely felsorolja a javaslatok, láthatja, hogy az egyes javaslatok van egy oszlop, amely jelöli az **pontszám hatás biztonságos**. A szám jelöli, mennyi a teljes biztonságos pontszám javítja, ha követi az ajánlások. Például az a képernyő, ha az alábbi, **tároló biztonsági konfigurációk a biztonsági rések**, a biztonságos pontszám 35 pontok nő.

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


