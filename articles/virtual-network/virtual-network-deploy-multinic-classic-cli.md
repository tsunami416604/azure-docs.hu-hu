---
title: "Hozzon létre egy virtuális gép (klasszikus) több hálózati adapter - Azure CLI 1.0 |} Microsoft Docs"
description: "Útmutató az Azure parancssori felület (CLI) 1.0 használatával több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5569209d3628003b3f3e169b227e069b920c03f
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Az Azure CLI 1.0 használatával több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Virtuális gépek (VM) létrehozása az Azure-ban, és csatlakoztassa a virtuális gépek mindegyikének több hálózati adapterek (NIC). Több hálózati adapter választhatók szét a forgalomtípusok engedélyezze a hálózati adapter között. Például egy hálózati adapter előfordulhat, hogy az internetes kommunikációt folytató, miközben egy másik kommunikál, csak a belső erőforrások nem csatlakozik az internethez. Azon különálló hálózati forgalom több hálózati adapter között számos hálózati virtuális készülékeket, például az alkalmazások biztosításán és WAN-optimalizálást megoldások szükség.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Útmutató: a következő lépések segítségével a [Resource Manager üzembe helyezési modellben](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Az alábbi lépéseket használja nevű erőforráscsoport *IaaSStory* a webkiszolgálók és az erőforráscsoport neve *IaaSStory-háttérrendszer* adatbázis-kiszolgálók.

## <a name="prerequisites"></a>Előfeltételek
Az adatbázis-kiszolgálók létrehozása előtt kell létrehoznia a *IaaSStory* erőforráscsoport ehhez a forgatókönyvhöz szükséges minden erőforráshoz. Ezek az erőforrások létrehozásához hajtsa végre az alábbi. Virtuális hálózat létrehozása a lépések a [hozzon létre egy virtuális hálózatot](virtual-networks-create-vnet-classic-cli.md) cikk.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>A háttér-virtuális gépek telepítése
A háttér-virtuális gépek létrehozását a következő erőforrások függ:

* **Az adatlemezek tárfiók**. A jobb teljesítmény érdekében az adatlemezek az adatbázis-kiszolgálók a tartós állapotú meghajtót (SSD) technológiát, amely a prémium szintű tárfiók szükséges fogja használni. Győződjön meg arról, hogy az Azure-hely támogatja a prémium szintű storage telepít.
* **Hálózati adapter**. Minden virtuális gép lesz a két hálózati adapterrel, egy adatbázis-hozzáférési és felügyeleti egyet.
* **A rendelkezésre állási csoport**. Minden adatbázis-kiszolgálók egyetlen rendelkezésre állási értékre, akkor ellenőrizze, hogy a virtuális gépek közül legalább egy, és a karbantartás során fut hozzáadandó.

### <a name="step-1---start-your-script"></a>1. lépés – a parancsfájl futtatásához
Letöltheti használt teljes bash parancsfájl [Itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Az alábbi lépésekkel módosíthatja a parancsfájlnak a környezetben:

1. A meglévő erőforráscsoport üzembe helyezett fent alapján az alábbi változók értékeinek módosítása [Előfeltételek](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. A háttérrendszer telepítéshez használni kívánt értékek alapján az alábbi változók értékeinek módosítása.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2. lépés - a szükséges erőforrásokat létrehozni a virtuális géphez
1. Hozzon létre egy új felhőszolgáltatás háttér virtuális gépen. Figyelje meg a a `$backendCSName` az erőforráscsoport neve, a változó és `$location` az Azure-régiót.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Az operációs rendszer és az adatlemezek saját virtuális gépek által használható egy prémium szintű storage-fiók létrehozása.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>3. lépés - a több hálózati adapterrel rendelkező virtuális gépek létrehozása
1. Indítsa el a hurok alapján több virtuális gép létrehozásához a `numberOfVMs` változók.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Az egyes virtuális gépek adja meg a nevét és IP-cím az egyes a két hálózati adaptert.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. A virtuális gép létrehozása. Figyelje meg a használatát a `--nic-config` paramétert, az összes hálózati adapter neve, alhálózatot és IP-cím listáját tartalmazó.

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

4. Az egyes virtuális gépek hozzon létre két adatlemezek.

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
Most, hogy a letöltött és a parancsfájl a igények alapján megváltozott, futtassa a hozzon létre a háttérbeli adatbázis virtuális gépek több hálózati adapter.

1. Mentse a parancsfájlt, és futtassa azt a **Bash** terminál. A kezdeti kimenetet fog látni, alább látható módon.

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

2. Néhány perc múlva a végrehajtása leáll, és látni fogja a kimeneti részeinek alább látható módon.

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

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5. lépés - a virtuális gép operációs rendszerében útválasztás konfigurálása

Az Azure DHCP rendeli hozzá az első (elsődleges) hálózati illesztő a virtuális géphez csatolt alapértelmezett átjárót. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. Másodlagos hálózati adapterrel, azonban kommunikálhat a alhálózati kívüli erőforrásokhoz. A másodlagos hálózati adapterrel-útválasztás konfigurálásához lásd: [egy virtuális gép operációs rendszerben több hálózati adapter az útválasztási](virtual-network-network-interface-vm.md).
