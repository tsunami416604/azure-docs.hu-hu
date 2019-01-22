---
title: Az Azure virtuális hálózati topológia megtekintése |} A Microsoft Docs
description: Ismerje meg, hogyan erőforrás megtekintését egy virtuális hálózatot és az erőforrásainak kapcsolatai.
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
ms.openlocfilehash: 501659a93306342c7a212d135b4fdd89be096451
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428169"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Az Azure-beli virtuális hálózathoz a topológia megtekintése

Ebből a cikkből megismerheti, hogyan erőforrás megtekintését egy Microsoft Azure virtuális hálózatban, és az erőforrásainak kapcsolatai. Ha például egy virtuális hálózatok alhálózatokat tartalmaznak. Alhálózatok erőforrások, például az Azure Virtual Machines (VM) tartalmaz. Virtuális gépek egy vagy több hálózati adapter rendelkeznek. Minden alhálózat egy hálózati biztonsági csoport és a egy útvonaltábla társítható rendelkezhet. A topológia funkció az Azure Network Watcher lehetővé teszi, hogy minden erőforrást egy virtuális hálózatban, az erőforrás megtekintését egy virtuális hálózatot és az erőforrásainak kapcsolatai lévő erőforrásokhoz tartozó.

Használhatja a [az Azure portal](#azure-portal), a [Azure CLI-vel](#azure-cli), vagy [PowerShell](#powershell) egy topológiájának megtekintéséhez.

## <a name = "azure-portal"></a>Topológia megtekintése – Azure portal

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely rendelkezik a szükséges [engedélyek](required-rbac-permissions.md).
2. A felső sarkában a Portalon, válassza left **minden szolgáltatás**.
3. Az a **minden szolgáltatás** írja be a Szűrő mezőbe *Network Watcher*. Amikor a **Network Watcher** elem megjelenik az eredmények között, válassza ki.
4. Válassza ki **topológia**. Ugyanabban a régióban, amely a virtuális hálózat, amely kíván létrehozni a topológia a network watchert a topológiák létrehozása szükséges. Ha nincs engedélyezve a régióban, amely a virtuális hálózatot szeretné létrehozni a topológia a network watchert, hálózati figyelő automatikusan létrejön, minden régióban. A hálózati figyelő nevű erőforráscsoportban létrehozott **NetworkWatcherRG**.
5. Válasszon ki egy előfizetést, az erőforráscsoport, egy virtuális hálózat meg szeretné tekinteni a topológiát, és válassza ki a virtuális hálózat. Az alábbi ábrán egy topológiáját nevű virtuális hálózat *MyVnet*, az erőforráscsoport neve *MyResourceGroup*:

    ![Topológia megtekintése](./media/view-network-topology/view-topology.png)

    Ahogy az előző képen látható, a virtuális hálózatot három alhálózatot tartalmaz. Egy alhálózattal rendelkezik, üzembe helyezett virtuális gép. A virtuális gépen több hálózati adapter csatlakozik, és egy nyilvános IP-cím társítva van hozzá. A két alhálózat egy útválasztási táblázatot kell rendelkeznie. Minden egyes útvonaltábla két útvonallal tartalmazza. Egy alhálózattal rendelkezik egy hálózati biztonsági csoport társítva van hozzá. Topológiai adatok csak akkor jelenik meg az erőforrásra, amely:
    
    - Az ugyanabban az erőforráscsoportban és régióban a *myVnet* virtuális hálózatot. Például egy hálózati biztonsági csoportot, amely nem egy erőforráscsoportban létezik *MyResourceGroup*, nem látható, akkor is, ha a hálózati biztonsági csoportot az alhálózathoz társított a *MyVnet* virtuális hálózat .
    - Belül vagy a kapcsolódó erőforrások belül, a *myVnet* virtuális hálózatot. Ha például egy hálózati biztonsági csoportot, amely nincs társítva a egy alhálózathoz vagy hálózati adapterhez a a *myVnet* virtuális hálózat nem jelenik meg, akkor is, ha a hálózati biztonsági csoport a a *MyResourceGroup* erőforráscsoport.

  A topológia a képen is látható a telepítése után létrehozott virtuális hálózat van a **egy hálózati virtuális berendezés-példaszkript – forgalom irányítása**, amelyeket használatával helyezhet üzembe a [Azure CLI-vel](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), vagy [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Válassza ki **topológia letöltése** letölti a lemezképet szerkeszthető, svg-formátumú fájlként.

Az ábrán látható erőforrások a virtuális hálózatban a hálózati összetevők egy részét. Például az a hálózati biztonsági csoport jelenik meg, amíg nem jelennek meg a benne lévő biztonsági szabályokat a diagram. Nem különbözteti meg a diagramon, bár a sorok mutatják be két olyan kapcsolat egyikét: *Tartalmazottsági* vagy *kapcsolódó*. A virtuális hálózat és az erőforrások közötti kapcsolat típusát az erőforrások teljes listájának megtekintéséhez, létrehozásához a topológia a [PowerShell](#powershell) vagy a [Azure CLI-vel](#azure-cli).

## <a name = "azure-cli"></a>Tekintse meg topológia – Azure CLI-vel

A parancsok a következő lépések futtatható:
- Az Azure Cloud shellben kiválasztásával **Kipróbálom** felső bármilyen parancs, jobb. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület, amely rendelkezik közös Azure-eszközök előre telepítve és konfigurálva a fiókjával való használat.
- A parancssori felület futtatásával a számítógépről. Ha futtatja a parancssori felület a számítógépről, a cikkben ismertetett lépések megkövetelése az Azure CLI 2.0.31-es verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

A fióknak rendelkeznie kell a szükséges [engedélyek](required-rbac-permissions.md).

1. Ha már rendelkezik egy network watcher-topológia a létrehozandó virtuális hálózatok ugyanabban a régióban, folytassa a 3. Hozzon létre egy erőforráscsoportot a network watchert tartalmazó [az csoport létrehozása](/cli/azure/group#az_group_create). A következő parancs létrehozza az erőforráscsoportot a *eastus* régió:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. A network watchert létrehozása [az a network watcher konfigurálása](/cli/azure/network/watcher#az-network-watcher-configure). Az alábbi példa létrehoz egy network watcher a a *eastus* régió:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. A topológia megtekintéséhez [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). Az alábbi példa megjeleníti a topológia nevű erőforráscsoport *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topológiai adatok csak az erőforrásra, amely ugyanabban az erőforráscsoportban mint belül adja vissza a *MyResourceGroup* erőforráscsoportot és a network watcher és ugyanabban a régióban. Például egy hálózati biztonsági csoportot, amely nem egy erőforráscsoportban létezik *MyResourceGroup*, nem látható, akkor is, ha a hálózati biztonsági csoportot az alhálózathoz társított a *MyVnet* virtuális hálózat .

  Tudjon meg többet a [kapcsolatok](#relationhips) és [tulajdonságok](#properties) visszaadott kimenet. Ha nem rendelkezik meglévő virtuális hálózattal egy topológiájának megtekintéséhez, hozhat létre egy a [forgalom irányítása hálózati virtuális készüléken keresztül](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) példaszkript. A topológia ábrázoló diagram megjelenítése, és töltse le egy szerkeszthető-fájlban, használja a [portál](#azure-portal).

## <a name = "powershell"></a>Topológia megtekintése – PowerShell

A parancsok a következő lépések futtatható:
- Az Azure Cloud shellben kiválasztásával **Kipróbálom** felső bármilyen parancs, jobb. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület, amely rendelkezik közös Azure-eszközök előre telepítve és konfigurálva a fiókjával való használat.
- Által a számítógépről futtatja a Powershellt. Ha a számítógépről futtatja a PowerShell, a cikkben ismertetett lépések verziójának 5.7.0 megkövetelése és újabb verzióiban az AzureRm-modul. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

A fióknak rendelkeznie kell a szükséges [engedélyek](required-rbac-permissions.md).

1. Ha már rendelkezik egy network watcher-topológia a létrehozandó virtuális hálózatok ugyanabban a régióban, folytassa a 3. Hozzon létre egy erőforráscsoportot a network watchert tartalmazó [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). A következő parancs létrehozza az erőforráscsoportot a *eastus* régió:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. A network watchert létrehozása [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher). Az alábbi példa létrehoz egy network watcher az eastus régióban:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Egy Network Watcher-példány beolvasása [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). Az alábbi példa lekéri a network watchert az USA keleti régiójában:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. A topológia a lekéréséhez [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). Az alábbi példa lekéri egy nevű erőforráscsoportot a virtuális hálózati topológia *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topológiai adatok csak az erőforrásra, amely ugyanabban az erőforráscsoportban mint belül adja vissza a *MyResourceGroup* erőforráscsoportot és a network watcher és ugyanabban a régióban. Például egy hálózati biztonsági csoportot, amely nem egy erőforráscsoportban létezik *MyResourceGroup*, nem látható, akkor is, ha a hálózati biztonsági csoportot az alhálózathoz társított a *MyVnet* virtuális hálózat .

  Tudjon meg többet a [kapcsolatok](#relationhips) és [tulajdonságok](#properties) visszaadott kimenet. Ha nem rendelkezik meglévő virtuális hálózattal egy topológiájának megtekintéséhez, hozhat létre egy a [forgalom irányítása hálózati virtuális készüléken keresztül](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) példaszkript. A topológia ábrázoló diagram megjelenítése, és töltse le egy szerkeszthető-fájlban, használja a [portál](#azure-portal).

## <a name="relationships"></a>Kapcsolatok

A topológiában visszaadott összes erőforrásnak rendelkeznie kell egy másik erőforráshoz való kapcsolat a következők egyikét:

| Kapcsolat típusa | Példa                                                                                                |
| ---               | ---                                                                                                    |
| Tartalmazottsági       | Virtuális hálózat egy alhálózatot tartalmaz. Egy alhálózat egy hálózati adaptert tartalmaz.                            |
| Társított        | Hálózati adapter kapcsolódik egy virtuális Gépet. Nyilvános IP-cím egy hálózati adapter társítva. |

## <a name="properties"></a>Tulajdonságok

Adott vissza a topológiában az összes erőforrás a következő tulajdonságokkal rendelkezik:

- **Név**: Az erőforrás neve
- **ID**: Az erőforrás URI azonosítója.
- **Hely**: Az Azure-régió, hogy az erőforrás megtalálható.
- **Társítások**: A hivatkozott objektum társításának listáját. Minden társítás rendelkezik a következő tulajdonságokkal:
    - **AssociationType**: Hivatkozik az gyermekobjektum és a szülő közötti kapcsolatot. Érvényes értékek a következők *Contains* vagy *társított*.
    - **Név**: A hivatkozott erőforrás neve.
    - **Erőforrás-azonosító**:-az erőforrás URI-ját a társítás hivatkozik.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [forgalomszűrési problémáinak diagnosztizálása a hálózati, illetve a virtuális gép](diagnose-vm-network-traffic-filtering-problem.md) funkció használatával a Network Watcher IP-folyamat ellenőrzése
- Ismerje meg, hogyan [útválasztási problémáinak diagnosztizálása a hálózati forgalom egy virtuális gépről](diagnose-vm-network-routing-problem.md) a Network Watcher következő ugrás képesség használatával
