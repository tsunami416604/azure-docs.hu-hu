---
title: Az Azure Security Center javaslatainak használata a biztonság fokozásához | Microsoft dokumentumok
description: " Ismerje meg, hogyan használhatja a biztonsági szabályzatokat és javaslatokat az Azure Security Centerben a biztonsági támadások csökkentése érdekében. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603279"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Az Azure Security Center javaslatainak használata a biztonság növelése érdekében
Csökkentheti egy jelentős biztonsági esemény esélyét egy biztonsági szabályzat konfigurálásával, majd az Azure Security Center által biztosított javaslatok megvalósításával. Ez a cikk bemutatja, hogyan használhatja a biztonsági házirendeket és a Security Center javaslatait a biztonsági támadások csökkentéséhez. 

A Security Center automatikusan folyamatos vizsgálatokat futtat az Azure-erőforrások biztonsági állapotának elemzéséhez. Amikor a Security Center potenciális biztonsági réseket azonosít, javaslatokat készít, amelyek végigvezetik a szükséges biztonsági vezérlők konfigurálásának folyamatán. A Security Center 24 órán belül frissíti a javaslatait, a következő kivételekkel:

- Az operációs rendszer biztonsági konfigurációs javaslatai 48 órán belül frissülnek
- A végpontvédelem vel kapcsolatos javaslatok 8 órán belül frissülnek

## <a name="scenario"></a>Forgatókönyv
Ez a forgatókönyv bemutatja, hogyan használhatja a Security Centert a biztonsági incidensek esélyének csökkentésére a Biztonsági központ ajánlásainak figyelésével és a műveletek műveletével. A forgatókönyv a fiktív contoso vállalatot és a Biztonsági központ tervezési és üzemeltetési útmutatójában bemutatott [szerepköröket](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)használja. Ebben az esetben a következő személyiségek szerepkörére összpontosítunk:

![Forgatókönyv-szerepkörök](./media/security-center-using-recommendations/scenario-roles.png)

A Contoso a közelmúltban áttelepítette a helyszíni erőforrásaik egy részét az Azure-ba. A Contoso meg szeretné védeni az erőforrásait, és csökkenteni szeretné az erőforrások biztonsági rését a felhőben.

## <a name="use-azure-security-center"></a>Az Azure Security Center használata
David, a Contoso informatikai biztonság, már úgy döntött, hogy a contoso-i előfizetések az Azure Security Center a biztonsági rések megelőzése és észlelése. 

A Security Center automatikusan elemzi a Contoso Azure-erőforrásainak biztonsági állapotát, és alkalmazza az alapértelmezett biztonsági szabályzatokat. Amikor a Security Center potenciális biztonsági réseket azonosít, **javaslatokat** hoz létre a biztonsági házirendben beállított vezérlők alapján. 

David az Azure Security standard szintjét futtatja az összes előfizetésükön keresztül, hogy megkapja a rendelkezésre álló javaslatok és biztonsági funkciók teljes csomagját. Jeff is bedeszkázminden meglévő helyszíni kiszolgálók, amelyek még nem telepítették át a felhőbe, hogy azok kihasználják a Security Center hibrid támogatást a [Windows](quick-onboard-windows-computer.md) és [Linux](quick-onboard-linux-computer.md) kiszolgálókon.

Jeff egy felhőalapú számítási feladatok tulajdonosa. Jeff felelős a Contoso biztonsági szabályzatának megfelelő biztonsági ellenőrzések alkalmazásáért. 

Jeff a következő feladatokat hajtja végre:

- A Security Center által biztosított biztonsági javaslatok figyelése
- Értékelje ki a biztonsági javaslatokat, és döntse el, hogy alkalmazzák-e vagy elutasítják-e a javaslatokat.
- Biztonsági javaslatok alkalmazása

### <a name="remediate-threats-using-recommendations"></a>A fenyegetések elhárítása ajánlásokkal
A napi figyelési tevékenységek részeként Jeff bejelentkezik az Azure-ba, és megnyitja a Security Centert. 

1. Jeff kiválasztja a számítási feladatok előfizetéseit.

2. Jeff ellenőrzi a **biztonságos pontszám,** hogy egy átfogó képet, hogy mennyire biztonságos az előfizetések, és látja, hogy a pontszám 548.

3. Jeffnek el kell döntenie, hogy melyik ajánlást kezelje először. Tehát Jeff rákattint Secure Score és elkezdi kezelni ajánlások alapján, hogy mennyire javítja a [Secure Score hatása](security-center-secure-score.md).

4. Mivel Jeff sok csatlakoztatott virtuális gépekkel és kiszolgálókkal rendelkezik, Jeff úgy dönt, hogy a számítási és **az alkalmazásokra**összpontosít.

5. Amikor Jeff a **Számítási és az alkalmazások**elemre kattint, megjelenik a javaslatok listája, és a Biztonságos pontszám hatásnak megfelelően kezeli őket.

6. Jeff számos internetes rendszerrel szembesülő virtuális géptel rendelkezik, és mivel a portjaik elérhetővé vannak téve, aggódnak, hogy a támadó átveheti az irányítást a kiszolgálók felett. Így Jeff úgy dönt, hogy használja [**just-in-time VM hozzáférés**](security-center-just-in-time.md).

Jeff továbbra is halad át a magas prioritású és közepes prioritású ajánlásokat, és döntéseket hoz a végrehajtásról. Minden egyes javaslat, Jeff megvizsgálja a biztonsági központ által biztosított részletes információkat, hogy megértse, mely erőforrások at érinti, mi a biztonságos pontszám hatása, mit jelent az egyes javaslatok és javítási lépéseket, hogyan lehet enyhíteni az egyes kérdések.

## <a name="conclusion"></a>Összegzés
A Security Center ben található figyelési javaslatok segítségével megszüntetheti a biztonsági réseket a támadás előtt. Javaslatok javításakor a biztonságos pontszám és a számítási feladatok biztonsági helyzete javul. A Security Center automatikusan felderíti az ön által telepített új erőforrásokat, felméri azokat a biztonsági házirend alapján, és új javaslatokat tesz azok védelmére.


## <a name="next-steps"></a>További lépések
Győződjön meg arról, hogy rendelkezik egy figyelési folyamat, amelyben rendszeresen ellenőrizze a javaslatokat a Security Center, így győződjön meg arról, hogy az erőforrások biztonsága az idő múlásával.

Ez a forgatókönyv bemutatja, hogyan használhatja a biztonsági házirendeket és a Security Center javaslatait a biztonsági támadások csökkentése érdekében.

Ismerje meg, hogyan reagálhat a fenyegetésekre a [biztonsági riasztások kezelésével és az azokra való reagálással.](security-center-managing-and-responding-alerts.md)
