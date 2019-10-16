---
title: 'Gyors útmutató: NAT-átjáró létrehozása – Azure CLI'
titlesuffix: Azure NAT service
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót az Azure CLI használatával
services: nat
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a NAT Gateway for outbound connectivity for my virtual network.
ms.service: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: e7b78451ea6bf0a19eb358c8777ce59c07e32fd8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376398"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Rövid útmutató: NAT-átjáró létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure NAT szolgáltatást, és hogyan hozhat létre NAT-átjárót az Azure-beli virtuális gépek kimenő kapcsolatának biztosításához. 

>[!NOTE] 
>Az Azure NAT szolgáltatás jelenleg nyilvános előzetes verzióként érhető el, és korlátozott számú [régióban](https://azure.microsoft.com/global-infrastructure/regions/)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot az Azure Cloud Shell használatával végezheti el, vagy a megfelelő parancsokat helyileg futtathatja.  Ha még soha nem használta az Azure Cloud shellt, [Jelentkezzen be most](https://shell.azure.com) , és lépjen be a kezdeti beállításba.

Ha a parancsok helyi futtatását választja, telepítenie kell a CLI-t.  Ehhez az oktatóanyaghoz az Azure CLI 2.0.71 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroupNAT* nevű erőforráscsoportot a *eastus2* helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános internethez való hozzáféréshez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIP** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

Egy vagy több nyilvános IP-cím-erőforrást, illetve egy vagy több nyilvános IP-előtagot vagy mindkettőt használhat a NAT-átjáróval. Ehhez a forgatókönyvhöz hozzá fogunk adni egy nyilvános IP-előtag-erőforrást, amely bemutatja.   Az [az Network Public-IP előtag Create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) paranccsal hozzon létre egy **myPublicIPprefix** nevű nyilvános IP-előtag-erőforrást a **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

Hozzon létre egy globális Azure NAT-átjárót az [az Network NAT Gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) nevű **myNATgateway** , amely a nyilvános IP- **myPublicIP** és a nyilvános IP-előtag **myPublicIPprefix** is használja, és az üresjárati időkorlátot 10 percre módosítja.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Ezen a ponton a NAT-átjáró működik, és az összes hiányzó beállítással konfigurálható, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gép üzembe helyezése és a NAT-átjáró használata előtt létre kell hozni a virtuális hálózatot.

Az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) paranccsal hozzon létre a **myResourceGroup** erőforráscsoportban egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal.  A virtuális hálózat IP-címe **192.168.0.0/16** , a virtuális hálózaton belüli alhálózat pedig **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT szolgáltatás konfigurálása a forrás-alhálózathoz

A virtuális hálózati **myVnet** lévő **mySubnet** a NAT-átjáró egy adott erőforrás- **myNAT** az [az Network vnet subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet)paranccsal való használatára konfigurálja.  Ez a parancs aktiválja a NAT szolgáltatást a megadott alhálózaton.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

A NAT szolgáltatás mostantól az összes internetes célhelyre irányuló kimenő forgalmat használja.  A UDR konfigurálása nem szükséges.

## <a name="create-a-vm-to-use-the-nat-service"></a>Virtuális gép létrehozása a NAT szolgáltatás használatához

Most létrehozunk egy virtuális gépet a NAT szolgáltatás használatához.  Ez a virtuális gép egy nyilvános IP-címmel rendelkezik, amely egy példány szintű nyilvános IP-címet használ, hogy lehetővé tegye a virtuális gép elérését.  A NAT szolgáltatás a flow irányának ismerete, és az alapértelmezett internetes célhelyet váltja fel az alhálózaton. A virtuális gép nyilvános IP-címe nem használható kimenő kapcsolatokhoz.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP-cím létrehozása a forrásként szolgáló virtuális gép számára

Létrehozunk egy nyilvános IP-címet, amelyet a virtuális gép eléréséhez kíván használni.  Az [az Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>NSG létrehozása virtuális géphez

Mivel a standard nyilvános IP-címek alapértelmezetten "biztonságosak", létre kell hozni egy NSG, amely lehetővé teszi az SSH-hozzáférés bejövő hozzáférését. Az [az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) paranccsal hozzon létre egy **myNSG** nevű NSG-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-végpont közzététele a forrásoldali virtuális gépen

Hozzunk létre egy szabályt a NSG a forrás virtuális géphez való SSH-hozzáféréshez. Az [az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) paranccsal hozzon létre egy **SSH** nevű NSG-SZABÁLYT a **myNSG** nevű NSG a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Hálózati adapter létrehozása virtuális géphez

Hozzon létre egy hálózati adaptert az az [Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa a nyilvános IP-címet és a hálózati biztonsági csoportot. 

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

Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.  A virtuális gép SSH-kulcsait generáljuk, és a titkos kulcsot később is használhatja.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys \
```

Várjon, amíg a virtuális gép üzembe helyezése befejeződik, majd folytassa a további lépések végrehajtásával.

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felderítése

Először fel kell deríteni a létrehozott virtuális gép IP-címét. A virtuális gép nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)paranccsal. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy hozzáférjen a virtuális géphez.

### <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépre

Az SSH hitelesítő adatait az előző műveletből kell tárolni a Cloud shellben.  Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre.

```bash
ssh <ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a NAT szolgáltatás használatához. Ha többet szeretne megtudni az Azure NAT szolgáltatásról, folytassa az Azure NAT szolgáltatással kapcsolatos egyéb oktatóanyagokkal.

A Azure Monitor mérőszámait megtekintheti a NAT szolgáltatás működésének megtekintéséhez és az olyan problémák diagnosztizálásához, mint például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címek vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel.

> [!div class="nextstepaction"]

