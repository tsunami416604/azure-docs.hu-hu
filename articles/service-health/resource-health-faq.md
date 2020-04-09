---
title: Az Azure-erőforrások állapotáról – gyakori kérdések
description: Az Azure-erőforrások állapotának áttekintése
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 7459a29dca01dc186d75b4545f89068569975607
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985031"
---
# <a name="azure-resource-health-faq"></a>Az Azure-erőforrások állapotáról – gyakori kérdések
Ismerje meg az Azure Resource Health szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat.

## <a name="what-is-azure-resource-health"></a>Mi az Azure Resource Health?
A Resource Health segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-ral kapcsolatos probléma hatással van az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.  

## <a name="what-is-the-resource-health-intended-for"></a>Mire szolgál az erőforrás-állapot?
Ha egy erőforrással kapcsolatos problémát észlelt, az Erőforrás állapota segíthet a kiváltó ok diagnosztizálásában. Segítséget nyújt a probléma és a technikai támogatás csökkentéséhez, ha további segítségre van szüksége az Azure-szolgáltatásokkal kapcsolatos problémákkal kapcsolatban.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Milyen állapot-ellenőrzéseket végez az Erőforrás állapota?
Az erőforrás állapota különböző ellenőrzéseket végez az [erőforrás típusának](resource-health-checks-resource-types.md)alapján. Ezek az ellenőrzések három féle probléma végrehajtására szolgálnak: 
- Nem tervezett események, például váratlan állomás újraindítása
- Tervezett események, például ütemezett gazdaoperációs rendszer frissítései
- Felhasználói műveletek által kiváltott események, például egy felhasználó újraindítása egy virtuális gépet

## <a name="what-does-each-of-the-health-status-mean"></a>Mit jelentenek az egyes egészségi állapotok?
Három különböző egészségi állapot létezik:
- Elérhető: Nincs olyan ismert probléma az Azure platformon, amely hatással lehet erre az erőforrásra
- Nem érhető el: Az erőforrás állapota olyan problémákat észlelt, amelyek hatással vannak az erőforrásra
- Ismeretlen: Az erőforrások állapota nem tudja meghatározni egy erőforrás állapotát, mert nem kapott információt róla. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Mit jelent az ismeretlen állapot? Valami baj van az erőforrásommal?
Az állapot ismeretlen, ha az Erőforrás állapota leállítja egy adott erőforrásadatainak fogadását. Bár ez az állapot nem végleges jelzi az erőforrás állapotát, azokban az esetekben, ahol problémákat tapasztal, azt jelezheti, hogy azure-probléma van.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hogyan kaphatok segítséget egy nem elérhető erőforráshoz?
Támogatási kérelmet küldhet a Resource Health panelről. Nincs szükség támogatási szerződésre a Microsofttal ahhoz, hogy megnyisson egy kérelmet, ha az erőforrás platformesemények miatt nem érhető el.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Az erőforrás-állapot különbséget tesz a platformproblémák által okozott elérhetetlenség és az általam okozott problémák között?
Igen, ha egy erőforrás nem érhető el, az Erőforrás állapota az alábbi kategóriák egyikén azonosítja a kiváltó okot: 
-   Felhasználó által kezdeményezett művelet
-   Tervezett esemény 
-   Nem tervezett esemény

A portálon a felhasználó által kezdeményezett műveletek egy kék értesítési ikonnal jelennek meg, míg a tervezett és nem tervezett események piros figyelmeztető ikonnal jelennek meg. További részletek az [Erőforrás állapotának áttekintése](Resource-health-overview.md)című témakörben találhatók.  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Integrálhatom az Erőforrás-állapot ot a figyelési eszközeimmel?
Az erőforrás-állapot [előzetes verziójú támogatást](resource-health-alert-arm-template-guide.md) nyújt a tevékenységnapló-alapú riasztásokhoz. A tevékenységnapló-riasztások [műveletcsoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) használatával értesítik a felhasználókat arról, hogy riasztás történt. A műveletcsoportok számos értesítési csatornát támogatnak, például az e-mail, az SMS, a webhook és az ITSM-műveleteket.

## <a name="where-do-i-find-resource-health"></a>Hol találom az Erőforrás-állapotot?
Miután bejelentkezett az Azure Portalra, többféleképpen is elérheti az Erőforrás-állapotot:
- Keresse meg az erőforrást. A bal oldali navigációs ban válassza **az Erőforrás állapota lehetőséget.**
- Nyissa meg az Azure Service Health panelt.  A bal oldali navigációsban válassza az **Erőforrás állapota**lehetőséget.
- Nyissa meg a **Súgó + Támogatás** panelt úgy, hogy a portál jobb felső sarkában lévő kérdőjelet, majd a Súgó + Támogatás lehetőséget **választja.** A panel megnyitása után válassza **az Erőforrás állapota** lehetőséget

A Resource Health API-t is használhatja az erőforrások állapotával kapcsolatos információk megszerzéséhez.

## <a name="is-resource-health-available-for-all-resource-types"></a>Az erőforrás-állapot minden erőforrástípushoz elérhető?
Az Erőforrás állapota által támogatott állapotellenőrzések és erőforrástípusok listája [itt](resource-health-checks-resource-types.md)található.

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Mi a teendő, ha az erőforrásom elérhető, de úgy gondolom, hogy nem?"
Egy erőforrás állapotának ellenőrzésekor közvetlenül az állapot alatt a Helytelen állapot jelentése gombra kattintva kattintson a **Jelentés gombra.** A jelentés elküldése előtt további részleteket is megad arról, hogy miért gondolja úgy, hogy az aktuális egészségi állapot helytelen.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Az Erőforrás-állapot minden Azure-régióban elérhető? 
Az erőforrások állapota minden Azure-földrajzi területen elérhető.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Miben különbözik az Erőforrás-állapot az Azure-állapottól vagy a Service Health irányítópultjától?
A Resource Health által megadott információk pontosabbak, mint amit az Azure-állapot vagy a Service Health irányítópult biztosít.

Mivel az [Azure állapota](https://status.azure.com) és a Service Health irányítópult tájékoztatja Önt az ügyfelek széles körét (például egy Azure-régiót) érintő szolgáltatási problémákról, a Resource Health részletesebb eseményeket jelenít meg, amelyek csak az adott erőforrás szempontjából relevánsak. Ha például egy gazdagép váratlanul újraindul, a Resource Health csak azokat az ügyfeleket riasztja, akiknek a virtuális gépei az adott gazdagépen futottak.

Fontos figyelembe venni, hogy az erőforrásokat befolyásoló események teljes láthatóságának biztosítása érdekében a Resource Health a Service Health irányítópulton közzétett eseményeket is megjelen.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Minden erőforráshoz aktiválnom kell az Erőforrás-állapot ot?
Nem, az állapotra vonatkozó információk az Erőforrás-állapot on keresztül elérhető összes erőforrástípushoz elérhetők. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Szükség van a szervezet erőforrás-állapot engedélyezésére?
Nem.  Az Azure Resource Health az Azure Portalon belül, beállítási követelmények nélkül érhető el.

## <a name="is-resource-health-available-free-of-charge"></a>A Resource Health ingyenesen elérhető?
Igen.  Az Azure Resource Health ingyenes.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Milyen javaslatokat tesz az Erőforrás-állapot?
Az állapot alapján a Resource Health javaslatokat nyújt a hibaelhárítással töltött idő csökkentésére. A rendelkezésre álló erőforrások esetében az ajánlások arra összpontosítanak, hogyan oldható meg az ügyfelek által tapasztalt leggyakoribb problémák. Ha az erőforrás nem érhető el egy Nem tervezett Azure-esemény miatt, a hangsúly a helyreállítási folyamat alatt és után történő segítségnyújtáson lesz. 

## <a name="next-steps"></a>További lépések

További információ az erőforrás-állapotról:
-  [Az Azure Resource Health áttekintése](Resource-health-overview.md)
-  [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapotellenőrzések](resource-health-checks-resource-types.md)
