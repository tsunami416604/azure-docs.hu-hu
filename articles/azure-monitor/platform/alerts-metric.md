---
title: Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával
description: Ismerje meg, hogyan hozhat létre, tekinthet meg és kezelhet metrikariasztási szabályokat az Azure Portal vagy a CLI használatával.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 03/13/2020
ms.subservice: alerts
ms.openlocfilehash: cefccd08ea66638f08f00e280fe2704444a7f916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369386"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral

Metrikariasztások az Azure Monitor ban leadott értesítés, ha az egyik metrikák átlépi a küszöbértéket. A metrikaalapú riasztások többdimenziós platformmetrikák, egyéni metrikák és az Application Insights standard és egyéni metrikáinak széles skálájával működnek. Ebben a cikkben bemutatjuk, hogyan hozhat létre, tekinthet meg és kezelhet metrikariasztási szabályokat az Azure Portalon és az Azure CLI-n keresztül. Metrikariasztási szabályokat is létrehozhat az Azure Resource Manager-sablonok használatával, [amelyeket egy külön cikkben ismertetett.](alerts-metric-create-templates.md)

További információ ametrikariasztások működéséről a [metrikariasztások áttekintéséből.](alerts-metric-overview.md)

## <a name="create-with-azure-portal"></a>Létrehozás az Azure Portalon

Az alábbi eljárás ismerteti, hogyan hozhat létre metrikariasztási szabályt az Azure Portalon:

1. Az [Azure Portalon](https://portal.azure.com)kattintson a **Figyelő**elemre. A Monitor panel egyetlen nézetben összesíti az összes figyelési beállítást és adatot.

2. Kattintson **a Riasztások,** majd **a + Új riasztási szabály**elemre.

    > [!TIP]
    > A legtöbb erőforrás-panelek is rendelkezik **riasztások az** erőforrás menüben **figyelése,** akkor létrehozhat riasztásokat onnan is.

3. Kattintson **a Cél kiválasztása**gombra a betöltődő környezet ablaktáblán válassza ki azt a célerőforrást, amelyről figyelmeztetni szeretne. Az **Előfizetés és** erőforrás **típusa** legördülő menüvel megkeresheti a figyelni kívánt erőforrást. A keresősáv segítségével is megkeresheti az erőforrást.

4. Ha a kiválasztott erőforrás rendelkezik mérőszámokkal, amelyekriasztásokat hozhat létre, a jobb alsó sarokban **elérhető jelek** mérőszámokat tartalmaznak. Ebben a [cikkben](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)megtekintheti a metrikariasztásokhoz támogatott erőforrástípusok teljes listáját.

5. Miután kiválasztottegy célerőforrást, kattintson a **Feltétel hozzáadása**gombra.

6. Megjelenik az erőforrás által támogatott jelek listája, válassza ki azt a metrikát, amelyről riasztást szeretne létrehozni.

7. Az elmúlt hat óra mérőszámdiagramja jelenik meg. A **Diagram időszak** legördülő kiválasztásához válassza ki, hogy hosszabb előzményeket tekintsen meg a metrikában.

8. Ha a metrika dimenziókkal rendelkezik, megjelenik a bemutatott dimenziótábla. Dimenziónként egy vagy több érték kijelölése.
    - A megjelenített dimenzióértékek az elmúlt három nap metrikai adatain alapulnak.
    - Ha a keresett dimenzióérték nem jelenik meg, egyéni érték hozzáadásához kattintson a "+" gombra.
    - A dimenziók bármelyikéhez **is választhat. \* ** **A \* kijelölés** dinamikusan méretezi a kijelölést egy dimenzió összes aktuális és jövőbeli értékére.

    A metrikariasztási szabály kiértékeli a kiválasztott értékek összes kombinációjának állapotát. [További információ a többdimenziós mutatók riasztásának működéséről.](alerts-metric-overview.md)

9. Válassza ki a **Küszöbérték,** **operátor**és **Aggregációs típust.** Ez határozza meg a metrikariasztási szabály által kiértékelt logikát.
    - **Statikus** küszöbérték használata esetén továbbra is adjon meg **egy Küszöbértéket.** A metrikadiagram segíthet meghatározni, hogy mi lehet egy ésszerű küszöbérték.
    - **Dinamikus** küszöbérték használata esetén továbbra is adja meg a **Küszöbérték érzékenységét.** A metrikadiagram a számított küszöbértékeket jeleníti meg a legutóbbi adatok alapján. [További információ a dinamikus küszöbértékek feltételtípusáról és érzékenységi beállításairól.](alerts-dynamic-thresholds.md)

10. Szükség esetén finomíthatja az állapotot az **összesítés részletességének** és **az értékelés gyakoriságának**módosításával. 

11. Kattintson a **Done** (Kész) gombra.

12. Ha egy összetett riasztási szabályt szeretne figyelni, adjon hozzá egy másik feltételt is. Jelenleg a felhasználók rendelkezhetnek riasztási szabályok dinamikus küszöbértékek feltételek egyetlen feltételként.

13. Töltse ki a **riasztás részleteit,** például a **riasztási szabály nevét**, a **leírást**és **a súlyossági szintet.**

14. Adjon hozzá egy műveletcsoportot a riasztáshoz egy meglévő műveletcsoport kijelölésével vagy egy új műveletcsoport létrehozásával.

15. Kattintson a **Kész** gombra a metrikariasztási szabály mentéséhez.

> [!NOTE]
> A portálon keresztül létrehozott metrikariasztási szabályok ugyanabban az erőforráscsoportban jönnek létre, mint a célerőforrás.

## <a name="view-and-manage-with-azure-portal"></a>Megtekintés és kezelés az Azure Portalon

A metrikariasztási szabályokat a Szabályok kezelése panelen, a Riasztások csoportban tekintheti meg és kezelheti. Az alábbi eljárás bemutatja, hogyan tekintheti meg a metrikariasztási szabályokat, és hogyan szerkesztheti az egyiket.

1. Az Azure Portalon keresse meg a **Figyelő**

2. Kattintson a Riasztások és **a Szabályok kezelése gombra** **Alerts**

3. A **Szabályok kezelése** panelen megtekintheti az összes riasztási szabályok előfizetések között. A szabályokat tovább szűrheti az **Erőforráscsoport**, **Az Erőforrás típusa**és az **Erőforrás**csoport használatával. Ha csak metrikariasztásokat szeretne látni, válassza a **Jeltípus** tmetrikaként lehetőséget.

    > [!TIP]
    > A **Szabályok kezelése** panelen több riasztási szabályt is kiválaszthat, és engedélyezheti/letilthatja azokat. Ez akkor lehet hasznos, ha bizonyos célerőforrásokat karbantartás alá kell helyezni

4. Kattintson a szerkesztendő metrikariasztási szabály nevére

5. A Szerkesztési szabályban kattintson a szerkesztésre kívánt **riasztási feltételekre.** Szükség szerint módosíthatja a metrikát, a küszöbértéket és más mezőket.

    > [!NOTE]
    > A metrikariasztás létrehozása után nem szerkesztheti a **célerőforrást** és a **riasztási szabály nevét.**

6. A szerkesztésmentéshez kattintson a **Kész** gombra.

## <a name="with-azure-cli"></a>Az Azure CLI-vel

Az előző szakaszok ismertetik, hogyan hozhat létre, tekintheti meg és kezelheti a metrikariasztási szabályokat az Azure Portal használatával. Ez a szakasz bemutatja, hogyan kell ugyanezt a platformfüggetlen [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)használatával. Az Azure CLI használatának leggyorsabb megkezdésére az Azure Cloud Shell szolgáltatás a [dajka.](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) Ebben a cikkben a Cloud Shell-t fogjuk használni.

1. Nyissa meg az Azure Portalt, kattintson a **Cloud Shell**elemre.

2. A parancssorban a parancsokkal ``--help`` együtt további tudnivalókat olvashat a parancsról és használatáról. A következő parancs például a metrikariasztások létrehozására, megtekintésére és kezelésére szolgáló parancsok listáját jeleníti meg.

    ```azurecli
    az monitor metrics alert --help
    ```

3. Létrehozhat egy egyszerű metrikariasztási szabályt, amely figyeli, hogy a virtuális gépek átlagos százalékos processzora nagyobb-e, mint 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Az erőforráscsoport összes metrikariasztását megtekintheti a következő paranccsal

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Egy adott metrikariasztási szabály részleteit a szabály nevével vagy erőforrás-azonosítójával láthatja.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. A metrikariasztási szabályt a következő paranccsal tilthatja le.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. A metrikariasztási szabályt a következő paranccsal törölheti.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>További lépések

- [Metrikariasztásokat hozhat létre az Azure Resource Manager-sablonokkal.](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Ismerje meg, hogyan működnek a metrikariasztások.](alerts-metric-overview.md)
- [Ismerje meg, hogyan működnek a dinamikus küszöbértékek feltételsel rendelkező metrikariasztások.](alerts-dynamic-thresholds.md)
- [Ismerje meg a metrikariasztások webes hook-sémáját](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

