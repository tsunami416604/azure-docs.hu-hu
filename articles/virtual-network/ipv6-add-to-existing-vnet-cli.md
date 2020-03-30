---
title: IPv6 hozzáadása egy IPv4-alkalmazáshoz az Azure virtuális hálózatában – Azure CLI
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepítheti az IPv6-címeket egy meglévő alkalmazásra az Azure VIRTUÁLIS hálózatban az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 5dc231febc2e9b605b9e7f603f5d036b8a2c62eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240760"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>IPv6 hozzáadása egy IPv4-alkalmazáshoz az Azure virtuális hálózatában – Azure CLI (előzetes verzió)

Ez a cikk bemutatja, hogyan adhat iPv6-címeket egy olyan alkalmazáshoz, amely IPv4 nyilvános IP-címet használ egy Azure virtuális hálózatban az Azure CLI használatával. A helybeni frissítés tartalmaz egy virtuális hálózatot és alhálózatot, egy standard terheléselosztót IPv4 + IPV6 előtér-konfigurációkkal, iPv4 + IPv6 konfigurációval rendelkező hálózati adapterekkel rendelkező virtuális gépeket, hálózati biztonsági csoportot és nyilvános IP-címeket.

> [!Important]
> Az Azure Virtual Network IPv6-támogatása jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy az Azure CLI-t helyileg telepíti és használja, ez a rövid útmutató az Azure CLI 2.0.28-as vagy újabb verzióját kell használnia. A telepített verzió megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a program `az --version` A telepítési vagy frissítési információkért tekintse meg az [Azure CLI telepítése](/cli/azure/install-azure-cli) című témakört.

## <a name="prerequisites"></a>Előfeltételek

### <a name="register-the-service"></a>A szolgáltatás regisztrálása

Mielőtt üzembe helyezne egy kétverű alkalmazást az Azure-ban, konfigurálnia kell az előfizetést ehhez az előzetes verzióhoz a következő Azure CLI használatával:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

A funkcióregisztráció befejezéséhez akár 30 perc is igénybe vesszen. A regisztrációs állapot ot a következő Azure CLI-parancs futtatásával ellenőrizheti:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

A regisztráció befejezése után futtassa a következő parancsot:

```azurecli
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása
Ez a cikk feltételezi, hogy üzembe helyezett egy standard terheléselosztót a rövid útmutatóban leírtak [szerint: Standard terheléselosztó létrehozása - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>IPv6-címek létrehozása

Hozzon létre nyilvános IPv6-címet az [az hálózati nyilvános ip-létrehozással](/cli/azure/network/public-ip) a standard terheléselosztóhoz. A következő példa létrehoz egy *PublicIP_v6* nevű IPv6 nyilvános IP-címet a *myResourceGroupSLB* erőforráscsoportban:

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Az IPv6 terheléselosztó előtérének konfigurálása

COnfigure a terheléselosztót az új IPv6 IP-címmel az [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) használatával az alábbiak szerint:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6-terheléselosztó háttérkészletének konfigurálása

Hozza létre az IPv6-címekkel rendelkező hálózati adapterek háttérkészletét az [az hálózati címkészlet létrehozása](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) segítségével az alábbiak szerint:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6-terheléselosztó szabályainak konfigurálása

IPv6-os terheléselosztó-szabályok létrehozása [az hálózati lb szabállyal létrehozva.](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6-címtartományok hozzáadása

Adja hozzá az IPv6-címtartományokat a virtuális hálózathoz és a terheléselosztót üzemeltető alhálózathoz az alábbiak szerint:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>IPv6-konfiguráció hozzáadása hálózati adapterekhez

Konfigurálja a virtuálisgép-hálózati adaptereket [IPv6-címmel az az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) használatával az alábbiak szerint:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Az IPv6 kétletétes virtuális hálózatának megtekintése az Azure Portalon
Az IPv6 kettős veremű virtuális hálózatot az Azure Portalon az alábbiak szerint tekintheti meg:
1. A portál keresősávján adja meg a *myVnet*.
2. Amikor **a myVnet** megjelenik a keresési eredmények között, jelölje ki. Ez elindítja a *myVNet*nevű kettős verem virtuális hálózat **áttekintése** oldalt. A kétverű virtuális hálózat a három hálózati adaptert jeleníti meg, amelyek iPv4- és IPv6-konfigurációkkal is rendelkeznek, és amelyek a *mySubnet*nevű kettős verem alhálózatban találhatók.

  ![IPv6 kettős halmozott virtuális hálózat az Azure-ban](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Az IPv6 for Azure virtuális hálózat érhető el az Azure Portalon írásvédett ebben az előzetes verzióban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy meglévő standard terheléselosztót frissített iPv4 előtér-IP-konfigurációval egy kettős veremkonfigurációra (IPv4 és IPv6). IPv6-konfigurációkat is hozzáadott a háttérkészletben lévő virtuális gépek hálózati adaptereihez. Ha többet szeretne tudni az IPv6-támogatásról az Azure virtuális hálózatokban, olvassa el [a Mi az IPv6 az Azure virtuális hálózathoz című témakörben?](ipv6-overview.md)
