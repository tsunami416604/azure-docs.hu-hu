---
title: Azure bejárati ajtó | Microsoft Docs
description: Ez a cikk az Azure Front Doorról nyújt áttekintést. Megtudhatja, hogy a megfelelő választás-e a felhasználói forgalom terheléselosztásához az alkalmazáshoz.
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
ms.openlocfilehash: 4574597c0b93f2985953bfbc815cca220ecc4f28
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515600"
---
# <a name="configure-your-rules-engine"></a>A szabályok motor konfigurálása 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="configure-rules-engine-in-azure-portal"></a>Szabályok konfigurálása motor a Azure Portal 
1. A szabályok motorja konfigurációjának létrehozása előtt [hozzon létre egy bejárati ajtót](quickstart-create-front-door.md).

2. A bejárati ajtó erőforrásában lépjen a **Beállítások** elemre, és válassza a **szabály motor konfigurálása**lehetőséget. Kattintson a **Hozzáadás**gombra, adja meg a konfiguráció nevét, és kezdje el létrehozni az első szabályok motor konfigurációját. 

![szabályok keresése motor](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Az első szabály létrehozásához kattintson a **szabály hozzáadása** elemre. Ezután a **feltétel hozzáadása** vagy a **művelet hozzáadása** lehetőségre kattintva megadhatja a szabályt. 
    
    *Megjegyzi*
    - Ha törölni szeretne egy feltételt vagy műveletet a szabályból, használja a szemetet az adott feltétel vagy művelet jobb oldalán.
    - Egy olyan szabály létrehozásához, amely az összes bejövő forgalomra vonatkozik, ne határozzon meg feltételeket. 
    - Ha az első egyeztetési feltétel teljesülése után le szeretné állítani a szabályok kiértékelését, akkor a **szabály kiértékelésének leállítása**gombra kell bejelentkeznie. 

![szabályok keresése motor](./media/front-door-rules-engine/rules-engine-tutorial-4.png)

4. Határozza meg a konfiguráción belüli szabályok prioritását a feljebb, a lejjebb, és a felülre lépés gombokkal. A prioritás növekvő sorrendben van megadva, ami azt jelenti, hogy az elsőként felsorolt szabály a legfontosabb szabály. 

5. Miután létrehozott egy vagy több szabályt, nyomja meg a **Mentés**gombot. Ez a művelet létrehozza a szabályok motorjának konfigurációját. 

6. Miután létrehozott egy vagy több konfigurációt, társítsa a szabályok motorjának konfigurációját egy útválasztási szabállyal. Habár egy konfiguráció számos útválasztási szabályra alkalmazható, az útválasztási szabályok csak egy szabály-végrehajtó konfigurációt tartalmazhatnak. A társítás létrehozásához nyissa meg a bevezető **ajtó tervezői** > **útvonalának szabályait**. Válassza ki azt az útválasztási szabályt, amelyhez hozzá szeretné adni a szabályok motorjának konfigurációját, lépjen a **Route details** > **Rules Engine Configuration**elemre, és válassza ki a hozzárendelni kívánt konfigurációt. 

![szabályok keresése motor](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Szabályok konfigurálása motor az Azure CLI-ben 

1. Ha még nem tette meg, telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t. Adja hozzá az "előtérben" kiterjesztést: az Extension Add--Name bejárati ajtót. Ezután jelentkezzen be, és váltson az előfizetésre az az Account set--előfizetés <name_or_Id>. 

2. Első lépésként hozzon létre egy szabályt motort – ez a példa egy, a fejléc-alapú művelettel és egy egyeztetési feltétellel rendelkező szabályt mutat be. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

2.  Sorolja fel az összes szabályt. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

3.  Adja meg a továbbítási útvonal felülbírálási műveletét. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

4.  Egy szabály összes műveletének listázása. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

5. Szabály-végrehajtói konfiguráció csatolása útválasztási szabályhoz.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

6. Szabályok leválasztása motor. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

További információkért tekintse meg az AFD Rules Engine parancsainak teljes listáját [itt](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest).   

## <a name="next-steps"></a>További lépések

- További információ a [AFD-szabályok motorról](front-door-rules-engine.md). 
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
- További részletekért tekintse meg a AFD-szabályok motor [CLI-referenciáját](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest). 
- További részletekért tekintse meg a AFD-szabályok motor [PowerShell-referenciáját](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
