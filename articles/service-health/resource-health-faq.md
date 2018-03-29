---
title: Az Azure Resource Health – gyakori kérdések |} Microsoft Docs
description: Azure-erőforrás állapotának áttekintése
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: BernardoAMunoz
ms.openlocfilehash: 2a0758f5db381f721120cbc1c018a491c018aa8d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-resource-health-faq"></a>Az Azure Resource Health – gyakori kérdések
További tudnivalók az Azure Resource Health kapcsolatos gyakori kérdésekre adott válaszok.

## <a name="what-is-azure-resource-health"></a>Mi az Azure-erőforrás állapota?
A Resource Health segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-ral kapcsolatos probléma hatással van az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.  

## <a name="what-is-the-resource-health-intended-for"></a>Mi szól a a Resource Health?
Amennyiben az erőforrás problémát észlelt, erőforrás állapota segítségével diagnosztizálhatja az alapvető okát. Biztosít segítenek mérsékelni a problémát, és a technikai támogatási szolgálathoz, ha az Azure-szolgáltatásokkal kapcsolatos problémákról további segítségre van szüksége.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Ellenőrzi, milyen állapotfigyelő végzi az erőforrás állapota?
Erőforrás állapota alapján különböző ellenőrzést hajt végre a [erőforrástípus](resource-health-checks-resource-types.md). Az ellenőrzések tervezett három típusú problémákat végrehajtásához: 
- Nem tervezett események, például egy nem várt állomás újraindítás
- Tervezett események, például a gazda operációs rendszer számára ütemezett frissítések
- Akkor kiváltott események végrehajtott műveletek, például egy felhasználó egy virtuális gép újraindítása

## <a name="what-does-each-of-the-health-status-mean"></a>Mit jelent egyes állapotát?
Három különböző egészségügyi állapot van:
- Rendelkezésre álló: Nincs olyan ismert probléma az Azure platform, amely tudta mely negatív hatással lehet az erőforrás
- Nem érhető el: Erőforrás állapota azt észlelte, hogy az erőforrás van érintő problémák
- Ismeretlen: Erőforrás állapota nem sikerült megállapítani az erőforrás állapotát, mert az információk fogadása leállt. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Mit jelent az ismeretlen állapot? Valamilyen probléma van vele a erőforrás?
Ha az erőforrás állapota nem fogadja az adott erőforrásra vonatkozó adatokat állapota ismeretlen értéke. Ez az állapot nem végleges feltüntetése azokban az esetekben, ahol tapasztal, az erőforrás állapota azt jelentheti, egy Azure probléma lépett fel.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hogyan tudjanak segítséget kérni, amely nem érhető el erőforrás?
A Resource Health paneljéről támogatási kérelmet elküldheti. Nem kell kérést nyithat, ha az erőforrás nem érhető el a Microsoft támogatási megállapodás mert platform események.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Erőforrás állapota platform problémák és valami tettem cased elérhetetlensége megkülönböztetni?
Igen, ha egy erőforrás nem érhető el, a erőforrás állapota azonosítja az alapvető ok belül e kategóriák közül: 
-   Felhasználó által kezdeményezett művelet
-   Tervezett esemény 
-   Nem tervezett esemény

A portálon felhasználó által kezdeményezett műveletek jelennek meg a kék értesítési ikon használatával tervezett és nem tervezett eseményeket használ, piros figyelmeztető ikon látható. További részletek szerepelnek a [erőforrás állapota – áttekintés](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>I integrálható erőforrás állapota a Hálózatfigyelő eszközök?
Erőforrás állapota egy olyan szolgáltatás, célja, hogy diagnosztizálni és megoldani az Azure szolgáltatásokkal kapcsolatos problémákról, amely hatással lehet az erőforrásokat. A Resource Health API segítségével programozott módon beszerzése az állapotát, de javasolt mérőszámok segítségével figyelheti az erőforrások. Ha problémát észlel, Resource Health segít meghatározni, az alapvető ok, és végigvezeti a műveletek hozzájuk. Látogasson el [Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) tudhat meg többet használatát metrikák az erőforrások kereséséhez.

## <a name="where-do-i-find-resource-health"></a>Hol található az erőforrás állapota?
Az Azure-portálon való bejelentkezést követően többféleképpen is Resource Health végezheti el:
- Nyissa meg az erőforrás. Válassza ki a bal oldali navigációs **erőforrás állapota**
- Nyissa meg az Azure-figyelő paneljét.  Válassza ki a bal oldali navigációs **erőforrás állapota**.
- Nyissa meg a **súgó + támogatás** panelen jelölje ki a kérdéses a portál jobb felső sarkában, és jelölje be **súgó + támogatás**. Ha a panel nyit, válassza ki **erőforrás állapota**

A Resource Health API segítségével beolvasni az erőforrások állapotával kapcsolatos információkat.

## <a name="is-resource-health-available-for-all-resource-types"></a>Az erőforrás állapota minden erőforrástípus esetén érhető el?
Állapot-ellenőrzési eredményeire és a Resource Health támogatott típusú erőforrások listája található [Itt](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Mit tegyek, ha az erőforrás elérhető láthatók, de I úgy érzi, nem?"
Egy erőforrás állapotának ellenőrzésekor a állapot szerinti kattinthat **helytelen állapot jelentést**. Mielőtt elküldi a jelentést, lehetősége van a miért úgy gondolja, a jelenlegi állapot érvénytelen. további részleteket biztosít.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Érhető el erőforrás állapota minden Azure-régió? 
Erőforrás állapota érhető el az összes Azure geos, kivéve az alábbi területek között:
- USA-beli államigazgatás – Virginia
- USA-beli államigazgatás – Iowa
- US DoD – Kelet
- US DoD – Középső régió
- Közép-Németország
- Északkelet-Németország

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Miben különbözik Resource Health Azure állapot vagy a szolgáltatás irányítópult?
A Resource Health által biztosított információk is pontosabb, mint mi az Azure állapot vagy a szolgáltatás irányítópult által biztosított.

Mivel [Azure állapot](https://status.azure.com) és a szolgáltatás irányítópult tájékoztatnak, szolgáltatásokkal kapcsolatos problémákról, amelyek hatással vannak a felhasználók (például egy Azure-régió) széles körét, erőforrás állapota kapcsolódik csak a részletesebb események közzététele a adott erőforrás. Például ha egy gazdagép váratlanul újraindul, a Resource Health csak ezek az ügyfelek amelynek virtuális gépek az adott gazdagépen futó riasztást küld.

Fontos, hogy figyelje meg, hogy biztosítja, hogy az erőforrások érintő események teljes láthatóságát, erőforrás állapota is Felfed, a szolgáltatás irányítópult közzé eseményeket.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Kell aktiválni az erőforrás állapota az egyes erőforrások?
Nem, állapottal kapcsolatos adatok érhető el az összes erőforrástípus Resource Health keresztül érhető el. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Szükséges a Resource Health engedélyezése a szervezetem számára?
Nem.  Az Azure Resource Health bármely telepítési követelmények nélkül az Azure-portálon belül érhető el.

## <a name="is-resource-health-available-free-of-charge"></a>Érhető el erőforrás állapota ingyenesen?
Igen.  Az Azure Resource Health ingyenesen elérhető.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Mik a javaslatok, amely erőforrás állapota?
Az állapot alapján, erőforrás állapota nyújt javaslatokat azzal a céllal, idejének csökkentése hibaelhárítási felhasznált. A rendelkezésre álló erőforrások a javaslatok fókusz a leggyakoribb problémák ügyfelek megoldására tapasztal. Ha az erőforrás nem érhető el az Azure nem tervezett esemény miatt, a fókusz segít alatt és a helyreállítási folyamat után lesz. 

## <a name="next-steps"></a>További lépések

További tudnivalók az erőforrás állapota:
-  [Az Azure Resource Health áttekintése](Resource-health-overview.md)
-  [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapot-ellenőrzések](resource-health-checks-resource-types.md)
