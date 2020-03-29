---
title: Klasszikus SQL Server virtuális gép (PowerShell) létrehozása
description: Lépéseket és PowerShell-parancsfájlokat tartalmaz az SQL Server virtuálisgép-lemezképekkel rendelkező Azure virtuális gép létrehozásához. Ez a témakör a klasszikus telepítési módot használja.
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
ms.openlocfilehash: 5bfdcfab37091a5f581ce147c0a6af5ccd8147a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914789"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>SQL Server rendszerű virtuális gép létrehozása az Azure PowerShell-lel (klasszikus)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

Ez a cikk a PowerShell-parancsmagok használatával az SQL Server virtuális gépek azure-beli létrehozásának lépéseit ismerteti.

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

A témakör Resource Manager-verziójáról az [SQL Server virtuális gép kiépítése az Azure PowerShell Resource Manager használatával című](../sql/virtual-machines-windows-ps-sql-create.md)témakörben olvashat.

### <a name="install-and-configure-powershell"></a>A PowerShell telepítése és konfigurálása:
1. Ha nem rendelkezik Azure-fiókkal, az [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/) biztosít.
2. [Töltse le és telepítse a legújabb Azure PowerShell-parancsokat.](/powershell/azure/overview)
3. Indítsa el a Windows PowerShellt, és csatlakoztassa az Azure-előfizetéséhez az **Add-AzureAccount** paranccsal.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Határozza meg a cél Azure-régiót

Az SQL Server virtuális gép egy adott Azure-régióban található felhőszolgáltatásban lesz tárolva. A következő lépések segítségével meghatározhatja a régiót, a tárfiókot és a felhőszolgáltatást, amelyet az oktatóanyag többi részében használni fog.

1. Határozza meg az SQL Server virtuális gép üzemeltetéséhez használni kívánt adatközpontot. A következő PowerShell-parancs megjeleníti az elérhető területnevek listáját.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Miután azonosította a kívánt helyet, állítson be egy **$dcLocation** nevű változót az adott régióra. A következő parancs például "USA keleti régió" lesz:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Az előfizetési és tárfiók beállítása

1. Határozza meg az új virtuális géphez használni kívánt Azure-előfizetést.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Rendelje hozzá a cél Azure-előfizetést a **$subscr** változóhoz. Ezután állítsa be ezt a jelenlegi Azure-előfizetésként.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Ezután ellenőrizze a meglévő tárfiókok. A következő parancsfájl megjeleníti az összes olyan tárfiókot, amely a kiválasztott régióban létezik:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Ha új tárfiókra van szüksége, először hozzon létre egy kisbetűs tárfiók nevét a New-AzureStorageAccount paranccsal, ahogy az a következő példában is:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Rendelje hozzá a céltárfiók nevét a **$staccount.** Ezután a **Set-AzureSubscription** segítségével állítsa be az előfizetést és az aktuális tárfiókot.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>SQL Server virtuálisgép-lemezkép kiválasztása

1. Ismerje meg az sql server virtuális gépek elérhető lemezképeinek listáját a katalógusból. Ezek a képek mind rendelkeznek egy **ImageFamily** tulajdonsággal, amely "SQL" kezdetű. A következő lekérdezés megjeleníti az SQL Server előtelepített lemezképcsaládot.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Ha megtalálta a virtuális gép képcsaládja, lehet, hogy több közzétett képek ebben a családban. A következő parancsfájl segítségével megkeresheti a kiválasztott lemezképcsalád legújabb virtuálisgép-lemezképnevét (például **a Windows Server 2012 R2 rendszeren futó SQL Server 2016 RTM Enterprise):**

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

Végül hozza létre a virtuális gépet a PowerShell használatával:

1. Hozzon létre egy felhőalapú szolgáltatást az új virtuális gép üzemeltetéséhez. Vegye figyelembe, hogy lehetőség van egy meglévő felhőszolgáltatás használatára is. Hozzon létre egy új **változót $svcname** a felhőszolgáltatás rövid nevével.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Adja meg a virtuális gép nevét és méretét. A virtuális gépek méretéről további információt az [Azure virtuálisgép-méretei című témakörben talál.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Adja meg a helyi rendszergazdai fiókot és jelszót.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Futtassa a következő parancsfájlt a virtuális gép létrehozásához.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> További magyarázat és konfigurációs beállításokért tekintse meg **a Parancskészlet létrehozása** szakaszban az [Azure PowerShell létrehozása és előzetes konfigurálása Windows-alapú virtuális gépek.](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="example-powershell-script"></a>Példa a PowerShell parancsprogramra

A következő parancsfájl egy teljes parancsfájlt mutat be, amely létrehoz egy **SQL Server 2016 RTM Enterprise rendszert Windows Server 2012 R2** virtuális gépen. Ha ezt a parancsfájlt használja, a témakör előző lépései alapján testre kell szabnia a kezdeti változókat.

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

## <a name="connect-with-remote-desktop"></a>Csatlakozás távoli asztalhoz

1. Hozza létre az aktuális felhasználó dokumentummappájában lévő RDP-fájlokat, hogy a virtuális gépek elindítsák a telepítés befejezéséhez:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. A dokumentumkönyvtárban indítsa el az RDP-fájlt. Csatlakozzon a korábban megadott rendszergazdai felhasználónévhez és jelszóhoz (például ha a felhasználóneve VMAdmin volt, adja meg felhasználóként a "\VMAdmin" értéket, és adja meg a jelszót).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Az SQL Server Machine távelérésre szolgáló konfigurációjának befejezése

Miután távoli asztallal jelentkezett be a gépre, konfigurálja az SQL Server kiszolgálót az [SQL Server-kapcsolat Azure-beli virtuális gépben történő konfigurálásához](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)szükséges lépések utasításai alapján.

## <a name="next-steps"></a>További lépések

A virtuális [gépek dokumentációjában](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)további utasításokat talál a powershellnel rendelkező virtuális gépek kiépítéséhez.

Sok esetben a következő lépés az adatbázisok áttelepítése az új SQL Server virtuális gépre. Az adatbázis-áttelepítéssel kapcsolatos útmutatásért olvassa el az [Adatbázis áttelepítése az SQL Serverkiszolgálóra Azure-beli virtuális gépen című témakört.](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)

Ha az Azure Portal használatával is szeretne SQL virtuális gépeket létrehozni, olvassa [el az SQL Server virtuális gép kiépítése az Azure-ban](../sql/virtual-machines-windows-portal-sql-server-provision.md)című témakört. Vegye figyelembe, hogy az oktatóanyag, amely végigvezeti a portálon a virtuális gépek et az ajánlott Erőforrás-kezelő modell használatával hoz létre, nem pedig a PowerShell-témakörben használt klasszikus modell használatával.

Ezen erőforrásokon kívül azt javasoljuk, hogy tekintse át az [SQL Server Azure virtuális gépeken való futtatásával kapcsolatos egyéb témaköröket](../sql/virtual-machines-windows-sql-server-iaas-overview.md)is.
