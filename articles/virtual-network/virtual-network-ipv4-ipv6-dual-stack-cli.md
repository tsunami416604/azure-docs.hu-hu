---
title: Az Azure virtual network – parancssori felület IPv6 kettős verem az alkalmazások üzembe helyezése
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja hogyan Azure CLI-vel az Azure virtual network IPv6 kettős verem az alkalmazások üzembe helyezése.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130919"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Az Azure virtual network – parancssori felület (előzetes verzió) IPv6-alapú kettős verem az alkalmazások üzembe helyezése

Ez a cikk bemutatja, hogyan egy kettős verem (IPv4 + IPv6) üzembe helyezése az Azure-ban, amely tartalmaz egy alhálózattal kettős verem (IPv4 + IPv6) kettős előtér-konfigurációkkal, kettős IP-konfigurációval rendelkező hálózati adapterrel rendelkező virtuális gépek egy terheléselosztó kettős verem virtuális hálózat kettős hálózati biztonsági csoport szabályait, és két nyilvános IP-címek.

> [!Important]
> Kettős verem IPv6-alapú Azure virtuális hálózat jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, telepítése és használata az Azure CLI helyileg, ehhez a rövid útmutatóhoz, hogy az Azure CLI-vel 2.0.49 verzió vagy újabb. A telepített verzió megkereséséhez futtassa `az --version`. Lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli) telepítési vagy frissítési adatai.

## <a name="prerequisites"></a>Előfeltételek
Az IPv6 protokollt használja az Azure virtual network szolgáltatáshoz, konfigurálnia kell az előfizetés az Azure PowerShell használatával az alábbiak szerint:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
A szolgáltatás regisztráció befejezése akár 30 percet vesz igénybe. A regisztrációs állapot a következő Azure CLI-parancs futtatásával ellenőrizheti:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
A regisztráció befejezése után futtassa a következő parancsot:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A kettős vermű virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group). A következő példában létrehozunk egy erőforráscsoportot, nevű *myRGDualStack* a a *eastus* helye:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>IPv4 és IPv6 típusú nyilvános IP-címek load Balancer létrehozása
Az IPv4 és IPv6-végpontokat internetes eléréséhez szükséges IPv4 és IPv6 típusú nyilvános IP-címek a terheléselosztó. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. A következő példában létrehozunk IPv4 és IPv6 típusú nyilvános IP-címet *dsPublicIP_v4* és *dsPublicIP_v6* a a *myRGDualStack* erőforráscsoportot:

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

Távoli eléréséhez a virtuális gépek az interneten, a nyilvános IP-címek IPv4-alapú virtuális gépek szüksége. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal.

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

Ebben a szakaszban kettős frontend-IP (IPv4 és IPv6-alapú) és a háttér-címkészletet a load Balancer konfigurálása, és hozzon létre egy alapszintű Load Balancer.

### <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Hozzon létre az alapszintű Load Balancer- [az network lb létrehozása](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) nevű **dsLB** , amely tartalmaz egy nevű előtérbeli címkészletet **dsLbFrontEnd_v4**, nevű háttérkészlet  **dsLbBackEndPool_v4** társítva a nyilvános IP-cím IPv4 **dsPublicIP_v4** az előző lépésben létrehozott. 

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

### <a name="create-ipv6-frontend"></a>IPv6-előtérbeli létrehozása

Hozzon létre egy IPV6-előtérbeli IP- [az network lb frontend-ip létrehozása](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). A következő példában létrehozunk egy nevű előtérbeli IP-konfiguráció *dsLbFrontEnd_v6* , és csatolja a *dsPublicIP_v6* címe:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6-alapú háttér-címkészlet konfigurálása

Hozzon létre egy IPv6-címet háttér-készletek [az network lb-címkészlet létrehozása](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). A következő példában létrehozunk háttér-címkészletet *dsLbBackEndPool_v6* IPv6-alapú Hálózatiadapter-konfigurációval, virtuális gépeket tartalmazza:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. 

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) paranccsal. A következő példában létrehozunk load balancer-szabályok nevű *dsLBrule_v4* és *dsLBrule_v6* , és elosztja a forgalmat a *TCP* port *80-as* , az IPv4 és IPv6-előtérbeli IP-konfigurációk:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Virtuális gépek üzembe helyezése, előtt létre kell hoznia támogató hálózati erőforrások – rendelkezésre állási csoport, a hálózati biztonsági csoport, a virtuális hálózat és a virtuális hálózati adapter. 
### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
Az alkalmazás rendelkezésre állásának növeléséhez helyezze a virtuális gépeket egy rendelkezésre állási csoportban.

Hozzon létre egy rendelkezésre állási csoportot az [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) paranccsal. A következő példában létrehozunk egy rendelkezésre állási csoport elnevezett *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a szabályokat, amelyek szabályozzák a virtuális hálózatok közötti bejövő és kimenő kommunikáció.

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoport [az network nsg létrehozása](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt bejövő és kimenő kapcsolatok

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt port 3389, internetes kapcsolaton keresztül az RDP-kapcsolatok engedélyezéséhez a 80-as porton keresztül, és a kimenő kapcsolatok [az network nsg-szabály létrehozása](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

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

Hozzon létre egy virtuális hálózatot az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot *dsVNET* alhálózattal *dsSubNET_v4* és *dsSubNET_v6*:

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

Virtuális hálózati adapterek létrehozása a virtuális gépek [az network nic létrehozása](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Az alábbi példa létrehoz egy virtuális hálózati Adapterhez az egyes virtuális Gépekhez. Minden egyes hálózati adapter két IP-konfigurációval (IPv4-config 1, 1 IPv6-konfiguráció) rendelkezik. Az IPV6-konfigurációhoz létrehozhat [az network nic ip-config létrehozása](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
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

Hozzon létre a virtuális gépeket a [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Az alábbi példa létrehoz két virtuális gépet, és a szükséges virtuális hálózati összetevőket, ha azok nem léteznek. 

Virtuális gép létrehozása *dsVM0* módon:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Virtuális gép létrehozása *dsVM1* módon:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtuális hálózat IPv6-kettős verem megtekintése az Azure Portalon
Tekintheti meg az IPv6-kettős verem virtuális hálózatot az Azure Portalon a következő:
1. Adja meg a portál keresősávjában *dsVnet*.
2. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki. Ezzel elindítja a **áttekintése** a kettős verem nevű virtuális hálózat lapján *dsVnet*. A kettős verem virtuális hálózat látható a két hálózati adapter IPv4- és IPv6-konfigurációval, a kettős verem nevű alhálózat található *dsSubnet*.

  ![IPv6-alapú kettős verem virtuális hálózat az Azure-ban](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Az IPv6-alapú Azure virtuális hálózat az Azure Portalon érhető el ebben az előzetes kiadásban a csak olvasható.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [az group delete](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy alapszintű Load Balancer kettős előtérbeli IP-konfiguráció (IPv4 és IPv6). Emellett létrehozott egy két virtuális gépet, amely a hálózati adapterek mellékelt kettős IP-konfigurációk (IPV4 + IPv6), amely a terheléselosztó háttérkészlet bővült. IPv6-támogatás az Azure virtuális hálózatokkal kapcsolatos további információkért lásd: [Mi az IPv6 és az Azure Virtual Network?](ipv6-overview.md)