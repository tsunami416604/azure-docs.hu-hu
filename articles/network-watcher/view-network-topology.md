---
title: Az Azure virtuális hálózati topológiájának megtekintése | Microsoft dokumentumok
description: Ismerje meg, hogyan tekintheti meg az erőforrásokat egy virtuális hálózatban, és az erőforrások közötti kapcsolatokat.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: 675919db55932d3ccc04fd5397f6f673832b4900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840570"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Az Azure virtuális hálózat topológiájának megtekintése

Ebből a cikkből megtudhatja, hogyan tekintheti meg az erőforrásokat a Microsoft Azure virtuális hálózatban, és az erőforrások közötti kapcsolatokat. Egy virtuális hálózat például alhálózatokat tartalmaz. Az alhálózatok erőforrásokat tartalmaznak, például az Azure virtuális gépeket (VM). A virtuális gépek egy vagy több hálózati adaptert rendelkeznek. Minden alhálózathoz tartozhat egy hálózati biztonsági csoport és egy útvonaltábla. Az Azure Network Watcher topológiája lehetővé teszi a virtuális hálózat összes erőforrásának, a virtuális hálózat erőforrásaihoz társított erőforrásoknak és az erőforrások közötti kapcsolatoknak a megtekintését.

Használhatja az [Azure Portalon,](#azure-portal)az [Azure CLI,](#azure-cli)vagy a [PowerShell](#powershell) egy topológia megtekintéséhez.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topológia megtekintése – Azure portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely rendelkezik a szükséges [engedélyekkel.](required-rbac-permissions.md)
2. A portál bal felső sarkában válassza a **Minden szolgáltatás**lehetőséget.
3. A **Minden szolgáltatás** szűrőmezőbe írja be a *Network Watcher (Hálózatfigyelő) mezőbe.* Ha megjelenik a **Network Watcher** az eredmények között, jelölje ki.
4. Válassza a **Topológia** lehetőséget. A topológia létrehozásához szükség van egy hálózati figyelő ugyanabban a régióban, ahol a virtuális hálózat, amelyhez létre kívánja hozni a topológia létezik. Ha nincs engedélyezve a hálózati figyelő abban a régióban, ahol a tanológiát létrehozni kívánt virtuális hálózat van, a rendszer automatikusan létrehozza a hálózati figyelők számára az összes régióban. A hálózati figyelők a **NetworkWatcherRG**nevű erőforráscsoportban jönnek létre.
5. Válasszon ki egy előfizetést, egy virtuális hálózat erőforráscsoportját, amelynek topológiáját meg szeretné tekinteni, majd válassza ki a virtuális hálózatot. Az alábbi képen egy *MyVnet*nevű virtuális hálózat topológiája látható a *MyResourceGroup*nevű erőforráscsoportban:

    ![Topológia megtekintése](./media/view-network-topology/view-topology.png)

    Amint az előző képen látható, a virtuális hálózat három alhálózatot tartalmaz. Az egyik alhálózatban telepítve van egy virtuális gép. A virtuális gép hez egy hálózati adapter van csatlakoztatva, és egy nyilvános IP-cím társítva. A másik két alhálózathoz egy útvonaltábla van társítva. Minden útvonaltábla két útvonalat tartalmaz. Az egyik alhálózathoz hálózati biztonsági csoport van társítva. A topológia információ csak a következő erőforrásokesetében jelenik meg:
    
    - Ugyanazon erőforráscsoporton és régión belül, mint a *myVnet* virtuális hálózat. Például egy hálózati biztonsági csoport, amely nem a *MyResourceGroup*erőforráscsoportban található, nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a *MyVnet* virtuális hálózat alhálózatához van társítva.
    - A *myVnet* virtuális hálózaton belüli vagy az erőforrásokhoz társított vagy azokhoz társítva. Például egy hálózati biztonsági csoport, amely nincs társítva egy alhálózat vagy hálózati adapter a *myVnet* virtuális hálózat nem jelenik meg, akkor is, ha a hálózati biztonsági csoport a *MyResourceGroup* erőforráscsoport.

   A képen látható topológia a virtuális hálózat létrehozása után létrehozott **útvonalforgalom egy hálózati virtuális berendezés parancsfájl minta**, amely telepítheti az [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), vagy [a PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)használatával.

6. Válassza **a Topológia letöltése** lehetőséget, ha a képet szerkeszthető fájlként szeretné letölteni svg formátumban.

Az ábrán látható erőforrások a virtuális hálózat hálózati összetevőinek egy részhalmazát képezik. Ha például egy hálózati biztonsági csoport jelenik meg, a benne lévő biztonsági szabályok nem jelennek meg a diagramon. Bár a diagram nem különböztethető meg, a sorok a következő két kapcsolat egyikét képviselik: *Elszigetelés* vagy *társított*. A virtuális hálózat erőforrásainak teljes listájának és az erőforrások közötti kapcsolat típusának megtekintéséhez hozza létre a topológiát a [PowerShell](#powershell) vagy az [Azure CLI](#azure-cli)segítségével.

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Topológia megtekintése - Azure CLI

A parancsokat a következő lépésekben futtathatja:
- Az Azure Cloud Shell ben válassza **a Try It** a jobb felső sarokban bármely parancs. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely a fiókjával való használatra előre telepített és konfigurált közös Azure-eszközökkel rendelkezik.
- A CLI futtatásával a számítógépről. Ha a CLI-t a számítógépről futtatja, a cikkben leírt lépésekhez az Azure CLI 2.0.31-es vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatnia, hogy hozzon létre egy kapcsolatot az Azure-ral.

A használt fióknak rendelkeznie kell a szükséges [engedélyekkel.](required-rbac-permissions.md)

1. Ha már rendelkezik hálózati figyelővel ugyanabban a régióban, mint az a virtuális hálózat, amelyhez topológiát szeretne létrehozni, ugorjon a 3. Hozzon létre egy erőforráscsoportot, amely tartalmazza az csoport létrehozási csoportját tartalmazó hálózati [figyelőt.](/cli/azure/group) A következő példa létrehozza az erőforráscsoportot az *eastus* régióban:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Hozzon létre egy hálózati figyelő az [hálózati figyelő konfigurálásával.](/cli/azure/network/watcher#az-network-watcher-configure) A következő példa létrehoz egy hálózati figyelőt az *eastus* régióban:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Tekintse meg a topológia [az hálózati watcher show-topológia](/cli/azure/network/watcher#az-network-watcher-show-topology). A következő példa egy *MyResourceGroup*nevű erőforráscsoport topológiáját tekinti meg:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    A topológia adatok csak a *MyResourceGroup* erőforráscsoportés a hálózati figyelővel azonos erőforráscsoportba tartozó erőforrások esetében adnak vissza topológiai adatokat. Például egy hálózati biztonsági csoport, amely nem a *MyResourceGroup*erőforráscsoportban található, nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a *MyVnet* virtuális hálózat alhálózatához van társítva.

   További információ a kapcsolatokról és [a tulajdonságokról](#properties) a visszaadott kimenetben. Ha nem rendelkezik egy meglévő virtuális hálózat megtekintéséhez a topológia, hozhat létre egyet a hálózati virtuális berendezés parancsfájlon [keresztül a forgalom átirányítása](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) használatával. A topológia diagramjának megtekintéséhez és szerkesztésre cserélhető fájlba való letöltéséhez használja a [portált.](#azure-portal)

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Topológia megtekintése – PowerShell

A parancsokat a következő lépésekben futtathatja:
- Az Azure Cloud Shell ben válassza **a Try It** a jobb felső sarokban bármely parancs. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely a fiókjával való használatra előre telepített és konfigurált közös Azure-eszközökkel rendelkezik.
- A PowerShell futtatásával a számítógépről. Ha a PowerShell a számítógépről, ez a `Az` cikk megköveteli az Azure PowerShell-modul. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

A használt fióknak rendelkeznie kell a szükséges [engedélyekkel.](required-rbac-permissions.md)

1. Ha már rendelkezik hálózati figyelővel ugyanabban a régióban, mint az a virtuális hálózat, amelyhez topológiát szeretne létrehozni, ugorjon a 3. Hozzon létre egy erőforráscsoportot, amely tartalmazza a hálózatfigyelőt a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)segítségével. A következő példa létrehozza az erőforráscsoportot az *eastus* régióban:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Hozzon létre egy hálózati figyelő a [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). A következő példa létrehoz egy hálózati figyelőt az eastus régióban:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Hálózati figyelőpéldány lekérése a [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher)segítségével. A következő példa egy hálózatfigyelőt olvas be az USA keleti régiójában:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Topológia lekérése a [Get-AzNetworkWatcherTopology segítségével.](/powershell/module/az.network/get-aznetworkwatchertopology) A következő példa egy virtuális hálózat topológiáját olvassa be a *MyResourceGroup*nevű erőforráscsoportban:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   A topológia adatok csak a *MyResourceGroup* erőforráscsoportés a hálózati figyelővel azonos erőforráscsoportba tartozó erőforrások esetében adnak vissza topológiai adatokat. Például egy hálózati biztonsági csoport, amely nem a *MyResourceGroup*erőforráscsoportban található, nem jelenik meg, még akkor sem, ha a hálózati biztonsági csoport a *MyVnet* virtuális hálózat alhálózatához van társítva.

   További információ a kapcsolatokról és [a tulajdonságokról](#properties) a visszaadott kimenetben. Ha nem rendelkezik egy meglévő virtuális hálózat megtekintéséhez a topológia, hozhat létre egyet a hálózati virtuális berendezés parancsfájlon [keresztül a forgalom átirányítása](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) használatával. A topológia diagramjának megtekintéséhez és szerkesztésre cserélhető fájlba való letöltéséhez használja a [portált.](#azure-portal)

## <a name="relationships"></a>Kapcsolatok

A topológiában visszaadott összes erőforrás nak a következő típusú kapcsolata van egy másik erőforrással:

| Kapcsolattípus | Példa                                                                                                |
| ---               | ---                                                                                                    |
| Befoglaltság       | A virtuális hálózat alhálózatot tartalmaz. Az alhálózat hálózati adaptert tartalmaz.                            |
| Társítva        | A hálózati adapter egy virtuális géphez van társítva. Nyilvános IP-cím van társítva egy hálózati adapterhez. |

## <a name="properties"></a>Tulajdonságok

A topológiában visszaadott összes erőforrás a következő tulajdonságokkal rendelkezik:

- **Név**: Az erőforrás neve
- **Azonosító:** Az erőforrás URI-ja.
- **Hely**: Az Az Azure-régió, amelyben az erőforrás létezik.
- **Társítások**: A hivatkozott objektumhoz való társítások listája. Minden társítás a következő tulajdonságokkal rendelkezik:
    - **AssociationType**: A gyermekobjektum és a szülő közötti kapcsolatra hivatkozik. Az érvényes értékek a *Következők: Tartalmazza* vagy *Társított.*
    - **Név**: A hivatkozott erőforrás neve.
    - **ResourceId**: - a társításban hivatkozott erőforrás URI-ja.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogy [miként diagnosztizálhat hálózati forgalomszűrő-problémát a Virtuális gép ről vagy a Virtuális gépről](diagnose-vm-network-traffic-filtering-problem.md) a Network Watcher IP-folyamat-ellenőrzési képességének használatával
- Ismerje meg, hogyan [diagnosztizálhat hálózati forgalom-útválasztási problémát virtuális gépről](diagnose-vm-network-routing-problem.md) a Network Watcher következő ugrási képességének használatával
