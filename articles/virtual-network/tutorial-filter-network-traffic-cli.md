---
title: Szűrheti a hálózati forgalom – Azure CLI-vel |} A Microsoft Docs
description: Ebben a cikkben megismerheti, hogyan szűrheti a hálózati forgalmat alhálózatokra egy hálózati biztonsági csoportot, az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3252395c7a511a00e8da0a31139fce3b2763decb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461841"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Hálózati forgalom szűrése hálózati biztonsági csoport az Azure CLI használatával

A virtuális hálózatok alhálózatainak bejövő vagy kimenő hálózati forgalmát hálózati biztonsági csoport segítségével szűrheti. A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak, amelyek IP-cím, port és protokoll szerint szűrik a hálózati forgalmat. A biztonsági szabályok az egyes alhálózatokban üzembe helyezett erőforrásokra vonatkoznak. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hálózati biztonsági csoport és biztonsági szabályok létrehozása
* Virtuális hálózat létrehozása és hálózati biztonsági csoport hozzárendelése egy alhálózathoz
* Virtuális gépek üzembe helyezése egy alhálózaton
* Forgalomszűrők tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.28-as vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak. A biztonsági szabályok egy forrást és egy célt határoznak meg. A források és célok lehetnek alkalmazásbiztonsági csoportok.

### <a name="create-application-security-groups"></a>Alkalmazásbiztonsági csoportok létrehozása

Először hozzon létre egy erőforráscsoportot a a jelen cikkben létrehozott összes erőforrás [az csoport létrehozása](/cli/azure/group#az_group_create). A következő példa létrehoz egy erőforráscsoportot az *eastus* helyen: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy alkalmazásbiztonsági csoportot a [az hálózati adaptertől létrehozása](/cli/azure/network/asg#az_network_asg_create). Az alkalmazásbiztonsági csoportok lehetővé teszik, hogy csoportokba rendezze a hasonló portszűrési követelményekkel rendelkező kiszolgálókat. Az alábbi példa két alkalmazásbiztonsági csoportot hoz létre.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoport [az network nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). Az alábbi példa egy *myNsg* nevű hálózati biztonsági csoportot hoz létre: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Biztonsági szabályok létrehozása

Hozzon létre egy biztonsági szabályt az [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Az alábbi példa egy olyan szabályt hoz létre, amely engedélyezi az internetről a *myWebServers* alkalmazásbiztonsági csoportba bejövő forgalmat a 80-as és 443-as porton keresztül.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

Az alábbi példa létrehoz egy szabályt, amely lehetővé teszi, hogy a forgalom az internetről bejövő a *myMgmtServers* alkalmazásbiztonsági csoport 22-es porton keresztül:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

Ez a cikk az SSH (22-es port) közvetlenül csatlakozik az internethez a *myAsgMgmtServers* virtuális Gépet. Éles környezetben, a 22-es port az internethez való csatlakoztatása helyett javasolt, hogy csatlakozni használatával kezelni kívánt Azure-erőforrások egy [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [privát](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hálózati kapcsolat.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa egy *myVirtualNetwork* virtuális hálózatot hoz létre:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Adjon hozzá egy alhálózatot a virtuális hálózat [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet). Az alábbi példa egy *mySubnet* nevű alhálózatot ad hozzá a virtuális hálózathoz, és hozzárendeli a *myNsg* hálózati biztonsági csoportot.

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózatban, hogy érvényesíthesse majd a fogalom szűrését egy később lépésben. 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példa egy virtuális gépet hoz létre, amely webkiszolgálóként fog szolgálni. A `--asgs myAsgWebServers` beállítás hatására az Azure-t, győződjön meg arról, a hálózati adaptert hoz létre a virtuális gép tagja a *myAsgWebServers* alkalmazásbiztonsági csoporthoz.

A `--nsg ""` megadva a beállítás megakadályozza, hogy az Azure alapértelmezett hálózati biztonsági csoport a hálózati adapter Azure hoz létre, amikor létrehozza a virtuális gép létrehozása. Ez a cikk egyszerűsítésére, jelszó használatos. Kulcsok jellemzően használják az éles környezetekben. Kulcsok használata, ha az SSH-ügynöktovábbítást a hátralévő lépéseket számára is konfigurálnia kell. További információkért tekintse meg az SSH-ügyfél dokumentációját. Cserélje le `<replace-with-your-password>` az a következő parancsot egy tetszőleges jelszót.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az alábbi példához hasonló kimenetet ad vissza: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Jegyezze fel a **publicIpAddress** értékét. Ezzel a címmel eléri a virtuális Gépet egy későbbi lépésben az internetről.  Hozzon létre egy felügyeleti kiszolgálóként szolgáló virtuális gépet:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után jegyezze fel a **publicIpAddress** visszaadott kimenet. Ezzel a címmel a következő lépésben a virtuális gép eléréséhez. Ne ugorjon a következő lépésre, amíg az Azure be nem fejezte a virtuális gép létrehozását.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

Használja az SSH-munkamenet létrehozásához a következő parancs a *myVmMgmt* virtuális Gépet. Cserélje le *<publicIpAddress>* a virtuális gép nyilvános IP-címét. A fenti példában az IP-cím van *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Amikor a rendszer jelszót kér, adja meg a megadott jelszó [hoz létre virtuális gépeket](#create-virtual-machines).

A kapcsolat sikeresen létrejön, mert a 22-es porton engedélyezve van az internetről bejövő a *myAsgMgmtServers* alkalmazásbiztonsági csoport, amely a hálózati adapter csatlakozik a *myVmMgmt* virtuális gép van.

Használja az ssh-n a következő parancsot a *myVmWeb* virtuális gépről a *myVmMgmt* virtuális Géphez:

```bash 
ssh azureuser@myVmWeb
```

A kapcsolat sikeresen létrejön, mert az egyes hálózati biztonsági csoportokon belüli alapértelmezett biztonsági szabály minden porton keresztül engedélyezi a forgalmat a virtuális hálózaton belüli összes IP-cím között. Ssh-KAPCSOLATOT nem lehet a *myVmWeb* virtuális Géphez az internetről, mert a biztonsági szabálya a *myAsgWebServers* port nem engedélyezi az internetről bejövő 22-es.

Használja az alábbi parancsokat az nginx-webkiszolgálót telepítettünk a *myVmWeb* virtuális Géphez:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

A *myVmWeb* engedélyezett, hogy a virtuális gép kimenő lekérni az nginx-et, mert az alapértelmezett biztonsági szabály lehetővé teszi, hogy az internetre irányuló összes kimenő forgalom az internethez. Kilépés a *myVmWeb* SSH-munkamenetből marad, a `username@myVmMgmt:~$` kérdés, a *myVmMgmt* virtuális gép. Az nginx üdvözlőképernyőn beolvasni a *myVmWeb* virtuális Gépet, adja meg a következő parancsot:

```bash
curl myVmWeb
```

Jelentkezzen ki a *myVmMgmt* virtuális Gépet. Annak ellenőrzéséhez, hogy elérhető a *myVmWeb* webkiszolgálót az Azure-on kívül, adja meg, `curl <publicIpAddress>` a saját számítógépéről. A kapcsolat sikeresen létrejön, mert a 80-as porton engedélyezve van az internetről bejövő a *myAsgWebServers* alkalmazásbiztonsági csoport, amely a hálózati adapter csatlakozik a *myVmWeb* virtuális gép van.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [az csoport törlése](/cli/azure/group#az_group_delete) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy hálózati biztonsági csoportot, és hozzárendelte azt egy virtuális hálózat alhálózatához. A hálózati biztonsági csoportokkal kapcsolatos további információ: [Hálózati biztonsági csoportok áttekintése](security-overview.md) és [Hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

Az Azure alapértelmezés szerint irányítja a forgalmat az alhálózatok között. Ehelyett lehetősége van arra, hogy egy virtuális gépen keresztül irányítsa a forgalmat az alhálózatok között, amely így például tűzfalként is szolgálhat. További információ [hozzon létre egy útválasztási táblázatot](tutorial-create-route-table-cli.md).
