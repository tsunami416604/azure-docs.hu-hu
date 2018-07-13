---
title: Több hálózati adapterrel – Azure CLI 1.0 (klasszikus) virtuális gép létrehozása |} A Microsoft Docs
description: Útmutató az Azure parancssori felület (CLI), 1.0-t használó több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0b56ab474ff23748487c50bd34487c80242c6429
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651986"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Az Azure CLI 1.0 használatával több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Hozza létre az Azure virtuális gépeken (VM), és csatolni a virtuális gépek mindegyike több hálózati adapterrel (NIC). Több hálózati adapter engedélyezze a hálózati adaptereken keresztüli forgalom típusainak elkülönítését. Ha például egy hálózati adapter előfordulhat, hogy kommunikáljon az internettel, amíg egy másik kommunikál, csak a belső erőforrásokhoz nem csatlakozik az internethez. Lehetővé teszi több hálózati adapter közötti hálózati forgalom külön számos hálózati virtuális berendezések, például az alkalmazásszolgáltatást és WAN-optimalizálás megoldások szükség.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, hogyan használja a következő lépésekkel a [Resource Manager üzemi modell](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Az alábbi lépéseket nevű erőforráscsoportot használ *IaaSStory* a webkiszolgálók és a egy erőforráscsoport nevű *IaaSStory-háttérrendszer* a DB kiszolgálók.

## <a name="prerequisites"></a>Előfeltételek
A DB kiszolgálók hozhat létre, meg kell hoznia a *IaaSStory* erőforráscsoportot az összes szükséges erőforrást ehhez a forgatókönyvhöz. Ezek az erőforrások létrehozásához hajtsa végre a szükséges. Virtuális hálózat létrehozása a lépéseket követve a [hozzon létre egy virtuális hálózatot](virtual-networks-create-vnet-classic-cli.md) cikk.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>A háttérbeli virtuális gép üzembe helyezése
A háttérbeli virtuális gépek létrehozása a következő erőforrások függenek:

* **Storage-fiók adatlemezek**. A jobb teljesítmény érdekében az adatbázis-kiszolgálók a adatlemezét szemben – tartós állapotú meghajtót (SSD) technológiát, amely megköveteli a premium storage-fiók fogja használni. Győződjön meg arról, hogy telepíti központilag a prémium szintű tárolást támogató Azure-helyen.
* **Hálózati adapterek**. Minden virtuális gép két hálózati adapterrel, egy adatbázis-hozzáférés, az lesz, a másik felügyeleti.
* **Rendelkezésre állási csoport**. Egyetlen rendelkezésre állási beállítása, biztosítása érdekében a virtuális gépek legalább egyikének működik, és karbantartási futtató összes adatbázis-kiszolgáló megjelenik.

### <a name="step-1---start-your-script"></a>1. lépés – a parancsfájl futtatásához
Letöltheti a teljes bash-szkript használt [Itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). A következő lépéseket követve módosítsa a parancsfájlt a saját környezetéhez:

1. A meglévő erőforráscsoportot, a fent telepített alapján az alábbi változók értékeinek módosítása [Előfeltételek](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. A háttérrendszer központi telepítéshez használni kívánt értékek alapján az alábbi változók értékeinek módosítása

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2. lépés – a virtuális gépek számára szükséges erőforrások létrehozása
1. Hozzon létre egy új felhőszolgáltatást a háttérbeli virtuális gépeket. Figyelje meg a a `$backendCSName` változó az erőforráscsoport nevéhez, és `$location` tartozó Azure-régió.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Hozzon létre egy prémium szintű tárfiók az Öné virtuális gépek által használt operációsrendszer- és adatlemezek.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>3. lépés – több hálózati adapterrel rendelkező virtuális gépek létrehozása
1. Indítsa el egy hurkot, és hozzon létre több virtuális gép alapján az `numberOfVMs` változókat.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Az egyes virtuális Gépekhez adja meg a nevét és az egyes két hálózati adapter IP-cím.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. A virtuális gép létrehozásához. Figyelje meg, hogy a használatát a `--nic-config` paraméter, amely az összes hálózati adapter neve, alhálózatot és IP-cím.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Az egyes virtuális Gépekhez hozzon létre két adatlemezeket.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>4. lépés: a parancsfájl futtatása
Most, hogy letöltötte és igényei alapján a parancsfájl megváltozott, futtassa a szkriptet létrehozni a háttér adatbázis virtuális gépek több hálózati adapterrel rendelkező.

1. Mentse a parancsfájlt, és futtathatja a **Bash** terminál. A kezdeti kimenetének, látni fogja, ahogy az alábbi.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Néhány perc múlva a végrehajtás befejeződik, és látni fogja a kimenet a többi alább látható módon.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5. lépés – a virtuális gép operációs rendszerén belül útválasztás konfigurálása

Az Azure DHCP egy alapértelmezett átjáró a virtuális géphez csatolt első (elsődleges) hálózati adapterhez rendeli. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. Másodlagos hálózati adapterrel, azonban kommunikálhat kívül található erőforrásokkal. Másodlagos hálózati adapterek útválasztásának konfigurálása, lásd: [útválasztás több hálózati adapterrel rendelkező virtuális gép operációs rendszerből](virtual-network-network-interface-vm.md).
