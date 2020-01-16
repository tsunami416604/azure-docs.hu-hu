---
title: Oktatóanyag – Azure-költségvetések létrehozása és kezelése | Microsoft Docs
description: Ez az oktatóanyag segít megtervezni és felszámítani az Azure-szolgáltatások által felhasznált költségeket.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 12735a9575328e404f5723fa305448eb21ca85b2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993803"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Oktatóanyag: Azure-költségvetések létrehozása és kezelése

A Cost Management költségvetései segítenek a tervezésben, és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. Segítenek tájékoztatni másokat a kiadásairól, hogy proaktív módon kezeljék a költségeket, és megfigyeljük, hogy a kiadások hogyan haladnak előre. A létrehozott költségvetési küszöbértékek túllépése esetén csak az értesítések kerülnek aktiválásra. A rendszer egyik erőforrást sem érinti, és a felhasználás nem áll le. A költségkeretek segítségével összehasonlíthatja és nyomon követheti a kiadásokat a költségek elemzése során.

A költségek és a használati adatok jellemzően 12-16 órán belül elérhetők, és a költségvetések kiértékelése négy óránként történik. Az e-mailes értesítések általában 12-16 órán belül érkeznek.

A költségvetések automatikusan alaphelyzetbe állnak az adott időszak végén (havi, negyedéves vagy évenkénti), amikor a későbbiekben kijelöl egy lejárati dátumot. Mivel ugyanezek a költségvetési összeggel térnek vissza, külön költségvetést kell létrehoznia, ha a költségvetési pénznemek összege eltér a jövőbeli időszakokban.

Az oktatóanyagban szereplő példák végigvezetik egy Azure Nagyvállalati Szerződés-(EA-) előfizetés költségvetésének létrehozásán és szerkesztésén.

Tekintse meg a [költségkeretek alkalmazása az előfizetésekre a Azure Portal videó segítségével](https://www.youtube.com/watch?v=UrkHiUx19Po) , amelyből megtudhatja, hogyan hozhat létre költségvetést az Azure-ban a kiadások figyeléséhez.


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

További információ az engedélyek Cost Management adatokhoz való hozzárendeléséről: [hozzáférés Kiosztása Cost Management adatokhoz](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-budget-in-the-azure-portal"></a>Hozzon létre költségvetést a Azure Portal

Létrehozhat egy Azure-előfizetési költségkeretet egy havi, negyedéves vagy éves időszakra. Az Azure Portal navigációs tartalma attól függ, hogy előfizetéshez vagy felügyeleti csoporthoz hozza létre a költségvetést.

Költségvetés létrehozásához vagy megtekintéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza ki a **Költségvetések** lehetőséget a menüben. Például navigáljon az **előfizetések**elemre, válasszon ki egy előfizetést a listából, majd válassza a **költségvetés** lehetőséget a menüben. A **hatókör** pirulát használva váltson át egy másik hatókörre, például egy felügyeleti csoportra a költségvetésekben. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

A költségvetések létrehozása után egy egyszerű nézetben tekintheti meg a hozzájuk tartozó jelenlegi kiadásait.

Kattintson a **Hozzáadás** parancsra.

![A példa a már létrehozott költségvetések listáját mutatja](./media/tutorial-acm-create-budgets/budgets01.png)

Győződjön meg róla, hogy a **Költségvetés létrehozása** ablakban feltüntetett hatókör helyes. Válassza ki a hozzáadni kívánt szűrőket. A szűrők lehetővé teszik, hogy adott költségekre, például az előfizetésben lévő erőforráscsoportokkal, illetve egy szolgáltatással, például a virtuális gépekkel járó költségekre vonatkozóan hozzon létre költségvetést. A költségelemzések során használt szűrőket alkalmazhatja a költségvetésekre is.

Miután azonosította a hatókört és a szűrőket, gépelje be a költségvetés nevét. Ezután válassza ki a havi, negyedéves vagy éves költségvetési alaphelyzetbe állítási időszakot. Ez a visszaállítási időtartam határozza meg azt az időszakot, amelyet a költségvetés vizsgál. A költségvetés által kiértékelt költség minden új periódus elején nulláról kezdődik. A negyedéves költségvetés ugyanúgy működik, mint a havi költségvetés. A különbség az, hogy a negyedévre vonatkozó költségvetési összeg egyenlően oszlik meg a negyedév három hónapja között. Az éves költségvetési összeg a naptári év mind a 12 hónapja között oszlik meg egyenlően.

Ha használatalapú, MSDN- vagy Visual Studio-előfizetéssel rendelkezik, a számlázási időszak nem feltétlenül esik egybe a naptári hónapokkal. Az ilyen típusú előfizetésekhez és erőforráscsoportokhoz olyan költségvetést is létrehozhat, amely a számlázási időszakhoz vagy a naptári hónapokhoz igazodnak. A számlázási időszakhoz igazított költségvetés létrehozásához válassza ki a **Számlázási hónap**, a **Számlázási negyedév**vagy a **Számlázási év**alaphelyzetbe állításának időtartamát. A naptári hónapra igazított költségvetés létrehozásához válassza ki a **havi**, **negyedéves**vagy **évenkénti**alaphelyzetbe állítási időszakot.

Ezután határozza meg a lejárati dátumot, amikortól a költségvetés érvénytelenné válik, és leáll a költségek kiértékelése.

A költségvetésben eddig kiválasztott mezők alapján egy grafikon jelenik meg, amely segítséget nyújt a költségvetéshez használandó küszöbérték kiválasztásában. A javasolt költségvetés a jövőbeli időszakokban potenciálisan felmerülő legmagasabb becsült költségen alapul. Igény esetén módosíthatja a költségvetési összeget.

![Példa költségvetés létrehozására havi költségadatokkal ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

A költségvetés összegének konfigurálása után kattintson a **tovább** gombra a költségvetési riasztások konfigurálásához. Meg kell adnia legalább egy költségküszöböt (a költségvetés %-ában) és egy hozzá tartozó e-mail-címet. Igény szerint akár öt küszöbértéket és öt e-mail-címet is megadhat egyetlen költségvetéshez. Ha egy költségvetési küszöbérték teljesül, az e-mail-értesítések általában kevesebb mint 20 órát kapnak. További információ az értesítésekről: a [díjszabási riasztások használata](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md). Az alábbi példában egy e-mail-riasztás jön létre, amikor eléri a költségvetés 90%-át. Ha költségvetést hoz létre a költségkeretek API-val, szerepköröket rendelhet hozzá a felhasználókhoz a riasztások fogadásához. A szerepkörök nem támogatottak a Azure Portalban. Az Azure budgets API-val kapcsolatos további információkért lásd: [költségkeretek API](/rest/api/consumption/budgets).

![Riasztási feltételeket mutató példa](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Miután létrehozta a költségvetést, a rendszer megjeleníti annak költségelemzését. Ha megtekinti a költségkeretet a kiadások trendjeivel kapcsolatban, az egyik első lépés a [költségek és a kiadások elemzésének](../../cost-management/quick-acm-cost-analysis.md)megkezdése.

![Példa költségelemzésben kimutatott költségvetésre és kiadásokra](./media/tutorial-acm-create-budgets/cost-analysis.png)

Az előző példában létrehozott egy költségvetést egy előfizetéshez. Költségvetést azonban erőforráscsoportokhoz is létrehozhat. Ha egy erőforráscsoport számára szeretne költségvetést létrehozni, keresse meg **Cost Management + számlázási** &gt; **előfizetéseket** , &gt; válasszon ki egy előfizetést > **erőforráscsoportokat** > válasszon ki egy erőforráscsoportot > **költségvetést** >, majd **adjon hozzá** egy költségvetést.

## <a name="trigger-an-action-group"></a>Műveleti csoport elindítása

Amikor költségvetést hoz létre vagy módosít egy előfizetés vagy erőforráscsoport hatókörére vonatkozóan, konfigurálhatja úgy, hogy meghívjanak egy műveletcsoportot. A műveletcsoport különböző műveleteket hajthat végre a költségvetési küszöbérték elérésekor. A műveleti csoportok jelenleg csak az előfizetés és az erőforráscsoport-hatókörök esetében támogatottak. A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../../azure-monitor/platform/action-groups.md). A költségvetési alapú automatizálás műveleti csoportokkal való használatával kapcsolatos további információkért lásd: [költségek kezelése az Azure-költségvetésekkel](../manage/cost-management-budget-scenario.md).



A műveleti csoportok létrehozásához vagy frissítéséhez kattintson a **műveleti csoportok kezelése** lehetőségre a költségvetés létrehozásakor vagy szerkesztésekor.

![Példa egy költségvetés létrehozására a kezelési műveleti csoportok megjelenítéséhez](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Ezután kattintson a **műveleti csoport hozzáadása** és a műveleti csoport létrehozása elemre.


![A műveleti csoport hozzáadása mező képe](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

A műveleti csoport létrehozása után a mező bezárásával térjen vissza a költségvetésbe.

Konfigurálja úgy a költségvetést, hogy az egyéni küszöbérték teljesülése esetén a műveleti csoportot használja. Legfeljebb öt különböző küszöbérték támogatott.

![Példa a műveleti csoport kiválasztására riasztási feltételhez](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

Az alábbi példa a 50%, 75% és 100% értékű költségvetési küszöbértékeket mutatja. Mindegyik úgy van konfigurálva, hogy a kijelölt műveleti csoporton belül aktiválja a megadott műveleteket.

![Példa a különböző műveleti csoportokkal és a műveletek típusával konfigurált riasztási feltételekre](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

A műveleti csoportokkal való költségvetés-integráció csak olyan műveleti csoportok esetében működik, amelyeken le van tiltva a közös riasztási séma. További információ a séma letiltásáról: [hogyan az általános riasztási séma engedélyezése?](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre költségvetést a Azure Portal
> * Költségvetés szerkesztése

Folytassa a következő oktatóanyaggal, amellyel ismétlődő exportálást hozhat létre a Cost Management-adataihoz.

> [!div class="nextstepaction"]
> [Exportált adathalmazok létrehozása és kezelése](tutorial-export-acm-data.md)
