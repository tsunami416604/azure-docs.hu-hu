---
title: Az Azure Resource Health – gyakori kérdések |} A Microsoft Docs
description: Az Azure Resource Health áttekintése
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.workload: Supportability
ms.openlocfilehash: b4062b3f0bc389de4403ac81b56688508f5ea50e
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579563"
---
# <a name="azure-resource-health-faq"></a>Az Azure Resource Health – gyakori kérdések
Ismerje meg az Azure Resource Health szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat.

## <a name="what-is-azure-resource-health"></a>Mi az Azure Resource Health?
A Resource Health segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-ral kapcsolatos probléma hatással van az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.  

## <a name="what-is-the-resource-health-intended-for"></a>Mi szól a a Resource Health?
Miután erőforrás hibát észlelt, a Resource Health segíthet a probléma alapvető okát. Segítenek mérsékelni a problémát, műszaki támogatást, ha az Azure-szolgáltatási problémák további segítségre van szüksége a biztosít.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Milyen állapot-ellenőrzések hajtja végre a Resource Health?
A Resource health alapján különböző ellenőrzéseket hajtja végre a [erőforrástípus](resource-health-checks-resource-types.md). Az ellenőrzések úgy tervezték, hogy háromféle problémák megvalósításához: 
- Nem tervezett események esetén például a gazdakiszolgáló váratlan újraindítása
- Tervezett események, például az operációs rendszer ütemezett gazdagép frissítései
- Események felhasználói műveletek, például egy felhasználó egy virtuális gép újraindítása

## <a name="what-does-each-of-the-health-status-mean"></a>Mit jelent az állapot mindegyike?
Nincsenek három különböző állapot-állapotok:
- Elérhető: Nincs olyan ismert probléma az Azure platformon, amely ezt az erőforrást érintő sikerült
- Nem érhető el: A Resource health észlelt problémákat, amelyek negatív hatással vannak az erőforráshoz
- Ismeretlen: A Resource health nem is meghatározni az erőforrás állapotát, mert leállt az információk fogadására. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Mit jelent az ismeretlen állapot? Nem megfelelő erőforrással rendelkező valamit?
Ha a Resource Health nem fogadja az adott erőforrásra vonatkozó adatokat az állapot ismeretlen értéke. Bár ez az állapot nem egy végleges arra utalhat, hogy az erőforrás azokban az esetekben, ahol olyan problémákat tapasztal, állapotát jelezheti probléma van az Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hogyan kaphatok segítséget egy erőforrás, amely nem érhető el?
A Resource Health panelen egy támogatási kérést küldhet. Nyissa meg a kérést, ha az erőforrás nem érhető el egy támogatási szerződés a Microsoft nem kell mert platform eseményeket.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>A Resource Health és valami, ahogyan a platformmal kapcsolatos probléma által kisbetűsek elérhetetlensége megkülönböztetéséhez?
Igen, amikor az erőforrás nem érhető el, a Resource Health azonosítja az ezen kategóriákon belül az alapvető ok: 
-   Felhasználó által kezdeményezett művelet
-   Esemény 
-   Nem tervezett esemény

A portálon a felhasználó által kezdeményezett műveletek láthatók használatával egy kék értesítési ikon jelenik meg, amíg a tervezett és nem tervezett események piros figyelmeztető ikon jelenik meg. További részletek szerepelnek a [Resource Health áttekintése](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Integrálhatom az saját monitorozási eszközökkel a Resource Health?
A Resource health rendelkezik [támogatása előzetes](resource-health-alert-arm-template-guide.md) tevékenységnapló-riasztások alapú. Tevékenységnapló-riasztások használata [Műveletcsoportok](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups) figyelmezteti a felhasználókat, hogy egy riasztás aktiválódott-e. Műveletcsoportok különböző értesítési csatornákat, például az e-mailben, SMS, webhook és ITSM-műveletek támogatásához.

## <a name="where-do-i-find-resource-health"></a>Hol találom meg a Resource Health?
Az Azure Portalra való bejelentkezés után többféleképpen is elérheti a Resource Health:
- Keresse meg az erőforrást. A bal oldali navigációs sávján válassza **a Resource health**
- Nyissa meg az Azure Service Health panelen.  A bal oldali navigációs sávján válassza **a Resource health**.
- Nyissa meg a **súgó + támogatás** panelen jelölje ki a kérdőjel a portál jobb felső sarokban, és kiválasztja az **súgó + támogatás**. Miután megnyílik a panel, válassza ki a **a Resource health**

A Resource Health API használatával szerezze be az erőforrások állapotával kapcsolatos információkat.

## <a name="is-resource-health-available-for-all-resource-types"></a>Minden erőforrástípus esetén elérhető-e a Resource Health?
Resource Health segítségével támogatott erőforrástípusok és állapot-ellenőrzések listáját találja [Itt](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Mit tegyek, ha saját erőforrás elérhető láthatók, de nincs véleményem?"
Ha egy erőforrás állapotának ellenőrzése, az állapot szerinti kattint **helytelen állapot jelentése**. A jelentés az elküldés előtt, hogy a lehetőséget, hogy további részleteket a miért véli, hogy az aktuális állapot-ellenőrzése nem megfelelő.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Az összes Azure-régióban érhető el a Resource Health? 
A Resource health az összes Azure helyeken érhető el.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Miben különbözik a Resource Health az Azure állapota, vagy a Service Health dashboard?
A Resource Health által biztosított információkkal pontosabb, mint amit az Azure állapota, vagy a Service Health dashboard által biztosított.

Mivel a [az Azure állapota](https://status.azure.com) és a Service Health dashboard azonosítható, hogy szolgáltatási problémák, amelyek befolyásolják a különböző ügyfelek (például egy Azure-régióban), a Resource Health tesz elérhetővé, amely csak a részletesebb események a adott erőforráshoz. Például ha egy gazdagép váratlanul újraindult, a Resource Health csak ezek az ügyfelek amelynek virtuális gépek ezen a gazdagépen futó riasztást küld.

Fontos, hogy figyelje meg, hogy biztosítson teljes látható-e az erőforrásokat érintő események, a Resource Health is Felfed, a Service Health irányítópultján közzétett események.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Kell az egyes erőforrások aktiválja a Resource Health?
Nem, egészségügyi adatok érhetők el a Resource Health segítségével elérhető erőforrástípusok. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Szükségünk van a Resource Health engedélyezése a szervezetem számára?
Nem.  Az Azure Resource Health beállítása követelmények nélkül az Azure Portalon érhető el.

## <a name="is-resource-health-available-free-of-charge"></a>A Resource Health érhető el ingyenes?
Igen.  Az Azure Resource Health az ingyenesen elérhető.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Mik azok a javaslatok, amely a Resource Health biztosít?
Az állapot alapján, a Resource Health biztosít a cél az időt töltött el hibaelhárítási javaslatokat. Rendelkezésre álló erőforrásokra hogyan lehet megoldani a leggyakoribb problémák ügyfelek javaslatok koncentrálhat találkozhat. Az erőforrás nem érhető el az Azure nem tervezett esemény miatt, ha a fókusz segít alatt és a helyreállítási folyamat után lesznek. 

## <a name="next-steps"></a>További lépések

További információ a Resource Health:
-  [Az Azure Resource Health áttekintése](Resource-health-overview.md)
-  [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapot-ellenőrzések](resource-health-checks-resource-types.md)
