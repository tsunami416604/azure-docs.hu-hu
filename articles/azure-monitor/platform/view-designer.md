---
title: Nézetek létrehozása a naplóadatok elemzéséhez az Azure Monitorban | Microsoft dokumentumok
description: A View Designer használatával az Azure Monitor, hozhat létre egyéni nézeteket, amelyek megjelennek az Azure Portalon, és a Log Analytics-munkaterület különböző vizualizációkat tartalmaznak. Ez a cikk áttekintést nyújt a Nézettervezőről, és bemutatja az egyéni nézetek létrehozására és szerkesztésére vonatkozó eljárásokat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/10/2019
ms.openlocfilehash: 9a7521f61dc59bd954629a05638c159ab0e70556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658489"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Egyéni nézetek létrehozása a Nézettervező használatával az Azure Monitorban
Az Azure Monitor Nézettervezőjével számos egyéni nézetet hozhat létre az Azure Portalon, amelyek segítségével vizualizálhatja az adatokat a Log Analytics-munkaterületen. Ez a cikk áttekintést nyújt a Tervező megtekintése és az egyéni nézetek létrehozására és szerkesztésére szolgáló eljárásokról.

> [!IMPORTANT]
> Az Azure Monitor nézetei fokozatosan megszűnnek, és további funkciókat biztosító [munkafüzetekkel helyettesítik](workbooks-overview.md) őket. Tekintse meg [az Azure Monitor nézet tervezője a munkafüzetek átmeneti útmutató](view-designer-conversion-overview.md) a meglévő nézetek munkafüzetek átalakítása részletekért.

A Tervező megtekintése című témakörben további információt a következő témakörben talál:

* [Csempehivatkozás](view-designer-tiles.md): Útmutatót ad az egyéni nézetekben elérhető csempék beállításaihoz.
* [Képi megjelenítési rész hivatkozása:](view-designer-parts.md)Útmutatót nyújt az egyéni nézetekben elérhető vizualizációs részek beállításaihoz.


## <a name="concepts"></a>Alapelvek
A nézetek az Azure-portál On Azure Monitor **áttekintése** lapján jelennek meg. Nyissa meg ezt a lapot az **Azure Monitor** menüben az Insights szakasz **Egyebek** szakasza További elemre kattintva. **Insights** Az egyes egyéni nézetekben lévő csempék betűrendben jelennek meg, és a figyelési megoldások csempéi ugyanazzal a munkaterülettel vannak telepítve.

![Áttekintő lap](media/view-designer/overview-page.png)

A Nézettervezővel létrehozott nézetek az alábbi táblázatban ismertetett elemeket tartalmazzák:

| Rész | Leírás |
|:--- |:--- |
| Csempék | Megjelennek az Azure Monitor **áttekintése** oldalon. Minden csempe az általa képviselt egyéni nézet vizuális összegzését jeleníti meg. Minden csempetípus más-más megjelenítést biztosít a bejegyzésekhez. Kijelöl egy csempét az egyéni nézet megjelenítéséhez. |
| Egyéni nézet | Csempe kijelölésétekkor jelenik meg. Minden nézet egy vagy több vizualizációs részt tartalmaz. |
| Képi megjelenítési részek | A Log Analytics munkaterületen egy vagy több naplólekérdezés alapján jelenítheti meg az adatok [megjelenítését.](../log-query/log-query-overview.md) A legtöbb alkatrész tartalmaz egy fejlécet, amely magas szintű megjelenítést biztosít, és egy listát, amely a legjobb eredményeket jeleníti meg. Minden egyes alkatrésztípus a Log Analytics-munkaterület rekordjainak eltérő vizualizációját biztosítja. Az elemeket a részben olyan naplólekérdezés végrehajtásához választhatja ki, amely részletes rekordokat tartalmaz. |

## <a name="required-permissions"></a>Szükséges engedélyek
A nézetek létrehozásához vagy módosításához legalább [közreműködői szintű engedélyekre](manage-access.md#manage-access-using-azure-permissions) van szükség a Log Analytics-munkaterületen. Ha nem rendelkezik ezzel az engedéllyel, akkor a Tervező megtekintése beállítás nem jelenik meg a menüben.


## <a name="work-with-an-existing-view"></a>Munka meglévő nézetben
A Nézettervezővel létrehozott nézetek a következő beállításokat jelenítik meg:

![Áttekintő menü](media/view-designer/overview-menu.png)

A beállításokat az alábbi táblázat ismerteti:

| Beállítás | Leírás |
|:--|:--|
| Frissítés   | Frissíti a nézetet a legfrissebb adatokkal. | 
| Naplók      | Megnyitja a [Log Analytics](../log-query/portals.md) adatok elemzéséhez naplólekérdezések. |
| Szerkesztés       | Megnyitja a nézetet a Nézettervezőben a tartalom és a konfiguráció szerkesztéséhez.  |
| Klónozás      | Új nézetet hoz létre, és megnyitja a Nézettervezőben. Az új nézet neve megegyezik az eredeti névvel, de a *Másolás* hozzáfűzve. |
| Dátumtartomány | Állítsa be a nézetben szereplő adatok dátum- és időtartomány-szűrőjét. Ez a dátumtartomány a nézetlekérdezésekben beállított bármely dátumtartomány előtt kerül alkalmazásra.  |
| +          | Egyéni szűrő definiálása, amely a nézethez van definiálva. |


## <a name="create-a-new-view"></a>Új nézet létrehozása
Új nézetet hozhat létre a View Designer programban, ha a Log Analytics-munkaterület menüjében a **Nézettervező** lehetőséget választja.

![Tervező megtekintése csempe](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>A Nézettervező vel való kapcsolat
A Designer megtekintése segítségével új nézeteket hozhat létre, vagy szerkesztheti a meglévőket. 

A Nézettervező három ablaktáblával rendelkezik: 
* **Tervezés**: A létrehozás vagy szerkesztés közben létrehozott vagy szerkesztett egyéni nézetet tartalmazza. 
* **Vezérlők**: A **Tervezés** ablaktáblához hozzáadott mozaikokat és részeket tartalmazza. 
* **Tulajdonságok**: A csempék vagy a kijelölt részek tulajdonságainak megjelenítése.

![Nézettervező](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>A nézetcsempe konfigurálása
Az egyéni nézetnek csak egy csempéje lehet. Az aktuális csempe megtekintéséhez vagy egy másik csempének kijelöléséhez válassza a **Mozaik** fület a **Vezérlő** ablaktáblán. A **Tulajdonságok** ablaktábla az aktuális csempe tulajdonságait jeleníti meg. 

A csempe tulajdonságait a [Mozaikhivatkozásban](view-designer-tiles.md) szereplő információk nak megfelelően konfigurálhatja, majd a módosítások mentéséhez kattintson az **Alkalmaz** gombra.

### <a name="configure-the-visualization-parts"></a>A vizualizációs részek konfigurálása
A nézet tetszőleges számú vizualizációs részt tartalmazhat. Ha alkatrészeket szeretne hozzáadni egy nézethez, válassza a **Nézet** lapot, majd jelöljön ki egy vizualizációs részt. A **Tulajdonságok** ablaktábla megjeleníti a kijelölt rész tulajdonságait. 

A nézet tulajdonságait a [Vizualizációs rész hivatkozásában](view-designer-parts.md) szereplő információk nak megfelelően konfigurálhatja, majd a módosítások mentéséhez kattintson az **Alkalmaz** gombra.

A nézetek csak egy sor vizualizációs részből állnak. A meglévő részeket átrendezheti, ha új helyre húzza őket.

A vizualizációs részt a nézetjobb felső részén lévő **X** kiválasztásával távolíthatja el a nézetből.


### <a name="menu-options"></a>Menübeállítások
A nézetek szerkesztési módban való munka lehetőségeit az alábbi táblázat ismerteti.

![Szerkesztés menü](media/view-designer/edit-menu.png)

| Beállítás | Leírás |
|:--|:--|
| Mentés        | Menti a módosításokat, és bezárja a nézetet. |
| Mégse      | Elveti a módosításokat, és bezárja a nézetet. |
| Nézet törlése | Törli a nézetet. |
| Exportálás      | Exportálja a nézetet egy [Azure Resource Manager-sablonba,](../../azure-resource-manager/templates/template-syntax.md) amelyet importálhat egy másik munkaterületre. A fájl neve a nézet neve, és *omsview* kiterjesztéssel rendelkezik. |
| Importálás      | Importálja a másik munkaterületről exportált *omsview* fájlt. Ez a művelet felülírja a meglévő nézet konfigurációját. |
| Klónozás       | Új nézetet hoz létre, és megnyitja a Nézettervezőben. Az új nézet neve megegyezik az eredeti névvel, de a *Másolás* hozzáfűzve. |

## <a name="next-steps"></a>További lépések
* [Csempék](view-designer-tiles.md) hozzáadása az egyéni nézethez.
* [Képi megjelenítési részek](view-designer-parts.md) hozzáadása az egyéni nézethez.
