---
title: Metrikai riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával
description: Megtudhatja, hogyan hozhat létre, tekinthet meg és kezelhet metrikus riasztási szabályokat a Azure Portal vagy a parancssori felület használatával.
author: harelbr
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: 00f5f37591ed2ed250cb756c686ea15136921512
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705530"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Metrikai riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával

Az Azure Monitor metrikaalapú riasztásai lehetőséget nyújtanak arra, hogy értesítést kapjon, ha az egyik metrika átlép egy küszöbértéket. A metrikaalapú riasztások többdimenziós platformmetrikák, egyéni metrikák és az Application Insights standard és egyéni metrikáinak széles skálájával működnek. Ebben a cikkben leírjuk, hogyan hozhat létre, tekinthet meg és kezelhet metrikariasztási szabályokat az Azure Portalon és az Azure CLI-n keresztül. Metrikus riasztási szabályokat Azure Resource Manager sablonok használatával is létrehozhat, amelyeket [külön cikkben](alerts-metric-create-templates.md)ismertetünk.

További információ a metrikus riasztások működéséről a [metrikus riasztások áttekintése című](alerts-metric-overview.md)témakörben olvasható.

## <a name="create-with-azure-portal"></a>Létrehozás Azure Portal

Az alábbi eljárás azt ismerteti, hogyan hozhat létre metrikus riasztási szabályt a Azure Portalban:

1. [Azure Portal](https://portal.azure.com)kattintson a **figyelés**elemre. A figyelő panel egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.

2. Kattintson a **riasztások** , majd az **+ új riasztási szabály**elemre.

    > [!TIP]
    > A legtöbb erőforrás-panel a **figyelés**alatt is tartalmaz **riasztásokat** az erőforrás menüjében, és onnan is létrehozhat riasztásokat.

3. Kattintson a **cél kiválasztása**lehetőségre, a betöltött környezet ablaktáblán válassza ki azt a cél erőforrást, amelyre a riasztást szeretné használni. A figyelni kívánt erőforrás megtalálásához használja az **előfizetés** és az **Erőforrás típusa** legördülő listát. Az erőforrást a keresősáv használatával is megkeresheti.

4. Ha a kiválasztott erőforrás metrikákkal rendelkezik, akkor a jobb alsó sarokban található **elérhető jelek** tartalmazzák a metrikákat is. Ebben a [cikkben](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)a metrikus riasztásokhoz támogatott erőforrástípusok teljes listáját tekintheti meg.

5. A cél erőforrás kiválasztása után kattintson a **feltétel hozzáadása**elemre.

6. Megjelenik az erőforrás által támogatott jelek listája, majd válassza ki azt a metrikát, amelyhez riasztást kíván létrehozni.

7. Szükség esetén pontosíthatja a metrikát az **időszak** és az **Összesítés**beállításával. Ha a metrika dimenziókkal rendelkezik, megjelenik a **dimenziók** táblázat. Jelöljön ki egy vagy több értéket egy dimenzióban. A metrika riasztása lefuttatja a kiválasztott értékek összes kombinációjának feltételét. [További információ a többdimenziós metrikák működésével kapcsolatos riasztásokról](alerts-metric-overview.md). Bármelyik dimenzióhoz **\*is kiválaszthat** . A **\*kiválasztása** esetén a rendszer dinamikusan méretezi a kijelölést egy dimenzió összes aktuális és jövőbeli értékére.

8. Ekkor megjelenik egy diagram a metrikához az elmúlt 6 órában. A riasztás paramétereinek megadása; A **feltétel típusa**, **gyakorisága**, **operátora** és **küszöbértéke** vagy **érzékenysége**határozza meg azt a logikát, amelyet a metrika riasztási szabálya kiértékel. [További információ a dinamikus küszöbértékek feltételének típusáról és az érzékenységi lehetőségekről](alerts-dynamic-thresholds.md).

9. Ha statikus küszöbértéket használ, a mérőszám diagram segíthet meghatározni, hogy mi lehet az ésszerű küszöbérték. Dinamikus küszöbértékek használata esetén a metrika diagram a legutóbbi adatok alapján jeleníti meg a számított küszöbértékeket.

10. Kattintson a **Kész** gombra.

11. Ha szeretne egy összetett riasztási szabályt figyelni, vegyen fel további feltételeket. A felhasználók jelenleg egyetlen feltételként rendelkezhetnek dinamikus küszöbértékekkel rendelkező riasztási szabályokkal.

12. Adja meg a **riasztás részleteit** , például a **riasztási szabály nevét**, **leírását** és **súlyosságát**

13. Adjon hozzá egy műveleti csoportot a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.

14. Kattintson a **kész** gombra a metrika riasztási szabályának mentéséhez.

> [!NOTE]
> A portálon létrehozott metrikai riasztási szabályok ugyanabban az erőforráscsoporthoz jönnek létre, mint a célként megadott erőforrás.

## <a name="view-and-manage-with-azure-portal"></a>Megtekintés és kezelés a Azure Portal

A metrika riasztási szabályai a riasztások alatt a szabályok kezelése panelen tekinthetők meg és kezelhetők. Az alábbi eljárás bemutatja, hogyan tekintheti meg a metrikus riasztási szabályokat, és szerkesztheti az egyiket.

1. A Azure Portalban navigáljon a **figyeléshez**

2. Kattintson a **riasztások** elemre, és a **szabályok kezelése** lehetőségre

3. A **szabályok kezelése** panelen megtekintheti az összes riasztási szabályt az előfizetések között. A szabályokat az **erőforráscsoport**, az **Erőforrás típusa** és az **erőforrás**alapján is szűrheti. Ha csak metrikai riasztásokat szeretne látni, válassza a **jel típusa** mérőszámként lehetőséget.

    > [!TIP]
    > A **szabályok kezelése** panelen több riasztási szabályt is kijelölhet, és engedélyezheti vagy tilthatja le őket. Ez akkor lehet hasznos, ha bizonyos cél erőforrásokat a karbantartás alá kell helyezni

4. Kattintson a szerkeszteni kívánt metrikai riasztási szabály nevére

5. A szabály szerkesztése területen kattintson a szerkeszteni kívánt **riasztási feltételekre** . A mérőszámot, a küszöbértéket és az egyéb mezőket szükség szerint módosíthatja

    > [!NOTE]
    > A metrikai riasztás létrehozása után nem szerkesztheti a **cél erőforrás** és a **riasztási szabály nevét** .

6. A módosítások mentéséhez kattintson a **kész** gombra.

## <a name="with-azure-cli"></a>Az Azure CLI-vel

Az előző szakasz ismerteti, hogyan hozhat létre, tekinthet meg és kezelhet metrikus riasztási szabályokat a Azure Portal használatával. Ez a szakasz azt írja le, hogyan végezheti el ugyanezt a platformfüggetlen [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)használatával. Az Azure CLI használatának leggyorsabb módja a [Azure Cloud Shellon](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)keresztül. Ebben a cikkben a Cloud shellt fogjuk használni.

1. Lépjen Azure Portal a **Cloud Shell**elemre.

2. A parancssorban az ``--help`` lehetőséggel rendelkező parancsok használatával többet tudhat meg a parancsról és annak használatáról. A következő parancs például megjeleníti a metrikai riasztások létrehozásához, megtekintéséhez és kezeléséhez elérhető parancsok listáját.

    ```azurecli
    az monitor metrics alert --help
    ```

3. Létrehozhat egy egyszerű metrikai riasztási szabályt, amely figyeli, hogy egy virtuális gépen az átlagos CPU-érték nagyobb-e, mint 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Az erőforráscsoporthoz tartozó összes metrikai riasztást az alábbi paranccsal tekintheti meg

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Egy adott metrikai riasztási szabály részleteit a szabály nevével vagy erőforrás-azonosítójával tekintheti meg.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. A metrika riasztási szabályait a következő parancs használatával tilthatja le.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Az alábbi parancs használatával törölheti a metrika riasztási szabályait.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Következő lépések

- [Metrikai riasztások létrehozása Azure Resource Manager-sablonok használatával](../../azure-monitor/platform/alerts-enable-template.md).
- A [metrikai riasztások működésének megismerése](alerts-metric-overview.md).
- [Megtudhatja, hogyan működik a metrikák riasztásai a dinamikus küszöbértékek feltételével](alerts-dynamic-thresholds.md).
- [A webes Hook sémájának ismertetése metrikus riasztásokhoz](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

