---
title: Az Azure Premium Storage használata az SQL Server |} A Microsoft Docs
description: Ez a cikk a klasszikus üzemi modellel létrehozott erőforrásokat használja, és az Azure Premium Storage használata az Azure Virtual machines szolgáltatásban futó SQL Server útmutatást biztosít.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3b3bb206286629a68c14b6444f3f88ffa0af50dd
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540873"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Az Azure Premium Storage és az SQL Server együttes használata virtuális gépeken

## <a name="overview"></a>Áttekintés

[Az Azure prémium szintű SSD-k](../disks-types.md) tárolási megoldás, amely kis késést és nagy átviteli sebességű i/o biztosít következő generációja. Kulcs i/o-igényű számítási feladatokhoz, például az SQL Server IaaS-on leginkább [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk tervezési és a prémium szintű Storage használata az SQL Server rendszerű virtuális gép történő áttelepítéséhez. Ez magában foglalja az Azure-infrastruktúra (hálózati, tárolási) és a vendég Windows virtuális gép lépéseket. Példa a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) nagyobb helyi SSD-tárkapacitáson a PowerShell-lel előnyeinek kihasználása érdekében nagyobb virtuális gépek áthelyezése egy teljes átfogó végpontok közötti migrálásának jeleníti meg.

Fontos megérteni kezelésénél Azure Premium Storage, SQL Server IAAS virtuális gépek esetében a végpontok közötti folyamatán. Az érintett műveletek közé tartoznak az alábbiak:

* A Premium Storage használatának előfeltételei azonosítása.
* Példák az SQL Server IaaS-on üzembe helyezése a Premium Storage az új üzembe helyezésekhez.
* Példák áttelepítése telepítéseit, önálló kiszolgálók és a központi telepítések SQL Always On rendelkezésre állási csoportok használatával.
* Lehetséges migrációs megközelítéseket.
* Teljes, végpontok közötti például, ha mindig a meglévő implementációjával Azure, a Windows és az SQL Server lépéseket ismertető.

További háttér-információkért az SQL Server Azure Virtual Machines szolgáltatásban találhat [SQL Server az Azure Virtual machines gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Szerző:** Daniel Sol **műszaki véleményezők:** A Luis Carlos Vargas hering, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Prémium szintű tárolás előfeltételei

Nincsenek több előfeltételei a Premium Storage tárolást használ.

### <a name="machine-size"></a>Gép mérete

A Premium Storage tárolást használ kell használnia a DS sorozatú virtuális gépek (VM). Ha még nem használta a felhőszolgáltatásban, mielőtt a DS sorozatú gépek, törölje a meglévő virtuális Gépet, tartsa a csatlakoztatott lemezeket, és előtt újra létre kellene hoznia a virtuális gép szerepkörméret DS *, majd új felhőszolgáltatás hozható létre. További információ a virtuálisgép-méretek: [virtuálisgép és Felhőszolgáltatás-méretek az Azure-ban](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Felhőszolgáltatások

Csak használhatja virtuális gépek DS * a Premium Storage egy új felhőszolgáltatást a létrehozásakor. Ha az Azure-beli SQL Server Always On használ, az mindig a figyelő egy felhőszolgáltatáshoz társított Azure belső vagy külső terheléselosztó IP-címre hivatkozik. Ez a cikk foglalkozik, hogyan telepítheti át a rendszer rendelkezésre állását ebben a forgatókönyvben a.

> [!NOTE]
> Egy DS * sorozat kell lennie az első virtuális gép, amelyet az új felhőalapú szolgáltatás.
>
>

### <a name="regional-vnets"></a>Regionális virtuális hálózatok

A virtuális gépek DS * konfigurálnia kell a virtuális hálózat (VNET) regionális kell a virtuális gépeket üzemeltet. Ez "szélesebb formája" a virtuális hálózathoz való csatlakozást, a nagyobb méretű virtuális gépeket lehet más fürtökön üzembe helyezett, és engedélyezi a kommunikációt közöttük. Az alábbi képernyőképen a kijelölt hely regionális virtuális hálózatok, látható, mivel az első eredményt jeleníti meg a "keskeny" virtuális hálózathoz.

![RegionalVNET][1]

A Microsoft támogatási jegy áttelepíteni egy regionális vnetre is növelheti. A Microsoft majd változtatást hajt végre. Az áttelepítéshez a regionális virtuális hálózatok, a hálózati konfigurációban AffinityGroup tulajdonsága módosítható. Először exportálja a PowerShell a hálózati konfigurációt, és cserélje a **AffinityGroup** tulajdonságot a **VirtualNetworkSite** elemet egy **hely** tulajdonság. Adja meg `Location = XXXX` ahol `XXXX` egy Azure-régióban van. Ezután importálja az új konfigurációt.

Ha például a mérlegeli a következő VNET-konfiguráció:

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

Szeretné áthelyezni, ez egy regionális vnetre Nyugat-Európában, a konfiguráció módosítása a következőhöz:
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

Hozzon létre egy új tárfiókot, amely konfigurálva van a Premium Storage kell. Figyelje meg, hogy prémium szintű tárolás használatát, nem az egyes virtuális merevlemezek, a storage-fiók beállított azonban DS * sorozatú virtuális gépek használatakor a prémium és standard szintű Storage-fiókok is csatlakoztatható VHD-k. Ezt érdemes megfontolni, ha nem szeretné, hogy az operációs rendszer virtuális merevlemezt a Premium Storage-fiók be.

A következő **New-AzureStorageAccountPowerShell** parancsot a "Premium_LRS" **típus** hoz létre a Premium Storage-fiók:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>VHDs Cache Settings

A fő különbség a lemezeket, amelyek egy prémium szintű Storage-fiók létrehozása az lemez gyorsítótár beállítása. Használata ajánlott az SQL Server-adatok mennyiségi lemezek, "**olvasási gyorsítótárazás**". A tranzakció naplózási köteteket, a lemez gyorsítótár-beállítást kell megadni '**None**". Ez különbözik a standard szintű tárfiókok kapcsolatos ajánlások.

A csatolást a virtuális merevlemezeket kell lett, a gyorsítótár-beállítása nem módosítható. Válassza le és csatlakoztassa újra a virtuális Merevlemezt egy frissített gyorsítótár beállítással kell.

### <a name="windows-storage-spaces"></a>Windows tárolóhelyek

Használhat [Windows tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) ugyanúgy korábbi a standard szintű Storage szolgáltatással, ez lehetővé teszi, hogy már van felhasználásával a tárolóhelyek virtuális gép áttelepítése. A példában a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (9, és a továbbítási. lépés) bemutatja a Powershell-kóddal kibontása és több virtuális merevlemezekkel rendelkező virtuális gép importálása.

Tárolókészletek használták a standard szintű Azure storage-fiókot az átviteli sebesség növelése és a késés csökkentése érdekében. Tesztelés a Tárolókészletek a Premium Storage segítségével az új központi telepítéseknél érték találhatja, de adnak hozzá a tárolás beállítása a további összetettséget.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Melyik Azure virtuális lemezek térkép a tárolókészletekhez megkeresése

Mivel különböző gyorsítótár beállítás javaslatok csatlakoztatott virtuális merevlemezek, dönthet, hogy másolja a VHD-k egy prémium szintű Storage-fiókot. Azonban őket az új DS sorozatú virtuális gép, fontos, amikor szüksége lehet a gyorsítótár beállításainak módosítását. Sokkal egyszerűbb, a alkalmazni a prémium szintű Storage, ajánlott a gyorsítótár beállításai, ha külön virtuális merevlemezek az SQL-adatfájlokat és a naplófájlok (nem pedig egy virtuális Merevlemezzel, amely egyaránt tartalmaz).

> [!NOTE]
> Ha az SQL Server adathoz és naplófájlhoz ugyanazon a köteten, a gyorsítótárazási lehetőséget is választja függ az adatbázis-munkaterhelés i/o-adathozzáférési mintáknak megfelelően. Csak tesztelési is bemutatják, milyen gyorsítótárazási lehetőség akkor a legjobb ehhez a forgatókönyvhöz.
>
>

Azonban ha használ több VHD-k, meg kell vizsgálni, melyek Windows tárolóhelyek az eredeti parancsfájlok azonosításához, amelyek csatolt VHD-k olyan milyen konkrét készlethez, így Ezután beállíthatja a gyorsítótár beállításait ennek megfelelően az egyes lemezek.

Ha nem rendelkezik az eredeti parancsfájl megjelenítése, amely a VHD-k hozzárendelése a tárolókészlet számára elérhető, a következő lépések segítségével határozza meg a lemez/tárolóleképezés készlet.

Az egyes lemezek kövesse az alábbi lépéseket:

1. A virtuális Géphez csatolt lemezek listájának beolvasása a **Get-AzureVM** parancsot:

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Vegye figyelembe a DiskName és LUN-t.

    ![DisknameAndLUN][2]
1. Távoli asztal a virtuális Gépen. Ezután lépjen a **számítógép-kezelés** | **Eszközkezelő** | **lemezmeghajtók**. Tekintse meg az egyes "Microsoft virtuális lemez" tulajdonságait

    ![VirtualDiskProperties][3]
1. Itt a LUN számot eszköztáblára mutató hivatkozás a LUN számot, adja meg a virtuális merevlemez csatlakoztatása a virtuális géphez.
1. Esetében a "Microsoft virtuális lemez" Ugrás a **részletek** lapon ezt a a **tulajdonság** lista, lépjen a **illesztőprogram kulcs**. Az a **érték**, vegye figyelembe a **eltolás**, 0002, az alábbi képernyőképen ez. A 0002 azt jelzi, hogy a Fizikailemez2, amely a tárolókészlet hivatkozik.

    ![VirtualDiskPropertyDetails][4]
1. Minden egyes tárolókészlethez kiírása a tartozó lemezek ki:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Most már használhatja ezt az információt társítása VHD-k csatolt fizikai lemezek a Tárolókészletekhez.

A tárolókészlet fizikai lemezek VHD-k leképezése után, majd is leválasztása és másolása egy prémium szintű Storage-fiókot, majd csatolja őket a megfelelő gyorsítótárba beállítással. A példa a a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), 8 – 12 lépést. Ezek a lépések bemutatják, hogyan lehet egy virtuális Géphez csatlakoztatott virtuális merevlemez lemez konfigurációját a CSV-fájlba csomagolja ki, másolja a VHD-k, az alter lemezkonfigurációs gyorsítótár beállításait és végül a csatlakoztatott adatlemezekkel rendelkező DS sorozat virtuális gép ismételt üzembe helyezése a virtuális gép.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Virtuális gép tárolási sávszélességet és a VHD-tároló átviteli sebesség

A tárolási teljesítmény függ megadott DS * Virtuálisgép-méret és a virtuális merevlemez méretét. A virtuális gépek rendelkeznek, csatolt VHD-k számát a különböző kedvezmények és a maximális sávszélesség (MB/s) támogatják. Az adott sávszélesség számok, lásd: [virtuálisgép és Felhőszolgáltatás-méretek az Azure-ban](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Nagyobb IOPS-nagyobb lemezméretek érhetők el. Vegye figyelembe ennek során, gondolja át a migrálásához. További információkért [lásd a táblázatot az IOPS és a lemeztípusok](../disks-types.md#premium-ssd).

Végül vegye figyelembe, hogy a virtuális gépek rendelkeznek, amelyek támogatják a csatlakoztatott lemezek különböző lemezek maximális sávszélessége. Nagy terhelés, sikerült telítsük a lemezek maximális sávszélesség érhető el a virtuális gépi szerepkör mérete. Például egy Standard_DS14 támogatja az akár 512 MB/s; a három P30 lemez, ezért a lemez sávszélességet a virtuális gép telítsük sikerült. Azonban ebben a példában az átviteli sebességhatár sikerült túllépte az olvasási és írási IOs vegyesen függően.

## <a name="new-deployments"></a>Új központi telepítéseknél

A következő két szakasz mutatja be, hogyan helyezhet SQL Server virtuális gépek Premium Storage. Ahogy korábban említettük, akkor nem feltétlenül kell az operációsrendszer-lemez prémium szintű storage-kiszolgálóra helyezi. Ha Ön a szándékos volt bármilyen intenzív i/o-munkaterhelések helyezni, a rendszer virtuális Merevlemeze ehhez választhatja.

Az első példa bemutatja, hogy meglévő Azure-katalógusbeli lemezképek használatával. A második példa bemutatja egy egyéni Virtuálisgép-rendszerképet, amely rendelkezik egy meglévő standard szintű tárfiókot használja.

> [!NOTE]
> Ezek a példák feltételezik, hogy már létrehozott regionális virtuális hálózat.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>A katalógus-lemezkép a Premium Storage egy új virtuális gép létrehozása

Az alábbi példa bemutatja, hogyan helyezze el a rendszert tartalmazó virtuális Merevlemezt a premium storage-kiszolgálóra, majd csatolja a Premium Storage virtuális merevlemezeket. Azonban a standard szintű tárfiók helyezze az operációsrendszer-lemez, és majd csatolja a VHD-k a prémium szintű Storage-fiókban található. Mindkét forgatókönyveket mutatjuk be.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>1. lépés: Prémium szintű Storage-fiók létrehozása

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>2. lépés: Új Felhőszolgáltatás hozható létre

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>3. lépés: Foglaljon le egy felhőalapú virtuális IP-címe (nem kötelező)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>4. lépés: Egy Virtuálisgép-tároló létrehozása

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>5. lépés: Helyezi el a rendszert tartalmazó virtuális Merevlemezt a Standard vagy prémium szintű Storage

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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Hozzon létre egy új virtuális Gépet a prémium szintű Storage használata egyéni rendszerképpel

Ebben a forgatókönyvben azt mutatja be, amelyekben meglévő testre szabott lemezképek, a standard szintű tárfiók található. Ahogy említettük, ha el szeretné-e helyezni a rendszert tartalmazó virtuális Merevlemezt a Premium Storage kell, hogy létezik-e a lemezkép másolása a standard szintű tárfiók, és helyezze át a Premium Storage használhatók legyenek. Ha rendelkezik helyszíni kép, érdemes is használhatja ezt a módszert másolni, amely közvetlenül a Premium Storage-fiók.

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

#### <a name="step-2-create-cloud-service"></a>2. lépés a felhőalapú szolgáltatás létrehozása

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>3. lépés: Meglévő rendszerkép használata

Használhat meglévő rendszerképet. Is [igénybe vehet egy meglévő gépre képe](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Vegye figyelembe a gép, amelynek nincs, kép, DS * gép lehet. Miután a lemezképet, a következő lépések bemutatják, hogyan másolja azt a prémium szintű tárfiókot a **Start-AzureStorageBlobCopy** PowerShell-parancsmag segítségével.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>4. lépés: Másolás Blob Storage-fiókok között

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>5. lépés: Rendszeresen ellenőrizze a példány állapota:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>6. lépés: Lemezkép-lemez hozzáadása a tárház előfizetésben az Azure disk

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Előfordulhat, hogy annak ellenére, hogy a sikeres állapotjelentések sikerült továbbra is megjelenik egy lemez bérleti hibaüzenet. Ebben az esetben várjon körülbelül 10 percet.

#### <a name="step-7--build-the-vm"></a>7. lépés:  A virtuális gép létrehozása

Itt hoz létre a virtuális gép a lemezkép és a VHD-k két Premium Storage:

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
$pass = "theM)stC0mplexP@ssw0rd!”


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Always On rendelkezésre állási csoportok nem használó meglévő központi telepítések

> [!NOTE]
> Meglévő üzemelő példánya esetében először tekintse meg a [Előfeltételek](#prerequisites-for-premium-storage) című szakaszát.

Nincsenek SQL Server Always On rendelkezésre állási csoportok és azok, amelyeket nem használó központi telepítések különböző szempontjai. Ha nem használ AlwaysOn, és rendelkezik egy meglévő önálló SQL Server, frissítheti a Premium Storage egy új felhőalapú és a tárolási fiók használatával. Vegye figyelembe a következő beállításokat:

* **Hozzon létre egy új SQL Server virtuális gép**. Létrehozhat egy új SQL Server virtuális Gépet egy prémium szintű Storage-fiókot használó új üzembe Helyezésekhez leírtak szerint. Biztonsági másolatot készít, és az SQL Server-konfiguráció és a felhasználói adatbázisok visszaállítása. Az alkalmazásnak kell frissíteni, hogy az új SQL Server hivatkozhat, ha a belső vagy külső van használatban. Minden "db" tartományon kívül objektumok másolásához, mintha egy egymás mellett (SxS) az SQL Server migrálási csinált kell. Ez magában foglalja az objektumok, például a bejelentkezéseket, a tanúsítványok és a csatolt kiszolgálók.
* **Egy meglévő SQL Server virtuális gép áttelepítése**. Ehhez az SQL Server rendszerű virtuális gép offline állapotba, majd áthelyezte azt egy új felhőalapú szolgáltatás, amely tartalmazza, másolja a csatlakoztatott virtuális merevlemezek mindegyikét a Premium Storage-fiók. A virtuális gép csatlakozik a hálózathoz, ha az alkalmazás a server gazdagép-nevére hivatkozik, előtt. Vegye figyelembe, hogy a meglévő lemez mérete befolyásolja a teljesítményt nyújt. Például egy 400 GB-os lemezt lekérdezi kerekítve között P20. Ha tudja, hogy a lemez teljesítménye nem igényelnek, majd sikerült hozza létre újra a virtuális Gépet a DS sorozat virtuális gépként, és csatolja a Premium Storage virtuális merevlemezek a szükséges méret-teljesítmény specifikáció. Ezután leválasztása sikerült, és csatlakoztassa újra az SQL DB fájlokat.

> [!NOTE]
> Amikor a érdemes figyelembe vennie a mérete, a méretétől függően VHD lemezek másolásának azt jelenti, hogy prémium szintű tárolólemez típusának esnek, ez határozza meg a lemez teljesítménye specifikáció. Az Azure kerekít a legközelebbi lemez mérete legfeljebb tehát ha van egy 400 GB lemez ez kerekítjük között P20. A meglévő IO követelményeitől függően az operációs rendszer virtuális merevlemez nem szüksége lehet áttelepíteni a Premium Storage-fiók.

Ha az SQL Server külsőleg érhető el, majd a felhőalapú szolgáltatás virtuális IP-cím megváltozik. Is záró pontot, a hozzáférés-vezérlési listák és a DNS beállításokat.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Always On rendelkezésre állási csoportok használó meglévő központi telepítések

> [!NOTE]
> Meglévő üzemelő példánya esetében először tekintse meg a [Előfeltételek](#prerequisites-for-premium-storage) című szakaszát.

Kezdetben ebben a szakaszban áttekintjük, hogyan Always On kommunikál az Azure-hálózatot. Ezután részletezzük a két esetben a migrálások: ahol némi állásidővel tolerálható-migrációk és az áttelepítések, ahol minimális állásidővel kell elérni.

A helyszíni SQL Server Always On rendelkezésre állási csoportok egy figyelőt a helyszíni, amely regisztrál egy virtuális DNS-nevet és egy vagy több SQL Server-kiszolgálók között megosztott IP-címet használja. Ha az ügyfelek csatlakoznak a figyelő IP-címet az elsődleges SQL-kiszolgálón keresztül halad. Ez az a kiszolgáló, hogy az mindig az IP-erőforrást birtokló.

![A DeploymentsUseAlways][6]

Csak egy IP-cím rendelve egy hálózati Adaptert a virtuális gép is van a Microsoft Azure-ban tehát az ugyanazon a helyszínen, mint absztrakciós réteg növelhető az Azure használja az IP-cím, amely a belső és a külső Terheléselosztók (ILB/ELB) hozzá van rendelve. Mivel az ILB-/ ELB az azonos IP-Címek az IP-erőforrást a kiszolgálók között megosztott van beállítva. Ez a DNS-ben közzétett és ügyfélforgalom kerül át az ILB-/ ELB az elsődleges SQL-kiszolgáló replika. Az ILB-/ ELB tudja, melyik SQL Server elsődleges mivel mintavételek számára, hogy megvizsgálja az mindig az IP-erőforrást használja. Az az előző példában azt minden egyes csomópont, amely rendelkezik egy végpontot az ELB/ILB által hivatkozott mintavételek, amelyik reagál az elsődleges SQL-kiszolgáló.

> [!NOTE]
> Az ILB és ELB is egy adott Azure-ban hozzárendelt felhőalapú szolgáltatás, ezért bármilyen Azure valószínűleg azt jelenti, hogy a Load Balancer IP-cím módosul a felhőbeli migrálás.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Áttelepítése Always On üzemelő példánya, amely lehetővé teszi némi állásidővel

Nincsenek mindig a központi telepítései áttelepítésének, amelyek lehetővé teszik a némi állásidővel két stratégia szerint:

1. **Több másodlagos replika hozzáadása egy meglévő mindig a fürthöz**
2. **Új mindig a fürt áttelepítése**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Több másodlagos replika hozzáadása egy meglévő mindig a fürthöz

Egyik stratégia, hogy az Always On rendelkezésre állási csoport ad hozzá több másodlagos példány hozható létre. Adja hozzá ezeket az új felhőalapú szolgáltatás, és frissítse a figyelő az új terheléselosztó IP kell.

##### <a name="points-of-downtime"></a>Állásidő pontok:

* Fürt ellenőrzése.
* Mindig a feladatátvétel tesztelése az új másodlagos példány hozható létre.

Ha használja a virtuális gépen Windows Tárolókészletek magasabb i/o-teljesítményt, akkor a rendszer offline állapotba kerül a teljes fürt ellenőrzése közben. Az ellenőrző teszt szükség a csomópontok hozzáadása a fürthöz. A teszt futtatásához szükséges idő változhat, így ez a reprezentatív tesztkörnyezet egy becsült időt, hogy mennyi ideig ekkor be kell tesztelni.

Ahol hajthatja végre manuális feladatátvételt és a chaos tesztelése annak biztosítása érdekében a magas rendelkezésre állást mindig a funkciók a várt módon az újonnan hozzáadott csomópontokon időt kell kiépítenie.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Ahol a Tárolókészletek használt SQL Server összes példánya le kell állítani az ellenőrzés futtatása előtt.
>
> ##### <a name="high-level-steps"></a>Magas szintű lépései
>

1. Két új SQL-kiszolgáló létrehozása az új cloud service-ben csatolt prémium szintű Storage.
2. Másolja át a teljes biztonsági mentés és visszaállítás a **NORECOVERY**.
3. Másolja át a "nem felhasználó DB" függő objektumok, például a bejelentkezéseket stb.
4. Létrehozhat új egy új belső Load Balancer (ILB) vagy egy külső Load Balancer (ELB) használja, és ezután terhelés elosztott terhelésű végpontok beállítása a két új csomópontra.

   > [!NOTE]
   > Ellenőrizze az összes csomópontja rendelkezik a megfelelő végpont-konfiguráció, a folytatás előtt
   >
   >
5. Állítsa le a felhasználói vagy alkalmazásbeli hozzáférés az SQL Server (Tárolókészletek használata) esetén.
6. Állítsa le az SQL Server adatbázismotor-szolgáltatások minden csomóponton (Tárolókészletek használata) esetén.
7. Adja hozzá az új csomópontok fürthöz, és teljes ellenőrzés futtatásához.
8. Sikeres ellenőrzés után indítsa el az összes SQL Server szolgáltatást.
9. Tranzakciós naplók biztonsági mentését, és állítsa vissza a felhasználói adatbázisokat.
10. Adja hozzá az új csomópontok az Always On rendelkezésre állási csoport, és helyezze be a replikációs **Synchronous**.
11. Adja hozzá az IP-cím erőforráshoz, az új Cloud Service ILB-/ ELB Powershellen keresztül, Always On többhelyes példa alapján a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). A Windows fürtszolgáltatás, állítsa be a **lehetséges tulajdonosok** , a **IP-cím** az új csomópontok régi erőforrást. A "Hozzáadás IP-cím erőforrás azonos alhálózatban lévő" című szakaszában talál a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Feladatátvétel az új csomópontok egyikére.
13. Győződjön meg az új csomópontok automatikus feladatátvételi partnerek és a teszt feladatátvételt.
14. Távolítsa el az eredeti csomópontok rendelkezésre állási csoportból.

##### <a name="advantages"></a>Előnyök

* Új SQL-kiszolgálók is lehetnek (SQL Server és az alkalmazás) tesztelt, Always On hozzáadásuk előtt.
* Módosítsa a virtuális gép méretét, és testre szabhatja a tároló az pontos igényeinek megfelelően. Azonban hasznos lenne, így az SQL Fájlelérési utak azonos.
* Szabályozhatja, hogy az adatbázis biztonsági másolatok a másodlagos replikák a átvitelét indításakor. Ez eltér az Azure- **Start-AzureStorageBlobCopy** parancsmag másolása virtuális merevlemezek, mert ez egy aszinkron példányt.

##### <a name="disadvantages"></a>Hátrányai

* A Windows Tárolókészletek használata esetén nincs fürt állásidő az új további csomópontok esetében a teljes fürt ellenőrzése közben.
* Az SQL Server aktuális verziója és a másodlagos replikák meglévő számát, attól függően, nem lehet több másodlagos replika eltávolítása a meglévő másodlagos adatbázisok nélkül adhat hozzá.
* Előfordulhat, hogy hosszú SQL adatátviteli idő a másodlagos példány hozható létre beállítása közben.
* Nincs további költség migráláskor miközben párhuzamosan futó új gépek van.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Új mindig a fürt áttelepítése

Egy másik olyan stratégia, ha teljesen új mindig a fürt létrehozása az új csomópontok új cloud service-ben, és ezután irányítsa át az ügyfeleket, az.

##### <a name="points-of-downtime"></a>Állásidő pontok

Nincs állásidő alkalmazások és a felhasználók az új Always On figyelőt az átvitel során. Az állásidő függ:

* Állítsa vissza a végső tranzakciónapló biztonsági mentései az új kiszolgálók adatbázisok idő.
* Ügyfélalkalmazások számára új mindig a figyelőt frissítéséhez szükséges idő.

##### <a name="advantages"></a>Előnyök

* A tényleges éles környezetben, az SQL Server, tesztelheti, és operációs rendszer buildje alapján módosításokat.
* Lehetősége van a storage testreszabásához, és esetleg a virtuális gép méretének csökkentésére. Ez a költségek csökkentéséhez eredményezhet.
* Az SQL Server-build vagy verzióra frissítheti a folyamat során. Az operációs rendszer frissíteni is lehet.
* Az előző mindig a fürt egy szilárd visszaállítás céljaként működhet.

##### <a name="disadvantages"></a>Hátrányai

* A figyelő DNS-nevét módosítani, ha azt szeretné, hogy mindkét fut egyszerre mindig a fürtök kell. Ez hozzáadja adminisztrációs terhelés a migrálás során, az ügyfél az alkalmazás-karakterláncok szeretné bemutatni az új figyelőjének nevét.
* A szinkronizálási mechanizmus az, hogy minimalizálja a végleges szinkronizálást végez a migrálás előtt a lehető legközelebb őket a két környezet között meg kell valósítani.
* Hiba kerül költség áttelepítés során rendelkezik az új környezet futtatása közben.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Áttelepítése mindig a központi telepítések a minimális állásidő érdekében

Nincsenek áttelepítése mindig a központi telepítés két stratégia szerint a minimális állásidő érdekében:

1. **Egy létező másodlagos használják: Egyetlen helyen**
2. **Használja meglévő másodlagos másodpéldányt: Többhelyes kapcsolat**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Egy létező másodlagos használják: Egyetlen helyen

Egyik stratégia a minimális állásidő érdekében, hogy egy meglévő felhőalapú másodlagos igénybe vehet, és távolítsa el a jelenlegi felhőszolgáltatás. Ezután másolja a VHD-k az új prémium szintű Storage-fiókot, és a virtuális gép létrehozása az új cloud service-ben. Frissítse a figyelő a fürtszolgáltatás és a feladatátvételt.

##### <a name="points-of-downtime"></a>Állásidő pontok

* Nincs állásidő utolsó csomópontja az elosztott terhelésű végponthoz frissítésekor.
* Az ügyfél újracsatlakozás az ügyfél és a DNS konfigurációjától függően előfordulhat, hogy később.
* Ha úgy dönt, hogy a fürt mindig a csoport offline eltarthat, hogy ki az IP-címek felcserélése, nincs további állásidőt. Ez a hozzáadott IP-cím erőforrás vagy típusú függőség és a lehetséges tulajdonosok használatával elkerülheti a. A "Hozzáadás IP-cím erőforrás azonos alhálózatban lévő" című szakaszában talál a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Ha azt szeretné, hogy az új csomópontot a részt vesz az, hogy az mindig a feladatátvételi partnerként, kell egy hivatkozást a terhelés szempontjából elosztott állítsa be az Azure-végpont hozzáadása. Ha futtatja a **Add-AzureEndpoint** ehhez parancs, nyissa meg a jelenlegi kapcsolatok továbbra is, de új kapcsolatokat a figyelő nem hozható létre mindaddig, amíg a terheléselosztó frissített tudni. A tesztelés jelent az utolsó 90 120seconds ez kell tesztelni.

##### <a name="advantages"></a>Előnyök

* Részeként áttelepítés során felmerült költségek.
* Egy-az-egyhez áttelepítés.
* Csökkentett összetettségét.
* Lehetővé teszi a prémium szintű Storage termékváltozatok nagyobb IOPS. Ha a lemezek le lett választva a virtuális gépről, és másolja az új felhőalapú szolgáltatás, a 3. fél eszköz segítségével növelheti a virtuális merevlemez mérete, amely magasabb szintű termékváltozatot kínál. Növelje a virtuális merevlemez mérete, lásd: Ez [fórum vitafórum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Hátrányai

* Nincs magas rendelkezésre ÁLLÁS és Vészhelyreállítás átmenetileg megszakad áttelepítés során.
* Mivel ez az áttelepítés 1:1, egy minimális Virtuálisgép-méretet, amely támogatja a virtuális merevlemezek, a számát, így nem fogja tudni a virtuális gépek becsléseim használandó rendelkezik.
* Ebben a forgatókönyvben az Azure használna **Start-AzureStorageBlobCopy** parancsmag, amely aszinkron. Másolás befejezése nincs nem biztosítunk szolgáltatói szerződést. A példányokat függ, bár ez függ attól is függ továbbítandó adatok mennyisége várólista várakozási. A másolási idő növeli, ha az átvitel egy másik Azure-adatközpontban, amely támogatja a Premium Storage egy másik régióban. Ha csak 2 csomóponttal rendelkezik, fontolja meg egy lehetséges kockázatcsökkentési abban az esetben a Másolás a vizsgálat hosszabb ideig tart. Többek között a következő ötleteit.
  * Adja hozzá egy ideiglenes 3. az SQL Server-csomópont a magas rendelkezésre ÁLLÁSÚ egyeztetett leállás az áttelepítés előtt.
  * Végezze el az áttelepítést, az ütemezett karbantartás az Azure-on kívül.
  * Győződjön meg arról, megfelelően konfigurálta a fürt kvórumát.  

##### <a name="high-level-steps"></a>Magas szintű lépései

Ez a dokumentum nem mutatja be egy teljes körű teljes példa, azonban a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) részletesen is javítható végezni.

![MinimalDowntime][8]

* Gyűjtse össze a lemezkonfiguráció, és eltávolíthatja a csomópontot (ne törölje a virtuális merevlemezekkel).
* Prémium szintű Storage-fiók létrehozása, és másolja át a standard szintű tárfiók VHD-k
* Új felhőszolgáltatás hozható létre, és telepítse újra a SQL2, amely a felhőszolgáltatás virtuális Géphez. A virtuális gép létrehozása a másolt eredeti rendszert tartalmazó virtuális Merevlemezt használ, és a másolt VHD.
* Konfigurálja az ILB / ELB, és adja hozzá a végpontok.
* Frissítse a figyelő által:
  * Az Always On csoport offline állapotba helyezése és frissítése az mindig a figyelő az új ilb-vel / ELB IP-cím.
  * Vagy az IP-cím erőforrás az új Cloud Service ILB-/ ELB Powershellen keresztül történő Windows-fürtszolgáltatás hozzáadásakor. Ezután állítsa be az IP-cím erőforrás lehetséges tulajdonosainak az áttelepített csomópontra SQL2, és a vagy típusú függőség a hálózati név címként. A "Hozzáadás IP-cím erőforrás azonos alhálózatban lévő" című szakaszában talál a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Ellenőrizze a DNS-konfiguráció/propagálás az ügyfelek számára.
* Az sql1 számítógép virtuális gép áttelepítése, és haladjon végig a 2 – 4. lépéseket.
* Ha lépéseket 5ii használja, majd adja hozzá az sql1 számítógép egy lehetséges tulajdonosként a hozzáadott IP-cím erőforráshoz
* Tesztelheti a feladatátvételeket.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Használja meglévő másodlagos másodpéldányt: Többhelyes kapcsolat

Ha egynél több Azure-adatközpontban (DC) csomóponttal rendelkezik, vagy ha hibrid környezettel rendelkezik, majd használhatja egy Always On konfigurációs ebben a környezetben hogy minimalizálják az állásidőt.

A megoldás, az mindig a szinkronizálás a helyszíni vagy a másodlagos Azure-Tartományvezérlőt, majd feladatátvétel felett, hogy az SQL Server Synchronous módosítani. Ezután másolja a VHD-k egy prémium szintű Storage-fiókot, és a gép ismételt üzembe helyezése egy új felhőalapú szolgáltatásba. Frissítse a figyelőt, és ezután feladat-visszavételt.

##### <a name="points-of-downtime"></a>Állásidő pontok

A leállás ideje a feladatátvételt a másodlagos tartományvezérlő és vissza idő áll. Attól is függ, az ügyfél és a DNS-konfiguráció, és az ügyfél újrakapcsolódási késhet.
Vegye figyelembe az alábbi példa egy hibrid mindig a konfiguráció:

![MultiSite1][9]

##### <a name="advantages"></a>Előnyök

* Használhat meglévő infrastruktúrájával.
* Lehetősége van a frissítés előtti először az Azure storage, a Vészhelyreállítás Azure tartományvezérlőn.
* A Vészhelyreállítás Azure DC tárolási újra tudja konfigurálni.
* Nincs legalább két feladatátvételeket áttelepítés, kivéve a feladatátvételi tesztek során.
* Nem kell áthelyezni az SQL Server-adatok biztonsági mentése és visszaállítása.

##### <a name="disadvantages"></a>Hátrányai

* Ügyfél-hozzáférési SQL Serverre, attól függően is késések, amikor az SQL Server egy másik tartományvezérlő futtatja az alkalmazást.
* A másolási idő a VHD-k a Premium storage hosszú lehet. Hatással lehet a döntést, hogy, hogy a csomópont a rendelkezésre állási csoportban. Megfontolandó szempontok a log intenzív munka mikor terhelések futnak, az áttelepítés során szükség, mivel az elsődleges csomópont árvává tranzakciók tartani a tranzakciós napló. Ezért ez sikerült jelentősen megnő.
* Ebben a forgatókönyvben az Azure használna **Start-AzureStorageBlobCopy** parancsmag, amely aszinkron. Nincs befejezési nem biztosítunk szolgáltatói szerződést. A példányokat függ attól, bár ez függ az üzenetsorban, várja meg a továbbítandó adatok mennyisége is függ. Ezért csak egy csomópont kell a 2. az adatközpontot, abban az esetben a Másolás hosszabb időt vesz igénybe, mint a tesztelés kárenyhítési lépések megtétele. A kárenyhítési lépések közé tartozik a következő ötleteit:
  * Adja hozzá a 2. az SQL ideiglenes csomópontot a magas rendelkezésre ÁLLÁSÚ egyeztetett leállás az áttelepítés előtt.
  * Végezze el az áttelepítést, az ütemezett karbantartás az Azure-on kívül.
  * Győződjön meg arról, megfelelően konfigurálta a fürt kvórumát.

Ez a forgatókönyv azt feltételezi, hogy a telepítés dokumentált, és tudja, hogyan le van képezve a tárhely optimális lemez gyorsítótár beállításait a módosítások elvégzéséhez.

##### <a name="high-level-steps"></a>Magas szintű lépései

![Multisite2][10]

* Győződjön meg arról, a helyszíni / Azure DC az SQL Server elsődleges alternatív, és azt adja meg a többi automatikus feladatátvételi Partner (AFP számára is).
* Gyűjtsön a lemez konfigurációs adatait a SQL2, és eltávolíthatja a csomópontot (ne törölje a virtuális merevlemezekkel).
* Prémium szintű Storage-fiók létrehozása, és másolja a VHD-k a standard szintű Storage-fiókból.
* Új felhőszolgáltatás hozható létre, és a díjak csatolt tárolólemezek a SQL2 virtuális gép létrehozása.
* Konfigurálja az ILB / ELB, és adja hozzá a végpontok.
* A mindig a figyelő frissítése az új ilb-vel / ELB IP cím és a teszt feladatátvételt.
* Ellenőrizze a DNS-konfigurációt.
* Módosítsa a AFP SQL2, és áttelepíteni az sql1 számítógépen, és haladjon végig a 2 – 5. lépéseket.
* Tesztelheti a feladatátvételeket.
* Váltson a AFP vissza az sql1 számítógép és SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>A függelék: A Premium Storage-ba való migrálás egy többhelyes fürtön mindig

Ez a cikk további része egy részletes példa a Premium storage egy többhelyes mindig a fürt konvertálása biztosít. Azt is alakítja a figyelő egy külső load balancer (ELB) használatával a belső terheléselosztó (ILB).

### <a name="environment"></a>Környezet

* Windows 2k12 / SQL 2k12
* SP 1 DB fájlokat
* Tárolókészletek / csomópont x 2

![Appendix1][11]

### <a name="vm"></a>VM:

Ebben a példában fogjuk az ILB-ELB a mozgó bemutatása. ELB ILB, előtt elérhető volt, így ez bemutatja, hogyan ILB váltson az áttelepítés során.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Előtti lépések: Összekapcsolás az előfizetéssel

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>1. lépés: Új Tárfiók létrehozása és a felhőalapú szolgáltatás

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>2. lépés: Növelje a erőforrásokon engedélyezett hibák \<nem kötelező >

Bizonyos erőforrások, az Always On rendelkezésre állási csoport tartozó korlátozva van a hány hiba fordul elő egy időszak, amelyben a fürtszolgáltatásnak próbál indítsa újra az erőforráscsoportot. Ajánlott növeli Ez ugyanakkor azt is, ajánljuk figyelmébe ebben az eljárásban óta Ha ezt nem manuális feladatátvétel és az eseményindító feladatátvételeket leállításával gépek megjelenik a közeli ezt a korlátot.

A hiba keretet, ehhez a Feladatátvevőfürt-kezelő duplája körültekintő lenne nyissa meg az Always On erőforráscsoport tulajdonságait:

![Appendix3][13]

Módosítsa a maximális hibaszám 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>3. lépés: Emellett IP-cím erőforrás fürtcsoporthoz tartozó \<nem kötelező >

Ha csak egy IP-cím esetében a fürtcsoportban, és ez a felhő alhálózat igazított, ügyeljen arra, ha véletlenül kapcsolat nélküli minden fürtcsomópontnak a hálózaton a felhőben, majd a fürt IP-erőforrás és a fürt hálózati neve nem képesek online állapotba. Ebben a helyzetben a fürt más erőforrásai megakadályozza a frissítéseket.

#### <a name="step-4-dns-configuration"></a>4. lépés: DNS-konfiguráció

Zökkenőmentes átmenet megvalósítása, attól függ, hogyan folyamatban van a DNS használt fel, és frissíteni.
Mindig telepítve van, amikor létrehoz egy Windows Cluster erőforráscsoportot, ha Feladatátvevőfürt-kezelő megnyitása, láthatja, hogy legalább három erőforrást használ, a két, amely hivatkozik a dokumentum:

* Virtuális hálózat neve (VNN) – a DNS-nevet, hogy az ügyfelek csatlakoznak, amikor SQL Server Always On keresztül csatlakozni kívánó.
* IP-cím erőforrás – az IP-cím, amelyhez a VNN társítva, több is van, és többhelyes konfigurációban van egy IP-címet minden egyes helyhez és alhálózathoz.

Ha csatlakozik az SQL Server, az SQL Server Client illesztőprogram lekéri a figyelőhöz tartozó DNS-rekordok, és megpróbál kapcsolódni minden egyes mindig a hozzárendelt IP-cím. Ezután tárgyaljuk, amely befolyásolhatja ennek tényezőket.

A figyelő nevét társított egyidejű DNS-rekordok száma attól függ, nem csak társítva, IP-címek száma, de a "RegisterAllIpProviders'setting a Feladatátvételi fürtszolgáltatásban az Always ON VNN erőforrás.

Telepítésekor mindig az Azure-ban hozza létre a figyelőt és az IP-címek különböző lépésből áll, meg kell manuálisan konfigurálnia a "RegisterAllIpProviders" 1-re, ez eltér egy helyszíni Always On üzemelő, azt már értéke 1.

Ha "RegisterAllIpProviders" 0, akkor Ön csak az egyiket látja a figyelőhöz tartozó DNS-rekordot a DNS-ben:

![Appendix4][14]

Ha a 'RegisterAllIpProviders' 1:

![Appendix5][15]

Az alábbi kódot a VNN beállításokat listázása, és beállítja azt az Ön számára. A módosítás érvénybe léptetéséhez szeretne a VNN offline állapotba, és állítsa online állapotba. Ez a figyelő időt vesz igénybe a kapcsolat nélküli okozó ügyfél kapcsolat megszakítása.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Egy későbbi lépésben áttelepítési frissítenie kell az Always On figyelő, amely hivatkozik a terheléselosztó frissített IP-címet, ebbe beletartozik egy IP-cím erőforrás eltávolítása és hozzáadását. Az IP-frissítés után kell a DNS-zóna frissítve lett az új IP-cím, valamint, hogy az ügyfelek frissítése folyamatban van a helyi gyorsítótárban.

Ha az ügyfelek egy másik hálózati szegmensben tartalmazhat, és a egy másik DNS-kiszolgálóra hivatkozik, fontolja meg, mi történik, kapcsolatos DNS-zóna átvitele az áttelepítés során, az alkalmazás újracsatlakozás idő kell által korlátozott legalább a zóna átvitele időt bármely új IP-cím a figyelő a címeket. Ha alatt itt az ideje megkötés, érdemes megvitatása és a egy növekményes zónaletöltést a Windows csapata kényszerítése, és a DNS-állomásrekord is helyezni egy alacsonyabb idő az élettartam (TTL), így az ügyfelek frissítése. További információkért lásd: [növekményes zónaletöltések](https://technet.microsoft.com/library/cc958973.aspx) és [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Alapértelmezés szerint a figyelő az Always On Azure-beli társított DNS-rekord Élettartama az 1200-as másodperc. Előfordulhat, hogy szeretne Ez csökkentheti, ha korlátozás ahhoz, hogy az ügyfelek a migrálás során a DNS frissítése az IP-cím frissítése a figyelő az idő alatt. Megtekintheti és módosíthatja a konfigurációt a konfiguráció a VNN az okai:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> The lower the ‘HostRecordTTL’, a higher amount of DNS traffic occurs.

##### <a name="client-application-settings"></a>Ügyfél-Alkalmazásbeállítások

Ha az SQL-ügyfélalkalmazás támogatja a .NET 4.5-ös SQLClient, akkor használhatja "MULTISUBNETFAILOVER = TRUE" kulcsszó. Ezt a kulcsszót kell alkalmazni, mivel lehetővé teszi a feladatátvétel során az SQL Always On rendelkezésre állási csoport gyorsabban kapcsolatot. Számba veszi a mindig bekapcsolva figyelő párhuzamosan társított összes IP-címeken keresztül, és a feladatátvétel alatt hajt végre a agresszívabb TCP-kapcsolat újrapróbálkozási sebessége.

Az előző beállításaival kapcsolatos további információkért lásd: [MultiSubnetFailover kulcsszó és a kapcsolódó szolgáltatások](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). További tájékoztatás [SqlClient támogatása magas rendelkezésre állási, vészhelyreállítási](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>5. lépés: Fürt kvórumbeállításainak megadása

Egyszerre tart meg legalább egy SQL Server le fog, ahogy kell módosítania a fürt Kvórum beállítása, ha két csomópont fájl megosztási tanúsító (FSW) használ, állítsa be a kvórum csomóponttöbbség engedélyezése és a dinamikus lehetőségre szavazott , amely állandó marad egy csomópont esetén lehetővé teszi.

```powershell
Set-ClusterQuorum -NodeMajority  
```

A kezelése és a fürt kvórum további információkért lásd: [konfigurálása és kezelése a Windows Server 2012 feladatátvevő fürt kvórum](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>6. lépés: Bontsa ki a meglévő végpontok és a hozzáférés-vezérlési listák

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Mentse ezt a szöveget egy fájlba.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>7. lépés: Feladatátvételi partnerek és a replikációs mód módosítása

Ha több mint két különböző SQL Server, kell "Szinkron" módosítsa a feladatátvételt egy másik másodlagos egy másik tartományvezérlő vagy a helyszínen, és győződjön meg arról, hogy az automatikus feladatátvételi Partner (AFP), ez így magas rendelkezésre ÁLLÁS fenntartása, miközben változtatásokat végez. Ezt megteheti a TSQL használatával is módosíthatja, ha ssms-ben:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>8. lépés: Másodlagos virtuális gép eltávolítása a felhőalapú szolgáltatás

Meg kell lennie áttelepítésének tervezése felhőalapú másodlagos csomópont először. Ha ez a csomópont jelenleg elsődleges, akkor manuális feladatátvételt kell kezdeményezni.

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
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>9. lépés: Lemez gyorsítótárazási beállítások a CSV-fájlban, és mentse

Adatkötetnél ezek beállításaként pedig a csak OLVASHATÓ.

TLOG kötetek ezeket kell beállítani a nincs.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>10. lépés: Másolja a VHD-k

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


A virtuális merevlemezeket, a prémium szintű tárfiókba másolása állapotát ellenőrizheti:

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

Várjon, amíg ezek mindegyike sikeres rögzíti.

További információ az egyes blobok számára:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>11. lépés: Operációsrendszer-lemez regisztrálása

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>12. lépés: Új felhőszolgáltatás másodlagos importálása

Az alábbi kódot is az itt hozzáadott lehetőséget használja a gép importálhatja, és a retainable VIP-címet használja.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>13. lépés: Adja hozzá a terhelést új felhőalapú Svc, az ILB létrehozása elosztott terhelésű végpontok és a hozzáférés-vezérlési listák

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

#### <a name="step-14-update-always-on"></a>14. lépés: Always On Update

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

Most már távolítsa el a régi felhőszolgáltatás IP-címet.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>15. lépés: DNS-frissítések ellenőrzése

Most ellenőrizze a DNS-kiszolgálók az SQL Server-ügyfél hálózatokon és győződjön meg arról, hogy a fürtszolgáltatás hozzáadta az extra állomásrekord a hozzáadott IP-cím. Ha ezeket a DNS-kiszolgálók még nem frissített, fontolja meg a DNS zónaletöltés kényszerítése, és ellenőrizze, hogy az ügyfelek a nincs alhálózat tudnia oldani mindig az IP-címe, ez így nem kell várnia automatikus DNS-replikáció az.

#### <a name="step-16-reconfigure-always-on"></a>16. lépés: Konfigurálja újra az Always On

Ezen a ponton, várjon, amíg a másodlagos adott csomópont, amely teljes mértékben szinkronizálja újra a helyi csomóponton, és váltson át a szinkron replikáció csomópont, és lehetővé teszi a AFP lett migrálva.  

#### <a name="step-17-migrate-second-node"></a>17. lépésben: Áttelepíteni a második csomópont

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
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>18. lépést: Lemez gyorsítótárazási beállítások a CSV-fájlban, és mentse

Adatkötetek a gyorsítótár-beállításokat kell állítani a csak olvasható.

TLOG kötetek a gyorsítótár beállításait NONE értékre kell állítani.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>19. lépést: Másodlagos csomópontján új független Storage-fiók létrehozása

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

#### <a name="step-20-copy-vhds"></a>20. lépés: Másolja a VHD-k

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

A VHD-másolat állapota az összes virtuális merevlemezek ellenőrizheti:

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

Várjon, amíg ezek mindegyike sikeres rögzíti.

További információ az egyes blobok számára:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>21. lépés: Operációsrendszer-lemez regisztrálása

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>22. lépés: Adja hozzá a terhelés elosztott terhelésű végpontok és a hozzáférés-vezérlési listák

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

#### <a name="step-23-test-failover"></a>23. lépés: Feladatátvétel tesztelése

Várjon, amíg a migrált csomópont szinkronizálja a helyszíni Always On csomóponttal. A szinkron replikáció módban helyezze el, és várjon, amíg szinkronizálva van. Majd feladatátvétel az a helyszínen az első fürtcsomópont áttelepítése, azaz a AFP számára is. Miután már működőképes, módosítsa az utolsó áttelepített csomópont a AFP.

Érdemes indított feladatátvételi tesztek ugyanúgy összes csomópont között, és bár káosz teszteket annak ellenőrzéséhez, a folyamatban lévő feladatátvételi teszteket munkához a várt, és a egy időben manor futtassa.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>24. lépés: Kerüljenek vissza a fürt kvórumbeállításainak megadása / DNS-Élettartamot / feladatátvételi Pntrs / szinkronizálási beállítások

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Azonos alhálózatban lévő IP-cím erőforrás hozzáadása

Ha csak két SQL-kiszolgáló, és szeretne áttérni őket az új felhőalapú szolgáltatás, de szeretné tartani őket ugyanazon az alhálózaton, elkerülheti a kapcsolat nélküli módban a figyelő véve törli az eredeti mindig az IP-címet, és adja hozzá az új IP-cím. Ha egy másik alhálózatnak a virtuális gépeket telepít át, nem kell ehhez további fürthálózat, az alhálózatra hivatkozó működik.

Miután a migrált másodlagos kerülnek sorra, és hozzáadja az új IP-cím erőforrás a meglévő elsődleges a feladatátvétel előtt az új felhőalapú szolgáltatás, ezeket a lépéseket belül a Feladatátvevőfürt-kezelőt kell tennie:

Adja hozzá az IP-cím, lásd: a függelék, 14. lépés.

1. Az aktuális IP-cím erőforráshoz a példában a "dansqlams4" a "Meglévő elsődleges SQL Server", a lehetséges tulajdonos módosítása:

    ![Appendix13][23]
2. Az új IP-cím erőforrás "Áttelepítve másodlagos SQL Server", "dansqlams5: a példában a lehetséges tulajdonos módosítása:

    ![Appendix14][24]
3. Ez beállítása után a feladatátvételt is, és az utolsó csomópont áttelepítésekor a lehetséges tulajdonosok között szerkeszthető-e, így lehetséges tulajdonosaként a csomóponton kerül:

    ![Appendix15][25]

## <a name="additional-resources"></a>További források

* [Azure Premium Storage](../disks-types.md)
* [Virtuális gépek](https://azure.microsoft.com/services/virtual-machines/)
* [Az SQL Server Azure virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
