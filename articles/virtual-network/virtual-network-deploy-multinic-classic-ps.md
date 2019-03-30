---
title: Több hálózati adapterrel – Azure PowerShell (klasszikus) virtuális gép létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális gép (klasszikus) PowerShell-lel több hálózati adapterrel rendelkező.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 087b52bd603e8aed6078ab340e84c1f6bd0e8082
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652195"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>PowerShell-lel több hálózati adapterrel rendelkező virtuális gép (klasszikus) létrehozása

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Hozza létre az Azure virtuális gépeken (VM), és csatolni a virtuális gépek mindegyike több hálózati adapterrel (NIC). Több hálózati adapter engedélyezze a hálózati adaptereken keresztüli forgalom típusainak elkülönítését. Ha például egy hálózati adapter előfordulhat, hogy kommunikáljon az internettel, amíg egy másik kommunikál, csak a belső erőforrásokhoz nem csatlakozik az internethez. Lehetővé teszi több hálózati adapter közötti hálózati forgalom külön számos hálózati virtuális berendezések, például az alkalmazásszolgáltatást és WAN-optimalizálás megoldások szükség.

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik:  [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, hogyan használja a következő lépésekkel a [Resource Manager üzemi modell](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Az alábbi lépéseket nevű erőforráscsoportot használ *IaaSStory* a webkiszolgálók és a egy erőforráscsoport nevű *IaaSStory-háttérrendszer* a DB kiszolgálók.

## <a name="prerequisites"></a>Előfeltételek

A DB kiszolgálók hozhat létre, meg kell hoznia a *IaaSStory* erőforráscsoportot az összes szükséges erőforrást ehhez a forgatókönyvhöz. Ezek az erőforrások létrehozásához hajtsa végre a szükséges. Virtuális hálózat létrehozása a lépéseket követve a [hozzon létre egy virtuális hálózatot](virtual-networks-create-vnet-classic-netcfg-ps.md) cikk.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>A háttérbeli virtuális gép létrehozása
A háttérbeli virtuális gépek létrehozása a következő erőforrások függenek:

* **Háttérbeli alhálózatot**. Az adatbázis-kiszolgálók külön alhálózathoz áthaladó forgalmat leválasszanak tagja lesz. Az alábbi parancsfájlt vár nevű virtuális hálózat szerepel, ez az alhálózat *WTestVnet*.
* **Storage-fiók adatlemezek**. A jobb teljesítmény érdekében az adatbázis-kiszolgálók a adatlemezét szemben – tartós állapotú meghajtót (SSD) technológiát, amely megköveteli a premium storage-fiók fogja használni. Győződjön meg arról, hogy telepíti központilag a prémium szintű tárolást támogató Azure-helyen.
* **Rendelkezésre állási csoport**. Egyetlen rendelkezésre állási beállítása, biztosítása érdekében a virtuális gépek legalább egyikének működik, és karbantartási futtató összes adatbázis-kiszolgáló megjelenik.

### <a name="step-1---start-your-script"></a>1. lépés – a parancsfájl futtatásához
Letöltheti a teljes PowerShell-parancsfájlt használja [Itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Módosítsa a parancsfájl futtatásához az alábbi lépésekkel.

1. A meglévő erőforráscsoportot, a fent telepített alapján az alábbi változók értékeinek módosítása [Előfeltételek](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. A háttérrendszer központi telepítéshez használni kívánt értékek alapján az alábbi változók értékeinek módosítása

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2. lépés – a virtuális gépek számára szükséges erőforrások létrehozása
Új felhőalapú szolgáltatás és a egy storage-fiókját az adatlemezeket az összes virtuális gép létrehozásához szükséges. Is kell egy képet, és a egy helyi rendszergazdai fiók megadása a virtuális gépek számára. Ezek az erőforrások létrehozásához hajtsa végre az alábbi lépéseket:

1. Új felhőszolgáltatás hozható létre.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Hozzon létre egy új prémium szintű storage-fiókot.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Állítsa be az előfizetéshez tartozó aktuális tárfiókkal a fent létrehozott tárfiókot.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Jelöljön ki egy képet a virtuális gép számára.

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
Szeretné használni egy hurkot hozhat létre, és a szükséges hálózati adapterek és virtuális gépek létrehozása a hurok belül tetszőleges számú virtuális gépeket. A hálózati adapterek és virtuális gépek létrehozásához hajtsa végre az alábbi lépéseket.

1. Indítsa el a `for` hozhatnak létre egy virtuális Gépet és két hálózati adapterrel, ahányszor csak szükség esetén ismételje meg a hurok értéke alapján a `$numberOfVMs` változó.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Hozzon létre egy `VMConfig` adja meg a lemezkép mérete és a virtuális gép rendelkezésre állási objektum.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. A virtuális gép, egy Windows virtuális gép.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Állítsa be az alapértelmezett hálózati Adaptert, és rendelje hozzá egy statikus IP-címet.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Adjon hozzá egy második hálózati Adaptert az egyes virtuális Gépekhez.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Hozzon létre az adatlemezeket az egyes virtuális Gépekhez.

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

7. Minden virtuális gép létrehozása, és a hurok vége.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>4. lépés: a parancsfájl futtatása
Most, hogy a letöltött és módosított a szkriptet, szükség szerint, a parancsfájl runt, több hálózati adapterrel rendelkező virtuális gépek háttér-adatbázis létrehozása.

1. Mentse a parancsfájlt, és futtathatja a **PowerShell** parancssort, vagy **PowerShell ISE-ben**. A kezdeti kimenetének, látni fogja, ahogy az alábbi.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Adja meg a hitelesítő adatokat kér, majd kattintson a szükséges adatokat **OK**. A következő eredményt adja vissza.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5. lépés – a virtuális gép operációs rendszerén belül útválasztás konfigurálása

Az Azure DHCP egy alapértelmezett átjáró a virtuális géphez csatolt első (elsődleges) hálózati adapterhez rendeli. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. A másodlagos hálózati adapterek kommunikálhatnak az alhálózatukon kívül található erőforrásokkal. Konfigurálja az útválasztást a másodlagos hálózati adapterrel, tekintse meg a következő cikkeket:

- [Windows virtuális gép több hálózati adapter konfigurálása](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Linux rendszerű virtuális gép több hálózati adapterek konfigurálása](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
