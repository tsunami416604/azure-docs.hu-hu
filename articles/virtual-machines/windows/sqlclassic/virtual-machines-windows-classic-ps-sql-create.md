---
title: SQL Server virtuális gép létrehozása az Azure PowerShell (klasszikus) |} A Microsoft Docs
description: Egy Azure virtuális gép létrehozása az SQL Server virtuálisgép-katalógus rendszerképek lépéseket és a PowerShell-parancsfájlok biztosít. Ez a témakör a klasszikus üzembe helyezési módot használja.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad8b59a9290c533a3687b5ff8956d8682fb6d9e9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332285"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használatával az SQL Server virtuális gép kiépítése

Ez a cikk lépéseit az SQL Server virtuális gép létrehozása az Azure-ban a PowerShell-parancsmagok használatával.

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a témakör erőforrás-kezelő verziója: [az Azure PowerShell Resource Manager az SQL Server virtuális gép kiépítése](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>PowerShell telepítése és konfigurálása:
1. Ha nem rendelkezik Azure-fiókkal, az [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/) biztosít.
2. [Töltse le és telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview).
3. Indítsa el a Windows Powershellt, és csatlakoztassa az Azure-előfizetésbe a **Add-AzureAccount** parancsot.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Határozza meg a cél Azure-régió

Az SQL Servert futtató virtuális gép-ban üzemeltetett alkalmazásban egy felhőalapú szolgáltatás, amely egy adott Azure-régióban található. A következő lépések segítenek meghatározni a régió, a storage-fiók, és a felhőalapú szolgáltatás, amely jelzi a az oktatóanyag további részeinek.

1. Határozza meg, hogy az SQL Server virtuális gép üzemeltetésére használni kívánt adatközpontban. A következő PowerShell-parancsot az elérhető régiónevek listáját jeleníti meg.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Az elsődleges hely azonosítása után állítsa be a nevű változó **$dcLocation** régióhoz. Az alábbi parancs például az "USA keleti régiója" régió állít be:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Az előfizetés és a storage-fiók beállítása

1. Határozza meg az Azure-előfizetés az új virtuális gép használhat.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. A cél Azure-előfizetés hozzárendelése, a **$subscr** változó. Az aktuális Azure-előfizetés, majd állítsa ezzel.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Ezután ellenőrizze a meglévő tárfiókok. Az alábbi parancsfájlt, amely a kiválasztott régióban található összes storage-fiókok jeleníti meg:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Szüksége van egy új tárfiókot, egy kisbetűs minden tárfiók neve először hozzon létre a New-AzureStorageAccount paranccsal az alábbi példában látható módon: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Rendelje hozzá a céloldali tárfiók neve, a **$staccount**. Ezután **Set-AzureSubscription** az előfizetés és az aktuális storage-fiók beállításához.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Válassza ki az SQL Server virtuálisgép-lemezkép

1. Ismerje meg az elérhető SQL Server virtual machines-lemezképek a katalógusból listája. Ezek a lemezképek minden rendelkezik egy **ImageFamily** tulajdonság "SQL" karakterlánccal kezdődik. A következő lekérdezést, hogy az SQL Server előre telepítve van a kép termékcsaládban elérhető megjeleníti.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Ha megtalálta a virtuális gép lemezkép operációsrendszer-család, lehet több közzétett rendszerképek a családban. A következő parancsfájl használatával keresse meg a kiválasztott kép termékcsalád legújabb közzétett virtuális gép rendszerkép nevét (például **SQL Server 2016 RTM Enterprise Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

Végül hozza létre a virtuális gépet a PowerShell-lel:

1. Hozzon létre egy felhőalapú szolgáltatás, az új virtuális gép üzemeltetésére. Vegye figyelembe, hogy lehetőség arra is használhatja egy meglévő felhőszolgáltatáshoz is. Hozzon létre egy új változót **$svcname** a felhőszolgáltatás rövid nevét.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Adja meg a virtuális gép nevét és méretét. Virtuálisgép-méretekkel kapcsolatos további információkért lásd: [az Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Adja meg a helyi rendszergazdai fiókkal és jelszóval.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Futtassa a következő szkriptet a virtuális gép létrehozásához.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> További magyarázat és konfigurációs lehetőségek: a **hozhat létre a parancskészlethez** szakasz [létrehozása és előkonfigurálása a Windows-alapú virtuális gépek az Azure PowerShell használatával](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Példa a PowerShell parancsprogramra

Az alábbi parancsprogram azt szemlélteti, egy teljes szkript, amely létrehoz egy **SQL Server 2016 RTM Enterprise Windows Server 2012 R2** virtuális gépet. Ha ezt a parancsfájlt használja, akkor testre kell szabnia a kezdeti változók alapján az előző témakörben leírt lépéseket.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Csatlakozzon a távoli asztallal

1. Ezek a virtuális gépek telepítés befejezéséhez indítsa el az aktuális felhasználó a dokumentum mappában hozza létre az RDP-fájlok:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. A dokumentumok könyvtárában nyissa meg az RDP-fájlt. Csatlakozás a rendszergazdai felhasználónevet és a korábban megadott jelszóval (például, ha a felhasználónév VMAdmin volt, adja meg a "\VMAdmin" a felhasználó, és adja meg a jelszót).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Az SQL Server-gép távelérés konfigurálása

A távoli asztali gépről való bejelentkezés után a következő témakör utasításait alapján az SQL Server konfigurálása [lépéseit az SQL Server-kapcsolat konfigurálása Azure-beli virtuális gépen](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>További lépések

Talál további útmutatást az üzembe helyezés a PowerShell használatával a virtuális gépek a [virtual machines dokumentációja](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Sok esetben a következő lépés, hogy az adatbázisokat az új SQL Server rendszerű virtuális gép. Az adatbázis-áttelepítési útmutatóját lásd: [-adatbázis áttelepítése az SQL Server-beli virtuális gépen](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Ha is érdekli az SQL virtuális gépek létrehozása, olvassa el az Azure portal használatával [kiépítése egy SQL Server virtuális gépet az Azure-ban](../sql/virtual-machines-windows-portal-sql-server-provision.md). Vegye figyelembe, hogy az oktatóanyag bemutatja a portálon hoz létre virtuális gépeket a klasszikus modellt, a PowerShell-témakörben használt helyett a javasolt Resource Manager-modell használatával.

Ezekkel az erőforrásokkal mellett azt javasoljuk, hogy tekintse át [Azure Virtual Machines szolgáltatásban futó SQL Server kapcsolatos témaköröket](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
