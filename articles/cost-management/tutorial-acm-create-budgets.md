---
title: Oktatóanyag – Azure-költségvetések létrehozása és kezelése | Microsoft Docs
description: Ez az oktatóanyag segít megtervezni és felszámítani az Azure-szolgáltatások által felhasznált költségeket.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8c3c0574389fc9808af3cd70c928ede82d375076
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720728"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Oktatóanyag: Azure-költségvetések létrehozása és kezelése

A Cost Management költségvetései segítenek a tervezésben, és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. Segítenek tájékoztatni másokat a kiadásairól, hogy proaktív módon kezeljék a költségeket, és megfigyeljük, hogy a kiadások hogyan haladnak előre. A létrehozott költségvetési küszöbértékek túllépése esetén csak az értesítések kerülnek aktiválásra. A rendszer egyik erőforrást sem érinti, és a felhasználás nem áll le. A költségkeretek segítségével összehasonlíthatja és nyomon követheti a kiadásokat a költségek elemzése során.

A költségek és a használati adatok jellemzően 12-16 órán belül elérhetők, és a költségvetések kiértékelése négy óránként történik. Az e-mailes értesítések általában 12-16 órán belül érkeznek.

A költségvetések automatikusan alaphelyzetbe állnak az adott időszak végén (havi, negyedéves vagy évenkénti), amikor a későbbiekben kijelöl egy lejárati dátumot. Mivel ugyanezek a költségvetési összeggel térnek vissza, külön költségvetést kell létrehoznia, ha a költségvetési pénznemek összege eltér a jövőbeli időszakokban.

Az oktatóanyagban szereplő példák végigvezetik egy Azure Nagyvállalati Szerződés-(EA-) előfizetés költségvetésének létrehozásán és szerkesztésén.

Tekintse meg, [hogyan hozhat létre költségvetést a kiadások figyeléséhez Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) videóval, hogy megtudja, hogyan hozhat létre költségvetést az Azure-ban a kiadások figyeléséhez.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre költségvetést a Azure Portal
> * Költségvetés szerkesztése

## <a name="prerequisites"></a>Előfeltételek

A költségvetés számos különböző Azure-fióktípus esetében támogatott. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A költségvetések megtekintéséhez legalább olvasási hozzáféréssel kell rendelkeznie az Azure-fiókhoz.

 Azure EA-előfizetések esetén olvasási hozzáféréssel kell rendelkeznie a költségvetések megtekintéséhez. A költségvetések létrehozásához és kezeléséhez közreműködői engedéllyel kell rendelkeznie. Az EA-előfizetésekhez és az erőforráscsoportokhöz egyéni költségvetést is létrehozhat. Azonban nem hozhat létre költségvetést az EA számlázási fiókokhoz.

A következő Azure-engedélyek vagy-hatókörök a költségvetésre vonatkozó előfizetések szerint felhasználónként és csoportonként támogatottak. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő és Cost Management közreműködő – saját költségvetésüket hozhat létre, módosíthat vagy törölhet. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- Olvasó és Cost Management olvasó – megtekintheti azokat a költségvetéseket, amelyekre jogosultak.

További információ az engedélyek Cost Management adatokhoz való hozzárendeléséről: [hozzáférés Kiosztása Cost Management adatokhoz](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-budget-in-the-azure-portal"></a>Hozzon létre költségvetést a Azure Portal

Az Azure-előfizetések költségkeretét havi, negyedéves vagy éves időszakra is létrehozhatja. A Azure Portal navigációs tartalma határozza meg, hogy létrehoz-e költségvetést egy előfizetéshez vagy egy felügyeleti csoporthoz.

Ha költségvetést szeretne létrehozni vagy megtekinteni, nyissa meg a kívánt hatókört a Azure Portalban, és válassza a **költségvetés** lehetőséget a menüben. Például navigáljon az **előfizetések**elemre, válasszon ki egy előfizetést a listából, majd válassza a **költségvetés** lehetőséget a menüben. A **hatókör** pirulát használva váltson át egy másik hatókörre, például egy felügyeleti csoportra a költségvetésekben. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

A költségvetések létrehozása után a rendszer egyszerű képet jelenít meg a jelenlegi kiadásairól.

Kattintson az **Add** (Hozzáadás) parancsra.

![A már létrehozott költségvetések listáját bemutató példa](./media/tutorial-acm-create-budgets/budgets01.png)

A **költségvetés létrehozása** ablakban ellenőrizze, hogy helyes-e a hatókör. Válassza ki a hozzáadni kívánt szűrőket. A szűrők lehetővé teszik, hogy bizonyos költségekkel, például az előfizetésben lévő erőforráscsoportok, vagy egy szolgáltatás, például a virtuális gépek számára hozzon létre költségvetést. A Cost Analysis szolgáltatásban használható szűrőket a költségvetésre is lehet alkalmazni.

Miután azonosította a hatókört és a szűrőket, írja be a költségvetés nevét. Ezután válassza ki a havi, negyedéves vagy éves költségvetési alaphelyzetbe állítási időszakot. Ez az alaphelyzetbe állítási időszak határozza meg a költségvetés által elemzett időintervallumot. A költségvetés által kiértékelt költségek minden új időszak elején nullával kezdődnek. Negyedéves költségvetés létrehozásakor a havi költségkerettel megegyező módon működik. A különbség az, hogy a negyedévre vonatkozó költségvetési összeg egyenlően oszlik meg a negyedév három hónapja között. Az éves költségvetési összeg a naptári év 12 hónapja között egyenlően oszlik meg.

Ha utólagos elszámolású, MSDN-vagy Visual Studio-előfizetéssel rendelkezik, előfordulhat, hogy a számla számlázási időszaka nem a naptári hónaphoz igazodik. Az előfizetési típusok és erőforráscsoportok esetében létrehozhat egy költségvetést, amely a számlázási időszakhoz vagy a naptári hónapokhoz van igazítva. A számlázási időszakhoz igazított költségvetés létrehozásához válassza ki a **Számlázási hónap**, a **Számlázási negyedév**vagy a **Számlázási év**alaphelyzetbe állításának időtartamát. A naptári hónapra igazított költségvetés létrehozásához válassza ki a **havi**, **negyedéves**vagy **évenkénti**alaphelyzetbe állítási időszakot.

Ezután azonosítsa a lejárati dátumot, amikor a költségvetés érvénytelenné válik, és leállítja a költségek kiértékelését.

Az eddigi költségvetésben kiválasztott mezők alapján egy gráf jelenik meg, amely segítséget nyújt a költségkerethez használandó küszöbérték kiválasztásában. A javasolt költségvetés a jövőbeli időszakokban felmerülő legmagasabb becsült költségen alapul. Módosíthatja a költségvetés összegét.

![Példa a költségvetés létrehozására havi költségadatok szerint ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

A költségvetés összegének konfigurálása után kattintson a **tovább** gombra a költségvetési riasztások konfigurálásához. A költségvetésekhez legalább egy költségek küszöbértéke (a költségvetés%-ában) és a hozzá tartozó e-mail-cím szükséges. Akár öt küszöbértéket és öt e-mail-címet is beállíthat egyetlen költségvetésben. Ha egy költségvetési küszöbérték teljesül, az e-mail-értesítések általában kevesebb mint 20 órát kapnak. További információ az értesítésekről: a [díjszabási riasztások használata](cost-mgt-alerts-monitor-usage-spending.md). Az alábbi példában egy e-mail-riasztás jön létre, amikor eléri a költségvetés 90%-át. Ha költségvetést hoz létre a költségkeretek API-val, szerepköröket rendelhet hozzá a felhasználókhoz a riasztások fogadásához. A szerepkörök nem támogatottak a Azure Portalban. Az Azure budgets API-val kapcsolatos további információkért lásd: [költségkeretek API](/rest/api/consumption/budgets).

![Riasztási feltételeket mutató példa](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

A költségvetés létrehozása után az megjelenik a Cost Analysis szolgáltatásban. Ha megtekinti a költségkeretet a kiadások trendjeivel kapcsolatban, az egyik első lépés a [költségek és a kiadások elemzésének](quick-acm-cost-analysis.md)megkezdése.

![Példa a költségek elemzésében bemutatott költségvetésre és kiadásokra](./media/tutorial-acm-create-budgets/cost-analysis.png)

Az előző példában egy előfizetéshez tartozó költségvetést hozott létre. Egy erőforráscsoport esetében azonban létrehozhat egy költségvetést is. Ha egy erőforráscsoport számára szeretne költségvetést létrehozni, keresse meg **Cost Management + számlázási** &gt; **előfizetéseket** &gt; válasszon ki egy előfizetést > **erőforráscsoportot** > válasszon ki egy erőforráscsoportot > **költségvetést** > majd **Adja hozzá** a költségvetést.

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

A műveleti csoportokkal való költségvetés-integráció csak olyan műveleti csoportok esetében működik, amelyeken le van tiltva a közös riasztási séma. További információ a séma letiltásáról: [hogyan az általános riasztási séma engedélyezése?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre költségvetést a Azure Portal
> * Költségvetés szerkesztése

Folytassa a következő oktatóanyaggal, amellyel ismétlődő exportálást hozhat létre a Cost Management-adataihoz.

> [!div class="nextstepaction"]
> [Exportált adathalmazok létrehozása és kezelése](tutorial-export-acm-data.md)
