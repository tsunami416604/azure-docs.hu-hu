---
title: Incidensek vizsgálata az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan vizsgálja ki az incidenseket az Azure Sentinel használatával.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780388"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>Oktatóanyag: Incidensek vizsgálata az Azure Sentinel előzetes verziójával

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az oktatóanyag segítséget nyújt az Azure Sentinel-fenyegetések észlelésében.

Miután [csatlakoztatta az](quickstart-onboard.md) adatforrásokat az Azure Sentinel szolgáltatáshoz, értesítést szeretne kapni, ha valamilyen gyanús esemény történik. Ennek lehetővé tételéhez az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre, amelyek olyan incidenseket állítanak elő, amelyek a környezetében felmerülő rendellenességek és fenyegetések mélyebb kivizsgálására használhatók. 

> [!div class="checklist"]
> * Incidensek létrehozása
> * Incidensek vizsgálata
> * Reagálás a fenyegetésekre

## <a name="investigate-incidents"></a>Incidensek vizsgálata

egy incidens több riasztást is tartalmazhat. Ez egy adott vizsgálatra vonatkozó összes releváns bizonyíték összesítése. az incidens az **elemzés** oldalon megadott riasztások alapján jön létre. A riasztásokhoz kapcsolódó tulajdonságok, például a súlyosság és az állapot az incidens szintjén vannak megadva. Miután engedélyezte, hogy az Azure Sentinel tudja, milyen veszélyforrásokat keres, és hogyan keresheti meg őket, az incidensek kivizsgálásával észlelt fenyegetéseket figyelheti. 

1. Válasszaaz incidensek lehetőséget. Az **incidensek** oldalon megtudhatja, hogy hány incidens van, hány van nyitva, hányan van **folyamatban**, és hány lezárult. Minden incidensnél láthatja a bekövetkezett időt, valamint az incidens állapotát. Tekintse meg a súlyosságot, hogy eldöntse, mi legyen az első kezelése. Az **incidensek** lapon kattintson a riasztások lapra az incidensekhez kapcsolódó összes riasztás megtekintéséhez. Az incidens részeként korábban leképezett entitások az entitások lapon tekinthetők meg.  Igény szerint szűrheti az incidenseket, például az állapot vagy a súlyosság alapján. Az **incidensek** lapon megtekintheti azokat a nyitott incidenseket, amelyek az **elemzés**által meghatározott észlelési szabályok által aktivált riasztásokat tartalmaznak. A felső részen az aktív incidensek, az új incidensek és a folyamatban lévő incidensek jelennek meg. Az incidensek súlyosság szerinti áttekintését is láthatja.

   ![Riasztási irányítópult](./media/tutorial-investigate-cases/cases.png)

2. A vizsgálat megkezdéséhez kattintson egy adott incidensre. A jobb oldalon megtekintheti az incidens részletes információit, beleértve annak súlyosságát, az érintett entitások számának összefoglalását (a megfeleltetés alapján). Minden incidens egyedi AZONOSÍTÓval rendelkezik. Az incidens súlyossága az incidensben foglalt legsúlyosabb riasztásnak megfelelően van meghatározva.  

1. Az incidensben lévő riasztásokkal és entitásokkal kapcsolatos további részletek megtekintéséhez kattintson a **teljes részletek megtekintése** elemre az incidens lapon, és tekintse át az incidens információit összefoglaló kapcsolódó lapokat.  A teljes incidens nézet összevonja a riasztásban lévő összes bizonyítékot, a kapcsolódó riasztásokat és entitásokat.

1. A **riasztások** lapon tekintse át magát a riasztást – az aktiváláskor, illetve a beállított küszöbértékek túllépése esetén. Láthatja a riasztással kapcsolatos összes releváns információt – a riasztást kiváltó lekérdezést, a lekérdezésekben visszaadott eredmények számát, valamint a riasztásokra vonatkozó forgatókönyvek futtatásának lehetőségét. Az incidensek számának további részletezéséhez kattintson a találatok száma elemre. Ekkor megnyílik az eredményeket generáló lekérdezés, valamint a riasztást kiváltó eredmények Log Analyticsban.

3. Az **entitások** lapon a riasztási szabály definíciójának részeként leképezett összes entitás látható. 

4. Ha aktívan vizsgálja az incidenst, érdemes lehet az incidens állapotát a **folyamatban lévő** állapotba állítani, amíg be nem zárul. Bezárhatja az incidenst is, ahol a lezárt megoldott állapot olyan incidensek állapota, amelyek jelzik, hogy az incidenst kezelték, a lezártak pedig a kezelést nem igénylő incidensek állapota. Az incidensek lezárásának indoklásához magyarázatokra van szükség.

5. Az incidensek egy adott felhasználóhoz rendelhetők hozzá. Minden incidenshez hozzárendelhet egy tulajdonost az incidens **tulajdonosa** mező beállításával. Az összes incidens nincs hozzárendelve. Megtekintheti az incidenseket, és a név alapján szűrheti az összes Ön által birtokolt incidenst. 

5. Kattintson a vizsgálat gombra a vizsgálati Térkép és a megsértés hatókörének megtekintéséhez a szervizelés lépéseivel. 



## <a name="respond-to-threats"></a>Reagálás a fenyegetésekre

Az Azure Sentinel két elsődleges lehetőséget kínál a fenyegetésekre való reagálásra forgatókönyvek használatával. Beállíthatja, hogy egy forgatókönyv automatikusan fusson riasztás indításakor, vagy egy riasztásra adott válaszként manuálisan futtasson egy forgatókönyvet.

- Beállíthatja, hogy egy forgatókönyv automatikusan fusson, ha a forgatókönyv konfigurálásakor riasztást indít el. 

- A riasztáson belül manuálisan is futtathat egy forgatókönyvet, ehhez kattintson a forgatókönyvek **megtekintése** elemre, majd válassza ki a futtatandó forgatókönyvet.




## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan kezdheti el az incidensek vizsgálatát az Azure Sentinel használatával. Folytassa az Oktatóanyaggal, hogy [miként reagálhat a fenyegetésekre automatizált forgatókönyvek használatával](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Válaszoljon](tutorial-respond-threats-playbook.md) a fenyegetésekre, hogy automatizálja a fenyegetésekre adott válaszokat.

