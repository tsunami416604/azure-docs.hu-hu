---
title: IPv6-alapú kétveremű alkalmazás telepítése - Alapszintű terheléselosztó – CLI
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan telepítegy IPv6-alapú kettős veremű alkalmazást az Azure CLI használatával az Azure virtuális hálózatban.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/17/2019
ms.author: kumud
ms.openlocfilehash: b9021784216f02fb117f6e63e150b37b07755912
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239855"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli-preview"></a>IPv6-alapú kettős veremű alkalmazás központi telepítése egyszerű terheléselosztó - CLI (előzetes verzió) használatával

Ez a cikk bemutatja, hogyan telepíthet kétverű (IPv4 + IPv6) alkalmazást alapszintű terheléselosztóval az Azure CLI használatával, amely kétverű virtuális hálózatot tartalmaz kétverű alhálózattal, egy alapszintű terheléselosztót kettős (IPv4 + IPv6) előtér-konfigurációval, hálózati adapterrel rendelkező virtuális gépeket amelyek kettős IP-konfigurációval, kettős hálózati biztonsági csoportszabályokkal és kettős nyilvános IP-címvel rendelkeznek.

Ha kétverű (IPV4 + IPv6) alkalmazást szeretne üzembe helyezni a Standard terheléselosztó használatával, [olvassa el az IPv6-alapú kettős veremalkalmazás üzembe helyezése standard terheléselosztóval az Azure CLI használatával](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)című témakört.

> [!Important]
> Az Azure Virtuális hálózat IPv6-alapú kettős veremjelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy az Azure CLI-t helyileg telepíti és használja, ez a rövid útmutató az Azure CLI 2.0.49-es vagy újabb verzióját kell használnia. A telepített verzió megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a program `az --version` A telepítési vagy frissítési információkért tekintse meg az [Azure CLI telepítése](/cli/azure/install-azure-cli) című témakört.

## <a name="prerequisites"></a>Előfeltételek
Az IPv6 azure-beli virtuális hálózati szolgáltatás használatához az előfizetést az Azure CLI használatával kell konfigurálnia az alábbiak szerint:

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

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A kétverkező virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot [az csoport létrehozásával.](/cli/azure/group) A következő példa létrehoz egy *DsResourceGroup01* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>IPv4- és IPv6-alapú nyilvános IP-címek létrehozása a terheléselosztóhoz
Az IPv4- és IPv6-végpontok interneten való eléréséhez iPv4- és IPv6-nyilvános IP-címekre van szükség a terheléselosztóhoz. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. A következő példa *dsPublicIP_v4* és *dsPublicIP_v6* nevű IPv4-és IPv6-nyilvános IP-címet hoz létre a *DsResourceGroup01* erőforráscsoportban:

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Nyilvános IP-címek létrehozása virtuális gépekhez

A virtuális gépek távolról való eléréséhez az interneten, a virtuális gépek hez iPv4 nyilvános IP-címek szükséges. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal.

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ebben a szakaszban konfigurálja a két előtér-IP-címet (IPv4 és IPv6) és a terheléselosztó háttércímkészletét, majd hozzon létre egy egyszerű terheléselosztót.

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozza létre az alapszintű terheléselosztót az [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) nevű **dsLB-vel,** amely tartalmazza **a dsLbFrontEnd_v4**nevű előtérkészletet , egy **dsLbBackEndPool_v4** nevű háttérkészletet, amely az előző lépésben létrehozott IPv4 nyilvános IP-cím **dsPublicIP_v4hez** van társítva. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6 előtér létrehozása

IPV6 előtér-IP létrehozása [az hálózati lb frontend-ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)létrehozással. A következő példa létrehoz egy *dsLbFrontEnd_v6* nevű előtér-IP-konfigurációt, és a *dsPublicIP_v6* címet csatolja:

```azurecli
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Az IPv6-háttércímkészlet konfigurálása

IPv6-os háttércímkészletek létrehozása [az hálózati lb címkészlet](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)létrehozásával. A következő példa dsLbBackEndPool_v6 nevű háttércímkészletet hoz létre *az* IPv6 hálózati adapterkonfigurációval rendelkező virtuális gépek felvételéhez:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Hozzon létre egy állapotmintát az [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) paranccsal a virtuális gépek állapotának monitorozásához. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. 

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) paranccsal. A következő példa dsLBrule_v4 és *dsLBrule_v6* nevű terheléselosztó-szabályokat hoz *létre,* és kiegyenlíti a *80-as* *TCP-port* és az IPv4-alapú előtér IP-konfigurációi közötti forgalmat:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Mielőtt telepítene néhány virtuális gépet, létre kell hoznia támogató hálózati erőforrásokat – rendelkezésre állási készletet, hálózati biztonsági csoportot, virtuális hálózatot és virtuális hálózati adaptereket. 
### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás rendelkezésre állásának javítása érdekében helyezze el a virtuális gépeket egy rendelkezésre állási csoportban.

Hozzon létre egy rendelkezésre állási [készletet az az vm rendelkezésre állási készletének létrehozása segítségével.](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) A következő példa létrehoz egy *dsAVset*nevű rendelkezésre állási készletet:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a virtuális hálózat bejövő és kimenő kommunikációját szabályozó szabályokhoz.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hálózati biztonsági csoport létrehozása [az hálózati nsg létrehozással](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Hálózati biztonságicsoport-szabály létrehozása bejövő és kimenő kapcsolatokhoz

Hozzon létre egy hálózati biztonsági csoport szabályt, amely engedélyezi az RDP-kapcsolatokat a 3389-es porton keresztül, az internetkapcsolatot a 80-as porton keresztül, valamint az [az hálózati nsg szabállyal](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)létesített kimenő kapcsolatok létrehozását.

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) paranccsal. A következő példa létrehoz egy *dsVNET* nevű virtuális hálózatot *dsSubNET_v4* és *dsSubNET_v6*alhálózatokkal:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Hálózati adapterek létrehozása

Hozzon létre virtuális hálózati adaptereket minden virtuális géphez [az hálózati nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)segítségével. A következő példa létrehoz egy virtuális hálózati adaptert minden virtuális géphez. Minden hálózati adapter két IP-konfigurációval rendelkezik (1 IPv4 konfiguráció, 1 IPv6 konfiguráció). Az IPV6-konfigurációt az [az network ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)segítségével hozza létre.

```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)segítségével. A következő példa két virtuális gépet és a szükséges virtuális hálózati összetevőket hoz létre, ha még nem léteznek. 

Hozzon létre virtuális gép *dsVM0* az alábbiak szerint:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Hozzon létre virtuális gépet *dsVM1* az alábbiak szerint:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Az IPv6 kétletétes virtuális hálózatának megtekintése az Azure Portalon
Az IPv6 kettős veremű virtuális hálózatot az Azure Portalon az alábbiak szerint tekintheti meg:
1. A portál keresősávján írja be a *dsVnet*.
2. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki. Ez elindítja a *dsVnet*nevű kettős verem virtuális hálózat **áttekintése** lapját. A kétverű virtuális hálózat a két hálózati adaptert jeleníti meg, amelyek iPv4- és IPv6-konfigurációkkal is rendelkeznek, és amelyek a *dsSubnet*nevű kettős veremalhálózatban találhatók.

  ![IPv6 kettős halmozott virtuális hálózat az Azure-ban](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Az IPv6 for Azure virtuális hálózat érhető el az Azure Portalon írásvédett ebben az előzetes verzióban.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, az [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gép és az összes kapcsolódó erőforrást.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy egyszerű terheléselosztót, amely kettős előtér-IP-konfigurációval (IPv4 és IPv6) rendelkezik. Két virtuális gépet is létrehozott, amelyek két IP-konfigurációval (IPV4 + IPv6) rendelkező hálózati adaptereket tartalmaztak, amelyeket a terheléselosztó háttérkészletéhez adtak hozzá. Ha többet szeretne tudni az IPv6-támogatásról az Azure virtuális hálózatokban, olvassa el [a Mi az IPv6 az Azure virtuális hálózathoz című témakörben?](ipv6-overview.md)
