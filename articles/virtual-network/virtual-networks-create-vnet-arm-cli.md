---
title: "Hozzon létre egy Azure virtuális hálózatra több alhálózatból állnak – az Azure parancssori felület |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre virtuális hálózatot, az Azure parancssori felület használatával több alhálózattal."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: feb8fb218b8bde88ba7fbdc3419e32c9313c7885
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>Hozzon létre egy virtuális hálózatot, az Azure parancssori felület használatával több alhálózattal

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások való kommunikációhoz az internetes és közvetlenül egymás mellett. Több alhálózattal a virtuális hálózat létrehozása lehetővé teszi, hogy a hálózati szegmenseket, így szűrheti vagy -vezérlőket a alhálózatok közötti forgalmat. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Alhálózat létrehozása
> * Virtuális gépek közötti hálózati kommunikációhoz tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli.md). 

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVirtualNetwork* a címelőtaggal rendelkező *10.0.0.0/16*. A parancs létrehoz egy alhálózat nevű *nyilvános*, a címelőtaggal rendelkező *10.0.0.0/24*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Mivel az előző parancsban nem adott meg egy helyet, Azure hoz létre a virtuális hálózat ugyanazon a helyen, amely a *myResourceGroup* erőforráscsoport megtalálható. A **-címelőtagokat** és **-előtagot** CIDR-formátumban vannak megadva. A megadott címelőtag az IP-címek 10.0.0.0-10.0.255.254 tartalmazza. A megadott alhálózat-előtagot a címelőtag a virtuális hálózathoz definiált tartományba kell esnie. Az Azure DHCP az alhálózati cím előtag IP-címet rendel egy alhálózaton üzembe helyezett erőforrás. Azure csak rendel a címek 10.0.0.4-10.0.0.254 belül üzembe helyezett erőforrás a **nyilvános** alhálózatot, mert Azure fenntartja az első négy címeket (10.0.0.0-10.0.0.3 az alhálózat ebben a példában) és a legutóbbi címek () az alhálózat ebben a példában 10.0.0.255-ig) minden alhálózatban.

## <a name="create-a-subnet"></a>Alhálózat létrehozása

Hozzon létre egy alhálózat [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Az alábbi példakód létrehozza nevű alhálózat *titkos* belül a *myVirtualNetwork* címelőtag a virtuális hálózati *10.0.1.0/24*. A címelőtag a virtuális hálózathoz definiált címelőtag belül kell lennie, és nem lehetnek átfedésben más alhálózatok virtuális hálózatban címelőtag.

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

Az Azure virtuális hálózatok és alhálózatok üzemi használatra való telepítése előtt javasoljuk, hogy alaposan megismerje a címterület [szempontok](virtual-network-manage-network.md#create-a-virtual-network) és [virtuális hálózati korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Amennyiben az erőforrások telepítése alhálózatokra, virtuális hálózati és alhálózati módosításokat, például megváltoztatni a címtartományt, alhálózatok belül rendszerbe meglévő Azure-erőforrások újratelepítés lehet szükség.

## <a name="test-network-communication"></a>Tesztelje a hálózati kommunikációban

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások való kommunikációhoz az internetes és közvetlenül egymás mellett. A virtuális hálózatba telepítése erőforrás egy típus egy virtuális gépet. Hozzon létre két virtuális gép a virtuális hálózat, amellyel tesztelheti őket, és az Internet közötti hálózati kommunikációhoz egy későbbi lépésben.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális gép létrehozása [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Az alábbi példa létrehoz egy virtuális gépet, nevű *myVmWeb* a a *nyilvános* alhálózat. A `--no-wait` paraméter lehetővé teszi, hogy az Azure a parancs végrehajtása a háttérben, így a következő paranccsal is. Ez az oktatóanyag egyszerűsítésére, a jelszó szolgál. Kulcsok általában használják az éles környezetekben. Ha kulcsok használja, konfigurálnia kell a SSH-ügynöktovábbítást a fennmaradó végrehajtásához. SSH-ügynöktovábbítást kapcsolatos további információkért az SSH-ügyfél a dokumentációban. Cserélje le `<replace-with-your-password>` az alábbi parancs a jelszóval.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Azure automatikusan 10.0.0.4 beléptetésiprofil-oszlopként hozzárendelve a magánhálózati IP-cím a virtuális gép, mert 10.0.0.4 az első elérhető IP-cím a *nyilvános* alhálózat. 

A virtuális gép létrehozása a *titkos* alhálózat.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI-kimenet visszaadása a következőhöz hasonló: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

A példa kimenetben megjelenik az **privateipaddress tulajdonságot** van *10.0.1.4*. Azure létrehozott egy [hálózati illesztő](virtual-network-network-interface.md), akkor a virtuális géphez csatolt, a hálózati adapter magánhálózati IP-címmel, és egy **macAddress**. Azure DHCP automatikusan rendelt 10.0.1.4 hálózati kapcsolat, mert az első elérhető IP-cím a *titkos* alhálózat. A privát IP-cím és MAC-címek maradnak hozzárendelve a hálózati adapter, amíg nem törli a hálózati illesztőt. 

Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt. Bár a virtuális gép nem kell egy nyilvános IP-címet kap, Azure rendel hozzá egy nyilvános IP-cím minden virtuális gépet hoz létre, alapértelmezés szerint. Útján kommunikálnak az interneten a virtuális gép, egy nyilvános IP-címet kell rendelni a virtuális géphez. Minden virtuális gépek kommunikálhatnak az internettel kimenő, függetlenül attól, egy nyilvános IP-címet a virtuális géphez van rendelve. Az Azure-ban kimenő internetes kapcsolatok kapcsolatos további információkért lásd: [az Azure-ban kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ez a cikk a létrehozott virtuális gépek rendelkezik ilyennel [hálózati illesztő](virtual-network-network-interface.md) dinamikusan hozzárendelt hálózati illesztő egy IP-címmel. A virtuális gép telepítése után is [több nyilvános és magánhálózati IP-címek hozzáadása vagy módosítása a IP-cím hozzárendelés metódus statikus](virtual-network-network-interface-addresses.md#add-ip-addresses). Is [hozzáadása a hálózati adapterek](virtual-network-network-interface-vm.md#vm-add-nic), akár által támogatott maximális a [Virtuálisgép-méretet](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) válassza, ha egy virtuális gépet hoz létre. Emellett [egygyökerű i/o-virtualizálás (SR-IOV) engedélyezése](create-vm-accelerated-networking-cli.md) a virtuális gép, de csak akkor, ha egy virtuális gép létrehozásához, amely a funkció támogatja a Virtuálisgép-méretet.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>A virtuális gépek között, és az internetes kommunikáció

Az SSH-munkamenetet létrehozni, az alábbi parancs segítségével a *myVmMgmt* virtuális gépet. Cserélje le `<publicIpAddress>` a virtuális gép a nyilvános IP-címmel. Az előző példában a nyilvános IP-cím van *13.90.242.231*. Amikor a rendszer kéri a jelszót, adja meg a megadott jelszó [virtuális gépek létrehozása](#create-virtual-machines).

```bash 
ssh azureuser@<publicIpAddress>
```

Biztonsági okokból általában távolról csatlakozhat a virtuális hálózat virtuális gépek számának korlátozása. Ebben az oktatóanyagban a *myVmMgmt* történő felügyeletére szolgál a virtuális gép a *myVmWeb* virtuális gép virtuális hálózatban. A következő parancs segítségével SSH-kapcsolatot a *myVmWeb* a virtuális gép a *myVmMgmt* virtuális gépet:

```bash 
ssh azureuser@myVmWeb
```

Való kommunikációra a *myVmMgmt* virtuális gépek létrehozását a *myVmWeb* virtuális gépet, adja meg a következő parancsot a parancssorba:

```
ping -c 4 myvmmgmt
```

A következő egy példa a kimenetre hasonló kimenetet jelenhet meg:
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
Láthatja, hogy a cím a *myVmMgmt* virtuális gép 10.0.1.4. 10.0.1.4 volt az első elérhető IP-címek tartománya, a *titkos* alhálózati központilag telepített a *myVmMgmt* az előző lépésben a virtuális gép.  Azt látja, hogy a virtuális gép teljesen minősített tartománynevét *myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net*. Bár a *hxehizax3z1udjnrx1r4gr30pg* a tartománynév része nem egyezik a virtuális gép, a tartomány nevét az többi részének azonosak. Alapértelmezés szerint az összes Azure virtuális gép használja az alapértelmezett Azure DNS szolgáltatást. Az összes virtuális gép a virtuális hálózaton belül az azonos virtuális hálózatban, Azure alapértelmezett DNS-szolgáltatás segítségével más virtuális gépek képes névfeloldásra. Azure alapértelmezett DNS-szolgáltatás helyett használhatja a saját DNS-kiszolgáló vagy a saját szolgáltatása az Azure DNS-szolgáltatás. További információkért lásd: [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) vagy [titkos tartományok Azure DNS használatával](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Az alábbi parancsokkal nginx kiszolgáló telepíthető a *myVmWeb* virtuális gépet:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Nginx telepítése után zárja be a *myVmWeb* SSH-munkamenetet, így Ön a írja be a *myVmMgmt* virtuális gépet. Adja meg a következő parancsot a nginx üdvözlőképernyő beolvasása a *myVmWeb* virtuális gépet.

```bash
curl myVmWeb
```

Az üdvözlőképernyőn nginx adja vissza.

Zárja be az SSH-munkamenetet a *myVmMgmt* virtuális gépet.

Azure létrehozásakor a *myVmWeb* virtuális gép, egy nyilvános IP-cím nevű *myVmWebPublicIP* is létrejött, de a virtuális géphez rendelt. Azure rendelni, amelyben a nyilvános címének [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#az_network_public_ip_show).

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

A saját számítógépéről, írja be a következő parancsot, cseréje `<publicIpAddress>` az előző parancs által visszaadott címmel:

```bash
curl <publicIpAddress>
```

A saját számítógépéről nginx üdvözlőképernyő curl sikertelen lesz. A kísérlet meghiúsul, mert a virtuális gépek üzembe helyezése, amikor Azure alapértelmezés szerint minden egyes virtuális gép hálózati biztonsági csoport létrehozása. 

Hálózati biztonsági csoport biztonsági szabályokat, amelyek engedélyezik vagy megtagadják a bejövő és kimenő hálózati forgalom port és az IP-címet tartalmazza. Az alapértelmezett hálózati biztonsági csoport létrehozása Azure lehetővé teszi a kommunikációt az azonos virtuális hálózatban lévő erőforrások közötti összes portokon keresztül. A Linux virtuális gépek összes portokon keresztül az alapértelmezett hálózati biztonsági csoport megtagadja a az internetről érkező összes bejövő forgalmat, fogadja el a TCP-port a 22-(SSH). Ennek eredményeképpen alapértelmezés szerint is létrehozhat SSH-munkamenetet közvetlenül a *myVmWeb* virtuális gép az internetről, annak ellenére, hogy nem érdemes port 22 nyissa meg a webkiszolgálón. Mivel a `curl` parancs, 80-as porton keresztül kommunikál a kommunikáció nem sikerül az internetről, mert nincs szabály átengedi a forgalmat a 80-as porton keresztül alapértelmezett hálózati biztonsági csoport.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure/group#az_group_delete) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudta, hogyan telepíthet egy virtuális hálózatot, több alhálózattal. Is megtanulta, hogy létrehozott egy Linux virtuális gép, Azure hoz létre, hogy azt a virtuális gép csatlakozik, és létrehoz egy hálózati biztonsági csoportot, amely csak forgalom porton 22, az internetről a hálózati adaptert. A következő oktatóanyag áttekintésével megismerheti, hogyan alhálózatokra, és nem az egyes virtuális gépek hálózati forgalom szűrésére továbblépés.

> [!div class="nextstepaction"]
> [Hálózati forgalom szűrésére alhálózathoz](./virtual-networks-create-nsg-arm-cli.md)
