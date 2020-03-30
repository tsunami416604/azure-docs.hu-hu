---
title: Az Azure Premium Storage használata az SQL Server rel | Microsoft dokumentumok
description: Ez a cikk a klasszikus üzembe helyezési modellel létrehozott erőforrásokat használja, és útmutatást ad az Azure Premium Storage azure-beli virtuális gépeken futó SQL Server használatával.
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
ms.openlocfilehash: 479f9abc667e20a136da5f6231e78a1e4052f087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965673"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Az Azure Premium Storage és az SQL Server együttes használata virtuális gépeken

## <a name="overview"></a>Áttekintés

[Az Azure prémium szintű SSD-k](../disks-types.md) a következő generációs tároló, amely alacsony késést és nagy átviteli io-t biztosít. A legfontosabb I/O-intenzív számítási feladatok, például az SQL Server iaaS [virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/)való kezeléséhez működik a legjobban.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk tervezési és útmutatást nyújt az SQL Servert futtató virtuális gépek prémium szintű storage használatához történő áttelepítéséhez. Ez magában foglalja az Azure-infrastruktúra (hálózatkezelés, tárolás) és a vendég Windows virtuális gép lépéseit. A [függelékben](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) látható egy teljes körű, teljes körű áttelepítése, hogyan helyezze át a nagyobb virtuális gépek, hogy kihasználják a továbbfejlesztett helyi SSD-tároló powershell.

Fontos megérteni az Azure Premium Storage és az SQL Server iAAS virtuális gépeken való felhasználásának teljes körű folyamatát. Az érintett műveletek közé tartoznak az alábbiak:

* A prémium szintű storage használatának előfeltételeinek azonosítása.
* Példák az SQL Server iaaS-alapú üzembe helyezésére a prémium szintű storage-ba az új központi telepítések hez.
* Példák a meglévő központi telepítések áttelepítésére, önálló kiszolgálók és az SQL Always On availability groups használatával.
* Lehetséges migrációs megközelítések.
* Teljes körű példa az Azure, a Windows és az SQL Server lépései egy meglévő Mindig a megvalósításon történő áttelepítéshez.

Az Azure virtuális gépek SQL Server szolgáltatásáról az SQL Server az [Azure virtuális gépekben](../sql/virtual-machines-windows-sql-server-iaas-overview.md)című témakörben talál további háttérinformációkat.

**Szerző:** Daniel Sol **Technikai Kritikusok:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>A prémium szintű tárolás előfeltételei

A prémium szintű storage használatának számos előfeltétele van.

### <a name="machine-size"></a>A gép mérete

A prémium szintű storage használatához a DS sorozatú virtuális gépek (VM) használatához kell használnia. Ha korábban még nem használta a DS-sorozatú gépeket a felhőszolgáltatásban, törölnie kell a meglévő virtuális gépet, meg kell őriznie a csatlakoztatott lemezeket, majd létre kell hoznia egy új felhőszolgáltatást, mielőtt újra létrehozza a virtuális gépet DS* szerepkörméretként. A virtuális gépek méretéről további információt az [Azure virtuálisgép- és felhőszolgáltatás-méretei című témakörben talál.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cloud-services"></a>Felhőszolgáltatások

A DS* virtuális gépek csak akkor használhatók prémium szintű tárterülettel, ha egy új felhőszolgáltatásban jönnek létre. Ha az SQL Server mindig be van kapcsolva az Azure-ban, az Always On Listener az Azure belső vagy külső terheléselosztó IP-címére hivatkozik, amely egy felhőszolgáltatáshoz van társítva. Ez a cikk az áttelepítésre összpontosít, miközben ebben a forgatókönyvben fenntartja a rendelkezésre állást.

> [!NOTE]
> A DS* sorozatnak az első virtuális gépnek kell lennie, amely telepítve van az új felhőszolgáltatásban.
>
>

### <a name="regional-vnets"></a>Regionális virtuális hálózatok

A DS* virtuális gépek hez a virtuális gépeket üzemeltető virtuális hálózatot (VNET) kell konfigurálnia regionális nak. Ez "kiszélesíti" a virtuális hálózat, hogy a nagyobb virtuális gépek kiépítése más fürtökben, és lehetővé teszi a kommunikációt közöttük. A következő képernyőképen a kiemelt hely a regionális virtuális hálózatokat jeleníti meg, míg az első eredmény egy "keskeny" virtuális hálózatot jelenít meg.

![RegionalVNET][1]

A Microsoft támogatási jegye a regionális virtuális hálózatra való áttelepítéshez emelhető ki. A Microsoft ezután módosítja a programot. A regionális virtuális hálózatokra való áttérés befejezéséhez módosítsa az AffinityGroup tulajdonságot a hálózati konfigurációban. Először exportálja a Hálózati konfigurációt a PowerShellben, majd cserélje le a **VirtualNetworkSite** elem **AffinityGroup** tulajdonságát egy **Hely** tulajdonságra. Adja `Location = XXXX` `XXXX` meg, hogy hol található az Azure-régió. Ezután importálja az új konfigurációt.

Például a következő virtuális hálózat konfigurációját figyelembe véve:

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

Ha ezt egy nyugat-európai regionális virtuális hálózatra szeretné áthelyezni, módosítsa a konfigurációt a következőkre:
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

Létre kell hoznia egy új tárfiókot, amely a prémium szintű storage-hoz van konfigurálva. Vegye figyelembe, hogy a prémium szintű storage-használat a tárfiókban van beállítva, nem az egyes virtuális merevlemezeken, azonban egy DS* sorozatú virtuális gép használata esetén a prémium és standard szintű storage-fiókokból virtuális merevlemezeket csatolhat. Érdemes lehet ezt figyelembe venni, ha nem szeretné elhelyezni az operációs rendszer virtuális merevlemezét a prémium szintű tárfiókba.

A következő **New-AzureStorageAccountPowerShell** parancs a "Premium_LRS" **típussal** prémium szintű tárfiókot hoz létre:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>VHD-k gyorsítótárának beállításai

A prémium szintű tárfiók részét szolgáló lemezek létrehozása közötti fő különbség a lemezgyorsítótár-beállítás. Az SQL Server Data kötetlemezek esetében ajánlott a '**Read Caching**' használata. Tranzakciónapló-kötetek esetén a lemezgyorsítótár-beállítást "**Nincs**" beállításra kell állítani. Ez eltér a standard tárfiókokra vonatkozó javaslatoktól.

A Virtuálisgépek csatlakoztatása után a gyorsítótár beállítása nem módosítható. A virtuális merevlemezt le kell választania, majd újra kell csatolnia egy frissített gyorsítótár-beállítással.

### <a name="windows-storage-spaces"></a>Windows tárolóhelyek

Használhatja [a Windows tárolóhelyek,](https://technet.microsoft.com/library/hh831739.aspx) mint a korábbi standard tároló, ez lehetővé teszi, hogy áttelepíteni egy virtuális gép, amely már használja tárolóhelyek. A [függelékben](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (9. lépés és előre) példa bemutatja a Powershell-kódot a több csatlakoztatott Virtuálisgép-kód kinyeréséhez és importálásához.

A storage-készletek et standard Azure-tárfiókkal használták az átviteli képesség növelése és a késés csökkentése érdekében. Előfordulhat, hogy értéket talál a storage-készletek tesztelése prémium szintű storage új központi telepítések, de ezek további összetettsége a tárolási beállítás.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Hogyan lehet megtalálni, hogy mely Azure virtuális lemezek képezzék le tárolókészletekre

Mivel a csatlakoztatott virtuális gépekhez különböző gyorsítótár-beállítási javaslatok vannak, dönthet úgy, hogy a Virtuálisgépeket egy prémium szintű tárfiókba másolja. Azonban, ha újra csatolja őket az új DS sorozat virtuális gép, előfordulhat, hogy módosítania kell a gyorsítótár beállításait. Egyszerűbb a prémium szintű storage ajánlott gyorsítótár-beállításainak alkalmazása, ha külön VIRTUÁLIS MEREVLEMEZek vannak az SQL Data-fájlokhoz és a naplófájlokhoz (nem pedig egyetlen virtuális merevlemez, amely mindkettőt tartalmazza).

> [!NOTE]
> Ha az SQL Server-adatok és a naplófájlok ugyanazon a köteten vannak, a választott gyorsítótárazási beállítás az adatbázis-számítási feladatok I/O-hozzáférési mintáitól függ. Csak a tesztelés mutatja, hogy melyik gyorsítótárazási lehetőség a legjobb ebben a forgatókönyvben.
>
>

Ha azonban több virtuális merevlemezből álló Windows-tárolóhelyeket használ, meg kell néznie az eredeti parancsfájlokat, hogy azonosíthassa, mely csatlakoztatott virtuális merevlemezek vannak az adott készletben, így az egyes lemezek gyorsítótár-beállításait ennek megfelelően állíthatja be.

Ha nem rendelkezik eredeti parancsfájllal, amely megmutatja, hogy mely virtuális merevlemezek képezik le a tárolókészletet, a következő lépésekkel határozhatja meg a lemez-tároló készlet hozzárendelését.

Minden lemezhez kövesse az alábbi lépéseket:

1. A **Get-AzureVM** paranccsal a virtuális géphez csatlakoztatott lemezek listájának leése:

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Vegye figyelembe a DiskName és a LUN.

    ![DisknameAndLUN][2]
1. Távoli asztal a virtuális gépbe. Ezután nyissa meg a **Számítógép-kezelő** | **eszközkezelő** | **lemezmeghajtóit.** Tekintse meg az egyes "Microsoft virtuális lemezek" tulajdonságait

    ![VirtualDiskProperties (VirtualDiskProperties)][3]
1. Az itt megadott lun-szám a virtuális merevlemez virtuális géphez való csatolásakor megadott lun-számra mutató hivatkozás.
1. A "Microsoft virtuális lemez" lapon a **Részletek** lapon, majd a **Tulajdonságlistában** az **Illesztőprogramkulcs**lapon talál. Az **Érték (Érték)** beállításban vegye figyelembe az **Eltolás**t, amely a következő képernyőképen 0002. A 0002 a storage készlet által hivatkozott PhysicalDisk2 rendszert jelöli.

    ![VirtualDiskPropertyDetails][4]
1. Minden tárolókészletesetében ürülje ki a kapcsolódó lemezeket:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Most már használhatja ezt az információt a csatlakoztatott virtuális merevlemezek társítására a tárolókészletek fizikai lemezeihez.

Miután leképezte a virtuális merevlemezeket a storage-készletek fizikai lemezeihez, leválaszthatja és átmásolhatja őket egy prémium szintű storage-fiókba, majd csatolhatja őket a megfelelő gyorsítótár-beállítással. Lásd a [függelék 8–12.](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) Ezek a lépések bemutatják, hogyan bonthatja ki a virtuális géphez csatlakoztatott virtuális merevlemez-konfigurációt egy CSV-fájlba, másolhatja a Virtuális merevlemezeket, módosíthatja a lemezkonfigurációs gyorsítótár beállításait, és végül újratelepítheti a virtuális gép egy DS sorozatú virtuális gépként az összes csatlakoztatott lemezzel.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Virtuális gép tárolási sávszélessége és virtuális merevlemez-tárolási átviteli igénye

A tárolási teljesítmény mennyisége a ds* virtuális gép megadott méretétől és a virtuális merevlemez méretétől függ. A virtuális gépek különböző kibocsátási egységek a virtuális gépek, amelyek csatlakoztathatók, és a maximális sávszélesség et támogatják (MB/s). A konkrét sávszélesség-számokról az [Azure virtuális gép- és felhőszolgáltatás-méretei című témakörben található.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

A nagyobb IOPS-t nagyobb lemezméretekkel érik el. Ezt figyelembe kell vennie, amikor a migrációs útvonalra gondol. További információt [az IOPS és a Lemeztípusok táblázatban talál.](../disks-types.md#premium-ssd)

Végül vegye figyelembe, hogy a virtuális gépek különböző maximális lemezsávszélességgel rendelkeznek, amelyeket az összes csatlakoztatott lemezhez támogatnak. Nagy terhelés alatt telítheti az adott virtuálisgép-szerepkör méretéhez rendelkezésre álló maximális lemezsávszélességet. Például egy Standard_DS14 legfeljebb 512 MB/s-ot támogat; ezért három P30 lemezrel telítheti a virtuális gép lemezsávszélességét. Ebben a példában azonban az átviteli korlát túlléphető az olvasási és írási IOs kombinációjától függően.

## <a name="new-deployments"></a>Új telepítések

A következő két szakasz bemutatja, hogyan telepítheti az SQL Server virtuális gépeket a prémium szintű storage-ba. Mint már említettük, nem feltétlenül kell elhelyezni az operációs rendszer lemezét a Prémium szintű tárolóra. Ezt akkor teheti meg, ha bármilyen intenzív IO-számítási feladatot kíván elhelyezni az operációs rendszer virtuális merevlemezén.

Az első példa bemutatja a meglévő Azure Gallery-képek használatával. A második példa bemutatja, hogyan használhatja az egyéni virtuálisgép-lemezképet, amely egy meglévő standard szintű tárfiókban rendelkezik.

> [!NOTE]
> Ezek a példák feltételezik, hogy már létrehozott egy regionális virtuális hálózatot.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Új virtuális gép létrehozása prémium szintű tárral a Galéria képével

Az alábbi példa bemutatja, hogyan helyezheti el az operációs rendszer virtuális merevlemezét a prémium szintű storage-ra, és hogyan csatolhat prémium szintű storage-alapú virtuális merevlemezeket. Azonban az operációs rendszer lemezét is elhelyezheti egy standard szintű tárfiókban, majd csatolhatja a prémium szintű tárfiókban található Virtuális merevlemezeket. Mindkét forgatókönyv bizonyított.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>1. lépés: Prémium szintű tárfiók létrehozása

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>2. lépés: Új felhőszolgáltatás létrehozása

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>3. lépés: Felhőszolgáltatás VIP lefoglalása (nem kötelező)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>4. lépés: Virtuálisgép-tároló létrehozása

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>5. lépés: Az operációs rendszer virtuális merevlemeze standard vagy prémium szintű tárhelyre helyezése

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>6. lépés: Virtuális gép létrehozása

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
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Hozzon létre egy új virtuális gép használata Premium Storage egyéni lemezkép

Ebben a forgatókönyvben bemutatja, hol rendelkezik a standard tárfiókban található testreszabott lemezképek. Mint már említettük, ha azt szeretné, hogy az operációs rendszer virtuális merevlemezprémium szintű storage-on kell másolni a lemezképet, amely létezik a standard tárfiókban, és át őket egy prémium szintű storage, mielőtt használható. Ha rendelkezik egy helyszíni lemezképpel, ezzel a módszerrel közvetlenül a prémium szintű tárfiókba másolhatja.

#### <a name="step-1-create-storage-account"></a>1. lépés: Tárfiók létrehozása

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>2. lépés Felhőalapú szolgáltatás létrehozása

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>3. lépés: Meglévő kép használata

Meglévő lemezképet is használhat. Vagy egy [meglévő gépet is](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)lefotózhat. Vegye figyelembe, hogy a készülék, hogy a kép nem kell DS * gép. Miután rendelkezik a lemezkép, a következő lépések bemutatják, hogyan másolja a Prémium szintű **storage-fiók a Start-AzureStorageBlobCopy** PowerShell parancskettel.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>4. lépés: Blob másolása tárfiókok között

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>5. lépés: Rendszeresen ellenőrizze a másolás állapotát:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>6. lépés: Lemezlemez hozzáadása az Azure lemeztárházba az előfizetésben

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Előfordulhat, hogy annak ellenére, hogy az állapotjelentések sikeresek, továbbra is lemezbérleti hibát kaphat. Ebben az esetben várjon körülbelül 10 percet.

#### <a name="step-7--build-the-vm"></a>7. lépés: A virtuális gép létrehozása

Itt építi fel a virtuális gép a lemezképből, és csatolja a két prémium szintű storage Virtuálislemez:

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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Meglévő, a Mindig rendelkezésre állási csoportokat nem használó központi telepítések

> [!NOTE]
> A meglévő központi telepítések, először tekintse meg a cikk [Előfeltételek](#prerequisites-for-premium-storage) című szakaszban.

Az SQL Server központi telepítései hez különböző szempontok tartoznak, amelyek nem használják az Always On Availability Groups-t és azokat, amelyek nem. Ha nem használja az Always On alkalmazást, és rendelkezik egy meglévő önálló SQL Server kiszolgálóval, új felhőszolgáltatás és tárfiók használatával frissíthet a Prémium szintű storage-ra. Vegye figyelembe a következő lehetőségeket:

* **Hozzon létre egy új SQL Server virtuális gép**. Létrehozhat egy új SQL Server virtuális gép, amely egy prémium szintű tárfiókot használ, az új telepítések dokumentált. Ezután biztonsági másolatot kell létrehoznia, és visszaállíthatja az SQL Server konfigurációját és a felhasználói adatbázisokat. Az alkalmazást frissíteni kell, hogy az új SQL Serverre hivatkozzon, ha belsővagy külső hozzáféréssel rendelkezik. Az összes "db-ből" objektumot úgy kell másolnia, mintha egymás mellett (SxS) végezne SQL Server-áttelepítést. Ez magában foglalja az olyan objektumokat, mint a bejelentkezések, tanúsítványok és csatolt kiszolgálók.
* **Meglévő SQL Server virtuális gép áttelepítése**. Ehhez az SQL Server virtuális gép offline állapotba helyezését, majd egy új felhőszolgáltatásba való átvitelét, amely magában foglalja az összes csatlakoztatott virtuális gép másolását a prémium szintű tárfiókba. Amikor a virtuális gép online állapotba kerül, az alkalmazás a kiszolgáló állomásnevére hivatkozik, mint korábban. Ne feledje, hogy a meglévő lemez mérete befolyásolja a teljesítmény jellemzőket. Egy 400 GB-os lemez például p20-ra lesz kerekítve. Ha tudja, hogy nincs szüksége a lemez teljesítményére, akkor újra létrehozhatja a virtuális gép egy DS sorozatú virtuális gép, és csatolja a prémium szintű storage virtuális merevlemezek a méret/teljesítmény specifikáció szükséges. Ezután leválaszthatja és újra csatlakoztathatja az SQL DB fájlokat.

> [!NOTE]
> A Virtuális merevlemezek másolásakor tisztában kell lennie a mérettel, a méretétől függően azt jelenti, hogy milyen prémium szintű tárolólemez-típusba tartoznak, ez határozza meg a lemez teljesítményének specifikációját. Az Azure a legközelebbi lemezméretre kerekít, így ha 400 GB-os lemezzel rendelkezik, akkor ez egy P20-ra lesz kerekítve. Az operációs rendszer virtuális merevlemezének meglévő I/O-követelményeitől függően előfordulhat, hogy nem kell áttelepítenie ezt egy prémium szintű storage-fiókba.

Ha az SQL Server külsőleg érhető el, majd a felhőszolgáltatás VIP változások. A végpontokat, az AC-eket és a DNS-beállításokat is frissítenie kell.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>A Mindig rendelkezésre állási csoportokat használó meglévő telepítések

> [!NOTE]
> A meglévő központi telepítések, először tekintse meg a cikk [Előfeltételek](#prerequisites-for-premium-storage) című szakaszban.

Kezdetben ebben a szakaszban azt vizsgáljuk, hogy a Always On hogyan kommunikál az Azure Networking szolgáltatással. Ezután két forgatókönyv szerint bontjuk le az áttelepítéseket: áttelepítések, ahol bizonyos állásidő tolerálható, és az áttelepítések, ahol minimális állásidőt kell elérni.

A helyszíni SQL Server Always On Availability Groups olyan helyszíni figyelőt használ, amely egy virtuális DNS-nevet regisztrál egy vagy több SQL-kiszolgáló között megosztott IP-címmel együtt. Amikor az ügyfelek csatlakoznak, a figyelő IP-címén keresztül az elsődleges SQL Server. Ez az a kiszolgáló, amely az always on IP erőforrás tulajdonosa abban az időben.

![TelepítésekUseAlways On][6]

A Microsoft Azure-ban csak egy IP-cím rendelhető a virtuális gép hálózati adapteréhez, így a helyszíni absztrakciós réteg elérése érdekében az Azure a belső/külső terheléselosztókhoz (ILB/ELB) rendelt IP-címet használja. A kiszolgálók között megosztott IP-erőforrás az ILB/ELB ip-címével megegyező IP-re van beállítva. Ez a DNS-ben van közzétéve, és az ügyfélforgalom az ILB/ELB-n keresztül jut el az elsődleges SQL Server kópiához. Az ILB/ELB tudja, hogy melyik SQL Server az elsődleges, mivel a mintavételek segítségével vizsgálja meg az Always On IP erőforrást. Az előző példában minden olyan csomópontot megvizsgál, amelynek az ELB/ILB által hivatkozott végpontja van, attól függően, hogy melyik válaszol, az az elsődleges SQL Server.

> [!NOTE]
> Az ILB és az ELB egy adott Azure-felhőszolgáltatáshoz vannak rendelve, ezért az Azure-ban történő felhőbevaló migrálás valószínűleg azt jelenti, hogy a terheléselosztó IP-címe megváltozik.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>A Mindig üzemelő telepítések áttelepítése, amelyek némi állásidőt tehetnek lehetővé

Az Always On telepítések áttelepítése két stratégiát is lehetővé teszi, amelyek lehetővé teszik az állásidőt:

1. **További másodlagos replikák hozzáadása meglévő, folyamatosan fürtön lévő kópiákhoz**
2. **Átköltözött egy új, mindig állapotban lévő fürtre**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. További másodlagos replikák hozzáadása egy meglévő, mindig fürtön lévő replikához

Az egyik stratégia az, hogy további másodlagos a Mindig rendelkezésre állás i. Ezeket hozzá kell adnia egy új felhőszolgáltatáshoz, és frissítenie kell a figyelőt az új terheléselosztó IP-címével.

##### <a name="points-of-downtime"></a>Állásidők:

* Fürt érvényesítése.
* Az új másodlagos műveletek feladatátvételi feladatátvételi tesztelése.

Ha a virtuális gépen belüli Windows storage-készleteket használja a nagyobb i/o átviteli kapacitás érdekében, akkor ezek offline állapotba kerülnek a teljes fürtérvényesítés során. Az érvényesítési teszt szükséges, ha csomópontokat ad hozzá a fürthöz. A teszt futtatásához szükséges idő változhat, ezért ezt a reprezentatív tesztkörnyezetben kell tesztelnie, hogy hozzávetőleges időt kapjon arról, hogy ez mennyi időt vesz igénybe.

Az üzembe állási idő, ahol manuális feladatátvételi és chaos tesztelés az újonnan hozzáadott csomópontok biztosítása érdekében mindig a magas rendelkezésre állás i funkciók a várt módon.

![Telepítéshasználatmindig bekapcsolva2][7]

> [!NOTE]
> Le kell állítania az SQL Server minden olyan példányát, ahol a tárolókészleteket használják az érvényesítés futtatása előtt.
>
> ##### <a name="high-level-steps"></a>Magas szintű lépések
>

1. Hozzon létre két új SQL-kiszolgálót az új felhőszolgáltatásban a csatlakoztatott prémium szintű tárterülettel.
2. Másolja át a teljes biztonsági mentéseket, és állítsa vissza a **NORECOVERY**segítségével.
3. Másolja át a "felhasználó DB" függő objektumokat, például bejelentkezéseket stb.
4. Hozzon létre egy új belső terheléselosztót (ILB), vagy használjon külső terheléselosztót (ELB), majd állítsa be a terheléselosztási végpontokat mindkét új csomóponton.

   > [!NOTE]
   > Ellenőrizze, hogy az összes csomópont megfelelő végpont-konfigurációval rendelkezik-e a folytatás előtt
   >
   >
5. Állítsa le a felhasználó/alkalmazás hozzáférést az SQL Server kiszolgálóhoz (tárolókészletek használata esetén).
6. Állítsa le az SQL Server Engine Services szolgáltatást minden csomóponton (tárolókészletek használata esetén).
7. Adjon hozzá új csomópontokat a fürthöz, és futtassa a teljes ellenőrzést.
8. Az érvényesítés sikeres befejezése után indítsa el az összes SQL Server Services szolgáltatást.
9. Tranzakciónaplók biztonsági mentése és felhasználói adatbázisok visszaállítása.
10. Adjon hozzá új csomópontokat a Mindig rendelkezésre állási csoporthoz, és helyezze a **replikációt a Szinkron**csoportba.
11. Adja hozzá az új felhőszolgáltatás ILB/ELB IP-címerőforrását a PowerShellen keresztül az Always On szolgáltatáshoz a [függelékben](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)található Többhelyes példa alapján. A Windows fürtözés, állítsa be a **lehetséges tulajdonosok** az **IP-cím** erőforrás az új csomópontok régi. Lásd a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)"IP-címerőforrás hozzáadása ugyanazon alhálózaton" című részét.
12. Feladatátvétel az új csomópontok egyikének.
13. Az új csomópontok automatikus feladatátvételi partnerek és a teszt feladatátvételek.
14. Az eredeti csomópontok eltávolítása a rendelkezésre állási csoportból.

##### <a name="advantages"></a>Előnyök

* Az új SQL-kiszolgálók tesztelhetők (SQL Server és Application), mielőtt hozzáadnák őket az Always On alkalmazáshoz.
* Módosíthatja a virtuális gép méretét, és testreszabhatja a tárolót a pontos követelményeknek megfelelően. Azonban hasznos lenne, ha az összes SQL fájlelérési utat azonosnak tartaná.
* Szabályozhatja, hogy mikor kezdődjenek el a DB biztonsági mentések átvitele a másodlagos replikákba. Ez eltér az Azure **Start-AzureStorageBlobCopy** parancsmag használatával a VHD-k másolásához, mert ez egy aszinkron másolat.

##### <a name="disadvantages"></a>Hátrányok

* A Windows tárolókészletek használatakor fürtleállás van az új további csomópontok teljes fürtérvényesítése során.
* Az SQL Server-verziótól és a másodlagos replikák meglévő számától függően előfordulhat, hogy a meglévő másodlagos replikák eltávolítása nélkül nem tud további másodlagos replikákat hozzáadni.
* A másodlagos adatok beállítása közben hosszú SQL adatátviteli idő lehet.
* Az áttelepítés során az új gépek párhuzamosan futó további költségek merülnek fel.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Áttelepítés egy új, mindig fürtön lévő fürtre

Egy másik stratégia egy teljesen új Always On Cluster létrehozása vadonatúj csomópontokkal az új felhőszolgáltatásban, majd átirányításazás az ügyfelek számára.

##### <a name="points-of-downtime"></a>Állásidő-pontok

Van állásidő, amikor átaz alkalmazások és a felhasználók az új Mindig a hallgató. Az állásidő a következőktől függ:

* A végső tranzakciós napló biztonsági másolatainak visszaállításához szükséges idő az új kiszolgálókadatbázisaiba.
* Az ügyfélalkalmazások frissítéséhez szükséges idő az új Always On figyelő használatához.

##### <a name="advantages"></a>Előnyök

* Tesztelheti a tényleges éles környezet, az SQL Server és az operációs rendszer build módosításait.
* Lehetősége van a tároló testreszabására és a virtuális gép méretének potenciális csökkentésére. Ez költségcsökkenést eredményezhet.
* A folyamat során frissítheti az SQL Server buildet vagy verziót. Az operációs rendszert is frissítheti.
* Az előző Mindig fürtön szolgáltatás szilárd visszaállítási célként működhet.

##### <a name="disadvantages"></a>Hátrányok

* Ha azt szeretné, hogy mindkét Always On fürt egyszerre fusson, módosítania kell a figyelő DNS-nevét. Ez növeli a felügyeleti többletterhelést az áttelepítés során, mivel az ügyfélalkalmazás-karakterláncok tükrözniük kell az új figyelő nevét.
* A két környezet közötti szinkronizálási mechanizmust kell megvalósítania, hogy azok a lehető legközelebb maradjanak, hogy az áttelepítés előtt minimálisra csökkentsék a végső szinkronizálási követelményeket.
* Az új környezet futtatása közben az áttelepítés során hozzáadott költség áll fenn.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>A Mindig üzemelő példányok áttelepítése minimális állásidő érdekében

A Mindig csak üzemelő példányok minimális állásidő érdekében történő áttelepítésére két stratégia létezik:

1. **Meglévő másodlagos: Egytelephelyes**
2. **Meglévő másodlagos replika(k) hasznosítása: Többtelephely**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Használja ki a meglévő másodlagos: Egy-site

A minimális állásidő egyik stratégiája egy meglévő másodlagos felhő, és távolítsa el azt az aktuális felhőszolgáltatásból. Ezután másolja a Virtuálisgépeket az új Prémium szintű storage-fiókba, és hozza létre a virtuális gép az új felhőszolgáltatásban. Ezután frissítse a figyelőt a fürtözés és a feladatátvétel során.

##### <a name="points-of-downtime"></a>Állásidő-pontok

* Van állásidő, amikor frissíti a végső csomópont ot a terhelés elosztása végpont.
* Az ügyfél/DNS-konfigurációtól függően az ügyfél újracsatlakozása késhet.
* További állásidő áll fenn, ha úgy dönt, hogy az Mindig fürtön csoportot offline állapotba helyezi az IP-címek cseréjéhez. Ezt elkerülheti egy VAGY-függőség és a Lehetséges tulajdonosok használatával a hozzáadott IP-cím erőforráshoz. Lásd a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)"IP-címerőforrás hozzáadása ugyanazon alhálózaton" című részét.

> [!NOTE]
> Ha azt szeretné, hogy a hozzáadott csomópont mindig feladatátvételi partnerként vegyen részt, hozzá kell adnia egy Azure-végpontot a terheléselosztási készletre mutató hivatkozással. Ha ehhez futtatja az **Add-AzureEndpoint** parancsot, a jelenlegi kapcsolatok nyitva maradnak, de a figyelőúj kapcsolatok nem jönnek létre, amíg a terheléselosztó nem frissül. A tesztelés során ez volt látható, hogy az utolsó 90-120seconds, ezt meg kell vizsgálni.

##### <a name="advantages"></a>Előnyök

* Nincs többletköltség az áttelepítés során.
* Egy-az-egyhez áttelepítés.
* Csökkentett komplexitás.
* Lehetővé teszi a prémium szintű storage-skus-ok ból származó nagyobb IOPS.For increased IOPS from Premium Storage SK. Amikor a lemezek levannak választva a virtuális gépről, és átmásolják őket az új felhőszolgáltatásba, egy harmadik féltől származó eszköz használható a virtuális merevlemez méretének növelésére, amely nagyobb átviteli kihívásokat biztosít. A növekvő VHD mérete, lásd ezt a [fórumot vita](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Hátrányok

* A HA és a DR átmeneti elvesztése a migráció során.
* Mivel ez egy 1:1-es áttelepítés, egy minimális virtuális gépméretet kell használnia, amely támogatja a virtuális gépek számát, így előfordulhat, hogy nem tudja csökkenteni a virtuális gépeket.
* Ebben a forgatókönyvben az Azure **Start-AzureStorageBlobCopy** parancsletet, amely aszinkron. A másolás befejezését követően nincs SLA. A másolatok ideje változó, míg ez a várólistán való várakozástól függ, az átvinni a továbbítandó adatok mennyiségétől is függ. A másolási idő növekszik, ha az átvitel egy másik Azure-adatközpontba kerül, amely támogatja a prémium szintű storage-ot egy másik régióban. Ha csak 2 csomópont, fontolja meg egy lehetséges megoldás esetén a másolat hosszabb időt vesz igénybe, mint a tesztelés. Ez a következő ötleteket foglalhatja magában.
  * Adjon hozzá egy ideiglenes 3rd SQL Server csomópontot a HA-hoz az áttelepítés előtt a megállapodás szerinti állásidővel.
  * Futtassa az áttelepítést az Azure ütemezett karbantartásán kívül.
  * Ellenőrizze, hogy megfelelően konfigurálta-e a fürt kvórumát.  

##### <a name="high-level-steps"></a>Magas szintű lépések

Ez a dokumentum nem mutatja be a teljes körű példa, de a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) olyan részleteket, amelyek felhasználhatók ennek végrehajtására.

![Minimális leállási idő][8]

* Gyűjtse össze a lemezkonfigurációt, és távolítsa el a csomópontot (ne törölje a csatlakoztatott Virtuális merevlemezeket).
* Prémium szintű tárfiók létrehozása és virtuális gépek másolása a Standard storage-fiókból
* Hozzon létre új felhőszolgáltatást, és telepítse újra az SQL2 virtuális gép, hogy a felhőszolgáltatás. Hozza létre a virtuális gép a másolt eredeti operációsrendszer-virtuális merevlemez használatával, és csatolja a másolt Virtuális merevlemezek.
* Konfigurálja az ILB / ELB értéket, és adja hozzá a végpontokat.
* A Figyelő frissítése: vagy:
  * Az Always On Group offline állapotba helyezése és az Always On Listener frissítése új ILB / ELB IP-címmel.
  * Vagy az új Felhőszolgáltatás ILB/ELB IP-címerőforrásának hozzáadása a PowerShellen keresztül a Windows fürtözéséhez. Ezután állítsa az IP-cím erőforrás lehetséges tulajdonosait az áttelepített csomópontra, az SQL2-re, és állítsa be vagy függőségként a Hálózatnév ben. Lásd a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)"IP-címerőforrás hozzáadása ugyanazon alhálózaton" című részét.
* Ellenőrizze a DNS-konfigurációt/propagálást az ügyfeleknek.
* Sql1 virtuális gép áttelepítése, és végig a 2–4.
* Az 5ii lépés ekkel, majd adja hozzá az SQL1-et a hozzáadott IP-címerőforrás lehetséges tulajdonosaként
* Feladatátvételek tesztelése.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Meglévő másodlagos replika(k) hasznosítása: Többtelephely

Ha több Azure-adatközpontban (DC) rendelkezik csomópontokkal, vagy hibrid környezettel rendelkezik, akkor ebben a környezetben használhatja az Always On konfigurációt az állásidő minimalizálásához.

A megközelítés az, hogy módosítsa a mindig szinkronban a szinkron a helyszíni vagy másodlagos Azure DC, majd feladatátvételt, hogy az SQL Server. Ezután másolja a Virtuálisgépeket egy prémium szintű storage-fiókba, és telepítse újra a számítógépet egy új felhőszolgáltatásba. Frissítse a figyelőt, majd tegye vissza a feladatát.

##### <a name="points-of-downtime"></a>Állásidő-pontok

Az állásidő az alternatív tartományvezérlőés vissza feladatátvételi időből áll. Ez az ügyfél/DNS-konfigurációtól is függ, és az ügyfél újracsatlakozása késhet.
Tekintsük a következő példát a hibrid Always On konfigurációra:

![Többhelyes oldal1][9]

##### <a name="advantages"></a>Előnyök

* Használhatja a meglévő infrastruktúrát.
* Lehetősége van először frissíteni az Azure storage-t a DR Azure DC-n.
* A DR Azure DC-tároló újrakonfigurálható.
* Legalább két feladatátvétel van az áttelepítés során, kivéve a tesztfeladat-átvételeket.
* Az SQL Server-adatokat nem kell biztonsági másolattal és visszaállítással áthelyezni.

##### <a name="disadvantages"></a>Hátrányok

* Az SQL Server hez való ügyfélhozzáféréstől függően előfordulhat, hogy nagyobb a késés, ha az SQL Server az alkalmazás alternatív tartományvezérlőn fut.
* A virtuális gépek prémium szintű tárhelyre történő másolási ideje hosszú lehet. Ez hatással lehet a döntés, hogy tartsa a csomópont ot a rendelkezésre állási csoportban. Vegye figyelembe ezt, ha az áttelepítés során naplóigényes munkaterhelések futnak, mivel az elsődleges csomópontnak meg kell őriznie a nem replikált tranzakciókat a tranzakciónaplóban. Ezért ez jelentősen növekedhet.
* Ebben a forgatókönyvben az Azure **Start-AzureStorageBlobCopy** parancsletet, amely aszinkron. Nincs SLA a befejezéskor. A másolatok ideje változó, míg ez a várólistán való várakozástól függ, az átvinni átvihető adatok mennyiségétől is függ. Ezért csak egy csomópont van a 2. Ezek a kockázatcsökkentési lépések a következő ötleteket tartalmazzák:
  * Adjon hozzá egy ideiglenes 2nd SQL csomópontot a HA-hoz az áttelepítés előtt a megállapodás szerinti állásidővel.
  * Futtassa az áttelepítést az Azure ütemezett karbantartásán kívül.
  * Ellenőrizze, hogy megfelelően konfigurálta-e a fürt kvórumát.

Ez a forgatókönyv feltételezi, hogy dokumentálta a telepítést, és tudja, hogyan van leképezve a tároló az optimális lemezgyorsítótár-beállítások módosítása érdekében.

##### <a name="high-level-steps"></a>Magas szintű lépések

![Többhelyes2][10]

* Tegye a helyszíni / alternatív Azure DC az SQL Server elsődleges, és ez a másik automatikus feladatátvételi partner (AFP).
* Gyűjtse össze a lemez konfigurációs adatait az SQL2-ből, és távolítsa el a csomópontot (ne törölje a csatlakoztatott virtuális merevlemezeket).
* Hozzon létre egy prémium szintű storage-fiókot, és másolja a VHD-k a standard tárfiókból.
* Hozzon létre egy új felhőszolgáltatást, és hozza létre az SQL2 virtuális gép a prémium szintű storage-lemezek csatlakoztatva.
* Konfigurálja az ILB / ELB értéket, és adja hozzá a végpontokat.
* Frissítse az Always On Listener új ILB / ELB IP-címet és a teszt feladatátvételt.
* Ellenőrizze a DNS-konfigurációt.
* Módosítsa az AFP-t SQL2-re, majd telepítse át az SQL1-et, és menjen végig a 2–5.
* Feladatátvételek tesztelése.
* Az AFP visszakapcsolása SQL1-re és SQL2-re

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Függelék: Több helyen lévő fürt áttelepítése prémium szintű tárolóba

A cikk további részében részletes példa egy többhelyen lévő fürt öthelyen lévő fürt prémium szintű tárolóvá konvertálására. Azt is átalakítja a figyelő egy külső terheléselosztó (ELB) egy belső terheléselosztó (ILB).

### <a name="environment"></a>Környezet

* Windows 2k12 / SQL 2k12
* 1 DB fájlok SP
* Csomópontonként 2 db tárolókészlet

![1. függelék][11]

### <a name="vm"></a>Vm:

Ebben a példában bemutatjuk az ELB-ről az ILB-re való áttérést. Az ELB az ILB előtt volt elérhető, így ez megmutatja, hogyan lehet az ILB-re váltani az áttelepítés során.

![2. függelék][12]

### <a name="pre-steps-connect-to-subscription"></a>Előre lép: Csatlakozás az előfizetéshez

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>1. lépés: Új tárfiók és felhőszolgáltatás létrehozása

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>2. lépés: Az erőforrások \<engedélyezett hibáinak növelése Nem kötelező>

A Mindig rendelkezésre állás csoporthoz tartozó bizonyos erőforrásokon korlátozva van, hogy hány hiba fordulhat elő egy adott időszakban, amikor a fürtszolgáltatás megpróbálja újraindítani az erőforráscsoportot. Javasoljuk, hogy növelje ezt, miközben végighalad ezen az eljáráson, mivel ha nem manuálisan feladatátvételt hajt végre, és a gépek leállításával elindítja a feladatátvételt, akkor megközelítheti ezt a korlátot.

Célszerű lenne megduplázni a hibaengedményt, ehhez a Feladatátvevői fürtkezelőben, lépjen a Mindig bekapcsolva erőforráscsoport tulajdonságai hoz:

![3. függelék][13]

Módosítsa a maximális hibákat 6-ra.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>3. lépés: IP-cím \<erőforrás hozzáadása a fürtcsoport nem kötelező>

Ha csak egy IP-címet a fürtcsoport, és ez igazodik a felhő alhálózat, vigyázz, ha véletlenül offline az összes fürtcsomópontok a felhőben, hogy a hálózat, akkor a fürt IP-erőforrás és a fürt hálózati neve nem tud nak online. Ebben az esetben megakadályozza a más fürterőforrások frissítését.

#### <a name="step-4-dns-configuration"></a>4. lépés: DNS-konfiguráció

A zökkenőmentes átmenet megvalósítása a ttól függ, hogy a DNS-t hogyan használják és frissítik.
Ha az Always On telepítve van, létrehoz egy Windows fürterőforrás-csoportot, ha megnyitja a Feladatátvevőfürt-kezelőt, láthatja, hogy legalább három erőforrással rendelkezik, amelyek közül a dokumentum a következő:

* Virtuális hálózat neve (VNN) – az a DNS-név, amelyhez az ügyfelek csatlakoznak, amikor az Always On szolgáltatáson keresztül szeretnének csatlakozni az SQL-kiszolgálókhoz.
* IP-cím erőforrás – az IP-cím, amely a VNN társított, akkor több, és egy többhelyes konfigurációban van egy IP-címet egy hely/ alhálózat.

Az SQL Server kiszolgálóhoz való csatlakozáskor az SQL Server ügyfélillesztőprogram lekéri a figyelőhöz társított DNS-rekordokat, és megpróbál csatlakozni az egyes Mindig bekapcsolt IP-címhez. Ezután megbeszélünk néhány tényezőt, amelyek befolyásolhatják ezt.

A figyelő nevéhez társított egyidejű DNS-rekordok száma nem csak a társított IP-címek számától függ, hanem a Mindig ON VNN erőforrás "RegisterAllIpProviders" beállításátis.

Ha telepíti mindig az Azure-ban vannak különböző lépések a figyelő és az IP-címek létrehozásához, manuálisan kell konfigurálnia a "RegisterAllIpProviders" 1, ez eltér a helyszíni Mindig üzembe helyezéskor, ahol már 1-re van állítva.

Ha a "RegisterAllIpProviders" 0, akkor csak egy DNS-rekord jelenik meg a Figyelőhöz társított DNS-ben:

![4. függelék][14]

Ha a "RegisterAllIpProviders" 1:

![5. függelék][15]

Az alábbi kód kidobja a VNN-beállításokat, és beállítja az Ön számára. Ahhoz, hogy a módosítás érvénybe lépjen, a VNN-t offline állapotba kell helyeznie, és újra online állapotba kell kapcsolnia. Ez a figyelő offline állapotba kerülése miatt az ügyfél-kapcsolat megszakadását.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Egy későbbi áttelepítési lépésben frissítenie kell a Mindig figyelőt egy frissített IP-címmel, amely egy terheléselosztóra hivatkozik, ez magában foglalja az IP-cím erőforrás eltávolítását és hozzáadását. Az IP-frissítés után meg kell győződnie arról, hogy az új IP-cím frissült a DNS-zónában, és hogy az ügyfelek frissítik a helyi DNS-gyorsítótárat.

Ha az ügyfelek egy másik hálózati szegmensben tartózkodnak, és egy másik DNS-kiszolgálóra hivatkoznak, meg kell fontolnia, hogy mi történik a DNS-zónaletöltéssel az áttelepítés során, mivel az alkalmazás újrakapcsolódási idejét legalább az új IP zónaátviteli ideje korlátozza. a figyelő címét. Ha itt időkorlátozás alatt áll, meg kell beszélnie és tesztelnie kell a növekményes zónaletöltést a Windows-csapatokkal, és a DNS-állomásrekordot alacsonyabb Time To Live (TTL) értékre kell helyeznie, hogy az ügyfelek frissülhessenek. További információ: [Incremental Zone Transfers](https://technet.microsoft.com/library/cc958973.aspx) and [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Alapértelmezés szerint a TTL dns-rekord, amely társítva van a figyelő az Azure-ban mindig 1200 másodperc. Ezt csökkentheti, ha az áttelepítés során időkorlátozás alatt áll, hogy az ügyfelek frissítsék a DNS-t a figyelő frissített IP-címével. A konfigurációt a VNN konfigurációjának kirakásával láthatja és módosíthatja:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Minél alacsonyabb a "HostRecordTTL", annál nagyobb mennyiségű DNS-forgalom történik.

##### <a name="client-application-settings"></a>Ügyfélalkalmazás-beállítások

Ha az SQL-ügyfélalkalmazás támogatja a .NET 4.5 SQLClient programot, akkor használhatja a "MULTISUBNETFAILOVER=TRUE" kulcsszót. Ezt a kulcsszót kell alkalmazni, mert lehetővé teszi a gyorsabb kapcsolatot az SQL Always On Availability Group feladatátvétel során. Az Always On figyelőhöz kapcsolódó összes IP-címet párhuzamosan számba veszi, és egy feladatátvétel során agresszívebb TCP-kapcsolat-újrapróbálkozási sebességet hajt végre.

Az előző beállításokról további információt a [MultiSubnetFailover kulcsszó és a kapcsolódó funkciók című témakörben talál.](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover) Lásd még: [SqlClient support for high availability, Disaster Recovery](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>5. lépés: Fürtkvórum beállításai

Mivel egyszerre legalább egy SQL Servert vesz ki, módosítania kell a fürt kvórumbeállítását, ha két csomóponton használja a File Share Witness (FSW) szolgáltatást, állítsa be a kvórumot a csomópont többségének engedélyezéséhez és a dinamikus szavazás használatához. , amely lehetővé teszi, hogy egyetlen csomópont állva maradjon.

```powershell
Set-ClusterQuorum -NodeMajority  
```

A fürtkvórum kezeléséről és konfigurálásáról a [Kvórum konfigurálása és kezelése Windows Server 2012 feladatátvevő fürtben](https://technet.microsoft.com/library/jj612870.aspx)című témakörben talál további információt.

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>6. lépés: Meglévő végpontok és acl-ok kinyerése

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Mentse a szöveget egy fájlba.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>7. lépés: Feladatátvevő partnerek és replikációs módok módosítása

Ha kettőnél több SQL-kiszolgálóval rendelkezik, akkor egy másik tartományvezérlőn vagy a helyszínen lévő másik másodlagos kiszolgáló feladatátvételét "Szinkron" értékre kell módosítania, és automatikus feladatátvételi partnerré (AFP) kell rendelkeznie, így a HA karbantartása a módosítások végrehajtása közben történik. Ezt megteheti a TSQL-en keresztül a módosításbár SSMS esetén:

![Függelék][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>8. lépés: Másodlagos virtuális gép eltávolítása a felhőszolgáltatásból

Először egy másodlagos felhőcsomópont áttelepítését kell megterveznie. Ha ez a csomópont jelenleg elsődleges, manuális feladatátvételt kell kezdeményeznie.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>9. lépés: Lemezgyorsítótárazási beállításainak módosítása a CSV-fájlban és mentés

Adatkötetek esetén ezeket READONLY-ra kell állítani.

TLOG kötetek esetén ezeket NEM-re kell állítani.

![7. függelék][17]

#### <a name="step-10-copy-vhds"></a>10. lépés: VHDs másolása

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


Ellenőrizheti a Virtuálisgépek másolási állapotát a prémium szintű tárfiókba:

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

![8. függelék][18]

Várja meg, amíg ezek a rögzítés sikeres.

Az egyes blobok esetén további információ:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>11. lépés: Operációs rendszer lemezeinek regisztrálása

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>12. lépés: Másodlagos importálás új felhőszolgáltatásba

Az alábbi kód is használja a hozzáadott opcióitt importálhatja a gépet, és használja a megőrzött VIP.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>13. lépés: ILB létrehozása az új felhős Svc-en, terheléselosztásos végpontok és ACL-k hozzáadása

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

#### <a name="step-14-update-always-on"></a>14. lépés: A frissítés mindig be van kapcsolva

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

![9. függelék][19]

Most távolítsa el a régi felhőszolgáltatás IP-címét.

![függelék10][20]

#### <a name="step-15-dns-update-check"></a>15. lépés: A DNS-frissítések ellenőrzése

Most ellenőrizze a DNS-kiszolgálókat az SQL Server ügyfélhálózatokon, és győződjön meg arról, hogy a fürtözés hozzáadta a hozzáadott IP-cím hez tartozó további állomásrekordot. Ha ezek a DNS-kiszolgálók nem frissültek, fontolja meg a DNS-zónaletöltés kényszerítését, és győződjön meg arról, hogy az alhálózatban lévő ügyfelek mindkét mindig ip-címre képesek feloldani, ez azért van, mert nem kell megvárnia az automatikus DNS-replikációt.

#### <a name="step-16-reconfigure-always-on"></a>16. lépés: A mindig bekapcsolt állapot újrakonfigurálása

Ezen a ponton megkell várnia a másodlagos, hogy az áttelepített csomópont teljes mértékben újraszinkronizálja a helyszíni csomóponttal, és átvált sszinkron replikációs csomópontra, és tegye az AFP-vé.  

#### <a name="step-17-migrate-second-node"></a>17. lépés: Második csomópont áttelepítése

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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>18. lépés: Lemezgyorsítótárazási beállításainak módosítása a CSV-fájlban és mentés

Adatkötetek esetén a gyorsítótár beállításait READONLY -ra kell állítani.

TLOG kötetek esetén a gyorsítótár beállításait NINCS-ra kell állítani.

![11. függelék][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>19. lépés: Új független tárfiók létrehozása a másodlagos csomóponthoz

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

#### <a name="step-20-copy-vhds"></a>20. lépés: VHDs másolása

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

Az összes Virtuális merevlemez vHD-jének állapotát ellenőrizheti:

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

![Függelék12][22]

Várja meg, amíg ezek a rögzítés sikeres.

Az egyes blobok esetén további információ:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>21. lépés: Operációs rendszer lemezeinek regisztrálása

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>22. lépés: Terheléselosztásos végpontok és AC-k hozzáadása

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

#### <a name="step-23-test-failover"></a>23. lépés: Feladatátvétel tesztje

Várja meg, amíg az áttelepített csomópont szinkronizálja a helyszíni Always On csomópontot. Helyezze szinkron replikációs módba, és várja meg, amíg szinkronizálódik. Ezután a feladatátvétel a telephelyről az első csomópontra, az AFP-re. Miután ez működött, módosítsa az utolsó áttelepített csomópontot az AFP-re.

Tesztelje a feladatátvételeket az összes csomópont között, és futtassa a chaos tesztek en keresztül, hogy a feladatátvételek a várt módon és időben működjenek.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>24. lépés: A fürt kvórumbeállításainak visszavétele / DNS TTL / Feladatátvételi pntrs / Szinkronizálási beállítások

##### <a name="adding-ip-address-resource-on-same-subnet"></a>IP-címerőforrás hozzáadása ugyanazon alhálózaton

Ha csak két SQL-kiszolgálóval rendelkezik, és át szeretné telepíteni őket egy új felhőszolgáltatásba, de ugyanazt az alhálózaton szeretné tartani őket, elkerülheti, hogy a figyelő offline állapotba kerüljön az eredeti Mindig IP-cím törléséhez és az új IP-cím hozzáadásához. Ha a virtuális gépeket egy másik alhálózatra telepíti át, ezt nem kell megtennie, mivel van egy további fürthálózat, amely az alhálózatra hivatkozik.

Miután hozta létre az áttelepített másodlagos és hozzá adta az új IP-cím erőforrás az új felhőszolgáltatás, mielőtt feladatátvétel a meglévő elsődleges, meg kell tennie ezeket a lépéseket a fürt feladatátvételi kezelő:

Az IP-cím hozzáadásához lásd a függelék 14.

1. Az aktuális IP-cím erőforrás esetében módosítsa a lehetséges tulajdonost "Meglévő elsődleges SQL Server" állapotra, a példában a "dansqlams4" :

    ![Függelék13][23]
2. Az új IP-cím erőforrás esetében módosítsa a lehetséges tulajdonost "Migrated secondary SQL Server" (Áttelepített másodlagos SQL Server' névre, a példában a "dansqlams5" példában:

    ![Függelék14][24]
3. Ha ez be van állítva, feladatátvételt, és amikor az utolsó csomópont áttelepítése a lehetséges tulajdonosok kell szerkesztett, hogy a csomópont hozzáadott lehetséges tulajdonos:

    ![Függelék15][25]

## <a name="additional-resources"></a>További források

* [Azure prémium szintű tárhely](../disks-types.md)
* [Virtuális gépek](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server az Azure virtuális gépekben](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
