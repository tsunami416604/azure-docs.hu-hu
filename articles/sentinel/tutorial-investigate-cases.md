---
title: Incidensek kivizsgálása az Azure Sentinelsegítségével| Microsoft dokumentumok
description: Az oktatóanyag ból megtudhatja, hogyan vizsgálhatja ki az Azure Sentinellel kapcsolatos incidenseket.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: ecd8c508d05bfeb541a6cb5efbcdf2fffd3c78d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587192"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Oktatóanyag: Incidensek kivizsgálása az Azure Sentinelsegítségével

> [!IMPORTANT]
> A vizsgálati grafikon jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)


Ez az oktatóanyag segít az Azure Sentinel incidensei kivizsgálásában. Miután csatlakoztatta az adatforrásokat az Azure Sentinelhez, értesítést szeretne kapni, ha valami gyanús történik. Ehhez az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre, amelyek olyan incidenseket hoznak létre, amelyeket hozzárendelhet és megvizsgálhat.

Ez a cikk a következőket tartalmazza:
> [!div class="checklist"]
> * Incidensek vizsgálata
> * A vizsgálati grafikon használata
> * Reagálás a fenyegetésekre

Egy incidens több riasztást is tartalmazhat. Ez egy összes bizonyíték összesítése egy konkrét nyomozáshoz. Az incidens az **Analytics-oldalon** létrehozott analitikus szabályok alapján jön létre. A riasztásokhoz kapcsolódó tulajdonságok, például a súlyosság és az állapot, az incidens szintjén vannak beállítva. Miután tudatta az Azure Sentinellel, hogy milyen típusú fenyegetéseket keres, és hogyan találja meg őket, az incidensek kivizsgálásával figyelheti az észlelt fenyegetéseket.

## <a name="prerequisites"></a>Előfeltételek
Csak akkor vizsgálhatja ki az esetet, ha az analitikus szabály beállításakor az entitásleképezési mezőket használta. A vizsgálati grafikon megköveteli, hogy az eredeti incidens entitásokat tartalmazzon.

## <a name="how-to-investigate-incidents"></a>Incidensek vizsgálata

1. Válassza **az Incidensek lehetőséget.** Az Incidensek lapon **megtudhatja,** hogy hány incidense van, hány van megnyitva, hány at állított be **folyamatban**, és hány annektált. Minden incidensnél láthatja a bekövetkezésének idejét és az incidens állapotát. Nézd meg a súlyossága eldönteni, hogy mely eseményekkezelésére először.

    ![Incidens súlyosságának megtekintése](media/tutorial-investigate-cases/incident-severity.png)

1. Szükség szerint szűrheti az incidenseket, például állapot vagy súlyosság szerint.

1. A vizsgálat megkezdéséhez válasszon ki egy adott incidenst. A jobb oldalon részletes információkat láthat az incidensről, beleértve annak súlyosságát, az érintett entitások számának összegzését, az incidenst kiváltó nyers eseményeket és az incidens egyedi azonosítóját.

1. Ha további részleteket szeretne megtudni az incidensben szereplő riasztásokról és entitásokról, válassza a **Részletek megtekintése az** incidensoldalon lehetőséget, és tekintse át az incidensadatait összegző megfelelő lapokat. A **Riasztások** lapon tekintse át magát a riasztást. Megtekintheti a riasztással kapcsolatos összes releváns információt – a riasztást kiváltó lekérdezést, a lekérdezésenként visszaadott eredmények számát és a forgatókönyvek futtatásának lehetőségét a riasztásokon. Ha még mélyebbre szeretne hatolni az incidensbe, válassza ki az **Események**számát. Ez megnyitja a lekérdezést, amely létrehozta az eredményeket, és az eseményeket, amelyek aktiválták a riasztást a Log Analytics. Az **Entitások** lapon láthatja az összes entitást, amelyet a riasztási szabály definíciója részeként leképezett.

    ![Riasztás részleteinek megtekintése](media/tutorial-investigate-cases/alert-details.png)

1. Ha aktívan vizsgál egy incidenst, célszerű az incidens állapotát **folyamatban** állapotba állítani, amíg be nem zárja.

1. Incidensek rendelhető egy adott felhasználóhoz. Minden incidenshez hozzárendelhet egy tulajdonost az **Incidens tulajdonosa** mező beállításával. Minden incidens ki nem osztottként kezdődik. Megjegyzéseket is hozzáadhat, hogy más elemzők is megérthessék, mit vizsgált meg, és mik az aggodalmai az incidens körül.

    ![Incidens hozzárendelése a felhasználóhoz](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. A **Vizsgálat gombra** a vizsgálati térkép megtekintéséhez válassza a Vizsgálat lehetőséget.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Használja a vizsgálati grafikon mély merülés

A vizsgálati grafikon lehetővé teszi az elemzők számára, hogy minden vizsgálathoz megfelelő kérdéseket tegyenek fel. A vizsgálati grafikon segítségével megismerheti a hatókörét, és azonosíthatja a potenciális biztonsági fenyegetés hatókörét, és azonosíthatja a potenciális biztonsági fenyegetés okát azáltal, hogy a releváns adatokat bármely érintett entitással összeveti. Mélyebbre merülhet, és megvizsgálhatja a grafikonon bemutatott bármely entitást, ha kiválasztja azt, és kiválasztja a különböző bővítési lehetőségeket.  
  
A vizsgálati grafikon a következőket tartalmazza:

- **Vizuális környezet nyers adatokból**: Az élő, vizuális grafikon a nyers adatokból automatikusan kinyert entitáskapcsolatokat jeleníti meg. Ez lehetővé teszi a különböző adatforrások közötti kapcsolatok egyszerű megtekintését.

- **Teljes vizsgálati hatókör felderítése:** Bővítse ki a vizsgálati hatókör beépített feltárási lekérdezések felszínre a teljes hatókörét a jogsértés.

- **Beépített vizsgálati lépések:** Használjon előre definiált feltárási lehetőségeket, hogy megbizonyosodjon arról, hogy a megfelelő kérdéseket teszi fel a fenyegetéssel szemben.

A vizsgálati grafikon használata:

1. Jelöljön ki egy incidenst, majd válassza **a Vizsgálat lehetőséget.** Ez elvezet a nyomozási grafikonhoz. A grafikon szemléltető térképet biztosít a riasztáshoz közvetlenül kapcsolódó entitásokról és minden további kapcsolódó erőforrásról.

   > [!IMPORTANT] 
   > Csak akkor vizsgálhatja ki az esetet, ha az analitikus szabály beállításakor az entitásleképezési mezőket használta. A vizsgálati grafikon megköveteli, hogy az eredeti incidens entitásokat tartalmazzon.

   ![Térkép megtekintése](media/tutorial-investigate-cases/map1.png)

1. Válasszon ki egy entitást az **Entitások** ablaktábla megnyitásához, hogy áttekinthesse az entitásra vonatkozó információkat.

    ![Entitások megtekintése a térképen](media/tutorial-investigate-cases/map-entities.png)
  
1. Bővítse a vizsgálatot úgy, hogy az egyes entitások fölé viszi a lebegtetést, hogy felfedje a biztonsági szakértőink és elemzőink által a vizsgálat elmélyítésére vonatkozó anamandó kérdések listáját. Hívjuk ezeket a lehetőségeket **feltárása lekérdezések**.

    ![További részletek megismerése](media/tutorial-investigate-cases/exploration-cases.png)

   A számítógépen például kapcsolódó riasztásokat kérhet. Ha egy feltárási lekérdezést választ, az eredményül kapott feljogosító adatok visszakerülnek a diagramba. Ebben a példában a Kapcsolódó riasztások lehetőséget **választva** a következő riasztásokat adta vissza a grafikonba:

    ![Kapcsolódó riasztások megtekintése](media/tutorial-investigate-cases/related-alerts.png)

1. Minden feltárási lekérdezésnél kiválaszthatja a nyers eseményeredmények és a Log Analytics szolgáltatásban használt lekérdezés megnyitásának lehetőségét az **Események\>** lehetőség kiválasztásával.

1. Az incidens megértéséhez a grafikon párhuzamos idővonalat ad.

    ![Idővonal megtekintése a térképen](media/tutorial-investigate-cases/map-timeline.png)

1. Mutasson az idővonalra, és nézze meg, hogy a diagramon milyen események történtek az időpontban.

    ![Idővonal használata a térképen a riasztások vizsgálatához](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan kezdheti el az Azure Sentinel használatával az incidensek kivizsgálását. Folytassa az oktatóanyagot, [hogy hogyan reagáljon a fenyegetésekre az automatizált forgatókönyvekkel.](tutorial-respond-threats-playbook.md)
> [!div class="nextstepaction"]
> [Reagáljon a fenyegetésekre,](tutorial-respond-threats-playbook.md) hogy automatizálhassa a fenyegetésekre adott válaszait.

