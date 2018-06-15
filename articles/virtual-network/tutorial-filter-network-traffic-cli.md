---
title: Hálózati forgalmat - az Azure parancssori felület |} Microsoft Docs
description: Ebből a cikkből megtanulhatja a hálózati biztonsági csoport, az Azure parancssori felülettel rendelkező alhálózathoz, a hálózati forgalom szűrésére.
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
ms.openlocfilehash: 11dc0e5f6ee398b2a745ed60cbc166e2a1697c3e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423177"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Hálózati forgalom szűrésére, a hálózati biztonsági csoport az Azure parancssori felület használatával

Szűrheti a hálózati forgalom a bejövő és kimenő virtuális hálózati alhálózat a hálózati biztonsági csoport. Hálózati biztonsági csoportok tartalmazzák a szabályokat, amelyek IP-cím, port és protokoll által a hálózati forgalom szűrésére. Egy alhálózaton üzembe helyezett erőforrás biztonsági szabályok érvényesek. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* A hálózati biztonsági csoport és a biztonsági szabályok létrehozása
* Hozzon létre egy virtuális hálózatot, és rendelje hozzá egy alhálózatot a hálózati biztonsági csoport
* Virtuális gépek (VM) telepítése egy alhálózatba
* Forgalomszűrők tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk számára szükséges, hogy futnak-e az Azure parancssori felület 2.0.28 verzió vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hálózati biztonsági csoport biztonsági szabályokat tartalmazza. Biztonsági szabályok adja meg a forrás és cél. Források és célok lehetnek biztonsági csoportok.

### <a name="create-application-security-groups"></a>Biztonsági csoportok létrehozása

Először hozzon létre egy erőforráscsoportot, ebben a cikkben létrehozott összes erőforrás [az csoport létrehozása](/cli/azure/group#az_group_create). A következő példa egy erőforrás csoportot hoz létre a *eastus* helye: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Az alkalmazás biztonsági csoport létrehozása [az hálózati asg létrehozása](/cli/azure/network/asg#az_network_asg_create). Az alkalmazáscsoport biztonsági lehetővé teszi csoport kiszolgálókat a hasonló Portszűrési követelmények. Az alábbi példakód létrehozza a két alkalmazás biztonsági csoportok.

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

Hozzon létre egy hálózati biztonsági csoport [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Szabályok létrehozása

A biztonsági szabály létrehozása [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Az alábbi példa létrehoz egy szabályt, amely lehetővé teszi, hogy a forgalom az internetről bejövő a *myWebServers* alkalmazáscsoport biztonsági 80-as és 443-as portokon keresztül:

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

Az alábbi példa létrehoz egy szabályt, amely lehetővé teszi, hogy a forgalom az internetről bejövő a *myMgmtServers* alkalmazáscsoport biztonsági 22-es porton keresztül:

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

Ebben a cikkben SSH (22-es portot) az interneten van kitéve a *myAsgMgmtServers* virtuális gép. Éles környezetben 22-es porton csatlakozik az internethez, hogy csökkenjen helyett javasoljuk, hogy csatlakozni használatával kezelni kívánt Azure-erőforrások egy [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [titkos](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hálózati kapcsolat.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa létrehoz egy virtuális nevű *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Adjon hozzá egy alhálózatot a virtuális hálózat [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). A következő példakóddal felveheti nevű alhálózat *mySubnet* a virtuális hálózat és társult a *myNsg* hálózati biztonsági csoportra, és azt:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépek a virtuális hálózat, hogy ellenőrizhesse a forgalom egy későbbi lépésben szűrést. 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példakód létrehozza a virtuális gép, egy webkiszolgáló erre a célra. A `--asgs myAsgWebServers` beállítás hatására az Azure létrehozza a virtuális gép tagja hálózati kapcsolat létrehozásához a *myAsgWebServers* alkalmazás biztonsági csoport.

A `--nsg ""` beállítás megadása a Azure hoz létre, amikor létrehozza a virtuális hálózati adapter alapértelmezett hálózati biztonsági csoport létrehozása az Azure megakadályozása. Ez a cikk egyszerűsítésére, a jelszó szolgál. Kulcsok általában használják az éles környezetekben. Ha kulcsok használja, konfigurálnia kell a SSH-ügynöktovábbítást a hátralévő lépéseket. További információkért az SSH-ügyfél a dokumentációban. Cserélje le `<replace-with-your-password>` az alábbi parancs a jelszóval.

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

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az alábbi példához hasonló kimenetet visszaadja: 

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

Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt.  Hozzon létre egy virtuális Gépet egy felügyeleti kiszolgálója:

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

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után vegye figyelembe a **publicIpAddress** visszaadott kimenet. Ez a cím a következő lépésben a virtuális gép eléréséhez használt. Nem folytathatja a következő lépés a virtuális gép létrehozása Azure befejezéséig.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

Az SSH-munkamenetet létrehozni a következő paranccsal a *myVmMgmt* virtuális gép. Cserélje le *<publicIpAddress>* a virtuális gép a nyilvános IP-címmel. A fenti példában az IP-cím van *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Amikor a rendszer kéri a jelszót, adja meg a megadott jelszó [hozzon létre virtuális gépek](#create-virtual-machines).

A csatlakozás sikeres, mert a 22-es portot az internetről bejövő engedélyezett a *myAsgMgmtServers* biztonsági csoport, amely a hálózati adapter csatlakozik a *myVmMgmt* virtuális gép.

A következő parancs segítségével SSH-kapcsolatot a *myVmWeb* virtuális gép a *myVmMgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

A csatlakozás sikeres, mert az egyes hálózati biztonsági csoportok alapértelmezett biztonsági szabály lehetővé teszi, hogy a forgalmat a virtuális hálózaton belül az összes IP-címek között az összes porton keresztül. SSH-kapcsolatot nem lehet a *myVmWeb* VM az internetről, mert a biztonsági szabály az a *myAsgWebServers* port nem engedélyezi az internetről bejövő 22.

Az alábbi parancsokkal nginx kiszolgáló telepíthető a *myVmWeb* VM:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

A *myVmWeb* VM engedélyezett kimenő nginx beolvasni, mert az alapértelmezett biztonsági szabály lehetővé teszi minden kimenő forgalom az internethez az internethez. Kilépés a *myVmWeb* SSH-munkamenetet, így a `username@myVmMgmt:~$` Rákérdezés a a *myVmMgmt* virtuális gép. A nginx üdvözlőképernyő beolvasása a *myVmWeb* VM, adja meg a következő parancsot:

```bash
curl myVmWeb
```

Jelentkezzen ki a *myVmMgmt* virtuális gép. Annak ellenőrzéséhez, hogy hozzá tud férni a *myVmWeb* webkiszolgáló, az Azure-on kívüli, adja meg `curl <publicIpAddress>` a saját számítógépéről. A csatlakozás sikeres, mert a 80-as porton az internetről bejövő engedélyezett a *myAsgWebServers* biztonsági csoport, amely a hálózati adapter csatlakozik a *myVmWeb* virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure/group#az_group_delete) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy hálózati biztonsági csoportot hozott létre, és azt társított virtuális hálózati alhálózat. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoport – áttekintés](security-overview.md) és [a hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

Alapértelmezés szerint az alhálózatok közötti forgalmat Azure útvonalak. Ehelyett választhatja, hogy egy virtuális Gépet, mint egy tűzfal keresztül alhálózatok közötti forgalom például. Megtudhatja, hogyan: [hoznia egy útválasztási táblázatot](tutorial-create-route-table-cli.md).