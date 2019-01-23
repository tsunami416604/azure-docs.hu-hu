---
title: Létrehozása, megtekintése és kezelése a metrika riasztások használata az Azure Monitor
description: Ismerje meg, hogyan létrehozása, megtekintése és kezelése a metrikaalapú riasztási szabályok az Azure portal vagy a parancssori felület használatával.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: d871cecda8e7957e3d0d3c37078b8a1fa2ab5214
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451255"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Létrehozása, megtekintése és kezelése az Azure Monitor használatával metrikákhoz kapcsolódó riasztások

Az Azure monitorban metrikákhoz kapcsolódó riasztások hardvermódosításainak értesítést kaphat, amikor egy, a mérőszámok átlépnek egy küszöbértéket. Metrikákhoz kapcsolódó riasztások platform többdimenziós metrikák, egyéni metrikákat, az Application Insights standard és egyéni mérőszámok széles működik. Ebben a cikkben azt azt ismerteti, hogyan hozhat létre, megtekintése és kezelése a metrikaalapú riasztási szabályok az Azure portal és az Azure CLI használatával. Metrikaalapú riasztási szabályok ismertetett Azure Resource Manager-sablonok használatával is létrehozhat [egy külön cikk](../../azure-monitor/platform/alerts-enable-template.md).

Több hogyan metrika kapcsolatos riasztások dolgozhassanak további [metrika riasztások – áttekintés](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Hozzon létre az Azure Portalon

Az alábbi eljárás ismerteti, hogyan lehet a metrikaalapú riasztási szabály létrehozása az Azure Portalon:

1. A [az Azure portal](https://portal.azure.com), kattintson a **figyelő**. A Monitor panel összesíti az összes figyelési beállítást és adatokat egyetlen nézetben.

2. Kattintson a **riasztások** kattintson **+ Új riasztási szabály**.

    > [!TIP]
    > A legtöbb erőforráspanelek is, hogy **riasztások** azok erőforrás menüben alatt **figyelés**, létrehozhat riasztásokat onnan.

3. Kattintson a **cél kiválasztása**, környezet, amely betölti panelén válassza a célként megadott erőforrás-riasztás kívánt. Használat **előfizetés** és **erőforrástípus** legördülő listákból a figyelni kívánt erőforrás található. A Keresősáv használatával keresse meg az erőforrást.

4. Ha a kiválasztott erőforrás riasztásokat is létrehozhat, a metrikák **elérhető jelek** aljának jobb metrikákat tartalmazza. Metrikákhoz kapcsolódó riasztások a támogatott erőforrástípusok teljes listáját megtekintheti [cikk](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Miután kiválasztotta a célként megadott erőforrás, kattintson a **feltétel hozzáadása**.

6. Most támogatott az erőforrás jelek listájának megtekintéséhez, válassza ki a riasztást hoznak létre a kívánt metrikát.

7. Szükség esetén pontosíthatja a metrika módosításával **időszak** és **összesítési**. Ha a metrika dimenziókat, látni fogja **dimenziók** bemutatott táblázatban. Válassza ki egy vagy több értéket dimenziónként. A metrikariasztás fog futni a kiválasztott értékek összes kombinációjához feltétel kiértékeléséhez. [További információ arról, hogyan működik a többdimenziós metrikák riasztási](alerts-metric-overview.md). Emellett **kiválasztása \***  a dimenziók bármelyikéhez. **Válassza ki \***  a kijelölést a dimenzió összes jelenlegi és jövőbeli érték méretezési csoport dinamikus lesz.

8. Az elmúlt 6 óra látni fogja a metrika egy diagram. A riasztási paramétereit; **Feltétel típusa**, **gyakorisága**, **operátor** és **küszöbérték** vagy **érzékenységi**, ezzel határozza meg, amelyek kiértékelik a metrikaalapú riasztási szabály a logikai. [Dinamikus küszöbérték feltételt és érzékenységi lehetőségekkel kapcsolatos további](alerts-dynamic-thresholds.md).

9. Ha statikus küszöbérték használ, a metrikadiagram is meghatározásához, hogy mi lehet egy ésszerű küszöbértéket. Dinamikus küszöbérték használja, ha a metrikadiagram jelenik meg a legújabb adatok alapján számított küszöbértékeket.

10. Kattintson a **Kész** gombra.

11. Opcionálisan adja hozzá másik szempontot, ha figyelemmel szeretné követni egy összetett riasztási szabályt. Jelenleg felhasználók lehetnek dinamikus küszöbértékek feltételekkel riasztási szabályok egy feltételt.

12. Töltse ki **riasztás részletei** például **riasztási szabály neve**, **leírás** és **súlyosság**

13. Műveletcsoport hozzáadása a riasztást, vagy egy új műveletcsoport létrehozása vagy meglévő műveletcsoport kiválasztása.

14. Kattintson a **kész** a metrikaalapú riasztási szabály mentéséhez.

> [!NOTE]
> Metrikariasztás portálon keresztül létrehozott szabályok jönnek létre a célként megadott erőforrás azonos erőforráscsoportban.

## <a name="view-and-manage-with-azure-portal"></a>Megtekintése és kezelése az Azure Portalon

Megtekintheti és kezelheti a riasztásokat szabályok kezelése panel használatával metrikariasztási szabályokra. Az alábbi eljárás bemutatja, hogyan a metrikaalapú riasztási szabályok megtekintése és szerkesztése, az egyiket.

1. Az Azure Portalon lépjen **figyelője**

2. Kattintson a **riasztások** és **szabályok kezelése**

3. Az a **szabályok kezelése** panelen megtekintheti a riasztási szabályok előfizetések között. A szabályok használatával tovább szűrheti **erőforráscsoport**, **erőforrástípus** és **erőforrás**. Ha meg szeretné tekinteni a csak metrikákhoz kapcsolódó riasztások, válassza ki a **típus jelezze** mérőszámokként.

    > [!TIP]
    > Az a **szabályok kezelése** panelen kiválaszthat több riasztási szabályok és azok engedélyezését vagy letiltását. Ez akkor lehet hasznos, ha bizonyos célerőforrások kell karbantartás alatt

4. Kattintson a szerkeszteni kívánt a metrikaalapú riasztási szabály neve

5. A szabály szerkesztése, kattintson a **riasztási feltételek** szerkesztéséhez. Módosíthatja a mérőszám, a küszöbérték feltétel és a további szükséges mezők

    > [!NOTE]
    > Nem lehet szerkeszteni a **Célerőforrásnál** és **riasztás szabálynév** a metrikariasztás létrehozása után.

6. Kattintson a **kész** a módosítások mentéséhez.

## <a name="with-azure-cli"></a>Az Azure CLI-vel

A fentebbi szakaszokban leírt létrehozása, megtekintése és kezelése a metrikaalapú riasztási szabályok az Azure portal használatával. Ez a szakasz azt ismerteti, hogyan végezze el ugyanezt a platformok közötti átjárhatóságról használatával való [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Leggyorsabb módja, hogy megkezdhesse az Azure CLI-vel van keresztül [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Ebben a cikkben a Cloud shell használjuk.

1. Ugrás az Azure Portalon, kattintson a **Cloud shell**.

2. A parancssorba a következő parancsokat használhatja ``--help`` tudhat meg többet a parancsot, és hogyan használható a beállítást. Ha például a következő parancs listáját jeleníti meg, létrehozása, megtekintése és kezelése metrikákhoz kapcsolódó riasztások esetén használható parancsokat

    ```azurecli
    az monitor metrics alert --help
    ```

3. Létrehozhat egy egyszerű a metrikaalapú riasztási szabály, amely figyeli, ha a virtuális gép átlagos százalékos Processzorhasználat nagyobb, mint 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Egy erőforráscsoport, a következő parancs használatával megtekintheti a metrikákhoz kapcsolódó riasztások

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Láthatja, hogy egy adott a metrikaalapú riasztási szabály nevét, vagy a szabály az erőforrás-azonosítója használatával részleteit.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. A következő parancsot a metrikaalapú riasztási szabály is letilthatja.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. A metrikaalapú riasztási szabály a következő paranccsal törölheti.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>További lépések

- [Hozzon létre az Azure Resource Manager-sablonok használatával metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-enable-template.md).
- [Megismerheti, hogyan riasztást küld a metrika a munkahelyi](alerts-metric-overview.md).
- [Megismerheti, hogyan metrika riasztások küszöbértékeit a dinamikus feltétel munkahelyi](alerts-dynamic-thresholds.md).
- [A web hook sémáját metrikákhoz kapcsolódó riasztások ismertetése](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

