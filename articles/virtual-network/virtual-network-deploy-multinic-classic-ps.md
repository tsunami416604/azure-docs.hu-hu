---
title: Virtuális gép (klasszikus) létrehozása több hálózati adapterrel – Azure PowerShell | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre virtuális gépet (klasszikus) több hálózati adapterrel a PowerShell használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: af22bc43a06be74c7a4b6c869725a19fc87a0f3e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058733"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Virtuális gép (klasszikus) létrehozása több hálózati adapterrel a PowerShell használatával

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Létrehozhat virtuális gépeket (VM-ket) az Azure-ban, és több hálózati adaptert (NIC) is csatolhat az egyes virtuális gépekhez. Több hálózati adapter teszi lehetővé a forgalmi típusok elkülönítését a hálózati adapterek között. Előfordulhat például, hogy egy hálózati adapter kommunikál az internettel, míg egy másik csak az internethez csatlakozó belső erőforrásokkal kommunikál. Számos hálózati virtuális berendezés, például az alkalmazások kézbesítése és a WAN-optimalizálási megoldások esetében több hálózati adapter közötti hálózati forgalom elkülönítésére van szükség.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához:  [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, hogyan hajthatja végre ezeket a lépéseket a [Resource Manager](../virtual-machines/windows/multiple-nics.md)-alapú üzemi modell használatával.

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

A következő lépések a *IaaSStory* nevű erőforráscsoportot használják a webkiszolgálók és a *IaaSStory-backend* nevű erőforráscsoport számára az adatbázis-kiszolgálók esetében.

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis-kiszolgálók létrehozása előtt létre kell hoznia a *IaaSStory* erőforráscsoportot az ehhez a forgatókönyvhöz szükséges összes erőforrással. Az erőforrások létrehozásához hajtsa végre az alábbi lépéseket. Hozzon létre egy virtuális hálózatot a [virtuális hálózat létrehozása](virtual-networks-create-vnet-classic-netcfg-ps.md) című cikk lépéseit követve.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>A háttérbeli virtuális gépek létrehozása
A háttérben futó virtuális gépek a következő erőforrások létrehozásával függenek:

* **Háttérbeli alhálózat** Az adatbázis-kiszolgálók egy különálló alhálózat részévé fognak válni a forgalom elkülönítése érdekében. Az alábbi parancsfájl azt várja, hogy ez az alhálózat létezik egy *WTestVnet*nevű vnet.
* **Adatlemezek Storage-fiókja**. A jobb teljesítmény érdekében az adatbázis-kiszolgálók adatlemezei SSD-technológiát használnak, amelyhez prémium szintű Storage-fiók szükséges. Győződjön meg arról, hogy az Azure-beli hely a Premium Storage támogatásához van üzembe helyezve.
* **Rendelkezésre állási csoport**. Minden adatbázis-kiszolgáló egyetlen rendelkezésre állási csoportba kerül, így biztosítva, hogy a virtuális gépek legalább egyike működőképes legyen a karbantartás során.

### <a name="step-1---start-your-script"></a>1\. lépés – a parancsfájl elindítása
Az [itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)használt teljes PowerShell-szkriptet letöltheti. Kövesse az alábbi lépéseket a parancsfájlnak a környezetben való működésének módosításához.

1. Módosítsa az alábbi változók értékeit az [Előfeltételek](#prerequisites)között a fent üzembe helyezett meglévő erőforráscsoport alapján.

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Módosítsa az alábbi változók értékeit a háttérbeli telepítéshez használni kívánt értékek alapján.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2\. lépés – a szükséges erőforrások létrehozása a virtuális gépekhez
Létre kell hoznia egy új felhőalapú szolgáltatást és egy Storage-fiókot minden virtuális gép adatlemezei számára. Meg kell adnia egy rendszerképet és egy helyi rendszergazdai fiókot is a virtuális gépekhez. Az erőforrások létrehozásához hajtsa végre a következő lépéseket:

1. Hozzon létre egy új felhőalapú szolgáltatást.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Hozzon létre egy új Premium Storage-fiókot.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Állítsa be a fent létrehozott Storage-fiókot az előfizetéséhez tartozó aktuális Storage-fiókkal.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Válasszon ki egy rendszerképet a virtuális géphez.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Adja meg a helyi rendszergazdai fiók hitelesítő adatait.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>3\. lépés – virtuális gépek létrehozása
A kívánt számú virtuális gép létrehozásához hurkot kell használnia, és létre kell hoznia a szükséges hálózati adaptereket és virtuális gépeket a hurokon belül. A hálózati adapterek és virtuális gépek létrehozásához hajtsa végre a következő lépéseket.

1. Indítson `for` el egy hurkot, és ismételje meg a parancsokat úgy, hogy a `$numberOfVMs` változó értéke alapján a szükséges számú virtuális gépet és két hálózati adaptert hozzon létre.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Hozzon `VMConfig` létre egy objektumot a virtuális gép rendszerképének, méretének és rendelkezésre állási készletének megadásával.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. A virtuális gép kiépítése Windows rendszerű virtuális gépként.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Állítsa be az alapértelmezett hálózati adaptert, és rendeljen hozzá egy statikus IP-címet.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Adjon hozzá egy második hálózati adaptert az egyes virtuális gépekhez.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Hozzon létre adatlemezeket az egyes virtuális gépekhez.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Hozza létre az egyes virtuális gépeket, és fejezze be a hurkot.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>4\. lépés – a parancsfájl futtatása
Most, hogy az igényeinek megfelelően letöltötte és módosította a parancsfájlt, csenevész a szkriptet a háttér-adatbázis virtuális gépei létrehozásához több hálózati adapterrel.

1. Mentse a parancsfájlt, és futtassa a **PowerShell** -parancssorból vagy a **PowerShell ISE**-ből. A kezdeti kimenet a lent látható módon jelenik meg.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Adja meg a hitelesítő adatok megadásához szükséges információkat, majd kattintson **az OK**gombra. A rendszer a következő kimenetet adja vissza.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5\. lépés – az Útválasztás konfigurálása a virtuális gép operációs rendszerén belül

Az Azure DHCP egy alapértelmezett átjárót rendel hozzá a virtuális géphez csatolt első (elsődleges) hálózati adapterhez. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. A másodlagos hálózati adapterek kommunikálhatnak az alhálózatukon kívül található erőforrásokkal. A másodlagos hálózati adapterek útválasztásának konfigurálásához tekintse meg a következő cikkeket:

- [Windows rendszerű virtuális gép konfigurálása több hálózati adapterhez](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Linux rendszerű virtuális gép konfigurálása több hálózati adapterhez](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
