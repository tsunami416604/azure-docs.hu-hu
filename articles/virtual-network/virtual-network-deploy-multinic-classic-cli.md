---
title: Virtuális gép (klasszikus) létrehozása több hálózati adapterrel – klasszikus Azure CLI | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre virtuális gépet (klasszikus) több hálózati adapterrel a klasszikus Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058746"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Virtuális gép (klasszikus) létrehozása több hálózati adapterrel a klasszikus Azure CLI használatával

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Létrehozhat virtuális gépeket (VM-ket) az Azure-ban, és több hálózati adaptert (NIC) is csatolhat az egyes virtuális gépekhez. Több hálózati adapter teszi lehetővé a forgalmi típusok elkülönítését a hálózati adapterek között. Előfordulhat például, hogy egy hálózati adapter kommunikál az internettel, míg egy másik csak az internethez csatlakozó belső erőforrásokkal kommunikál. Számos hálózati virtuális berendezés, például az alkalmazások kézbesítése és a WAN-optimalizálási megoldások esetében több hálózati adapter közötti hálózati forgalom elkülönítésére van szükség.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához:  [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, hogyan hajthatja végre ezeket a lépéseket a [Resource Manager](../virtual-machines/linux/multiple-nics.md)-alapú üzemi modell használatával.

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

A következő lépések a *IaaSStory* nevű erőforráscsoportot használják a webkiszolgálók és a *IaaSStory-backend* nevű erőforráscsoport számára az adatbázis-kiszolgálók esetében.

## <a name="prerequisites"></a>Előfeltételek
Az adatbázis-kiszolgálók létrehozása előtt létre kell hoznia a *IaaSStory* erőforráscsoportot az ehhez a forgatókönyvhöz szükséges összes erőforrással. Az erőforrások létrehozásához hajtsa végre az alábbi lépéseket. Hozzon létre egy virtuális hálózatot a [virtuális hálózat létrehozása](virtual-networks-create-vnet-classic-cli.md) című cikk lépéseit követve.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>A háttérben futó virtuális gépek üzembe helyezése
A háttérben futó virtuális gépek a következő erőforrások létrehozásával függenek:

* **Adatlemezek Storage-fiókja**. A jobb teljesítmény érdekében az adatbázis-kiszolgálók adatlemezei SSD-technológiát használnak, amelyhez prémium szintű Storage-fiók szükséges. Győződjön meg arról, hogy az Azure-beli hely a Premium Storage támogatásához van üzembe helyezve.
* **Hálózati adapterek**. Mindegyik virtuális gép két hálózati adapterrel rendelkezik, egyet az adatbázis-hozzáféréshez, egyet pedig a felügyelethez.
* **Rendelkezésre állási csoport**. Minden adatbázis-kiszolgáló egyetlen rendelkezésre állási csoportba kerül, így biztosítva, hogy a virtuális gépek legalább egyike működőképes legyen a karbantartás során.

### <a name="step-1---start-your-script"></a>1\. lépés – a parancsfájl elindítása
Az [itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)használt teljes bash-szkriptet letöltheti. A következő lépések végrehajtásával módosíthatja a parancsfájlt, hogy működjön a környezetben:

1. Módosítsa az alábbi változók értékeit az [Előfeltételek](#prerequisites)között a fent üzembe helyezett meglévő erőforráscsoport alapján.

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Módosítsa az alábbi változók értékeit a háttérbeli telepítéshez használni kívánt értékek alapján.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2\. lépés – a szükséges erőforrások létrehozása a virtuális gépekhez
1. Hozzon létre egy új felhőalapú szolgáltatást az összes háttérbeli virtuális gép számára. Figyelje meg a `$backendCSName` változó használatát az erőforráscsoport neveként és az Azure- `$location` régióban.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Hozzon létre egy prémium szintű Storage-fiókot az operációs rendszer és az adatlemezek számára, amelyeket a tiéd virtuális gépek használnak.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>3\. lépés – virtuális gépek létrehozása több hálózati adapterrel
1. Indítson el egy hurkot több virtuális gép létrehozásához `numberOfVMs` a változók alapján.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Minden egyes virtuális gép esetében adja meg a két hálózati adapter nevét és IP-címét.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Hozza létre a virtuális gépet. Figyelje meg a `--nic-config` paraméter használatát, amely tartalmazza az összes nevet, alhálózatot és IP-címet tartalmazó hálózati adapterek listáját.

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

4. Minden virtuális gép esetében hozzon létre két adatlemezt.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>4\. lépés – a parancsfájl futtatása
Most, hogy az igényeinek megfelelően letöltötte és módosította a parancsfájlt, futtassa a szkriptet a háttér-adatbázis virtuális gépei létrehozásához több hálózati adapterrel.

1. Mentse a parancsfájlt, és futtassa a **bash** -terminálról. A kezdeti kimenet a lent látható módon jelenik meg.

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

2. Néhány perc elteltével a végrehajtás véget ért, és megjelenik a kimenet többi része az alább látható módon.

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

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5\. lépés – az Útválasztás konfigurálása a virtuális gép operációs rendszerén belül

Az Azure DHCP egy alapértelmezett átjárót rendel hozzá a virtuális géphez csatolt első (elsődleges) hálózati adapterhez. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. A másodlagos hálózati adapterek kommunikálhatnak az alhálózatukon kívül található erőforrásokkal. A másodlagos hálózati adapterek útválasztásának konfigurálásához tekintse meg [a virtuális gépek operációs rendszerének útválasztása több hálózati adapterrel](virtual-network-network-interface-vm.md)című témakört.
