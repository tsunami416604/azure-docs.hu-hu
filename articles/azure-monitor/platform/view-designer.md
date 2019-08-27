---
title: Nézetek létrehozása a naplófájlok elemzéséhez Azure Monitorban | Microsoft Docs
description: A Azure Monitorban megjelenő tervező használatával létrehozhat olyan egyéni nézeteket, amelyek megjelennek a Azure Portalban, és a Log Analytics munkaterületen lévő adatvizualizációk különböző vizualizációit is tartalmazhatják. Ez a cikk a Tervező nézet áttekintését és az egyéni nézetek létrehozásához és szerkesztéséhez szükséges eljárásokat mutatja be.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 33930823fbeb42011d8e2a368d17c9a21070a243
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035600"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Egyéni nézetek létrehozása a Azure Monitor View Designer használatával
A Azure Monitor Tervező nézetében számos egyéni nézetet hozhat létre a Azure Portalban, amelyek segíthetnek a Log Analytics munkaterületen lévő adatmegjelenítésben. Ez a cikk áttekintést nyújt az egyéni nézetek létrehozásához és szerkesztéséhez szükséges tervezőről és eljárásokról.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

További információ a tervező nézetéről:

* [Csempe leírása](view-designer-tiles.md): Útmutatást nyújt az egyéni nézetekben elérhető csempék beállításaihoz.
* [Vizualizációs rész hivatkozása](view-designer-parts.md): Az egyéni nézetekben elérhető vizualizációs részek beállításait ismertető útmutató.


## <a name="concepts"></a>Alapelvek
A nézetek a Azure Portal Azure Monitor **Áttekintés** lapján jelennek meg. Nyissa meg ezt a lapot a **Azure monitor** menüjéből, és kattintson a **továbbiak** elemre az **áttekintések** szakaszban. Az egyes egyéni nézetek csempéi betűrendben jelennek meg, a figyelési megoldások csempéi pedig ugyanarra a munkaterületre lesznek telepítve.

![Áttekintő lap](media/view-designer/overview-page.png)

A tervező nézettel létrehozott nézetek a következő táblázatban ismertetett elemeket tartalmazzák:

| Részeként | Leírás |
|:--- |:--- |
| Mozaik elrendezés | Megjelenik a Azure Monitor **áttekintése** oldalon. Az egyes csempék az általa képviselt egyéni nézet vizuális összegzését jelenítik meg. Mindegyik csempe típusa különböző vizualizációkat biztosít a rekordokhoz. Az egyéni nézet megjelenítéséhez ki kell választania egy csempét. |
| Egyéni nézet | Egy csempe kiválasztásakor jelenik meg. Minden nézet egy vagy több vizualizációs alkatrészt tartalmaz. |
| Vizualizációs részek | Egy vagy több naplózási lekérdezésen alapuló adatvizualizáció megjelenítése a Log Analytics [](../log-query/log-query-overview.md)munkaterületen. A legtöbb rész tartalmaz egy fejlécet, amely egy magas szintű vizualizációt és egy listát mutat be, amely a legfontosabb eredményeket jeleníti meg. Az egyes típusok a Log Analytics munkaterületen lévő rekordok különböző vizualizációit biztosítják. A részben található elemeket kiválasztva olyan napló-lekérdezést hajthat végre, amely részletes rekordokat tartalmaz. |

## <a name="required-permissions"></a>Szükséges engedélyek
A nézetek létrehozásához vagy módosításához legalább [közreműködői szintű engedélyekre](manage-access.md#manage-access-using-azure-permissions) van szükség a log Analytics munkaterületen. Ha nem rendelkezik ezzel az engedéllyel, a Tervező nézet nem jelenik meg a menüben.


## <a name="work-with-an-existing-view"></a>Meglévő nézet használata
A tervező nézettel létrehozott nézetek a következő beállításokat jelenítik meg:

![Áttekintés menü](media/view-designer/overview-menu.png)

A beállításokat a következő táblázat ismerteti:

| Beállítás | Leírás |
|:--|:--|
| Frissítés   | A legújabb adattal frissíti a nézetet. | 
| Logs      | Megnyitja a [log Analytics](../log-query/portals.md) , hogy a rendszer naplózza az adatelemzési lekérdezéseket. |
| Szerkesztés       | A nézet megnyitása a tervezőben a tartalom és a konfiguráció szerkesztéséhez.  |
| Klónozás      | Létrehoz egy új nézetet, és megnyitja a Tervező nézetben. Az új nézet neve megegyezik az eredeti névvel, de a *Másolás* hozzá van fűzve. |
| Dátumtartomány | Állítsa be a nézetbe foglalt adatok dátum-és időtartomány-szűrőjét. Ezt a dátumtartományt a nézetben a lekérdezésekben beállított dátumtartomány előtt alkalmazza a rendszer.  |
| +          | Definiáljon egy egyéni szűrőt, amely a nézethez van definiálva. |


## <a name="create-a-new-view"></a>Új nézet létrehozása
Az Log Analytics munkaterület menüjében a **tervező megjelenítése** lehetőség kiválasztásával létrehozhat egy új nézetet a Tervező nézetében.

![Tervezői csempe megtekintése](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>A View Designer használata
A Tervező nézet használatával új nézeteket hozhat létre, vagy szerkesztheti a meglévőket. 

A Tervező nézet három ablaktáblával rendelkezik: 
* **Kialakítás**: A létrehozott vagy szerkesztett egyéni nézetet tartalmazza. 
* **Vezérlők**: A **tervezési** ablaktáblához hozzáadott csempéket és részeket tartalmazza. 
* **Tulajdonságok**: Megjeleníti a csempék vagy a kijelölt részek tulajdonságait.

![Nézettervező](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>A nézet csempe konfigurálása
Egy egyéni nézet csak egyetlen csempét tartalmazhat. Az aktuális csempe megtekintéséhez vagy egy másik lehetőség kiválasztásához válassza a **csempe** fület a **vezérlés** ablaktáblán. A **Tulajdonságok** ablaktáblán az aktuális csempe tulajdonságai láthatók. 

A csempe tulajdonságait a csempék hivatkozásában található információk alapján konfigurálhatja [](view-designer-tiles.md) , majd a módosítások mentéséhez kattintson az **alkalmaz** gombra.

### <a name="configure-the-visualization-parts"></a>A vizualizációs részek konfigurálása
A nézetek tetszőleges számú vizualizációs alkatrészt tartalmazhatnak. Ha részeket szeretne hozzáadni egy nézethez, válassza a **nézet** fület, majd válasszon ki egy vizualizációs részt. A **Tulajdonságok** ablaktáblán a kiválasztott rész tulajdonságai láthatók. 

A View (megjelenítés) tulajdonságokat a [vizualizációs részben](view-designer-parts.md) található információk alapján konfigurálhatja, majd a módosítások mentéséhez kattintson az **alkalmaz** gombra.

A nézetek csak egy sor vizualizációs részből állnak. A meglévő részek átrendezéséhez húzza őket egy új helyre.

A vizualizáció részét eltávolíthatja a nézetből a rész jobb felső sarkában található **X** gombra kattintva.


### <a name="menu-options"></a>Menüpontok
A nézetek szerkesztési módban való használatának lehetőségeit az alábbi táblázat ismerteti.

![Szerkesztés menü](media/view-designer/edit-menu.png)

| Beállítás | Leírás |
|:--|:--|
| Mentés        | Menti a módosításokat, és bezárja a nézetet. |
| Mégse      | Elveti a módosításokat, és bezárja a nézetet. |
| Nézet törlése | Törli a nézetet. |
| Exportálás      | Exportálja a nézetet egy olyan [Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) -sablonba, amelyet importálhat egy másik munkaterületre. A fájl neve a nézet neve, és *omsview* kiterjesztéssel rendelkezik. |
| Importálás      | Importálja a másik munkaterületről exportált *omsview* fájlt. Ez a művelet felülírja a meglévő nézet konfigurációját. |
| Klónozás       | Létrehoz egy új nézetet, és megnyitja a Tervező nézetben. Az új nézet neve megegyezik az eredeti névvel, de a *Másolás* hozzá van fűzve. |

## <a name="next-steps"></a>További lépések
* [Csempék](view-designer-tiles.md) hozzáadása az egyéni nézethez.
* [Vizualizációs részek](view-designer-parts.md) hozzáadása az egyéni nézethez.
