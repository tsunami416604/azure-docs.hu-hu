---
title: "Hozzon létre egy virtuális gép (klasszikus) több hálózati adapter - Azure PowerShell |} Microsoft Docs"
description: "Tudnivalók a PowerShell segítségével több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 824af3f0f5f6d42341f6f1dc94104f6009b568b8
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>PowerShell-lel több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Virtuális gépek (VM) létrehozása az Azure-ban, és csatlakoztassa a virtuális gépek mindegyikének több hálózati adapterek (NIC). Több hálózati adapter választhatók szét a forgalomtípusok engedélyezze a hálózati adapter között. Például egy hálózati adapter előfordulhat, hogy az internetes kommunikációt folytató, miközben egy másik kommunikál, csak a belső erőforrások nem csatlakozik az internethez. Azon különálló hálózati forgalom több hálózati adapter között számos hálózati virtuális készülékeket, például az alkalmazások biztosításán és WAN-optimalizálást megoldások szükség.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Útmutató: a következő lépések segítségével a [Resource Manager üzembe helyezési modellben](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Az alábbi lépéseket használja nevű erőforráscsoport *IaaSStory* a webkiszolgálók és az erőforráscsoport neve *IaaSStory-háttérrendszer* adatbázis-kiszolgálók.

## <a name="prerequisites"></a>Előfeltételek

Az adatbázis-kiszolgálók létrehozása előtt kell létrehoznia a *IaaSStory* erőforráscsoport ehhez a forgatókönyvhöz szükséges minden erőforráshoz. Ezek az erőforrások létrehozásához hajtsa végre az alábbi. Virtuális hálózat létrehozása a lépések a [hozzon létre egy virtuális hálózatot](virtual-networks-create-vnet-classic-netcfg-ps.md) cikk.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>A háttér-virtuális gépek létrehozása
A háttér-virtuális gépek létrehozását a következő erőforrások függ:

* **Backend alhálózathoz**. Az adatbázis-kiszolgálókhoz külön alhálózathoz, hogy áthaladó forgalmat leválasszanak része lesz. Az alábbi parancsfájl vár az alhálózat léteznie egy nevű vnetet a *WTestVnet*.
* **Az adatlemezek tárfiók**. A jobb teljesítmény érdekében az adatlemezek az adatbázis-kiszolgálók a tartós állapotú meghajtót (SSD) technológiát, amely a prémium szintű tárfiók szükséges fogja használni. Győződjön meg arról, hogy az Azure-hely támogatja a prémium szintű storage telepít.
* **A rendelkezésre állási csoport**. Minden adatbázis-kiszolgálók egyetlen rendelkezésre állási értékre, akkor ellenőrizze, hogy a virtuális gépek közül legalább egy, és a karbantartás során fut hozzáadandó.

### <a name="step-1---start-your-script"></a>1. lépés – a parancsfájl futtatásához
Letöltheti használt teljes PowerShell-parancsfájl [Itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Módosíthatja a parancsfájlnak a környezetben az alábbi lépésekkel.

1. A meglévő erőforráscsoport üzembe helyezett fent alapján az alábbi változók értékeinek módosítása [Előfeltételek](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. A háttérrendszer telepítéshez használni kívánt értékek alapján az alábbi változók értékeinek módosítása.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2. lépés - a szükséges erőforrásokat létrehozni a virtuális géphez
Az összes virtuális gépet egy új felhőalapú szolgáltatás és az adatlemezek tárfiók létrehozásához szükséges. Meg kell adnia a képet, és egy helyi rendszergazdai fiók a virtuális gépek esetén is. Ezek az erőforrások létrehozásához kövesse az alábbi lépéseket:

1. Új felhőalapú szolgáltatás létrehozása.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Hozzon létre egy új prémium szintű storage-fiók.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Állítsa be az előfizetéshez tartozó aktuális tárfiókkal a fenti létrehozott tárfiók.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Kép kiválasztása a virtuális gép számára.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Állítsa be a helyi rendszergazdai fiók hitelesítő adatait.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>3. lépés – a virtuális gépek létrehozása
Hurok segítségével létrehozott egy tetszőleges számú virtuális gépet, és a szükséges hálózati adapterek és virtuális gépek létrehozása a hurkon belül kell. A hálózati adapterek és a virtuális gépek létrehozásához hajtsa végre az alábbi lépéseket.

1. Indítsa el a `for` hozhatnak létre a virtuális gépek és két hálózati adaptert annyiszor szükséges, ismételje meg a hurok értéke alapján a `$numberOfVMs` változó.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Hozzon létre egy `VMConfig` objektumot adja meg a lemezkép mérete és rendelkezésre állási csoportot a virtuális gép számára.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. A virtuális gép telepítéséhez, egy Windows virtuális gép.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Az alapértelmezett hálózati adapter, és rendelje hozzá egy statikus IP-címet.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Adja hozzá a második hálózati adapter az egyes virtuális gépek.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Hozzon létre egy adatlemezt az egyes virtuális gépek.

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

7. Minden virtuális gép létrehozása, és a hurok végződnie.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>4. lépés: a parancsfájl futtatása
Most, hogy a letöltött és módosított a parancsfájl igényei szerint, a parancsfájl runt több hálózati adapterrel rendelkező a háttéradatbázis virtuális gépek létrehozásához.

1. Mentse a parancsfájlt, és futtassa azt a **PowerShell** parancssort, vagy **PowerShell ISE**. A kezdeti kimenetet fog látni, alább látható módon.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Töltse ki a hitelesítő adatokat kér, majd kattintson a szükséges információkat **OK**. A következő eredményt adja vissza.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5. lépés - a virtuális gép operációs rendszerében útválasztás konfigurálása

Az Azure DHCP rendeli hozzá az első (elsődleges) hálózati illesztő a virtuális géphez csatolt alapértelmezett átjárót. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. Másodlagos hálózati adapterrel, azonban kommunikálhat a alhálózati kívüli erőforrásokhoz. A másodlagos hálózati adapterrel-útválasztás konfigurálásához lásd: [egy virtuális gép operációs rendszerben több hálózati adapter az útválasztási](virtual-network-network-interface-vm.md).
