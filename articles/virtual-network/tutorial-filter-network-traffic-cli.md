---
title: Hálózati forgalom szűrése - Azure CLI | Microsoft dokumentumok
description: Ebben a cikkben megtudhatja, hogyan szűrheti a hálózati forgalmat egy alhálózat, egy hálózati biztonsági csoport, az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 72c8b4d57b5064af34665cff1386179e62324938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235080"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Hálózati forgalom szűrése hálózati biztonsági csoporttal az Azure CLI használatával

A virtuális hálózatok alhálózatainak bejövő vagy kimenő hálózati forgalmát hálózati biztonsági csoport segítségével szűrheti. A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak, amelyek IP-cím, port és protokoll szerint szűrik a hálózati forgalmat. A biztonsági szabályok az egyes alhálózatokban üzembe helyezett erőforrásokra vonatkoznak. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hálózati biztonsági csoport és biztonsági szabályok létrehozása
* Virtuális hálózat létrehozása és hálózati biztonsági csoport hozzárendelése egy alhálózathoz
* Virtuális gépek üzembe helyezése egy alhálózaton
* Forgalomszűrők tesztelése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.0.28-as vagy újabb verzióját futassza. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak. A biztonsági szabályok egy forrást és egy célt határoznak meg. A források és célok lehetnek alkalmazásbiztonsági csoportok.

### <a name="create-application-security-groups"></a>Alkalmazásbiztonsági csoportok létrehozása

Először hozzon létre egy erőforráscsoportot a cikkben létrehozott összes erőforráshoz az [az csoport létrehozásával.](/cli/azure/group) A következő példa létrehoz egy erőforráscsoportot az *eastus* helyen: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy alkalmazásbiztonsági csoportot [az hálózati asg create](/cli/azure/network/asg)segítségével. Az alkalmazásbiztonsági csoportok lehetővé teszik, hogy csoportokba rendezze a hasonló portszűrési követelményekkel rendelkező kiszolgálókat. Az alábbi példa két alkalmazásbiztonsági csoportot hoz létre.

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

Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create](/cli/azure/network/nsg)segítségével. Az alábbi példa egy *myNsg* nevű hálózati biztonsági csoportot hoz létre: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Biztonsági szabályok létrehozása

Hozzon létre egy biztonsági szabályt [az az network nsg szabállyal létrehozva.](/cli/azure/network/nsg/rule) Az alábbi példa egy olyan szabályt hoz létre, amely engedélyezi az internetről a *myWebServers* alkalmazásbiztonsági csoportba bejövő forgalmat a 80-as és 443-as porton keresztül.

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

A következő példa létrehoz egy szabályt, amely lehetővé teszi az internetről a *myMgmtServers* alkalmazás biztonsági csoportjába érkező forgalmat a 22-es porton keresztül:

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

Ebben a cikkben az SSH (22-es port) elérhető a *myAsgMgmtServers* vm internetkapcsolatának. Éles környezetben, ahelyett, hogy a 22-es port az interneten, javasoljuk, hogy csatlakozzon az Azure-erőforrások, amelyek [vpn-vagy](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [magánhálózati](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kapcsolaton keresztül szeretne kezelni.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Az alábbi példa egy *myVirtualNetwork* virtuális hálózatot hoz létre:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Alhálózat hozzáadása az hálózati [virtuális hálózat hoz](/cli/azure/network/vnet/subnet)létre alhálózattal rendelkező virtuális hálózathoz. Az alábbi példa egy *mySubnet* nevű alhálózatot ad hozzá a virtuális hálózathoz, és hozzárendeli a *myNsg* hálózati biztonsági csoportot.

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

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa egy virtuális gépet hoz létre, amely webkiszolgálóként fog szolgálni. A `--asgs myAsgWebServers` beállítás hatására az Azure a virtuális gép számára létrehozott hálózati adaptert a *myAsgWebServers* alkalmazás biztonsági csoportjának tagjává teszi.

A `--nsg ""` beállítás megakadályozza, hogy az Azure hozzon létre egy alapértelmezett hálózati biztonsági csoportot az Azure által létrehozott hálózati csatolóhoz, amikor létrehozza a virtuális gép. A cikk egyszerűsítéséhez jelszót használ. A kulcsokat általában éles környezetekben használják. Ha kulcsokat használ, a fennmaradó lépésekhez konfigurálnia kell az SSH ügynöktovábbítást is. További információt az SSH-ügyfél dokumentációjában talál. Cserélje `<replace-with-your-password>` le a következő parancsot egy ön által választott jelszóra.

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után a következő példához hasonló kimenetet ad vissza: 

```output
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

Jegyezze fel a **publicIpAddress** értékét. Ez a cím egy későbbi lépésben az internetről való eléréséhez használható.  Hozzon létre egy felügyeleti kiszolgálóként szolgáló virtuális gépet:

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után vegye figyelembe a **nyilvános IpAddress** a visszaadott kimenetben. Ez a cím a virtuális gép eléréséhez használható a következő lépésben. Ne ugorjon a következő lépésre, amíg az Azure be nem fejezte a virtuális gép létrehozását.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

Az alábbi paranccsal ssh-munkamenetet hozhat létre a *myVmMgmt* virtuális géppel. Cserélje le * \<a nyilvánosIpAddress>* a virtuális gép nyilvános IP-címére. A fenti példában az IP-cím *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Amikor jelszót kér, írja be a [Virtuális gépek létrehozása mezőbe](#create-virtual-machines)beírt jelszót.

A kapcsolat sikeres, mert a 22-es port bevihető az internetről a *myVmMgmt* virtuális géphez csatlakoztatott hálózati illesztőhöz csatlakoztatott *myAsgMgmtServers* alkalmazás biztonsági csoportjába.

Használja a következő parancsot az SSH-hoz a *myVmWeb* virtuális géphez a *myVmMgmt* virtuális gépről:

```bash 
ssh azureuser@myVmWeb
```

A kapcsolat sikeresen létrejön, mert az egyes hálózati biztonsági csoportokon belüli alapértelmezett biztonsági szabály minden porton keresztül engedélyezi a forgalmat a virtuális hálózaton belüli összes IP-cím között. Nem ssh a *myVmWeb* virtuális gép az internetről, mert a biztonsági szabály a *myAsgWebServers* nem teszi lehetővé port 22 bejövő az internetről.

A nginx webkiszolgáló tavweb *myVmWeb* virtuális gépre való telepítéséhez használja a következő parancsokat:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

A *myVmWeb* virtuális gép az internetre való kimenő hozzáféréssel érhető el a nginx lekéréséhez, mivel az alapértelmezett biztonsági szabály lehetővé teszi az összes kimenő forgalmat az internetre. Lépjen ki a *myVmWeb* SSH munkamenetből, így a `username@myVmMgmt:~$` *myVmMgmt* virtuális gép üzenetében marad. A nginx üdvözlőképernyő *myVmWeb* virtuális gépről való lekéréséhez írja be a következő parancsot:

```bash
curl myVmWeb
```

Kijelentkezés a *myVmMgmt* virtuális gépből. Annak ellenőrzéséhez, hogy az Azure-on kívülről is `curl <publicIpAddress>` elérhető-e a *myVmWeb* webkiszolgáló, írja be a saját számítógépéről. A kapcsolat sikeres, mert a 80-as port bevihető az internetről a *myVmWeb* virtuális géphez csatlakoztatott myVmWeb virtuális géphez csatlakoztatott hálózati adapter által csatlakoztatott *myAsgWeb alkalmazás* biztonsági csoportjába.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlésével](/cli/azure/group) távolítsa el az erőforráscsoportot és az összes benne lévő erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy hálózati biztonsági csoportot, és egy virtuális hálózati alhálózathoz társította. A hálózati biztonsági csoportokkal kapcsolatos további információ: [Hálózati biztonsági csoportok áttekintése](security-overview.md) és [Hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

Az Azure alapértelmezés szerint irányítja a forgalmat az alhálózatok között. Ehelyett lehetősége van arra, hogy egy virtuális gépen keresztül irányítsa a forgalmat az alhálózatok között, amely így például tűzfalként is szolgálhat. Ebből megtudhatja, hogyan, [lásd: Útvonaltábla létrehozása](tutorial-create-route-table-cli.md).
