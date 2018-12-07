---
title: Oktatóanyag – létrehozása és kezelése az Azure költségvetése |} A Microsoft Docs
description: Ez az oktatóanyag segít tervet és optimalizáljuk az Azure-szolgáltatások felhasznált fiók.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: f64a82cc3096c3c4d2a47568f4d6d516c685ee04
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52991753"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Oktatóanyag: Létrehozása és kezelése az Azure költségvetése

A Cost Management költségvetései segítenek a tervezésben és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. Ezek segítenek mások tájékoztatja a költségkeret-beállítási proaktív módon kezelheti a költségeket, és figyelheti a költségkeret-beállítási hogyan halad idővel. Ha a létrehozott költségvetés küszöbérték túllépése esetén csak az értesítések aktiválódnak. Az erőforrások egyike sem érintett, és a használat nem leállt. Költségvetése segítségével nyomon követheti a Költekezési, a költségek elemzése és összehasonlítása.

A költségvetéshez automatikusan átállítani (havonta, negyedévente vagy évente) időszak végén a költségvetés akkora lejárati dátumot a jövőben kiválasztásakor. A költségvetés akkora alaphelyzetbe állítása, mert kell létrehoznia a különálló költségvetéseket, ha a tervezett pénznem összegek különböznek a későbbi elszámolási időszakokra.

Ebben az oktatóanyagban szereplő példák végigvezetik létrehozása és módosítása az Azure nagyvállalati szerződés (EA) előfizetésre költségvetést.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetési szerkesztése

## <a name="prerequisites"></a>Előfeltételek

Költségvetése minden Azure nagyvállalati szerződéssel rendelkező ügyfelek számára érhetők el. Költségvetések megtekintése az Azure nagyvállalati szerződéssel rendelkező előfizetés olvasási hozzáféréssel kell rendelkeznie. Hozhat létre és kezelheti költségvetése közreműködő engedéllyel kell rendelkeznie. Az EA-előfizetések és -erőforráscsoportok egyes költségvetése hozhat létre. Azonban a nagyvállalati szerződéssel rendelkező fiókok számlázási költségvetése nem hozható létre.

A következő Azure-engedélyeket felhasználó és csoport által támogatott költségvetése előfizetésenként:

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő, és a Cost Management közreműködője – létrehozása, módosítása vagy törlése a saját költségvetése. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- Olvasó és a Cost Management olvasó – megtekintheti a kiadásoktól, amelyek engedéllyel rendelkeznek.

Engedélyek hozzárendelése a Cost Management adataihoz kapcsolatos további információkért lásd: [Cost Management adataihoz való hozzáférés hozzárendelése](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-budget-in-the-azure-portal"></a>Költségvetés létrehozása az Azure Portalon

Létrehozhat egy Azure-előfizetés költségvetés havi, negyedéves és éves időszakra. A navigációs tartalom az Azure Portalon határozza meg, hogy költségvetés-előfizetéssel, vagy egy erőforráscsoport létrehozása.

Az Azure Portalon lépjen **Költségkezelés + számlázás** &gt; **előfizetések** &gt; válasszon ki egy előfizetést &gt; **költségvetése**. Ebben a példában a költségvetést, hoz létre a kiválasztott előfizetéshez van.

Után a költségvetés hoz létre, azok őket az aktuális költségeket az egyszerű nézet jelenik meg.

Kattintson a **Hozzáadás** parancsra.

![A Cost Management költségvetése](./media/tutorial-acm-create-budgets/budgets01.png)

Az a **létrehozás költségvetés** ablakban adja meg a költségvetés és a költségvetés összege. Ezután válassza ki egy havi, negyedéves, vagy egy éves időtartama. Ezután válassza ki a befejező dátum. Költségvetés legalább egy költséget küszöb (% költségvetés) és a egy megfelelő e-mail-címre van szükség. Szükség esetén belefoglalhatja a legfeljebb öt küszöbértékeket és a egy egyetlen költségvetésben öt e-mail címet. Amikor teljesül a költségvetés küszöbértéket, e-mail-értesítések általában kevesebb mint nyolc óra fogadott.

Íme egy példa egy havi költségvetés hoz létre az $4500. E-mailek beolvasása riasztás 90 %-a költségvetés elérésekor.

![Havi költségvetés-példa](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Negyedéves költségvetési létrehozásakor az havi költségvetési azonos módon működik. A különbség az, hogy a költségvetési összeg a negyedév egyenletesen vannak osztva a három hónapos negyedév. Ahogy, éves költségvetés összeg egyenletesen osztva az év minden 12 hónapig.

Támasztott költségeket aktuális frissül, amikor a Cost Management fogadja a frissített számlázási adatokat. Általában, naponta.

![Jelenlegi támasztott költségeket](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Miután létrehozta a költségvetést, a költségek elemzése jelenik meg. Megtekintés a költségvetést, a költési trend viszonyítva az egyik az első lépéseket megkezdésekor [elemezheti a költségeket, és a költségkeret-beállítási](quick-acm-cost-analysis.md).

![Költségvetés látható költségelemzés](./media/tutorial-acm-create-budgets/cost-analysis.png)

Az előző példában létrehozott egy költségvetés-előfizetéssel. Azonban egy erőforráscsoporthoz költségvetési is létrehozhat. Ha azt szeretné, hozzon létre egy erőforráscsoportot a költségvetést, lépjen **Költségkezelés + számlázás** &gt; **előfizetések** &gt; válasszon ki egy előfizetést > **erőforrás csoportok** > Válasszon ki egy erőforráscsoportot > **költségvetése** >, majd **Hozzáadás** költségvetés keretében.

## <a name="edit-a-budget"></a>Költségvetési szerkesztése

Függően a hozzáférési szintet, amelyet rendelkezik szerkesztheti a költségvetési tulajdonságainak módosításához. A következő példában az egyes tulajdonságok írásvédettek, mert a felhasználó csak az előfizetés közreműködő engedéllyel rendelkezik. Jelenleg a **lejárati dátum** le van tiltva, és a beállítása után nem módosítható.

![Költségvetés – közreműködői engedélye szerkesztése](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetési szerkesztése

Folytassa a következő oktatóanyag a költségkezelési adatokhoz olyan ismétlődő exportálásának létrehozásához.

> [!div class="nextstepaction"]
> [Hozzon létre, és az exportált adatok kezelése](tutorial-export-acm-data.md)
