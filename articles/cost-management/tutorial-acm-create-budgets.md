---
title: Oktatóanyag – létrehozása és kezelése az Azure költségvetése |} A Microsoft Docs
description: Ez az oktatóanyag segít tervet és optimalizáljuk az Azure-szolgáltatások felhasznált fiók.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 59ae3b587751bd4af2c9e5ab0abefb8a5c3bf8e6
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843942"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Oktatóanyag: Azure-költségvetések létrehozása és kezelése

A Cost Management költségvetései segítenek a tervezésben és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. Ezek segítenek mások tájékoztatja a költségkeret-beállítási proaktív módon kezelheti a költségeket, és figyelheti a költségkeret-beállítási hogyan halad idővel. Ha a létrehozott költségvetés küszöbérték túllépése esetén csak az értesítések aktiválódnak. Az erőforrások egyike sem érintett, és a használat nem leállt. Költségvetése segítségével nyomon követheti a Költekezési, a költségek elemzése és összehasonlítása.

A havi költségkeretek kiértékelése négy óránként történik. A felhasznált erőforrások esetében azonban nyolc órán belül elérhetők az adatmennyiségek. Ha egy költségvetési küszöbérték teljesül, az e-mail-értesítések fogadása általában kevesebb, mint 12 óra. 

A költségvetéshez automatikusan átállítani (havonta, negyedévente vagy évente) időszak végén a költségvetés akkora lejárati dátumot a jövőben kiválasztásakor. A költségvetés akkora alaphelyzetbe állítása, mert kell létrehoznia a különálló költségvetéseket, ha a tervezett pénznem összegek különböznek a későbbi elszámolási időszakokra.

Ebben az oktatóanyagban szereplő példák végigvezetik létrehozása és módosítása az Azure nagyvállalati szerződés (EA) előfizetésre költségvetést.

Tekintse meg, [hogyan hozhat létre költségvetést a kiadások figyeléséhez Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) videóval, hogy megtudja, hogyan hozhat létre költségvetést az Azure-ban a kiadások figyeléséhez.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetési szerkesztése

## <a name="prerequisites"></a>Előfeltételek

A költségvetés számos különböző Azure-fióktípus esetében támogatott. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A költségvetések megtekintéséhez legalább olvasási hozzáféréssel kell rendelkeznie az Azure-fiókhoz.

 Azure EA-előfizetések esetén olvasási hozzáféréssel kell rendelkeznie a költségvetések megtekintéséhez. Hozhat létre és kezelheti költségvetése közreműködő engedéllyel kell rendelkeznie. Az EA-előfizetések és -erőforráscsoportok egyes költségvetése hozhat létre. Azonban a nagyvállalati szerződéssel rendelkező fiókok számlázási költségvetése nem hozható létre.

A következő Azure-engedélyek vagy-hatókörök a költségvetésre vonatkozó előfizetések szerint felhasználónként és csoportonként támogatottak. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő, és a Cost Management közreműködője – létrehozása, módosítása vagy törlése a saját költségvetése. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- Olvasó és a Cost Management olvasó – megtekintheti a kiadásoktól, amelyek engedéllyel rendelkeznek.

Engedélyek hozzárendelése a Cost Management adataihoz kapcsolatos további információkért lásd: [Cost Management adataihoz való hozzáférés hozzárendelése](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-budget-in-the-azure-portal"></a>Költségvetés létrehozása az Azure Portalon

Létrehozhat egy Azure-előfizetés költségvetés havi, negyedéves és éves időszakra. A Azure Portal navigációs tartalma határozza meg, hogy létrehoz-e költségvetést egy előfizetéshez vagy egy felügyeleti csoporthoz.

Ha költségvetést szeretne létrehozni vagy megtekinteni, nyissa meg a kívánt hatókört a Azure Portalban, és válassza a **költségvetés** lehetőséget a menüben. Például navigáljon az előfizetések elemre, válasszon ki egy előfizetést a listából, majd válassza a **költségvetés** lehetőséget a menüben. A **hatókör** pirulát használva váltson át egy másik hatókörre, például egy felügyeleti csoportra a költségvetésekben. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

Után a költségvetés hoz létre, azok őket az aktuális költségeket az egyszerű nézet jelenik meg.

Kattintson a **Hozzáadás**lehetőségre.

![A már létrehozott költségvetések listáját bemutató példa](./media/tutorial-acm-create-budgets/budgets01.png)

A **költségvetés létrehozása** ablakban ellenőrizze, hogy helyes-e a hatókör. Válassza ki a hozzáadni kívánt szűrőket. A szűrők lehetővé teszik, hogy bizonyos költségekkel, például az előfizetésben lévő erőforráscsoportok, vagy egy szolgáltatás, például a virtuális gépek számára hozzon létre költségvetést. A Cost Analysis szolgáltatásban használható szűrőket a költségvetésre is lehet alkalmazni.

Miután azonosította a hatókört és a szűrőket, írja be a költségvetés nevét. Ezután válassza ki a havi, negyedéves vagy éves költségvetési alaphelyzetbe állítási időszakot. Ez az alaphelyzetbe állítási időszak határozza meg a költségvetés által elemzett időintervallumot. A költségvetés által kiértékelt költségek minden új időszak elején nullával kezdődnek. Negyedéves költségvetési létrehozásakor az havi költségvetési azonos módon működik. A különbség az, hogy a költségvetési összeg a negyedév egyenletesen vannak osztva a három hónapos negyedév. Az éves költségvetési összeg a naptári év 12 hónapja között egyenlően oszlik meg.

Ha utólagos elszámolású, MSDN-vagy Visual Studio-előfizetéssel rendelkezik, előfordulhat, hogy a számla számlázási időszaka nem a naptári hónaphoz igazodik. Az előfizetési típusok és erőforráscsoportok esetében létrehozhat egy költségvetést, amely a számlázási időszakhoz vagy a naptári hónapokhoz van igazítva. A számlázási időszakhoz igazított költségvetés létrehozásához válassza ki a **Számlázási hónap**, a **Számlázási negyedév**vagy a **Számlázási év**alaphelyzetbe állításának időtartamát. A naptári hónapra igazított költségvetés létrehozásához válassza ki a **havi**, **negyedéves**vagy **évenkénti**alaphelyzetbe állítási időszakot.

Ezután azonosítsa a lejárati dátumot, amikor a költségvetés érvénytelenné válik, és leállítja a költségek kiértékelését.

Az eddigi költségvetésben kiválasztott mezők alapján egy gráf jelenik meg, amely segítséget nyújt a költségkerethez használandó küszöbérték kiválasztásában. A javasolt költségvetés a jövőbeli időszakokban felmerülő legmagasabb becsült költségen alapul. Módosíthatja a költségvetés összegét.

![Példa a költségvetés létrehozására havi költségadatok szerint ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

A költségvetés összegének konfigurálása után kattintson a **tovább** gombra a költségvetési riasztások konfigurálásához. Költségvetés legalább egy költséget küszöb (% költségvetés) és a egy megfelelő e-mail-címre van szükség. Szükség esetén belefoglalhatja a legfeljebb öt küszöbértékeket és a egy egyetlen költségvetésben öt e-mail címet. Amikor teljesül a költségvetés küszöbértéket, e-mail-értesítések általában kevesebb mint nyolc óra fogadott. További információ az értesítésekről: a [díjszabási riasztások használata](cost-mgt-alerts-monitor-usage-spending.md). Az alábbi példában egy e-mail-riasztás jön létre, amikor eléri a költségvetés 90%-át.

![Riasztási feltételeket mutató példa](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Miután létrehozta a költségvetést, a költségek elemzése jelenik meg. Megtekintés a költségvetést, a költési trend viszonyítva az egyik az első lépéseket megkezdésekor [elemezheti a költségeket, és a költségkeret-beállítási](quick-acm-cost-analysis.md).

![Példa költségvetés és a kiadások a cost analysis látható](./media/tutorial-acm-create-budgets/cost-analysis.png)

Az előző példában létrehozott egy költségvetés-előfizetéssel. Azonban egy erőforráscsoporthoz költségvetési is létrehozhat. Ha azt szeretné, hozzon létre egy erőforráscsoportot a költségvetést, lépjen **Költségkezelés + számlázás** &gt; **előfizetések** &gt; válasszon ki egy előfizetést > **erőforrás csoportok** > Válasszon ki egy erőforráscsoportot > **költségvetése** >, majd **Hozzáadás** költségvetés keretében.

## <a name="trigger-an-action-group"></a>Műveleti csoport elindítása

Ha egy előfizetéshez vagy erőforráscsoport-hatókörhöz hoz létre vagy szerkeszt költségvetést, beállíthatja egy műveleti csoport meghívására. A műveleti csoport különböző műveleteket hajthat végre, ha a költségvetési küszöbérték teljesül. A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../azure-monitor/platform/action-groups.md). A költségvetési alapú automatizálás műveleti csoportokkal való használatával kapcsolatos további információkért lásd: [költségek kezelése az Azure-költségvetésekkel](../billing/billing-cost-management-budget-scenario.md).

A műveleti csoportok létrehozásához vagy frissítéséhez kattintson a **műveleti csoportok kezelése** lehetőségre a költségvetés létrehozásakor vagy szerkesztésekor.

![Példa egy költségvetés létrehozására a kezelési műveleti csoportok megjelenítéséhez](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Ezután kattintson a **műveleti csoport hozzáadása** és a műveleti csoport létrehozása elemre.


![A műveleti csoport hozzáadása mező képe](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

A műveleti csoport létrehozása után a mező bezárásával térjen vissza a költségvetésbe.

Konfigurálja úgy a költségvetést, hogy az egyéni küszöbérték teljesülése esetén a műveleti csoportot használja. Legfeljebb öt különböző küszöbérték támogatott.

![Példa a műveleti csoport kiválasztására riasztási feltételhez](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Az alábbi példa a 50%, 75% és 100% értékű költségvetési küszöbértékeket mutatja. Mindegyik úgy van konfigurálva, hogy a kijelölt műveleti csoporton belül aktiválja a megadott műveleteket.

![Példa a különböző műveleti csoportokkal és a műveletek típusával konfigurált riasztási feltételekre](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetési szerkesztése

Folytassa a következő oktatóanyag a költségkezelési adatokhoz olyan ismétlődő exportálásának létrehozásához.

> [!div class="nextstepaction"]
> [Hozzon létre, és az exportált adatok kezelése](tutorial-export-acm-data.md)
