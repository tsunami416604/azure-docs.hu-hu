---
title: SQL Server virtuális gép létrehozása az Azure PowerShell (klasszikus) |} Microsoft Docs
description: Lépéseket és a PowerShell-parancsfájlok biztosít az Azure virtuális gép létrehozása az SQL Server virtuális gép a gyűjtemény lemezképei. Ez a témakör a klasszikus üzembe helyezési módot használ.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.openlocfilehash: 66f44e27562f33373e0b67fe6e0ebf9c6bf99e03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399661"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Egy SQL Server rendszerű virtuális gép (klasszikus) Azure PowerShell használatával

Ez a cikk lépéseit az SQL Server virtuális gép létrehozása az Azure-ban a PowerShell-parancsmagok használatával.

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a témakör erőforrás-kezelő verziója: [egy SQL Server rendszerű virtuális gép Azure PowerShell Resource Manager használatával](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>PowerShell telepítése és konfigurálása:
1. Ha nem rendelkezik Azure-fiókkal, az [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/) biztosít.
2. [Töltse le és telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview).
3. Indítsa el a Windows Powershellt, és csatlakoztassa az Azure-előfizetése a **Add-AzureAccount** parancsot.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Határozza meg a cél Azure-régió

Az SQL Server virtuális gépen egy felhőalapú szolgáltatás, amely egy adott Azure-régióban található fog üzemelni. Az alábbi lépések segítségével megállapíthatja a régió, a tárfiók, és a felhőalapú szolgáltatás, amely jelzi a többi részének az oktatóanyag.

1. Az Adatközpont, amely az SQL Server virtuális gép üzemeltetésére használni kívánt határozza meg. A következő PowerShell-parancsot a rendelkezésre álló terület neveinek listáját jeleníti meg.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Miután az elsődleges hely állapította meg, állítsa be a nevű változó **$dcLocation** a régióba. A következő parancs például "Számunkra keleti" régió állít be:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Az előfizetés és a tárolási fiók beállítása

1. Határozza meg az Azure-előfizetés az új virtuális gépet használunk.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Rendelje hozzá a cél Azure-előfizetéssel való a **$subscr** változó. Ezután állítsa be a jelenlegi Azure-előfizetése.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Ezután ellenőrizze a meglévő tárfiókok. A következő parancsfájl megjeleníti a kiválasztott régióban található összes storage-fiók:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Új tárfiók szükséges, ha először létre kell hoznia egy kisbetű minden tárfiók nevét az alábbi példában látható módon a New-AzureStorageAccount paranccsal: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. A cél tárfiók neve való hozzárendelése a **$staccount**. Ezután **Set-AzureSubscription** az előfizetés és az aktuális tárfiók.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Egy SQL Server virtuálisgép-lemezkép kiválasztása

1. Ismerje meg a rendelkezésre álló SQL Server virtuális gépek rendszerképek listája a gyűjteményből. Ezeket a lemezképeket mindegyik rendelkezik egy **ImageFamily** "SQL" kezdetű tulajdonság. A következő lekérdezés jeleníti meg a lemezkép termékcsalád érhető el, hogy az SQL Server előre telepítve van.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Ha megtalálja a virtuálisgép-lemezkép termékcsalád, lehet több közzétett lemezképet a családban. A kiválasztott kép családba legújabb közzétett virtuálisgép-lemezkép nevet található a következő parancsfájl segítségével (például **SQL Server 2016 Windows Server 2012 R2 RTM Enterprise**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

Végezetül hozza létre a virtuális gépet a PowerShell használatával:

1. Az új virtuális gép üzemeltetésére felhőalapú szolgáltatás létrehozása. Ne feledje, hogy is használhatja egy meglévő felhőalapú szolgáltatást is. Hozzon létre egy új változót **$svcname** a felhőalapú szolgáltatás rövid nevét.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Adja meg a virtuális gép és a mérete. További információ a virtuálisgép-méretek: [Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Adja meg a helyi rendszergazda fiókot és jelszót.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Futtassa a következő parancsfájlt a virtuális gép létrehozásához.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> További magyarázata és konfigurációs beállítások, a **összeállítása a parancskészlethez** szakasz [a Azure PowerShell szolgáltatás használatával hozzon létre, és előre konfigurálása a Windows-alapú virtuális gépek](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Példa PowerShell-parancsfájl

A következő parancsfájl egy példát a teljes parancsfájl, amely létrehoz egy **SQL Server 2016 Windows Server 2012 R2 RTM Enterprise** virtuális gépet. Ha ezt a parancsfájlt használja, akkor testre kell szabnia a kezdeti változók az előző témakörben leírt lépések alapján.

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

## <a name="connect-with-remote-desktop"></a>Csatlakozzon a távoli asztal

1. Az RDP-fájlok létrehozása az aktuális felhasználó a dokumentum mappában indítsa el a virtuális gépek a telepítés befejezéséhez:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. A Dokumentumok mappájába nyissa meg az RDP-fájlt. Csatlakozás a rendszergazdai felhasználónevet és jelszót adott meg a korábban (például ha a felhasználónév VMAdmin volt, a felhasználó adja meg a "\VMAdmin", és adja meg a jelszót).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Az SQL Server számítógépet, a távelérés konfigurálásának befejezése

A számítógép a távoli asztal való bejelentkezés után konfigurálja az SQL Servert az utasítások alapján [egy Azure virtuális gép az SQL Server-kapcsolat beállításának lépései](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>További lépések

A PowerShell használatával a virtuális gépek rendszerbe állításához további útmutatás található a [virtual machines – dokumentáció](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Sok esetben a következő lépés az adatbázis áttelepítése az új SQL Server virtuális gép. Adatbázis-áttelepítési útmutatóért lásd: [adatbázis Migrálása az SQL Server egy Azure virtuális gépen](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Ha is érdekli az SQL virtuális gépeket létrehozni, olvassa el az Azure portál használatával [Azure SQL Server virtuális gépek kiépítése](../sql/virtual-machines-windows-portal-sql-server-provision.md). Ne feledje, hogy az oktatóanyag, amely végigvezeti a portál helyett a javasolt Resource Manager modellt, a klasszikus modellt a jelen PowerShell témakörben használt virtuális gépek.

Ezekkel az erőforrásokkal mellett azt javasoljuk, hogy tekintse át [SQL Servert futtató Azure virtuális gépek kapcsolatos további témakörökre mutatnak](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
