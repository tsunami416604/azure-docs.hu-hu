---
title: 'Rövid útmutató: NAT-átjáró létrehozása – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót az Azure CLI használatával
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 9402960927f56092e226ab81bd3e6ede0cf6a52d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202195"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Rövid útmutató: NAT-átjáró létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure virtuális hálózati NAT-szolgáltatást. NAT-átjárót hoz létre, hogy kimenő kapcsolatot biztosítson egy virtuális gép számára az Azure-ban. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot az Azure Cloud Shell használatával befejezheti, vagy helyileg futtathatja a megfelelő parancsokat.  Ha még soha nem használta az Azure Cloud [Shellt, jelentkezzen be most](https://shell.azure.com) a kezdeti beállításhoz.
Ha úgy dönt, hogy ezeket a parancsokat helyileg futtatja, telepítenie kell a CLI-t.  Ez az oktatóanyag megköveteli, hogy az Azure CLI 2.0.71-es vagy újabb verziójának verzióját futtassa. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy **myResourceGroupNAT** nevű erőforráscsoportot az **eastus2** helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>A NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános internet eléréséhez egy vagy több nyilvános IP-címre van szükség a NAT-átjáróhoz. Használja [az hálózati nyilvános ip create-hoz](https://docs.microsoft.com/cli/azure/network/public-ip) teremt egy nyilvános IP-cím erőforrás nevű **myPublicIP** **a myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

Használhatja egy vagy több nyilvános IP-cím erőforrások, nyilvános IP-előtagok, vagy mindkettő nat átjáró. Ehhez a forgatókönyvhöz hozzáadunk egy nyilvános IP-előtag-erőforrást.   Használja [az az hálózati nyilvános ip előtagot,](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) hogy hozzon létre egy nyilvános IP-előtag erőforrás nevű **myPublicIPprefix** a **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró-erőforrás létrehozása

Ez a szakasz részletezi, hogyan hozhat létre és konfigurálhat a NAT szolgáltatás következő összetevőit a NAT átjáróerőforrás használatával:
  - Nyilvános IP-készlet és nyilvános IP-előtag a NAT átjáró erőforrás által lefordított kimenő folyamatokhoz.
  - Módosítsa az alapjárati időoutot az alapértelmezett 4 percről 10 percre.

Hozzon létre egy globális Azure NAT-átjárót a **myNATgateway**nevű [hálózati nat átjáróval.](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) A parancs a nyilvános IP-címet **használja myPublicIP** és a nyilvános IP előtag **myPublicIPprefix**. A parancs az tétlen időoutot **10** percre módosítja.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Ezen a ponton a NAT-átjáró működőképes, és csak azt kell konfigurálni, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

Mielőtt üzembe helyezne egy virtuális gépet, és használhatja a NAT-átjárót, létre kell hoznunk a virtuális hálózatot.

Hozzon létre egy **myVnet** nevű virtuális hálózatot egy **myResourceGroupNAT** nevű alhálózattal az [az hálózati virtuális hálózat létrehozása](https://docs.microsoft.com/cli/azure/network/vnet)használatával. **mySubnet**  A virtuális hálózat IP-címterülete **192.168.0.0/16**. A virtuális hálózaton belüli **alhálózat: 192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-szolgáltatás konfigurálása a forrásalhálózathoz

A virtuális hálózati **myVnet** ben konfiguráljuk a **mySubnet** forrásalhálózatot egy adott NAT átjáró-szolgáltatás **myNATgateway használatára** [az az hálózati vnet alhálózati frissítéssel.](https://docs.microsoft.com/cli/azure/network/vnet/subnet)  Ez a parancs aktiválja a NAT szolgáltatást a megadott alhálózaton.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Az internetes célállomásokra irányuló összes kimenő forgalom most már a NAT átjárót használja.  Nem szükséges konfigurálni az UDR-t.

## <a name="create-a-vm-to-use-the-nat-service"></a>Virtuális gép létrehozása a NAT szolgáltatás használatához

Most hozzon létre egy virtuális gép a NAT szolgáltatás használatához.  Ez a virtuális gép egy nyilvános IP-cím ként használható példányszintű nyilvános IP-cím, amely lehetővé teszi a virtuális gép eléréséhez.  A NAT-szolgáltatás áramlásirány-függő, és lecseréli az alhálózat alapértelmezett internetes célját. A virtuális gép nyilvános IP-címe nem lesz használva a kimenő kapcsolatokhoz.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP létrehozása a forrás virtuális géphez

Létrehozunk egy nyilvános IP-t a virtuális gép eléréséhez.  Használja [az hálózati nyilvános ip create-t](https://docs.microsoft.com/cli/azure/network/public-ip) egy **myPublicIPVM** nevű nyilvános IP-címerőforrás létrehozásához a **myResourceGroupNAT-ban.**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>NSG létrehozása virtuális géphez

Mivel a szabványos nyilvános IP-címek "alapértelmezés szerint biztonságosak", létre kell hoznunk egy NSG-t, hogy engedélyezzük az ssh-hozzáférés bejövő elérését. Az [hálózati nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) használatával hozzon létre egy **myNSG** nevű NSG-erőforrást a **myResourceGroupNAT-ban.**

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-végpont felfedése a forrás virtuális gépén

Létrehozunk egy szabályt az NSG-ben az SSH-hozzáférés a forrás vm.We create a rule in the NSG for SSH access to the source vm. Az [hálózati nsg szabály létrehozása](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) az **ssh** nevű NSG-szabály létrehozásához a **myResourceGroupNAT** **myNSG** nevű NSG-ben.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Hálózati adapter létrehozása virtuális géphez

Hozzon létre egy hálózati [adaptert az az network nic](/cli/azure/network/nic#az-network-nic-create) létrehozásával, és társítani a nyilvános IP-címet és a hálózati biztonsági csoportot. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create)segítségével.  SSH-kulcsokat hozunk létre ehhez a virtuális géphez, és tároljuk a később használandó személyes kulcsot.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Várja meg, amíg a virtuális gép üzembe helyezése, majd folytassa a többi lépéssel.

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felfedezése

Először is fel kell derítenünk a létrehozott virtuális gép IP-címét. A virtuális gép nyilvános IP-címének lekéréséhez használja az [az hálózati nyilvános ip show-t.](/cli/azure/network/public-ip#az-network-public-ip-show) 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, így használhatja a virtuális gép eléréséhez.

### <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépbe

Az SSH hitelesítő adatokat az előző műveletből származó Felhőrendszerhéjban kell tárolni.  Nyisson meg egy [Azure Cloud Shellt](https://shell.azure.com) a böngészőjében. Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez.

```bash
ssh <ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a használatához. 

Tekintse át a metrikákat az Azure Monitorban a NAT-szolgáltatás működésének megtekintéséhez. Diagnosztizálja az olyan problémákat, mint például a rendelkezésre álló SNAT-portok erőforrás-kimerülése.  Az SNAT-portok erőforrás-kimerülése további nyilvános IP-címerőforrások vagy nyilvános IP-előtag-erőforrások vagy mindkettő hozzáadásával történik.


- Learn about [Azure Virtual Network NAT](./nat-overview.md)
- További információ a [NAT átjáró-erőforrásról.](./nat-gateway-resource.md)
- Rövid útmutató a [NAT átjáróerőforrás Azure CLI használatával történő](./quickstart-create-nat-gateway-cli.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT átjáróerőforrás Azure PowerShell használatával történő](./quickstart-create-nat-gateway-powershell.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT-átjáró-erőforrás Azure Portalon való](./quickstart-create-nat-gateway-portal.md)telepítéséhez.
> [!div class="nextstepaction"]

