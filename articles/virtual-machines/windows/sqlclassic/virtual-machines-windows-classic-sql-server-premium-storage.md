---
title: Az Azure Premium Storage használata SQL Server használatával | Microsoft Docs
description: Ez a cikk a klasszikus üzemi modellel létrehozott erőforrásokat használja, és útmutatást nyújt az Azure-Premium Storage az Azure Virtual Machines-on futó SQL Server használatával.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ca11fce252192cbf8e5f0bc2cfb5fcd38f5d4443
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020880"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Az Azure Premium Storage és az SQL Server együttes használata virtuális gépeken

## <a name="overview"></a>Áttekintés

Az [Azure Premium SSD](../disks-types.md) -k a tárterület következő generációja, amely alacsony késést és nagy teljesítményű IO-t biztosít. A legjobb a legfontosabb IO-igényű számítási feladatokhoz, mint például a IaaS- [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)SQL Server.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk a SQL Server rendszert futtató virtuális gépek Premium Storage használatára való áttelepítésének tervezését és útmutatását ismerteti. Ez magában foglalja az Azure-infrastruktúrát (Hálózatkezelés, tárolás) és a vendég Windowsos virtuális gép lépéseit. A [függelékben](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) szereplő példa átfogó teljes körű áttelepítést mutat be, amellyel a nagyobb méretű virtuális gépeket áthelyezheti a továbbfejlesztett helyi SSD-tároló kihasználása érdekében a PowerShell használatával.

Fontos megérteni az Azure-Premium Storage az IAAS-alapú virtuális gépekkel való SQL Serverának teljes körű folyamatát. Az érintett műveletek közé tartoznak az alábbiak:

* A Premium Storage használatának előfeltételeinek azonosítása.
* Példák a SQL Server IaaS való üzembe helyezésére Premium Storage az új központi telepítések esetén.
* Példák a meglévő központi telepítések áttelepítésére, az önálló kiszolgálók és az SQL always on rendelkezésre állási csoportokat használó központi telepítések.
* Lehetséges áttelepítési módszerek.
* Teljes körű példa az Azure-ra, a Windowsra és a SQL Server a meglévő always on-implementáció áttelepítésének lépéseire.

Az Azure Virtual Machines SQL Serverával kapcsolatos további háttérinformációk: [SQL Server az azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Szerző:** Daniel Sol **technikai felülvizsgálók:** Luis Carlos Vargas hering, Sanjay, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Az Premium Storage előfeltételei

A Premium Storage használatának számos előfeltétele van.

### <a name="machine-size"></a>Számítógép mérete

A Premium Storage használatához a DS Series Virtual Machines (VM) szolgáltatást kell használnia. Ha korábban még nem használta a DS sorozatú gépeket a felhőalapú szolgáltatásban, törölnie kell a meglévő virtuális gépet, meg kell őriznie a csatlakoztatott lemezeket, majd létre kell hoznia egy új felhőalapú szolgáltatást, mielőtt újra létrehozza a virtuális gépet a DS * szerepkör méreteként. A virtuális gépek méretével kapcsolatos további információkért lásd: [virtuális gépek és felhőalapú szolgáltatások mérete az Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-hoz.

### <a name="cloud-services"></a>Felhőszolgáltatások

A DS * virtuális gépeket csak akkor használhatja Premium Storage, ha új felhőalapú szolgáltatásban jönnek létre. Ha SQL Server always on Azure-t használja, az Always On figyelő a felhőalapú szolgáltatáshoz társított Azure belső vagy külső Load Balancer IP-címére hivatkozik. Ez a cikk azt ismerteti, hogyan lehet migrálni a rendelkezésre állást a jelen forgatókönyvben.

> [!NOTE]
> A DS * sorozatnak az első olyan virtuális gépnek kell lennie, amelyet az új felhőalapú szolgáltatáshoz telepítenek.
>
>

### <a name="regional-vnets"></a>Regionális virtuális hálózatok

A DS * virtuális gépek esetében konfigurálnia kell a virtuális gépeket üzemeltető Virtual Network (VNET) régiót. Ez a "kiszélesíti" a VNET lehetővé teszi, hogy a nagyobb virtuális gépek kiépítve legyenek más fürtökbe, és lehetővé tegyék a közöttük zajló kommunikációt. A következő képernyőfelvételen a Kiemelt hely a regionális virtuális hálózatok jeleníti meg, míg az első eredmény egy "keskeny" VNET mutat.

![RegionalVNET][1]

A Microsoft támogatási jegyét a regionális VNET való Migrálás céljából teheti fel. A Microsoft ezután megváltoztatja a változást. A regionális virtuális hálózatok való áttelepítés befejezéséhez módosítsa a AffinityGroup tulajdonságot a hálózati konfigurációban. Először exportálja a hálózati konfigurációt a PowerShellben, majd cserélje le a **AffinityGroup** tulajdonságot a **VirtualNetworkSite** elemre egy **Location** tulajdonsággal. Itt adhatja meg `Location = XXXX` , hogy hol `XXXX` található az Azure-régió. Ezután importálja az új konfigurációt.

Például a következő VNET-konfigurációt figyelembe véve:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Ha át szeretné helyezni ezt egy Nyugat-európai regionális VNET, módosítsa a konfigurációt a következőre:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Tárfiókok

Létre kell hoznia egy új, Premium Storagehoz konfigurált Storage-fiókot. Vegye figyelembe, hogy a Premium Storage használata a Storage-fiókra van beállítva, nem az egyes virtuális merevlemezeken, azonban ha DS * sorozatú virtuális gépet használ, a virtuális merevlemezt a prémium és a standard szintű Storage-fiókokból is csatlakoztathatja. Ezt akkor érdemes megfontolni, ha nem szeretné az operációs rendszer virtuális merevlemezét az Premium Storage-fiókba helyezni.

A következő **New-AzureStorageAccountPowerShell** parancs "Premium_LRS" **típussal** hoz létre egy Premium Storage fiókot:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>VHD-gyorsítótár beállításai

Premium Storage fiók részét képező lemezek létrehozása közötti fő különbség a lemezgyorsítótár beállítása. SQL Server adatkötet-lemezek esetében ajánlott az "**olvasási gyorsítótárazás**" használata. A tranzakciónapló-kötetek esetében a lemezgyorsítótár beállítását "**none**" értékre kell állítani. Ez eltér a standard Storage-fiókokra vonatkozó javaslatokkal.

A virtuális merevlemezek csatolása után a gyorsítótár beállítása nem módosítható. A virtuális merevlemezt le kell választania és újra kell csatlakoztatnia egy frissített gyorsítótár-beállítással.

### <a name="windows-storage-spaces"></a>Windows-tárolóhelyek

A [Windows tárolóhelyeket](https://technet.microsoft.com/library/hh831739.aspx) a korábbi szabványos tárolók használatával is használhatja, így egy olyan virtuális gépet telepíthet át, amely már használja a tárolóhelyeket. A [függelékben](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) szereplő példa (9. lépés és továbbítás) azt mutatja be, hogy a PowerShell-kód több csatlakoztatott VHD-vel rendelkező virtuális gép kinyerésére és importálására használható.

A Storage-készleteket standard Azure Storage-fiókkal használták az átviteli sebesség növelése és a késés csökkentése érdekében. Előfordulhat, hogy az új központi telepítések esetében a Premium Storage a tárolási készletek teszteléséhez szükséges értéket, de további bonyolultságot biztosít a tárterület beállításával.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>A Storage-készletekhez hozzárendelt Azure-beli virtuális lemezek megkeresése

Mivel a csatlakoztatott virtuális merevlemezek esetében eltérőek a gyorsítótár-beállítási javaslatok, dönthet úgy, hogy a virtuális merevlemezeket egy Premium Storage-fiókba másolja. Ha azonban újra csatolja őket az új DS sorozatú virtuális géphez, előfordulhat, hogy módosítania kell a gyorsítótár beállításait. A Premium Storage ajánlott gyorsítótár-beállítások alkalmazása egyszerűbb, ha különálló VHD-ket biztosít az SQL-adatfájlokhoz és a naplófájlokhoz (nem pedig egyetlen, mindkettőt tartalmazó VHD-fájlt).

> [!NOTE]
> Ha az adatok és a naplófájlok ugyanazon a köteten vannak SQL Server, a választott gyorsítótárazási lehetőség az adatbázis számítási feladataihoz tartozó IO-hozzáférési mintáktól függ. Csak a tesztelés képes bizonyítani, hogy melyik gyorsítótárazási lehetőség a legmegfelelőbb ehhez a forgatókönyvhöz.
>
>

Ha azonban több VHD-vel rendelkező Windows-tárolóhelyet használ, meg kell vizsgálnia az eredeti parancsfájlokat annak azonosításához, hogy mely csatlakoztatott virtuális merevlemezek vannak az adott készletben, így az egyes lemezek esetében ennek megfelelően állíthatja be a gyorsítótár beállításait.

Ha nem áll rendelkezésre az eredeti szkript, amely megjeleníti, hogy mely VHD-k képezhetők le a tárolóhoz, a következő lépések végrehajtásával határozhatja meg a lemez/tároló készletének leképezését.

Minden lemez esetében kövesse az alábbi lépéseket:

1. A virtuális géphez csatolt lemezek listájának beolvasása a **Get-AzureVM** paranccsal:

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Jegyezze fel a DiskName és a LUN-ot.

    ![DisknameAndLUN][2]
1. Távoli asztal a virtuális géphez. Ezután nyissa meg a **Számítógép-kezelés**  |  **Eszközkezelő**  |  **lemezmeghajtók**lehetőséget. Tekintse meg a "Microsoft virtuális lemezek" tulajdonságait

    ![VirtualDiskProperties][3]
1. A LUN száma itt a virtuális merevlemez virtuális géphez való csatolásakor megadott LUN-számra mutató hivatkozás.
1. A "Microsoft virtuális lemez" területen lépjen a **részletek** lapra, majd a **Tulajdonságok** listájában lépjen az illesztőprogram- **kulcs**elemre. Az **Érték mezőben**jegyezze fel az **eltolást**, amely a következő képernyőképen 0002. A 0002 azt a Fizikailemez2 jelöli, amelyet a Storage-készlet hivatkozik.

    ![VirtualDiskPropertyDetails][4]
1. Az egyes tárolóhelyek esetében a kapcsolódó lemezek kiírása:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Ezzel az információval társíthatja a csatlakoztatott virtuális merevlemezeket a Storage-készletekben található fizikai lemezekhez.

Miután leképezte a virtuális merevlemezeket a Storage-készletekben található fizikai lemezekre, leválaszthatja és átmásolhatja őket egy Premium Storage-fiókba, majd csatolhatja őket a megfelelő gyorsítótár-beállítással. Tekintse meg a [függelékben](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)szereplő példát, 8 – 12. lépést. Ezek a lépések bemutatják, hogyan lehet kibontani a virtuális gépekhez csatlakoztatott virtuális merevlemezek konfigurációját egy CSV-fájlba. másolja a virtuális merevlemezeket, módosítsa a lemez konfigurációs gyorsítótárának beállításait, és végül telepítse újra a virtuális gépet egy, a csatlakoztatott lemezekkel rendelkező DS-sorozatú virtuális géphez

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>A virtuális gép tárolási sávszélessége és a VHD-tároló átviteli sebessége

A tárolási teljesítmény mennyisége a megadott DS * VM-mérettől és a VHD-mérettől függ. A virtuális gépek eltérő jogosultságokkal rendelkeznek a csatolható virtuális merevlemezek számához és az általa támogatott maximális sávszélességhez (MB/s). Az adott sávszélesség-számok esetében lásd: [virtuális gépek és felhőalapú szolgáltatások mérete az Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-hoz.

A megnövekedett IOPS nagyobb méretű lemezekkel érhetők el. Ezt akkor érdemes figyelembe venni, amikor az áttelepítési útvonalat gondolja. További részletekért [tekintse meg a IOPS és a lemezek típusának táblázatát](../disks-types.md#premium-ssd).

Végezetül vegye figyelembe, hogy a virtuális gépek eltérő maximális sávszélességgel rendelkeznek, amelyeket az összes csatolt lemez támogat. A nagy terhelés alatt az adott virtuálisgép-szerepkör mérete számára elérhető maximális sávszélesség telített. Például egy Standard_DS14 legfeljebb 512 MB/s-t támogat. ezért három P30 lemezzel telített a virtuális gép lemezes sávszélessége. Ebben a példában azonban az írási és írási IOs-kombinációtól függően túlléphető az átviteli sebesség korlátja.

## <a name="new-deployments"></a>Új központi telepítések

A következő két szakaszban bemutatjuk, hogyan helyezhet üzembe SQL Server virtuális gépeket Premium Storage. Ahogy korábban említettük, nem feltétlenül kell az operációsrendszer-lemezt a Premium Storage-ra helyeznie. Ezt akkor is választhatja, ha az operációs rendszer VHD-jét szeretné, ha bármilyen intenzív IO-munkaterhelést kíván elhelyezni.

Az első példa a meglévő Azure Gallery-rendszerképek használatát mutatja be. A második példa azt szemlélteti, hogyan használható egyéni virtuálisgép-rendszerkép egy meglévő szabványos Storage-fiókban.

> [!NOTE]
> Ezek a példák feltételezik, hogy már létrehozott egy regionális VNET.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Új virtuális gép létrehozása Premium Storagesal a katalógus Rendszerképével

Az alábbi példa bemutatja, hogyan helyezheti el az operációs rendszer virtuális merevlemezét Premium Storage-ra, és hogyan csatolhat Premium Storage VHD-ket. Az operációsrendszer-lemezt azonban egy standard Storage-fiókba is helyezheti, majd Premium Storage fiókban található virtuális merevlemezeket csatolhat. Mindkét forgatókönyvet bemutatjuk.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>1. lépés: Premium Storage fiók létrehozása

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>2. lépés: új felhőalapú szolgáltatás létrehozása

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>3. lépés: Cloud Service VIP foglalása (opcionális)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>4. lépés: virtuálisgép-tároló létrehozása

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>5. lépés: az operációs rendszer virtuális merevlemezének elhelyezése standard vagy Premium Storage

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>6. lépés: virtuális gép létrehozása

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified '-DiskSizeInGB 1023', this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Új virtuális gép létrehozása Premium Storage egyéni rendszerkép használatával

Ez a forgatókönyv azt mutatja be, hogy hol találhatók a standard Storage-fiókban meglévő testreszabott lemezképek. Ahogy azt említettük, hogy az operációs rendszer virtuális merevlemezét Premium Storage kell helyeznie, át kell másolnia a standard Storage-fiókban található rendszerképet, és a használat előtt át kell vinnie egy Premium Storage. Ha a helyszíni rendszerképtel rendelkezik, ezt a módszert is használhatja, ha közvetlenül a Premium Storage fiókba másolja.

#### <a name="step-1-create-storage-account"></a>1. lépés: a Storage-fiók létrehozása

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>2. lépés – felhőalapú szolgáltatás létrehozása

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>3. lépés: meglévő rendszerkép használata

Használhat meglévő rendszerképet is. Vagy [egy meglévő gépről](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)is készíthet képet. Vegye figyelembe, hogy a rendszerképnek nem kell DS * számítógépnek lennie. Miután elvégezte a rendszerképet, a következő lépések bemutatják, hogyan másolhatja át a Premium Storage-fiókba a **Start-AzureStorageBlobCopy PowerShell-** parancsmagot.

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>4. lépés: blob másolása a Storage-fiókok között

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>5. lépés: a másolási állapot rendszeres ellenőrzését:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>6. lépés: lemezkép-lemez hozzáadása az Azure Disk adattárhoz az előfizetésben

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Előfordulhat, hogy annak ellenére, hogy az állapotjelentés sikeresnek minősülnek, továbbra is kaphat lemez címbérleti hibát. Ebben az esetben várjon körülbelül 10 percet.

#### <a name="step-7--build-the-vm"></a>7. lépés: a virtuális gép létrehozása

Itt épít a virtuális gépet a lemezképből, és két Premium Storage virtuális merevlemezt csatol:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Az Always On rendelkezésre állási csoportokat nem használó meglévő telepítések

> [!NOTE]
> A meglévő központi telepítések esetében először tekintse meg a jelen cikk [Előfeltételek](#prerequisites-for-premium-storage) című szakaszát.

Az Always On rendelkezésre állási csoportokkal és azokkal nem rendelkező SQL Server központi telepítések esetében különböző szempontokat érdemes figyelembe venni. Ha nem az Always on-t használja, és rendelkezik egy meglévő önálló SQL Server, akkor a Premium Storage új felhőalapú szolgáltatás-és Storage-fiókkal frissíthet. Vegye figyelembe a következő lehetőségeket:

* **Hozzon létre egy új SQL Server VM**. Létrehozhat egy új SQL Server VM, amely egy Premium Storage-fiókot használ az új központi telepítésekben dokumentált módon. Ezután biztonsági mentést készíthet, és visszaállíthatja SQL Server konfigurációját és felhasználói adatbázisait. Az alkalmazást úgy kell frissíteni, hogy az új SQL Serverre hivatkozzon, ha a belső vagy külső elérésű. Ha párhuzamosan (SxS) SQL Server áttelepítést végez, az összes "db" objektumot át kell másolnia. Ide tartoznak az olyan objektumok, mint a bejelentkezések, a tanúsítványok és a csatolt kiszolgálók.
* **Meglévő SQL Server VM migrálása**. Ehhez a SQL Server VM offline állapotba kell helyezni, majd át kell vinni egy új felhőalapú szolgáltatásba, amely magában foglalja az összes csatlakoztatott virtuális merevlemez másolását a Premium Storage-fiókba. Amikor a virtuális gép online állapotba kerül, az alkalmazás a kiszolgáló állomásneve a korábbi módon hivatkozik. Vegye figyelembe, hogy a meglévő lemez mérete befolyásolja a teljesítmény jellemzőit. Például egy 400 GB méretű lemez fel lesz kerekítve egy P20. Ha tudja, hogy nincs szüksége a lemez teljesítményére, akkor újra létrehozhatja a virtuális gépet DS-sorozatú virtuális gépként, és csatolhatja Premium Storage VHD-t a szükséges mérethez/teljesítményhez. Ezután leválaszthatja és újracsatolhatja az SQL DB-fájlokat.

> [!NOTE]
> A VHD-lemezek másolásakor a mérettől függően tisztában kell lennie a mérettel, ami azt jelenti, hogy milyen Premium Storage a lemez típusa. Ez határozza meg a lemez teljesítményének specifikációját. Az Azure felfelé kerekít a legközelebbi lemez méretére, így ha van 400GB-lemeze, akkor ez egy P20 lesz kerekítve. Az operációs rendszer virtuális merevlemezének meglévő i/o-követelményeitől függően előfordulhat, hogy nem kell áttelepítenie Premium Storage fiókba.

Ha a SQL Server kívülről érhető el, akkor a Cloud Service VIP megváltozik. Emellett frissítenie kell a végpontokat, az ACL-eket és a DNS-beállításokat is.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Always On rendelkezésre állási csoportokat használó meglévő telepítések

> [!NOTE]
> A meglévő központi telepítések esetében először tekintse meg a jelen cikk [Előfeltételek](#prerequisites-for-premium-storage) című szakaszát.

A szakasz első részében azt vizsgáljuk meg, hogy az Azure-hálózatkezelés hogyan működik az AlwaysOn. Ezután két forgatókönyvbe bontják le az áttelepítést: a Migrálás során bizonyos állásidők tolerálható, és a Migrálás során minimális állásidőt kell elérni.

A helyszíni SQL Server always on rendelkezésre állási csoportok a helyszíni figyelőt használják, amely a virtuális DNS-nevet regisztrálja egy vagy több SQL-kiszolgáló között megosztott IP-címmel. Amikor az ügyfelek csatlakoznak, a figyelő IP-címéről az elsődleges SQL Server irányítja. Ez az a kiszolgáló, amely az Always On IP-erőforrást az adott időpontban birtokolja.

![DeploymentsUseAlways bekapcsolva][6]

Microsoft Azure a virtuális gépen csak egy IP-cím rendelhető hozzá egy hálózati adapterhez, így a helyszíni absztrakciós réteghez hasonlóan az Azure is a belső/külső terheléselosztó (ILB/ELB) által hozzárendelt IP-címet használja. A kiszolgálók között megosztott IP-erőforrás ugyanarra az IP-re van beállítva, mint a ILB/ELB. Ezt a rendszer közzéteszi a DNS-ben, és a ILB/ELB keresztül továbbítja az ügyfelek forgalmát az elsődleges SQL Server replikára. A ILB/ELB tudja, hogy melyik SQL Server elsődleges, mivel mintavételt használ a always on IP-erőforrás mintavételéhez. Az előző példában a ELB/ILB által hivatkozott végponttal rendelkező csomópontok mindegyikét az elsődleges SQL Server.

> [!NOTE]
> A ILB és a ELB egyaránt hozzá van rendelve egy adott Azure Cloud Service-szolgáltatáshoz, ezért az Azure-beli Felhőbeli áttelepítés valószínűleg azt jelenti, hogy a Load Balancer IP-cím megváltozik.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Az Always On üzemelő példányok áttelepítése, amelyek lehetővé teszik bizonyos állásidőt

Az egyes állásidőket engedélyező központi telepítések esetén két stratégiát kell áttelepíteni:

1. **További másodlagos replikák hozzáadása egy meglévő always on-fürthöz**
2. **Migrálás új always on-fürtre**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. további másodlagos replikák hozzáadása egy meglévő always on-fürthöz

Az egyik stratégia az Always On rendelkezésre állási csoport további formátumú másodlagos zónák hozzáadása. Ezeket hozzá kell adnia egy új felhőalapú szolgáltatáshoz, és frissítenie kell a figyelőt az új Load Balancer IP-címmel.

##### <a name="points-of-downtime"></a>Leállási pontok:

* Fürt ellenőrzése.
* Mindig tesztelje az új formátumú másodlagos zónák feladatátvételeit.

Ha a virtuális gépen a Windows Storage-készleteket használja magasabb IO-átviteli sebességre, akkor ezeket a rendszer a fürt teljes érvényesítése során offline állapotba hozza. Az érvényesítési teszt akkor szükséges, ha csomópontokat ad hozzá a fürthöz. A teszt futtatásához szükséges idő eltérő lehet, ezért a reprezentatív tesztkörnyezetben kell tesztelni, hogy a rendszer mennyi ideig tart.

Olyan időt kell kialakítania, ahol manuális feladatátvételi és káosz-tesztelési műveleteket hajthat végre az újonnan hozzáadott csomópontokon, hogy az a vártnál mindig magas rendelkezésre állást biztosítson.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Az érvényesítés futtatása előtt állítsa le SQL Server összes példányát, ahol a rendszer a tárolási készleteket használja.
>
> ##### <a name="high-level-steps"></a>Magas szintű lépések
>

1. Hozzon létre két új SQL-kiszolgálót az új Cloud Service-ben csatolt Premium Storageokkal.
2. Másolja át a teljes biztonsági mentést **, és állítsa vissza a**helyreállítást.
3. Másolás a felhasználói adatbázis függő objektumaira (például bejelentkezések stb.)
4. Hozzon létre új belső Load Balancer-t (ILB), vagy használjon külső Load Balancer (ELB), majd állítsa be az elosztott terhelésű végpontokat mindkét új csomópontra.

   > [!NOTE]
   > A folytatás előtt győződjön meg arról, hogy az összes csomópont megfelelő végpont-konfigurációval rendelkezik.
   >
   >
5. A felhasználó/alkalmazás hozzáférésének leállítása a SQL Serverhoz (ha tárolási készleteket használ).
6. Állítsa le SQL Server Engine Services szolgáltatást az összes csomóponton (ha tárolási készleteket használ).
7. Új csomópontok hozzáadása a fürthöz és a teljes ellenőrzés futtatása.
8. Az ellenőrzés sikerességét követően indítsa el az összes SQL Server szolgáltatást.
9. A biztonsági mentési tranzakciós naplók és a felhasználói adatbázisok visszaállítása.
10. Vegyen fel új csomópontokat az Always On rendelkezésre állási csoportba, és helyezze a replikálást **szinkronba**.
11. Adja hozzá az új Cloud Service ILB/ELB IP-cím erőforrását a PowerShell-en keresztül, hogy mindig a (z) [függelékben](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)található többhelyes példa alapján. A Windows-fürtszolgáltatásban állítsa be az **IP-cím** erőforrás **lehetséges tulajdonosait** az új csomópontok régi értékére. Tekintse meg a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)"IP-cím erőforrásának hozzáadása ugyanazon alhálózaton" szakaszát.
12. Feladatátvétel az egyik új csomópontra.
13. Tegye elérhetővé az új csomópontok automatikus feladatátvételi partnereit és a feladatátvételi teszteket.
14. Az eredeti csomópontok eltávolítása a rendelkezésre állási csoportból.

##### <a name="advantages"></a>Előnyök

* Új SQL Server-kiszolgálókat lehet tesztelni (SQL Server és az alkalmazás), mielőtt hozzáadja őket az Always On értékhez.
* Módosíthatja a virtuális gép méretét, és testre szabhatja a tárterületet a pontos követelmények szerint. Azonban hasznos lenne az összes SQL-fájl elérési útjának megtartása.
* Megadhatja, hogy a rendszer elindítsa-e az adatbázis biztonsági másolatainak átvitelét a másodlagos replikára. Ez eltér az Azure **Start-AzureStorageBlobCopy** parancsmagot a virtuális merevlemezek másolásához, mert ez egy aszinkron másolat.

##### <a name="disadvantages"></a>Hátrányok

* A Windows Storage-készletek használatakor a fürt teljes érvényesítése során az új további csomópontok esetében a fürt leállása is fennáll.
* A SQL Server és a meglévő másodlagos replikák számától függően előfordulhat, hogy nem tud további másodlagos replikákat hozzáadni a meglévő formátumú másodlagos zónák eltávolítása nélkül.
* Az SQL-adatátviteli idő hosszú lehet a formátumú másodlagos zónák beállításakor.
* Az áttelepítés során további költségeket kell megfizetni, miközben az új gépek párhuzamosan futnak.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrálás új always on-fürtre

Egy másik stratégia az új Cloud Service-ben új csomópontokkal rendelkező új, always on fürt létrehozása, majd az ügyfelek átirányítása a használatára.

##### <a name="points-of-downtime"></a>Leállási pontok

Az alkalmazásoknak és a felhasználóknak az új always on-figyelőre való átvitele állásidővel jár. A leállás a következőktől függ:

* A tranzakciós naplók végső biztonsági mentésének visszaállítása az új kiszolgálókon lévő adatbázisokra.
* Az ügyfélalkalmazások új always on Listener használatára való frissítéséhez szükséges idő.

##### <a name="advantages"></a>Előnyök

* Tesztelheti a tényleges éles környezetet, a SQL Server és az operációsrendszer-Build módosításait.
* Lehetősége van testreszabni a tárolót és a virtuális gép méretének csökkentését. Ez a költséghatékonyság csökkenését eredményezheti.
* A folyamat során frissítheti SQL Server buildjét vagy verzióját. Az operációs rendszert is frissítheti.
* Az előző always on fürt megbízható visszaállítási célként működhet.

##### <a name="disadvantages"></a>Hátrányok

* Módosítania kell a figyelő DNS-nevét, ha azt szeretné, hogy a mindig egyszerre futó fürtökön is fusson. Ez adminisztrációs terhelést ad az áttelepítés során, mivel az ügyfélalkalmazás karakterláncának tükröznie kell az új figyelő nevét.
* Egy szinkronizálási mechanizmust kell megvalósítani a két környezet között, hogy a lehető leghamarabb meg lehessen őrizni az áttelepítés előtt a végső szinkronizálási követelményeket.
* Az áttelepítés során további költségeket is fel kell venni, miközben az új környezet fut.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Az Always On üzemelő példányok áttelepítése minimális állásidő esetén

Az Always On üzemelő példányok minimális állásidőre való áttelepítésének két stratégiája van:

1. **Meglévő másodlagos: egy hely kihasználása**
2. **Meglévő másodlagos replikák (k) kihasználtsága: több hely**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. meglévő másodlagos: egy-hely kihasználása

Egy stratégia a minimális állásidőhez a meglévő Felhőbeli másodlagos, és az aktuális felhőalapú szolgáltatásból való eltávolítása. Ezután másolja a virtuális merevlemezeket az új Premium Storage-fiókba, és hozza létre a virtuális gépet az új felhőalapú szolgáltatásban. Ezután frissítse a figyelőt a fürtözésben és a feladatátvételben.

##### <a name="points-of-downtime"></a>Leállási pontok

* Ha a végső csomópontot a terheléselosztási végponttal frissíti, a rendszer leállást végez.
* Az ügyfél/DNS-konfigurációtól függően előfordulhat, hogy az ügyfél újracsatlakoztatása késleltetve van.
* További állásidő áll rendelkezésre, ha úgy dönt, hogy az IP-címek kiváltásához az Always On cluster csoportot offline állapotba helyezi. Ezt elkerülheti a további IP-cím erőforráshoz tartozó vagy függőség és lehetséges tulajdonosok használatával. Tekintse meg a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)"IP-cím erőforrásának hozzáadása ugyanazon alhálózaton" szakaszát.

> [!NOTE]
> Ha azt szeretné, hogy a hozzáadott csomópont mindig feladatátvételi partnerként vegyen részt, hozzá kell adnia egy Azure-végpontot, amely a terheléselosztási készletre mutató hivatkozást tartalmaz. Az **Add-AzureEndpoint** parancs futtatásakor a jelenlegi kapcsolatok nyitva maradnak, de a figyelőhöz való új kapcsolatok nem hozhatók létre, amíg a terheléselosztó nem frissült. A tesztelés során ez az utolsó 90-120seconds volt látható.

##### <a name="advantages"></a>Előnyök

* Az áttelepítés során felmerülő extra költségek nem merültek fel.
* Egy az egyhez Migrálás.
* Kisebb összetettség.
* Lehetővé teszi a Premium Storage SKU-ból való nagyobb IOPS. Ha a lemezeket leválasztják a virtuális gépről, és az új felhőalapú szolgáltatásba másolják, a rendszer külső eszközt is felhasználhat a VHD méretének növelésére, amely nagyobb átviteli sebességet biztosít. A virtuális merevlemezek méretének növeléséhez tekintse meg ezt a [vitafórumot](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Hátrányok

* Az áttelepítés során a HA és a DR ideiglenes adatvesztést okoz.
* Mivel ez egy 1:1-es Migrálás, a virtuális merevlemezek számát támogató minimális virtuálisgép-méretet kell használnia, így előfordulhat, hogy nem fogja tudni felépíteni a virtuális gépeket.
* Ez a forgatókönyv az Azure **Start-AzureStorageBlobCopy** parancsmagot használja, amely aszinkron módon történik. A másolás befejezésekor nincs SLA. A másolatok időpontja változó, míg ez a várakozási sorból függ, az átvinni kívánt adatok mennyiségétől is függ. A másolási idő növekszik, ha az átvitel egy másik Azure-adatközpontba kerül, amely támogatja a Premium Storage egy másik régióban. Ha csak 2 csomóponttal rendelkezik, vegye fontolóra a lehetséges megoldást arra az esetre, ha a másolás hosszabb időt vesz igénybe, mint a tesztelés során. Ez a következő ötleteket tartalmazhatja.
  * Vegyen fel egy ideiglenes 3rd SQL Server csomópontot, hogy az áttelepítést a megállapodott állásidővel megelőzően.
  * Futtassa az áttelepítést az Azure ütemezett karbantartásán kívül.
  * Győződjön meg arról, hogy megfelelően konfigurálta a fürt kvórumát.  

##### <a name="high-level-steps"></a>Magas szintű lépések

Ez a dokumentum nem mutatja be a teljes végpontok közötti példát, azonban a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) olyan részleteket tartalmaz, amelyek a végrehajtásához használhatók.

![MinimalDowntime][8]

* Gyűjtse össze a lemez konfigurációját, és távolítsa el a csomópontot (ne törölje a csatlakoztatott virtuális merevlemezeket).
* Premium Storage fiók létrehozása és virtuális merevlemezek másolása a standard Storage-fiókból
* Hozzon létre új felhőalapú szolgáltatást, és telepítse újra a SQL2 virtuális gépet a felhőalapú szolgáltatásban. Hozza létre a virtuális gépet a másolt eredeti operációs rendszer virtuális merevlemezének használatával, és csatolja a másolt virtuális merevlemezeket.
* Konfigurálja a ILB/ELB, és adja hozzá a végpontokat.
* A figyelő frissítése a következők egyikével:
  * Az Always On Group offline állapotba helyezése és az Always On-figyelő frissítése új ILB/ELB IP-címmel.
  * Vagy adja hozzá az új Cloud Service ILB/ELB IP-cím erőforrását a PowerShellen keresztül a Windows fürtözéshez. Ezután állítsa be az IP-cím erőforrás lehetséges tulajdonosait az áttelepített csomópontra, SQL2, és állítsa be ezt as vagy függőségként a hálózat nevében. Tekintse meg a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)"IP-cím erőforrásának hozzáadása ugyanazon alhálózaton" szakaszát.
* A DNS-konfiguráció/propagálás engedélyezése az ügyfelek számára.
* Telepítse át a SQL1 virtuális gépet, és folytassa a 2 – 4. lépéssel.
* Ha a 5ii lépések használata esetén a SQL1 lehetőséget adja hozzá lehetséges tulajdonosként a hozzáadott IP-cím erőforráshoz
* Feladatátvételi teszt.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. a meglévő másodlagos replikák (k) kihasználtsága: több hely

Ha több Azure-adatközpontban (DC) is rendelkezik csomópontokkal, vagy ha hibrid környezete van, akkor az állásidő csökkentése érdekében a környezet minden always on konfigurációját használhatja.

Ennek a módszernek a célja, hogy módosítsa az Always On szinkronizálást a helyszíni vagy másodlagos Azure-TARTOMÁNYVEZÉRLŐre, majd a feladatátvételt az adott SQL Server. Ezután másolja a virtuális merevlemezeket egy Premium Storage-fiókba, és telepítse újra a gépet egy új felhőalapú szolgáltatásba. Frissítse a figyelőt, majd hajtsa végre a feladat-visszavétel.

##### <a name="points-of-downtime"></a>Leállási pontok

Az állásidő az alternatív TARTOMÁNYVEZÉRLŐre és vissza történő feladatátvétel idejéből áll. Az ügyfél/DNS-konfigurációtól is függ, és az ügyfél újracsatlakoztatása késleltethető lehet.
Vegye figyelembe a következő példát a hibrid always on konfigurációra:

![MultiSite1][9]

##### <a name="advantages"></a>Előnyök

* A meglévő infrastruktúrát is használhatja.
* Lehetősége van az Azure Storage előzetes frissítésére a DR Azure DC-ben.
* A DR Azure DC-tárolót újra lehet konfigurálni.
* Az áttelepítés során legalább két feladatátvételt kell megadnia, kivéve a feladatátvételi tesztet.
* A biztonsági mentéssel és visszaállítással nem kell áthelyeznie SQL Server az adatátvitelt.

##### <a name="disadvantages"></a>Hátrányok

* Az SQL Serverhoz való ügyfél-hozzáféréstől függően előfordulhat, hogy a késleltetés nagyobb késéssel jár, ha SQL Server egy másik TARTOMÁNYVEZÉRLŐn fut az alkalmazásban.
* A virtuális merevlemezek a Premium Storage-ba történő másolásának ideje hosszú lehet. Ez befolyásolhatja a döntést arról, hogy a csomópontot megtartja-e a rendelkezésre állási csoportban. Ezt akkor érdemes figyelembe venni, ha az áttelepítés során a log intenzív munkaterhelések futnak, mivel az elsődleges csomópontnak meg kell őriznie a nem replikált tranzakciókat a tranzakciós naplójában. Ezért ez jelentős növekedéshez vezethet.
* Ez a forgatókönyv az Azure **Start-AzureStorageBlobCopy** parancsmagot használja, amely aszinkron módon történik. A befejezéshez nem tartozik SLA. A másolatok időpontja változó, míg ez a várakozási sorból függ, az átvinni kívánt adatok mennyiségétől is függ. Ezért csak egy csomóponttal rendelkezik a második adatközpontban, érdemes enyhíteni a lépéseket abban az esetben, ha a másolás hosszabb időt vesz igénybe, mint a tesztelés során. Ezek a kockázatcsökkentő lépések a következő ötleteket tartalmazzák:
  * Vegyen fel egy ideiglenes második SQL-csomópontot, hogy az áttelepítést a megegyezett állásidővel megelőzően.
  * Futtassa az áttelepítést az Azure ütemezett karbantartásán kívül.
  * Győződjön meg arról, hogy megfelelően konfigurálta a fürt kvórumát.

Ez a forgatókönyv feltételezi, hogy a telepítésének dokumentálása és a tárterület leképezett állapotának megismerése érdekében a gyorsítótárak optimális beállításainak módosításához szükséges.

##### <a name="high-level-steps"></a>Magas szintű lépések

![Multisite2][10]

* Tegye a helyszíni/alternatív Azure DC-t a SQL Server elsődlegesre, és tegye a másik automatikus feladatátvételi partnert (AFP).
* Gyűjtse össze a lemez konfigurációs adatait a SQL2-ből, és távolítsa el a csomópontot (ne törölje a csatlakoztatott virtuális merevlemezeket).
* Hozzon létre egy Premium Storage fiókot, és másolja a virtuális merevlemezeket a standard Storage-fiókból.
* Hozzon létre egy új felhőalapú szolgáltatást, és hozza létre a SQL2 virtuális gépet a mellékelt Premium Storage-lemezekkel.
* Konfigurálja a ILB/ELB, és adja hozzá a végpontokat.
* Frissítse az Always On figyelőt új ILB/ELB IP-címmel és feladatátvételi teszttel.
* Keresse meg a DNS-konfigurációt.
* Módosítsa az AFP-t a SQL2-re, majd telepítse át a SQL1, és folytassa a 2 – 5. lépéssel.
* Feladatátvételi teszt.
* Állítsa vissza az AFP-t SQL1 és SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Függelék: több hely áttelepítése mindig a fürtön Premium Storage

A cikk további részében részletesen ismertetjük a többhelyes always on fürtön a Premium Storage-ra való átalakítás részleteit. Emellett átalakítja a figyelőt külső terheléselosztó (ELB) és belső terheléselosztó (ILB) használatával.

### <a name="environment"></a>Környezet

* Windows 2k12/SQL 2k12
* 1 DB-fájl az SP-ben
* 2 x tárolási készlet/csomópont

![Appendix1][11]

### <a name="vm"></a>VM

Ebben a példában egy ELB ILB-re való áthelyezését mutatjuk be. A ELB a ILB előtt volt elérhető, ezért ez azt mutatja be, hogyan válthat a ILB az áttelepítés során.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Előzetes lépések: csatlakozás az előfizetéshez

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>1. lépés: új Storage-fiók és felhőalapú szolgáltatás létrehozása

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>2. lépés: a megengedett hibák javítása az erőforrásokon\<Optional>

Az Always On rendelkezésre állási csoportba tartozó egyes erőforrásokon korlátok vannak attól függően, hogy hány hiba fordulhat elő egy adott időszakban, ahol a fürtszolgáltatás megkísérli újraindítani az erőforráscsoportot. Javasoljuk, hogy ezt az eljárást csak akkor növelje, ha a folyamat leállításával nem végez manuálisan feladatátvételt, és nem indítja el a feladatátvételt. ehhez a korláthoz közelebb kerülhet.

Érdemes lenne megduplázni a meghibásodási támogatást, hogy ezt Feladatátvevőfürt-kezelőban végezze el, lépjen a mindig az erőforráscsoport tulajdonságaira:

![Appendix3][13]

Módosítsa a maximális hibákat 6-ra.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>3. lépés: a fürthöz tartozó IP-cím erőforrásának hozzáadása\<Optional>

Ha a fürthöz csak egy IP-cím tartozik, és ez a Felhőbeli alhálózathoz van igazítva, ügyeljen arra, hogy ha véletlenül offline állapotba helyezi az összes fürtcsomópontot a felhőben ezen a hálózaton, akkor a fürt IP-erőforrása és a fürt hálózati neve nem fog tudni online állapotba helyezni. Ebben az esetben megakadályozza a más fürterőforrás-erőforrások frissítését.

#### <a name="step-4-dns-configuration"></a>4. lépés: DNS-konfiguráció

A zökkenőmentes átállás megvalósítása a DNS kihasználtságának és frissítésének módjától függ.
Ha a mindig bekapcsolt állapotú, akkor egy Windows-fürterőforrás-csoportot hoz létre, ha megnyit Feladatátvevőfürt-kezelőt, láthatja, hogy legalább három erőforrása van, a kettő, amelyre a dokumentum hivatkozik:

* Virtual Network name (VNN) – az a DNS-név, amelyhez az ügyfelek csatlakoznak, amikor az SQL-kiszolgálókhoz való kapcsolódást szeretnék az Always On használatával.
* IP-cím erőforrása – a VNN társított IP-cím több is lehet, és egy többhelyes konfigurációban hely/alhálózat IP-címmel rendelkezik.

SQL Serverhoz való csatlakozáskor a SQL Server ügyfél-illesztőprogram lekéri a figyelőhöz társított DNS-rekordokat, és megpróbál csatlakozni az Always On társított IP-címekhez. Ezután megbeszéljük azokat a tényezőket, amelyek befolyásolhatják ezt.

A figyelő nevével társított egyidejű DNS-rekordok száma nem csak a társított IP-címek számától függ, a RegisterAllIpProviders'setting pedig az Always ON VNN erőforráshoz tartozó feladatátvételi fürtszolgáltatásban.

Ha az Azure-ban üzembe helyezi az Always on-t, a figyelő és az IP-címek létrehozásához különböző lépések szükségesek, a "RegisterAllIpProviders"-et manuálisan kell konfigurálnia, ez azonban nem azonos a helyszíni always on üzemelő példányokkal, ahol az már 1 értékre van állítva.

Ha a "RegisterAllIpProviders" értéke 0, akkor csak egy DNS-rekord jelenik meg a figyelőhöz társított DNS-ben:

![Appendix4][14]

Ha a "RegisterAllIpProviders" értéke 1:

![Appendix5][15]

Az alábbi kód kiírja a VNN beállításait, és beállítja az Ön számára. Ahhoz, hogy a módosítás életbe lépjen, a VNN offline állapotba kell állítania, majd újra kell kapcsolnia. Ez a figyelő offline állapotba állítása okozza az ügyfél-kapcsolat megszakadását.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Egy későbbi áttelepítési lépésben frissítenie kell az Always On figyelőt egy olyan frissített IP-címmel, amely egy Load balancerre hivatkozik. Ez magában foglalja az IP-címek erőforrás-eltávolítását és hozzáadását. Az IP-frissítés után győződjön meg arról, hogy az új IP-cím frissült a DNS-zónában, és hogy az ügyfelek frissítik a helyi DNS-gyorsítótárat.

Ha az ügyfelek egy másik hálózati szegmensben találhatók, és egy másik DNS-kiszolgálóra hivatkoznak, akkor meg kell fontolnia, hogy mi történik a DNS-zónaletöltés az áttelepítés során történő átvitele során, mivel az alkalmazás újrakapcsolódási idejét a figyelőhöz tartozó új IP-címekhez tartozó zónaletöltési idő korlátozza. Ha itt van az időkorlátja, érdemes megbeszélni és tesztelni a növekményes zónaletöltést a Windows-csapatokkal, és a DNS-gazdagép rekordját is egy alacsonyabb élettartamra (TTL) kell helyeznie, így az ügyfelek frissülnek. További információ: [növekményes zónaletöltések](https://technet.microsoft.com/library/cc958973.aspx) és [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Alapértelmezés szerint a figyelőhöz társított DNS-rekord ÉLETTARTAMa az Azure-ban mindig 1200 másodperc. Előfordulhat, hogy csökkenteni kívánja ezt, ha az áttelepítés során időkorlátot tapasztal, így biztosítva, hogy az ügyfelek a figyelőhöz tartozó frissített IP-címmel frissítik a DNS-t. A konfigurációt a VNN konfigurációjának kiírásával tekintheti meg és módosíthatja:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Minél kisebb a "HostRecordTTL", annál nagyobb a DNS-forgalom mennyisége.

##### <a name="client-application-settings"></a>Ügyfélalkalmazás beállításai

Ha az SQL-ügyfélalkalmazás támogatja a .NET 4,5 SQLClient, akkor a "MULTISUBNETFAILOVER = TRUE" kulcsszót használhatja. Ezt a kulcsszót alkalmazni kell, mert lehetővé teszi az SQL always on rendelkezésre állási csoport gyorsabb csatlakozását a feladatátvétel során. A rendszer az Always On-figyelőhöz kapcsolódó összes IP-címet párhuzamosan sorolja fel, és a feladatátvétel során agresszívebb TCP-kapcsolatok újrapróbálkozási sebességét hajtja végre.

További információ az előző beállításokról: [MultiSubnetFailover kulcsszó és társított szolgáltatások](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Lásd még: [SqlClient-támogatás a magas rendelkezésre álláshoz, vész-helyreállításhoz](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>5. lépés: a fürt Kvórumának beállításai

Ahogy egy időben legalább egy SQL Server le fog állni, módosítania kell a fürt Kvórumának beállítását, ha a tanúsító fájlmegosztás (FSW) használata két csomóponttal történik, a kvórumot úgy kell beállítania, hogy engedélyezze a csomópontok többségét, és dinamikus szavazást használjon, amely lehetővé teszi, hogy egyetlen csomópont álljon továbbra is.

```powershell
Set-ClusterQuorum -NodeMajority  
```

A fürt Kvórumának kezelésével és konfigurálásával kapcsolatos további információkért lásd: [a kvórum konfigurálása és kezelése Windows Server 2012 feladatátvevő fürtben](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>6. lépés: meglévő végpontok és ACL-ek kibontása

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Mentse ezt a szöveget egy fájlba.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>7. lépés: a feladatátvételi partnerek és a replikációs módok módosítása

Ha több mint két SQL-kiszolgálója van, akkor egy másik másodlagos TARTOMÁNYVEZÉRLŐn vagy a helyszínen a "szinkron" értékre kell váltania, és ezt egy automatikus feladatátvételi partnernek (AFP) kell megadnia, így Ön is megtartja, amíg a módosítások végrehajtása folyamatban van. Ezt megteheti a módosítás TSQL, de a SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>8. lépés: a másodlagos virtuális gép eltávolítása a Cloud Service-ből

Először a Felhőbeli másodlagos csomópontok áttelepítését kell megtervezni. Ha ez a csomópont jelenleg elsődleges, akkor manuális feladatátvételt kell kezdeményezni.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>9. lépés: a lemez gyorsítótárazási beállításainak módosítása CSV-fájlban és mentés

Az adatkötetek esetében ezeket csak ReadOnly értékre kell beállítani.

A TLOG kötetek esetében ezeket a NONE értékre kell állítani.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>10. lépés: virtuális merevlemezek másolása

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


A virtuális merevlemezek másolási állapotát a Premium Storage fiókban tekintheti meg:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

Várjon, amíg a rendszer az összeset sikeresként rögzíti.

Egyéni Blobokkal kapcsolatos információk:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>11. lépés: operációsrendszer-lemez regisztrálása

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>12. lépés: másodlagos importálása az új Cloud Service-be

Az alábbi kód a hozzáadott lehetőséget is használja, amely importálhatja a gépet, és használhatja a megőrzött VIP-t is.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>13. lépés: ILB létrehozása új Felhőbeli SVC-ben, elosztott terhelésű végpontok és ACL-ek hozzáadása

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>14. lépés: a frissítés mindig bekapcsolva

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

Most távolítsa el a régi felhőalapú szolgáltatás IP-címét.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>15. lépés: DNS-frissítések keresése

Most ellenőriznie kell a DNS-kiszolgálókat a SQL Server-ügyfélszámítógépeken, és győződjön meg arról, hogy a fürtözés hozzá lett adva a további IP-címhez tartozó további gazdagép-rekordhoz. Ha ezek a DNS-kiszolgálók nem frissültek, vegye fontolóra a DNS-zónaletöltések kényszerítését, és gondoskodjon arról, hogy az ott lévő alhálózaton lévő ügyfelek képesek legyenek feloldani a mindig IP-címekre, így nem kell megvárnia az automatikus DNS-replikációt.

#### <a name="step-16-reconfigure-always-on"></a>16. lépés: AlwaysOn újrakonfigurálása

Ekkor megvárja, amíg a másodlagos csomópontot áttelepítette, hogy teljesen újraszinkronizálja a helyszíni csomópontot, és váltson szinkron replikálási csomópontra, és állítsa be az AFP-t.  

#### <a name="step-17-migrate-second-node"></a>17. lépés: a második csomópont migrálása

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>18. lépés: a lemez gyorsítótárazási beállításainak módosítása CSV-fájlban és mentés

Az adatkötetek esetében a gyorsítótár beállításait ReadOnly értékre kell beállítani.

A TLOG kötetek esetében a gyorsítótár beállításait a NONE értékre kell beállítani.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>19. lépés: új, független Storage-fiók létrehozása másodlagos csomóponthoz

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>20. lépés: virtuális merevlemezek másolása

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

A VHD-másolási állapotot az összes virtuális merevlemezen is megtekintheti:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

Várjon, amíg a rendszer az összeset sikeresként rögzíti.

Egyéni Blobokkal kapcsolatos információk:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>21. lépés: operációsrendszer-lemez regisztrálása

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>22. lépés: elosztott terhelésű végpontok és ACL-ek hozzáadása

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>23. lépés: feladatátvételi teszt

Várjon, amíg az áttelepített csomópont szinkronizálva van a helyszíni always on csomóponttal. Helyezze szinkron replikálási módba, és várjon, amíg a szinkronizálás be nem fejeződik. Ezután a helyszíni feladatátvételt az első áttelepített csomópontra, amely az AFP. A munka után módosítsa az utolsó áttelepített csomópontot az AFP-be.

Tesztelje a feladatátvételt az összes csomópont között, és futtassa a Chaos-teszteket annak érdekében, hogy a feladatátvételek a várt módon működjenek, és egy időben a kastélyban.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>24. lépés: a fürt Kvórumának beállítása/DNS TTL/feladatátvételi Pntrs/szinkronizálási beállítások

##### <a name="adding-ip-address-resource-on-same-subnet"></a>IP-cím erőforrásának hozzáadása ugyanazon az alhálózaton

Ha csak két SQL-kiszolgálóval rendelkezik, és szeretné áttelepíteni őket egy új felhőalapú szolgáltatásba, de szeretné megtartani őket ugyanazon az alhálózaton, akkor a figyelő offline állapotba helyezésével törölheti az eredeti always on IP-címet, és hozzáadhatja az új IP-címet. Ha a virtuális gépeket egy másik alhálózatra telepíti át, nem kell ezt megtennie, mert az alhálózatra hivatkozó további fürthálózat is van.

Miután felvette az áttelepített másodlagos szolgáltatást, és hozzáadta az új felhőalapú szolgáltatás új IP-cím erőforrásához a meglévő elsődleges feladatátvétel előtt, hajtsa végre ezeket a lépéseket a fürtön Feladatátvételi felügyelő:

Az IP-cím hozzáadásához tekintse meg a következő függeléket: 14. lépés.

1. Az aktuális IP-cím erőforrás esetében módosítsa a lehetséges tulajdonost "meglévő elsődleges SQL Server" értékre, a példában a "dansqlams4":

    ![Appendix13][23]
2. Az új IP-cím erőforrás esetében módosítsa a lehetséges tulajdonost "áttelepített másodlagos SQL Server" értékre, a példában a "dansqlams5":

    ![Appendix14][24]
3. Ha ez a beállítás meg van adva, feladatátvételt végezhet, és az utolsó csomópont áttelepítése után a lehetséges tulajdonosokat szerkeszteni kell, hogy a csomópontot a rendszer a lehetséges tulajdonosként adja hozzá:

    ![Appendix15][25]

## <a name="additional-resources"></a>További források

* [Azure-Premium Storage](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server az Azure-ban Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
