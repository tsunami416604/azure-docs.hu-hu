---
title: Klasszikus SQL Server VM létrehozása (PowerShell)
description: Útmutatást és PowerShell-szkripteket biztosít az Azure-beli virtuális gépek SQL Server virtuálisgép-katalógus rendszerképeivel való létrehozásához. Ez a témakör a klasszikus üzembe helyezési módot használja.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 8757b634b76867a2d5ccce3dcfdc9d66ef25c25e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032739"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>SQL Server virtuális gép kiépítése Azure PowerShell használatával (klasszikus)

Ez a cikk a SQL Server virtuális gépek Azure-beli létrehozásával kapcsolatos lépéseit ismerteti a PowerShell-parancsmagok használatával.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A jelen témakör Resource Manager-verziójának használatával kapcsolatban lásd: [SQL Server virtuális gép kiépítése Azure PowerShell Resource Managerrel](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>A PowerShell telepítése és konfigurálása:
1. Ha nem rendelkezik Azure-fiókkal, az [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/) biztosít.
2. [Töltse le és telepítse a legújabb Azure PowerShell-parancsokat](/powershell/azure/overview).
3. Indítsa el a Windows PowerShellt, és kapcsolja hozzá az Azure-előfizetéséhez az **Add-AzureAccount** paranccsal.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>A cél Azure-régió meghatározása

A SQL Server virtuális gép egy adott Azure-régióban található felhőalapú szolgáltatásban lesz üzemeltetve. A következő lépések segítségével meghatározhatja a régiót, a Storage-fiókot és a felhőalapú szolgáltatást, amelyet az oktatóanyag további részében fog használni.

1. Határozza meg azt az adatközpontot, amelyet a SQL Server VM üzemeltetéséhez használni kíván. A következő PowerShell-parancs megjeleníti az elérhető régiók neveinek listáját.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Miután azonosította az előnyben részesített helyet, állítson be egy **$dcLocation** nevű változót az adott régióra. Például a következő parancs a régiót "Kelet-USA"-ra állítja be:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Az előfizetés és a Storage-fiók beállítása

1. Határozza meg az új virtuális géphez használni kívánt Azure-előfizetést.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Rendelje hozzá a célként megadott Azure-előfizetést a **$subscr** változóhoz. Ezt követően állítsa be az aktuális Azure-előfizetését.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Ezután keresse meg a meglévő Storage-fiókokat. A következő parancsfájl a választott régióban található összes Storage-fiókot megjeleníti:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Ha új Storage-fiókra van szüksége, először hozzon létre egy teljesen alacsony szintű Storage-fióknevet a New-AzureStorageAccount paranccsal a következő példában látható módon: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Rendelje hozzá a cél Storage-fiók nevét a **$staccounthoz**. Ezután a **set-azuresubscription parancsot** használatával állítsa be az előfizetést és az aktuális Storage-fiókot.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>SQL Server virtuális gép rendszerképének kiválasztása

1. Ismerje meg az elérhető SQL Server Virtual Machines-lemezképek listáját a katalógusból. Ezek a lemezképek mind rendelkeznek egy **ImageFamily** -tulajdonsággal, amely az "SQL" kifejezéssel kezdődik. Az alábbi lekérdezés az elérhető rendszerképeket jeleníti meg, amelyek SQL Server előre telepítve vannak.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Ha megtalálta a virtuális gép lemezképének családját, több közzétett rendszerkép is szerepelhet ebben a családban. A következő parancsfájl használatával megkeresheti a kiválasztott rendszerkép-család legújabb közzétett virtuálisgép-lemezképének nevét (például **SQL Server 2016 RTM Enterprise Windows Server 2012 R2**rendszeren):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

Végül hozza létre a virtuális gépet a PowerShell használatával:

1. Hozzon létre egy felhőalapú szolgáltatást az új virtuális gép üzemeltetéséhez. Vegye figyelembe, hogy ehelyett meglévő felhőalapú szolgáltatást is használhat. Hozzon létre egy új változót **$svcname** a Cloud Service rövid nevével.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Adja meg a virtuális gép nevét és méretét. A virtuálisgép-méretekkel kapcsolatos további információkért lásd: [virtuális gépek méretei az Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-hoz.

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. A helyi rendszergazdai fiók és a jelszó megadása.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Futtassa a következő szkriptet a virtuális gép létrehozásához.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> További magyarázatért és konfigurációs lehetőségekhez tekintse meg a [Windows-alapú Virtual Machines létrehozásához és előkonfigurálásához használja a Azure PowerShell használata](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)a következőben: a **parancsbővítések összeállítása** című szakaszt.

## <a name="example-powershell-script"></a>Példa PowerShell-parancsfájlra

A következő parancsfájl egy olyan teljes parancsfájlt mutat be, amely egy **SQL Server 2016 RTM Enterprise rendszert hoz létre a Windows Server 2012 R2 rendszerű** virtuális gépen. Ha ezt a parancsfájlt használja, a jelen témakör előző lépései alapján testre kell szabnia a kezdeti változókat.

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

## <a name="connect-with-remote-desktop"></a>Kapcsolat a Távoli asztallal

1. Hozza létre az aktuális felhasználó dokumentum mappájába az RDP-fájlokat, hogy a telepítés befejezéséhez a következő virtuális gépeket indítsa el:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. A dokumentumok könyvtárban indítsa el az RDP-fájlt. Kapcsolódjon a korábban megadott rendszergazdai felhasználónévvel és jelszóval (például ha a Felhasználónév VMAdmin volt, a felhasználóként adja meg a "\VMAdmin" nevet, és adja meg a jelszót).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>A SQL Server gép konfigurálásának befejezése a táveléréshez

Miután bejelentkezett a gépre a távoli asztal használatával, konfigurálja a SQL Server a [SQL Server-kapcsolat Azure-beli virtuális gépen való konfigurálásának lépései](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)című részben leírtak szerint.

## <a name="next-steps"></a>Következő lépések

A Virtual Machines és a [Virtual Machines dokumentációjában](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)további útmutatást talál a virtuális gépek üzembe helyezéséhez.

Sok esetben a következő lépés az adatbázisok migrálása erre az új SQL Server VMra. Az adatbázis-áttelepítési útmutató: [adatbázis áttelepítése Azure-beli virtuális gépen SQL Server](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Ha az SQL-Virtual Machines létrehozásához is érdekli a Azure Portal használata, tekintse [meg a SQL Server virtuális gép Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)-beli üzembe helyezésével foglalkozó témakört. Vegye figyelembe, hogy az oktatóanyag, amely végigvezeti a portálon, létrehozza a virtuális gépeket az ajánlott Resource Manager-modell használatával, nem pedig a jelen PowerShell-témakörben használt klasszikus modellt.

Ezen erőforrások mellett azt javasoljuk, hogy tekintse át az [Azure Virtual Machines futó SQL Serverával kapcsolatos egyéb témaköröket](../sql/virtual-machines-windows-sql-server-iaas-overview.md)is.
