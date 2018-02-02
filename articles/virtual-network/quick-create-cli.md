---
title: "Hozzon létre egy virtuális hálózat – az Azure parancssori felület |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy virtuális hálózatot az Azure parancssori felület használatával. A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálhatnak egymással."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b2fc8a622549a9858c6c769a7e648fe07a3d01c1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Hozzon létre egy virtuális hálózatot az Azure parancssori felület használatával

Ebből a cikkből megismerheti, hogyan hozhat létre virtuális hálózatot. Virtuális hálózat létrehozása után két virtuális gépek telepítése a virtuális hálózathoz, és közvetlenül a Microsoftnak egymás közötti kommunikációhoz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A telepített verzió megkereséséhez futtassa `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen. Az összes Azure-erőforrások jönnek létre az Azure-beli hely (vagy területen).

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozása a [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create) parancsot. Az alábbi példa létrehoz egy alapértelmezett nevű virtuális hálózatot *myVirtualNetwork* nevű egyetlen alhálózattal *alapértelmezett*. Nincs megadva a hely, mivel Azure virtuális hálózat és az erőforráscsoport ugyanazon a helyen hoz létre.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

A virtuális hálózat létrejötte után, a visszaadott adatok egy részét az alábbiak szerint van.

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

Minden virtuális hálózat rendelkezik egy vagy több címelőtagokat hozzájuk rendelve. Egy címelőtagot nincs megadva, a virtuális hálózat létrehozásakor, mivel Azure határozza meg a 10.0.0.0/16 címtér alapértelmezett. A címterület CIDR-formátumban van megadva. A cím terület 10.0.0.0/16 10.0.0.0-10.0.255.254 magában foglalja.

Az adatokat egy másik része érkezett a **címelőtagja** a *10.0.0.0/24* a a *alapértelmezett* a parancsban megadott alhálózat. A virtuális hálózati nulla vagy több alhálózatot tartalmaz. A paranccsal létrehozta nevű egyetlen alhálózattal *alapértelmezett*, de nem-címelőtag van megadva az alhálózat. Amikor egy címelőtagot egy virtuális hálózat vagy az alhálózat nincs megadva, Azure meghatározása 10.0.0.0/24 szerint az első alhálózat címelőtag alapértelmezés szerint. Ennek eredményeképpen az alhálózat 10.0.0.0-10.0.0.254 foglal magában, de csak 10.0.0.4-10.0.0.254 érhetők el, mert a Azure fenntartja az első négy címeket (0 – 3) és az utolsó cím minden alhálózatban.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálhatnak egymással. A virtuális hálózatba telepítése erőforrás egy típus egy virtuális gépet. Hozzon létre két virtuális gép a virtuális hálózat, hogy ellenőrizze a virtuális hálózatban lévő virtuális gépek közötti kommunikáció egy későbbi lépésben működésének megismerése.

A virtuális gép létrehozása a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. Az alábbi példa létrehoz egy virtuális gépet, nevű *myVm1*. Ha SSH-kulcsok még nem léteznek a kulcs alapértelmezett helye, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A `--no-wait` beállítás hoz létre a virtuális gép a háttérben, így továbbra is a következő lépéssel.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure automatikusan létrehozza a virtuális gép a *alapértelmezett* alhálózata a *myVirtualNetwork* virtuális hálózat, mert a virtuális hálózat már létezik az erőforráscsoportot, és nem virtuális hálózat vagy a parancsban megadva alhálózat. Azure DHCP automatikusan rendelt 10.0.0.4 a virtuális gép létrehozása, mert az első elérhető címe a *alapértelmezett* alhálózat. A helyre, amely a virtuális gép jön létre a virtuális hálózat szerepel ugyanazon a helyen kell lennie. A virtuális gép nem kell lennie, a virtuális gép ugyanabban az erőforráscsoportban, ha az ebben a cikkben.

Hozzon létre egy második virtuális gépet. Alapértelmezés szerint az Azure is létrehoz a virtuális gép a *alapértelmezett* alhálózat.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI-kimenet visszaadása a következőhöz hasonló: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

A példában látható, amely a **privateipaddress tulajdonságot** van *10.0.0.5*. Az Azure automatikusan hozzárendeli a DHCP *10.0.0.5* a virtuális géphez, mert a következő elérhető cím volt a *alapértelmezett* alhálózat. Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt. A nyilvános IP-cím nincs hozzárendelve a virtuális hálózat vagy az alhálózati cím előtagokat. Nyilvános IP-címek vannak rendelve a egy [rendelt egyes Azure-régiókban címkészletet](https://www.microsoft.com/download/details.aspx?id=41653). Azure tudja, melyik nyilvános IP-cím hozzá van rendelve egy virtuális gépet, miközben a virtuális gépen futó operációs rendszer van nincs tájékoztatási bármely nyilvános IP-cím hozzárendelve.

## <a name="connect-to-a-virtual-machine"></a>Csatlakozzon a virtuális géphez

Az SSH-munkamenetet létrehozni, az alábbi parancs segítségével a *myVm2* virtuális gépet. Cserélje le `<publicIpAddress>` a virtuális gép a nyilvános IP-címmel. A fenti példában az IP-cím van *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="validate-communication"></a>Kommunikációs ellenőrzése

Erősítse meg a kommunikációt a következő paranccsal *myVm1* a *myVm2*:

```bash
ping myVm1 -c 4
```

A négy választ kap *10.0.0.4*. Kommunikálhat *myVm1* a *myVm2*, mert mindkét virtuális gépek magánhálózati IP-címét, amelyet a *alapértelmezett* alhálózat. Biztosan tudni pingelni állomásnév szerint, mert az Azure DNS-névfeloldás automatikusan biztosít a virtuális hálózaton belül minden gazdagép.

A következő paranccsal ellenőrizze a kimenő kommunikációt az internethez:

```bash
ping bing.com -c 4
```

Négy választ kap bing.com. Alapértelmezés szerint a virtuális gépek virtuális hálózatban képes kommunikálni a kimenő internetkapcsolat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [az csoport törlése](/cli/azure/group#az_group_delete) parancs beírásával távolítsa el az erőforráscsoportot és a benne található erőforrásokat. Lépjen ki az SSH-munkamenetet a virtuális géphez, majd törli az erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy alapértelmezett virtuális hálózatot egyetlen alhálózattal és két virtuális gép üzembe. Annak megismerése, hogyan hozzon létre egy egyéni virtuális hálózatot, több alhálózattal és alapvető felügyeleti feladatok elvégzéséhez, továbbra is az egyéni virtuális hálózat létrehozása és kezelése az oktatóanyag.


> [!div class="nextstepaction"]
> [Hozzon létre egy egyéni virtuális hálózatot és az adatbázis felügyeletét](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
