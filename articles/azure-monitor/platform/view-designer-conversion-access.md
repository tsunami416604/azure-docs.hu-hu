---
title: Az Azure Monitor nézet tervezője a munkafüzetek konverziós összegzéséhez és eléréséhez
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658846"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>A tervező és a munkafüzetek konverziós összegzésének és elérésének megtekintése
[A View designer](view-designer.md) az Azure Monitor egyik szolgáltatása, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti az adatokat a Log Analytics-munkaterületen, diagramokkal, listákkal és ütemtervekkel. Ezeket fokozatosan megszüntetik, és munkafüzetekre cserélik, amelyek további funkciókat biztosítanak. Ez a cikk bemutatja, hogyan hozhat létre áttekintést és a munkafüzetek eléréséhez szükséges engedélyeket.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>A munkaterület összegzésének létrehozása az Azure Irányítópultról
Előfordulhat, hogy a tervezői felhasználók ismerik a nézetek egy készletének ábrázolására létrehozott áttekintő csempével. A vizuális áttekintés, például a nézettervezői munkaterület összegzése érdekében a munkafüzetek rögzített lépéseket kínálnak, amelyek rögzíthetők az [Azure Portal irányítópultján.](../../azure-portal/azure-portal-dashboards.md) A Munkaterület összegzése című témaköráttekintő csempékhez hasonlóan a rögzített munkafüzetelemek is közvetlenül a munkafüzet nézetre hivatkoznak.

Kihasználhatja az Azure irányítópultjaihoz tartozó magas szintű testreszabási funkciókat, amelyek lehetővé teszik az automatikus frissítést, a áthelyezést, a méretezést és a rögzített elemek és vizualizációk további szűrését. 

![Irányítópult](media/view-designer-conversion-access/dashboard.png)

Hozzon létre egy új Azure-irányítópultot, vagy válasszon ki egy meglévő irányítópultot a munkafüzet-elemek rögzítésének megkezdéséhez.

Az egyes elemek rögzítéséhez engedélyeznie kell az adott lépés pin ikonját. Ehhez válassza ki a **lépéshez** tartozó Szerkesztés gombot, majd a fogaskerék ikont a **Speciális beállítások**megnyitásához. Jelölje be a Beállításlehetőséget, hogy **mindig megjelenjen a pin ikon ezen a lépésen,** és egy pin ikon jelenik meg a lépés jobb felső sarkában. Ez a pin lehetővé teszi, hogy adott vizualizációkat rögzítsen az irányítópulton, például az áttekintő csempékre.

![Pin lépés](media/view-designer-conversion-access/pin-step.png)


A munkafüzetből vagy a munkafüzet teljes tartalmából több vizualizációt is rögzíthet egy irányítópulton. A teljes munkafüzet rögzítéséhez a felső eszköztár **Szerkesztés gombjára** kattintva kapcsolja be a **Szerkesztési módot**. Megjelenik egy pin ikon, amely lehetővé teszi a teljes munkafüzetelem, illetve a munkafüzet ben lévő összes egyes lépés és vizualizáció rögzítését.

![Az összes rögzítése](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Engedélyek megosztása és megtekintése 
A munkafüzetek előnye, hogy magánjellegűek vagy megosztott dokumentumok. Alapértelmezés szerint a mentett munkafüzetek a **Saját jelentések**csoportban kerülnek mentésre, ami azt jelenti, hogy csak a készítő tekintheti meg ezt a munkafüzetet.

A munkafüzeteket úgy oszthatja meg, hogy a felső eszköztár **Megosztás** ikonját **választja a Szerkesztés idomában**. A program kéri, hogy helyezze át a munkafüzetet a **Megosztott jelentések**mappába, amely olyan hivatkozást hoz létre, amely közvetlen hozzáférést biztosít a munkafüzethez.

Ahhoz, hogy a felhasználó megtekinthessen egy közös munkafüzetet, hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez és erőforráscsoporthoz, amely alatt a munkafüzet et menti.

![Előfizetés-alapú hozzáférés](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>További lépések

- [Gyakori feladatok](view-designer-conversion-tasks.md)