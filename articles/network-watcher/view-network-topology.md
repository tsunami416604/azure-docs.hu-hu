---
title: Az Azure virtuális hálózati topológia megtekintése |} Microsoft Docs
description: 'Útmutató: virtuális hálózat és az erőforrásainak kapcsolatai az erőforrások megtekintése.'
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: 6ef165ddc481bf84c6189635e36b97eb9518261e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077891"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Egy Azure virtuális hálózat topológiája megtekintése

Ebből a cikkből megtanulhatja a Microsoft Azure virtuális hálózat és az erőforrásainak kapcsolatai erőforrások megtekintése. Például a virtuális hálózat alhálózatot tartalmaz. Alhálózatok erőforrások, például az Azure virtuális gépek (VM) tartalmaz. Virtuális gépek egy vagy több hálózati illesztőre van szükség. Minden egyes alhálózatot a hálózati biztonsági csoport és a hozzá tartozó útválasztási táblázatot lehet. Azure hálózati figyelőt topológia képességet lehetővé teszi, hogy láthatóvá összes erőforrását egy virtuális hálózatban a virtuális hálózat és az erőforrásainak kapcsolatai erőforrásokkal társított erőforrásokat.

Használhatja a [Azure-portálon](#azure-portal), a [Azure CLI](#azure-cli), vagy [PowerShell](#powershell) a topológia megtekintéséhez.

## <a name = "azure-portal"></a>Nézet topológia - Azure-portálon

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely rendelkezik a szükséges [engedélyek](required-rbac-permissions.md).
2. A felső sarkában a portálon, válassza balra **minden szolgáltatás**.
3. Az a **minden szolgáltatás** szűrő mezőbe, írja be *hálózati figyelőt*. Ha **hálózati figyelőt** megjelenik az eredmények között, jelölje be.
4. Válassza ki **topológia**. A topológia generálásához szükséges ugyanabban a régióban szeretné létrehozni a topológiát a virtuális hálózat már szerepel egy hálózati figyelőt. Nincs olyan hálózati figyelőt, a régiót, amelyben a virtuális hálózat esetén jöjjön létre a topológia van engedélyezve, ha hálózati figyelő automatikusan létrejönnek minden régióban. A hálózati figyelő létrehozott nevű erőforráscsoport **NetworkWatcherRG**.
5. Válasszon egy előfizetést, az erőforráscsoport egy virtuális hálózat meg szeretné tekinteni a topológiát, és válassza ki a virtuális hálózat. Az alábbi képen látható, a topológia látható a virtuális hálózat nevű *MyVnet*, az erőforráscsoport neve *MyResourceGroup*:

    ![Topológia megtekintése](./media/view-network-topology/view-topology.png)

    Mivel az előző ábrán látható, a virtuális hálózat három alhálózatot tartalmaz. Több alhálózattal rendelkezik az üzembe helyezett virtuális gép. A virtuális gép nem csatlakoztatható egy hálózati adapter és a hozzá tartozó nyilvános IP-cím rendelkezik. A két alhálózat rendelkezik egy útválasztási táblázatot hozzájuk társítva. Mindegyik útválasztási táblázatot két útvonalak tartalmazza. Egy alhálózatot a hálózati biztonsági csoport társítva van. Topológiainfomációja csak akkor jelenik meg, amelyek erőforrások: - a ugyanazt az erőforráscsoportot és a régióban legyen, mint a *myVnet* virtuális hálózat. Például egy hálózati biztonsági csoport nem található erőforráscsoport *MyResourceGroup*, nem látható, akkor is, ha a hálózati biztonsági csoportot az alhálózathoz társított a *MyVnet* virtuális hálózat .
        -Belül vagy a kapcsolódó erőforrások belül, a *myVnet* virtuális hálózat. Például egy hálózati biztonsági csoportot, amely nincs társítva egy alhálózat vagy hálózati felülettel a *myVnet* virtuális hálózat nem látható, akkor is, ha a hálózati biztonsági csoport a *MyResourceGroup* erőforráscsoport.

    A topológia a képen látható a virtuális hálózathoz való telepítése után létrehozott van a **keresztül virtuális készülék parancsfájl minta hálózati forgalmat**, amelyek használatával telepíthető a [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), vagy [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Válassza ki **töltse le a topológia** svg formátumban szerkeszthető fájlként letölti a lemezképet.

Az erőforrások, az ábrán is látható a virtuális hálózat a hálózati összetevők részhalmazát jelentik. Például az a hálózati biztonsági csoport jelenik meg, amíg nem jelennek meg a biztonsági szabályok benne a diagram. Bár nem különbözteti meg a diagramon, a sorok határoz meg egy két: *elszigetelési* vagy *tartozó*. Az erőforrásoknak a virtuális hálózat és az erőforrások közötti kapcsolat típusát a teljes lista megtekintéséhez készítése a topológia [PowerShell](#powershell) vagy a [Azure CLI](#azure-cli).

## <a name = "azure-cli"></a>Topológia – az Azure parancssori felület megtekintése

A parancsokat az alábbi lépések:
- Az Azure felhőalapú kiválasztásával rendszerhéj **, próbálja meg** felső sarkában található bármely parancs. Az Azure-felhő rendszerhéj egy ingyenes interaktív rendszerhéj, amely rendelkezik közös Azure eszközök előtelepített és konfigurált a fiókhoz.
- A parancssori felület futtatásával a számítógépről. Ha futtatja a CLI-t a számítógépről, a cikkben található lépéseket igényelnek-e az Azure parancssori felület 2.0.31 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

A használt fióknak rendelkeznie kell a szükséges [engedélyek](required-rbac-permissions.md).

1. Ha már van egy hálózati figyelőt, amelyet szeretne az olyan topológiák létrehozását a virtuális hálózat ugyanabban a régióban, ugorjon a 3. Hozzon létre egy erőforráscsoportot az egy hálózati figyelőt tartalmazó [az csoport létrehozása](/cli/azure/group#az_group_create). Az alábbi példakód létrehozza a erőforráscsoportja a *eastus* régió:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Hozzon létre egy hálózati figyelőt a [az hálózati figyelőt konfigurálása](/cli/azure/network/watcher#az-network-watcher-configure). Az alábbi példa létrehoz egy hálózati figyelőt a a *eastus* régió:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. A topológia megtekintése [az hálózati figyelő megjelenítése-topológia](/cli/azure/network/watcher#az-network-watcher-show-topology). Az alábbi példa nézetek nevű erőforráscsoport topológia *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Ugyanabban az erőforráscsoportban, belüli erőforrások csak akkor kapunk topológiainfomációja a *MyResourceGroup* erőforráscsoport és a hálózati figyelőt és ugyanabban a régióban. Például egy hálózati biztonsági csoport nem található erőforráscsoport *MyResourceGroup*, nem látható, akkor is, ha a hálózati biztonsági csoportot az alhálózathoz társított a *MyVnet* virtuális hálózat .

  További információ a [kapcsolatok](#relationhips) és [tulajdonságok](#properties) visszaadott kimenet. Ha egy topológia megtekintéséhez meglévő virtuális hálózat nem rendelkezik, hozhat létre egy a [irányíthatja a forgalmat egy hálózati virtuális készüléken keresztül](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) parancsfájl minta. A topológia ábrázoló diagram megjelenítése, és töltse le egy szerkeszthető fájlban, használja a [portal](#azure-portal).

## <a name = "powershell"></a>Topológia - PowerShell megtekintése

A parancsokat az alábbi lépések:
- Az Azure felhőalapú kiválasztásával rendszerhéj **, próbálja meg** felső sarkában található bármely parancs. Az Azure-felhő rendszerhéj egy ingyenes interaktív rendszerhéj, amely rendelkezik közös Azure eszközök előtelepített és konfigurált a fiókhoz.
- Futtassa a PowerShell a számítógépről. Ha futtatja a PowerShell a számítógépről, a cikkben ismertetett szükséges-e a 5.7.0 verzió vagy újabb AzureRm modul. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

A használt fióknak rendelkeznie kell a szükséges [engedélyek](required-rbac-permissions.md).

1. Ha már van egy hálózati figyelőt, amelyet szeretne az olyan topológiák létrehozását a virtuális hálózat ugyanabban a régióban, ugorjon a 3. Hozzon létre egy erőforráscsoportot az egy hálózati figyelőt tartalmazó [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Az alábbi példakód létrehozza a erőforráscsoportja a *eastus* régió:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Hozzon létre egy hálózati figyelőt a [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher). Az alábbi példa létrehoz egy hálózati figyelőt eastus régióban:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Egy hálózati figyelőt példány beolvasása [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). A következő példa egy hálózati figyelőt, az USA keleti régiójában kéri le:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. A topológia beolvasása [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). A következő példa egy topológia a virtuális hálózat nevű erőforráscsoportban lekéri *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Ugyanabban az erőforráscsoportban, belüli erőforrások csak akkor kapunk topológiainfomációja a *MyResourceGroup* erőforráscsoport és a hálózati figyelőt és ugyanabban a régióban. Például egy hálózati biztonsági csoport nem található erőforráscsoport *MyResourceGroup*, nem látható, akkor is, ha a hálózati biztonsági csoportot az alhálózathoz társított a *MyVnet* virtuális hálózat .

  További információ a [kapcsolatok](#relationhips) és [tulajdonságok](#properties) visszaadott kimenet. Ha egy topológia megtekintéséhez meglévő virtuális hálózat nem rendelkezik, hozhat létre egy a [irányíthatja a forgalmat egy hálózati virtuális készüléken keresztül](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) parancsfájl minta. A topológia ábrázoló diagram megjelenítése, és töltse le egy szerkeszthető fájlban, használja a [portal](#azure-portal).

## <a name="relationships"></a>Kapcsolatok

Adott vissza a topológia összes erőforrást egy másik erőforrásra való kapcsolat a következő típusok egyikével rendelkeznek:

| Kapcsolat típusa | Példa                                                                                                |
| ---               | ---                                                                                                    |
| A befoglaltsági       | A virtuális hálózati alhálózat tartalmazza. Egy alhálózatot a hálózati adaptert tartalmaz.                            |
| Kapcsolódó        | A hálózati kapcsolat egy társítva van egy virtuális Gépet. Egy nyilvános IP-címet a hálózati adapter társítva. |

## <a name="properties"></a>Tulajdonságok

Adott vissza a topológia összes erőforrást a következő jellemzőkkel rendelkezik:

- **Név**: az erőforrás neve
- **Azonosító**: az URI a erőforrás.
- **Hely**: az erőforrás már szerepel az Azure-régió.
- **Társítások**: a hivatkozott objektum társítást listáját. Minden társításhoz tulajdonságai a következők:
    - **AssociationType**: a gyermekobjektum és a szülő közötti kapcsolat hivatkozik. Érvényes értékek a következők *Contains* vagy *társított*.
    - **Név**: a hivatkozott erőforrás nevét.
    - **ResourceId**:-az URI a található a hivatkozott erőforrás.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [diagnosztizálhatja a hálózati forgalom szűrő hiba, vagy a virtuális gép](diagnose-vm-network-traffic-filtering-problem.md) hálózati figyelőt IP folyamata segítségével ellenőrzési funkciók
- Megtudhatja, hogyan [diagnosztizálhatja a hálózati forgalom útválasztási hiba VM](diagnose-vm-network-routing-problem.md) hálózati figyelőt a következő Ugrás funkcióval