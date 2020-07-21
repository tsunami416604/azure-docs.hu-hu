---
title: 'Oktatóanyag: szabályok konfigurálása motor – Azure bejárati ajtó'
description: Ez a cikk bemutatja, hogyan konfigurálhatja a szabályok motorját a Azure Portal és a parancssori felületen.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: b40bb0e426571acc66d4f5f1b992fb4c6b67494a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536952"
---
# <a name="configure-your-rules-engine"></a>A szabályok motor konfigurálása

Ez a cikk a szabályok motor konfigurációjának és az első szabálynak a Azure Portal és a CLI-ben való létrehozásának lépéseit ismerteti. 

## <a name="configure-rules-engine-in-azure-portal"></a>Szabályok konfigurálása motor a Azure Portal
1. A szabályok motorja konfigurációjának létrehozása előtt [hozzon létre egy bejárati ajtót](quickstart-create-front-door.md).

2. A bejárati ajtó erőforrásában lépjen a **Beállítások** elemre, és válassza a **szabály motor konfigurálása**lehetőséget. Kattintson a **Hozzáadás**gombra, adja meg a konfiguráció nevét, és kezdje el létrehozni az első szabályok motor konfigurációját.

    ![szabályok keresése motor](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Az első szabály létrehozásához kattintson a **szabály hozzáadása** elemre. Ezután a **feltétel hozzáadása** vagy a **művelet hozzáadása** lehetőségre kattintva megadhatja a szabályt.
    
    > [!NOTE]
    >- Ha törölni szeretne egy feltételt vagy műveletet a szabályból, használja a szemetet az adott feltétel vagy művelet jobb oldalán.
    > - Egy olyan szabály létrehozásához, amely az összes bejövő forgalomra vonatkozik, ne határozzon meg feltételeket.
    > - Ha meg szeretné szüntetni a szabályok kiértékelését az első egyeztetési feltétel teljesülése után, akkor a **hátralévő szabály kiértékelésének leállítása**gombra kell bejelentkeznie. Ha be van jelölve, és egy adott szabály összes egyeztetési feltétele teljesül, a konfigurációban maradó többi szabály nem lesz végrehajtva.  

    ![szabályok keresése motor](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

4. Határozza meg a konfiguráción belüli szabályok prioritását a feljebb, a lejjebb, és a felülre lépés gombokkal. A prioritás növekvő sorrendben van megadva, ami azt jelenti, hogy az elsőként felsorolt szabály a legfontosabb szabály.

5. Miután létrehozott egy vagy több szabályt, nyomja meg a **Mentés**gombot. Ez a művelet létrehozza a szabályok motorjának konfigurációját.

6. Miután létrehozott egy vagy több konfigurációt, társítsa a szabályok motorjának konfigurációját egy útválasztási szabállyal. Habár egy konfiguráció számos útválasztási szabályra alkalmazható, az útválasztási szabályok csak egy szabály-végrehajtó konfigurációt tartalmazhatnak. A társítás létrehozásához nyissa meg a bevezető **ajtó tervezői**  >  **útvonalának szabályait**. Válassza ki azt az útválasztási szabályt, amelyhez hozzá szeretné adni a szabályok motorjának konfigurációját, lépjen a **Route details**  >  **Rules Engine Configuration**elemre, és válassza ki a hozzárendelni kívánt konfigurációt.

    ![szabályok keresése motor](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Szabályok konfigurálása motor az Azure CLI-ben

1. Ha még nem tette meg, telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t. Adja hozzá az "előtérben" kiterjesztést: az Extension Add--Name bejárati ajtót. Ezután jelentkezzen be, és váltson az előfizetésre az az Account set--előfizetés <name_or_Id>.

2. Első lépésként hozzon létre egy szabályt motort – ez a példa egy, a fejléc-alapú művelettel és egy egyeztetési feltétellel rendelkező szabályt mutat be. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

3. Sorolja fel az összes szabályt. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

4. Adja meg a továbbítási útvonal felülbírálási műveletét. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

5. Egy szabály összes műveletének listázása. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

6. Szabály-végrehajtói konfiguráció csatolása útválasztási szabályhoz.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

7. Szabályok leválasztása motor. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

További információkért tekintse meg az AFD Rules Engine parancsainak teljes listáját [itt](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest).   

## <a name="next-steps"></a>Következő lépések

- További információ a [AFD-szabályok motorról](front-door-rules-engine.md). 
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
- További részletekért tekintse meg a AFD-szabályok motor [CLI-referenciáját](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest). 
- További részletekért tekintse meg a AFD-szabályok motor [PowerShell-referenciáját](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
