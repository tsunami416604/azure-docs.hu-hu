---
title: "Az Azure virtuális hálózatok és a Linux virtuális gépek |} Microsoft Docs"
description: "Az oktatóanyag - kezelése az Azure virtuális hálózatok és a Linux virtuális gépek az Azure parancssori felület"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0e7f4308290a14e592cf1739fa5b0b3360d7c68b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Egy Azure virtuális hálózatot és a Linux virtuális gépek az Azure parancssori felület kezelése

Az Azure virtuális gépek Azure hálózatkezelés belső és külső hálózati kommunikációhoz használni. Ez az oktatóanyag végigvezeti a két virtuális gép telepítése és konfigurálása ezen a virtuális gépek Azure hálózatkezelés. Ebben az oktatóanyagban szereplő példák feltételezik, hogy, hogy a virtuális gépeket üzemeltető egy adatbázis-háttér webalkalmazás azonban egy alkalmazás nincs telepítve az oktatóanyag. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózat és alhálózat
> * Hozzon létre egy nyilvános IP-címet
> * Előtér-virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttér-virtuális gép létrehozása

Az oktatóanyag végrehajtása során létrehozott ezeket az erőforrásokat látható:

![Két alhálózat virtuális hálózat](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – a virtuális hálózat, amely a virtuális gépek használatával kommunikálnak egymással, és az interneten.
- *myFrontendSubnet* – az alhálózati *myVNet* az előtér-erőforrások használják.
- *myPublicIPAddress* -használt nyilvános IP-cím elérésére *myFrontendVM* az internetről.
- *myFrontentNic* -által használt hálózati illesztőt *myFrontendVM* kommunikálni *myBackendVM*.
- *myFrontendVM* – az internet közötti kommunikációra használja a virtuális gép és *myBackendVM*.
- *myBackendNSG* – a hálózati biztonsági csoportot, amely a közötti kommunikáció a *myFrontendVM* és *myBackendVM*.
- *myBackendSubnet* -a társított alhálózati *myBackendNSG* és a háttér-erőforrások használják.
- *myBackendNic* -által használt hálózati illesztőt *myBackendVM* kommunikálni *myFrontendVM*.
- *myBackendVM* -port a 22-es és 3306 való kommunikációra használja a virtuális gépi *myFrontendVM*.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="vm-networking-overview"></a>VM-hálózat – áttekintés

Egy Azure virtuális hálózatot a virtuális gépek, az internetes és más Azure-szolgáltatások például az Azure SQL-adatbázis között a biztonságos hálózati kapcsolatok engedélyezése. Virtuális hálózatok logikai szegmensekben – ún alhálózatok osztható. Alhálózatok folyamatábrán hálózati és biztonsági határaként szolgálnak. A virtuális gép telepítésekor általában tartalmazza a virtuális hálózati adaptert, alhálózat van csatolva.

## <a name="create-a-virtual-network-and-subnet"></a>Hozzon létre egy virtuális hálózat és alhálózat

Ebben az oktatóanyagban egy virtuális hálózaton, két alhálózattal jön létre. A webalkalmazás üzemeltetéséhez előtér-alhálózatot, és egy adatbázis-kiszolgálót futtató háttér-alhálózatot.

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot, a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot *myRGNetwork* eastus a helyen.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Használja a [az hálózati vnet létrehozása](/cli/azure/network/vnet#create) parancs futtatásával hozzon létre egy virtuális hálózatot. Ebben a példában a hálózati neve *mvVNet* , és egy címelőtagot *10.0.0.0/16*. Egy alhálózat is létrejön, melynek a neve *myFrontendSubnet* és a előtaggal *10.0.1.0/24*. Az oktatóanyag későbbi részében egy előtér-VM a alhálózathoz csatlakozik. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Hozzon létre az alhálózatot

Egy új alhálózatot hozzá lett adva a virtuális hálózat a [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#create) parancsot. Ebben a példában az alhálózat neve *myBackendSubnet* , és egy címelőtagot *10.0.2.0/24*. Ez az alhálózat összes háttérszolgáltatások használatos.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Ezen a ponton a hálózati létrehozott és szegmentált két alhálózat, egy az előtér-szolgáltatásokat, és egy másikat a háttér-szolgáltatásaihoz. A következő szakaszban virtuális gépek létrehozása és ezek alhálózatok csatlakozik.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános IP-cím lehetővé teszi, hogy az Azure-erőforrások elérhetővé az interneten. A kiosztási módszer a nyilvános IP-címet, dinamikus vagy statikus konfigurálhatók. Alapértelmezés szerint egy nyilvános IP-cím dinamikusan történik. Dinamikus IP-címek van kiadva, ha egy virtuális gép felszabadítása. Ennek következtében az IP-cím módosítása bármely, amely tartalmazza a virtuális gép felszabadítás művelet során.

A kiosztási módszer statikus, amely biztosítja, hogy az IP-cím egy megszüntetett állapot esetén egy virtuális géphez hozzárendelt maradnak állítható be. A statikusan lefoglalt IP-cím használata esetén nem adható meg az IP-címet saját magát. Ehelyett az történik a rendelkezésre álló címek készletét.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

A virtuális gép létrehozásakor a [az virtuális gép létrehozása](/cli/azure/vm#create) parancs, az alapértelmezett nyilvános IP-cím címkiosztási módszerét dinamikus. Használatával egy virtuális gép létrehozásakor a [az virtuális gép létrehozása](/cli/azure/vm#create) paranccsal, tartalmazza a `--public-ip-address-allocation static` argumentum egy statikus nyilvános IP-címet hozzárendelni. Ez a művelet nem mutatja be ebben az oktatóanyagban azonban a következő szakaszban a dinamikusan kiosztott IP-címet egy statikusan lefoglalt címet változik. 

### <a name="change-allocation-method"></a>Elosztási módszer váltása

Az IP-cím címkiosztási módszerét használatával módosíthatók a [az hálózati nyilvános ip-frissítés](/cli/azure/network/public-ip#update) parancsot. Ebben a példában az IP-cím címkiosztási módszerét az előtér virtuális gép statikus értékre változott.

Első lépésként felszabadítani a virtuális Gépet.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Használja a [az hálózati nyilvános ip-frissítés](/cli/azure/network/public-ip#update) parancs foglalási módjának frissítésére. Ebben az esetben a `--allocation-method` állítják be *statikus*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Indítsa el a virtuális Gépet.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Nyilvános IP-cím

Gyakran egy virtuális Gépet nem kell elérhetőnek kell lennie az interneten keresztül. Egy virtuális gép, egy nyilvános IP-cím nélküli létrehozásához használja a `--public-ip-address ""` dupla idézőjelek között egy üres számú argumentum. Ez a konfiguráció bemutatott az oktatóanyag későbbi részében.

## <a name="create-a-front-end-vm"></a>Előtér-virtuális gép létrehozása

Használja a [az virtuális gép létrehozása](/cli/azure/vm#create) parancsot nevű virtuális gép létrehozásához *myFrontendVM* használatával *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Biztonságos hálózati adatforgalom

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Lehet, hogy az NSG-k társított alhálózat vagy az egyes hálózati adapterek. Amikor egy NSG-t egy adott hálózati csatoló kapcsolódik, csak a kapcsolódó virtuális gép vonatkozik. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. 

### <a name="network-security-group-rules"></a>Hálózatbiztonságicsoport-szabályok

NSG-szabályok határozza meg, amelyben forgalom engedélyezett vagy megtagadott hálózati portok. A szabályok lehetnek forrás és cél IP-címtartományok, hogy az egyes rendszerek vagy az alhálózatok közötti forgalmat szabályozott. NSG-szabályok terjednie a prioritással (1 – és 4096). Szabályok prioritási sorrendben értékeli ki a rendszer. A 100 prioritással kiértékeli előtt szabály 200 prioritással.

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat.

Az NSG-ket az alapértelmezett szabályok a következők:

- **Virtuális hálózati** - származó forgalmat, és a befejezési egy virtuális hálózat bejövő és kimenő irányban is.
- **Internet** – a kimenő forgalom engedélyezve van, de a bejövő forgalmat blokkol.
- **Terheléselosztó** -engedélyezése Azure terheléselosztó számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ha nem használ elosztott terhelésű készlet, ez a szabály lehet felülbírálni.

### <a name="create-network-security-groups"></a>Hálózati biztonsági csoportok létrehozása

Hálózati biztonsági csoport is létrehozható az azonos időpontban egy virtuális gép használata a [az virtuális gép létrehozása](/cli/azure/vm#create) parancsot. Ennek során, amikor az NSG tartozik a virtuális gépek hálózati illesztő és az NSG-szabályok automatikusan létrehozott forgalmat engedélyezi a port *22* forrásból. Ez az oktatóanyag során korábban küldje el az előtér-NSG lett automatikus létrehozása a az előtér virtuális Géphez. Az NSG-szabályok is 22-es port számára létrehozott automatikus volt. 

Bizonyos esetekben hasznos lehet egy NSG-t, például amikor alapértelmezett SSH szabályokat nem lehet létrehozni, vagy amikor az NSG-t kell csatlakoztatni egy alhálózat előre létrehozásához. 

Használja a [az hálózati nsg létrehozása](/cli/azure/network/nsg#create) parancsot a hálózati biztonsági csoport létrehozásához.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

Helyett a hálózati illesztő NSG társítása, hozzárendelnek egy alhálózathoz. Ebben a konfigurációban a virtuális gép alhálózathoz csatolt örökli az NSG-szabályok.

Frissítse a meglévő nevű alhálózat *myBackendSubnet* rendelkező új NSG.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Biztonságos bejövő forgalom

Az előtér virtuális gép létrehozása után az NSG-szabály létrehozási 22-es portot a bejövő adatforgalom engedélyezésére. Ez a szabály lehetővé teszi, hogy a virtuális gép SSH-kapcsolatok. Ebben a példában a forgalmat is engedélyezni kell a porton *80*. Ez a konfiguráció lehetővé teszi, hogy egy webes alkalmazás érhető el a virtuális Gépen.

Használja a [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#create) parancs futtatásával hozzon létre egy szabályt port *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Az előtér virtuális gép csak az elérhető porton *22* és port *80*. A hálózati biztonsági csoport összes egyéb bejövő forgalom blokkolva van. Az NSG-szabályok konfigurációi megjelenítéséhez hasznos lehet. Térjen vissza a az NSG-konfiguráció a [az hálózati szabálylistában](/cli/azure/network/nsg/rule#list) parancsot. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Virtuális gép biztonságos a virtuális gépek forgalma

Virtuális gépek közötti hálózati biztonsági csoportszabályok is alkalmazhat. Az ebben a példában az előtér-VM a háttér-VM porton kommunikálnia kell *22* és *3306*. Ez a konfiguráció lehetővé teszi, hogy az SSH-kapcsolatok az előtér virtuális gépről, és is lehetővé teszi egy alkalmazás az előtér virtuális gépen egy háttér-beli MySQL database kommunikálni. Az összes többi forgalom le kell tiltani az előtér- és virtuális gépek között.

Használja a [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#create) parancs futtatásával hozzon létre egy szabályt a 22-es portot. Figyelje meg, hogy a `--source-address-prefix` argumentum meghatározza a érték *10.0.1.0/24*. Ez a konfiguráció biztosítja, hogy az csak az előtér-alhálózatból forgalom engedélyezve van-e az NSG keresztül.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Most adja hozzá egy forgalomra vonatkozó szabály MySQL 3306 porton.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Végül mivel az NSG-k egy alapértelmezett szabály, amely lehetővé teszi az ugyanazon virtuális gépek közötti összes forgalom, egy szabály hozható létre a háttér-NSG-ket minden forgalom blokkolása. Figyelje meg, itt, amely a `--priority` értéket kap *300*, amely alacsonyabb, hogy az NSG-t és a MySQL szabályok. Ez a konfiguráció biztosítja, hogy SSH és a MySQL-forgalom továbbra is engedélyezett az NSG keresztül.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Háttér-virtuális gép létrehozása

Most hozzon létre egy virtuális gép, amely csatolva van a *myBackendSubnet*. Figyelje meg, hogy a `--nsg` argumentum értéke üres dupla idézőjelek között. Az NSG nem kell létrehozni a virtuális Gépet. A háttér-alhálózathoz, az előre létrehozott háttér-NSG védi a virtuális Géphez van csatolva. Az NSG-t a virtuális gép vonatkozik. Emellett az információkban, amely a `--public-ip-address` argumentum értéke üres dupla idézőjelek között. Ez a konfiguráció nélkül egy nyilvános IP-cím egy virtuális Gépet hoz létre. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A háttér-virtuális gép csak az elérhető porton *22* és port *3306* az előtér-alhálózatból. A hálózati biztonsági csoport összes egyéb bejövő forgalom blokkolva van. Az NSG-szabályok konfigurációi megjelenítéséhez hasznos lehet. Térjen vissza a az NSG-konfiguráció a [az hálózati szabálylistában](/cli/azure/network/nsg/rule#list) parancsot. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létre és a védett virtuális gépek kapcsolatos Azure-hálózatok. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózat és alhálózat
> * Hozzon létre egy nyilvános IP-címet
> * Előtér-virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttér-virtuális gép létrehozása

Előzetes tájékozódhat az adatvédelem az Azure backup segítségével virtuális gépeket a következő oktatóanyagot. 

> [!div class="nextstepaction"]
> [Készítsen biztonsági másolatot a Linux virtuális gépek Azure-ban](./tutorial-backup-vms.md)
