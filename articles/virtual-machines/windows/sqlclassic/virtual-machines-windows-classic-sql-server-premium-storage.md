---
title: "Prémium szintű Azure Storage használata az SQL Server |} Microsoft Docs"
description: "Ez a cikk a klasszikus üzembe helyezési modellel létrehozott erőforrást használ, és a prémium szintű Azure Storage használata az Azure virtuális gépeken futó SQL Server útmutatást ad."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
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
ms.author: jroth
ms.openlocfilehash: 3d3fdd8865a293c5e2f0df6a97910ac8e2a07d4c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Az Azure Premium Storage és az SQL Server együttes használata virtuális gépeken
## <a name="overview"></a>Áttekintés
[Prémium szintű Storage](../premium-storage.md) tárhelyet biztosít alacsony késéssel és magas teljesítmény IO következő generációja van. A kulcs IO igényes munkaterhelések, például az SQL Server IaaS a legjobban [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

Ez a cikk ismerteti a tervezési és a prémium szintű Storage SQL Servert futtató virtuális gép áttelepítését. Ez magában foglalja az Azure-infrastruktúra (hálózati, tárolási) és a vendég Windows virtuális gép lépéseket. Példa a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) továbbfejlesztett helyi SSD-tárhelyre PowerShell előnyeinek kihasználása érdekében nagyobb virtuális gépek áthelyezése a teljes átfogó végpontok közötti áttelepítését mutatja.

Fontos tudni, hogy terjesztése prémium szintű Azure Storage az infrastruktúra-szolgáltatási virtuális gépeken futó SQL Server-végpontok közötti folyamatán. Ehhez a következőket:

* Prémium szintű Storage használatának előfeltételei azonosítása.
* Prémium szintű Storage IaaS az SQL Server telepítése az új központi telepítéseknél példát.
* Önálló kiszolgálók és a üzembe helyezése SQL Always On rendelkezésre állási csoportok áttelepítése meglévő telepítés példát.
* Lehetséges áttelepítési módszer.
* Az áttelepítés végrehajtásának egy meglévő Always On Azure, a Windows és az SQL Server lépéseket bemutató teljes-végpontok példa.

Tekintse meg az SQL Server Azure virtuális gépek további háttérinformációkat [SQL Server Azure virtuális gépek](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Szerző:** Daniel Sol **műszaki véleményezők:** Luis Carlos Vargas hering, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Prémium szintű Storage előfeltételei
Prémium szintű Storage használatával több előfeltételei van.

### <a name="machine-size"></a>Mérete
Prémium szintű Storage használatához kell használnia a virtuális gépek (VM) DS adatsorozat. DS adatsorozat gépek nem használta a felhőszolgáltatásban előtt, ha törli a meglévő virtuális Gépet, a csatlakoztatott lemezek tároljuk, és majd új felhőalapú szolgáltatás létrehozása előtt újra létrehozni a virtuális gép méreteként DS * szerepkör. További információ a virtuálisgép-méretek: [virtuális gépek és Felhőszolgáltatások mérete az Azure-](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Felhőszolgáltatások
Csak használata virtuális gépek DS * prémium szintű Storage az új felhőalapú szolgáltatás létrehozásakor. Ha az SQL Server Always On használ az Azure-ban, az mindig a figyelő az Azure belső vagy külső terheléselosztási terheléselosztó IP-cím egy felhőalapú szolgáltatás társított hivatkozik. Ez a cikk foglalkozik, hogyan telepítheti át az ebben a forgatókönyvben rendelkezésre állásának a.

> [!NOTE]
> DS * kell a első virtuális gép, amely az új felhőalapú szolgáltatás van telepítve.
>
>

### <a name="regional-vnets"></a>Regionális VNETEK
Virtuális gépek DS * kell konfigurálnia a virtuális hálózatot (VNET) kell lennie a regionális a virtuális gépeket üzemeltet. A "szélesebb formája" a virtuális hálózat lehetővé teszi a nagyobb virtuális gépek építhető ki más fürtöket, és engedélyezheti a köztük folyó kommunikációt. Az alábbi képernyőfelvételen látható a kijelölt hely regionális Vnetek látható, mivel az első eredmény azt mutatja, a "keskeny" virtuális hálózat.

![RegionalVNET][1]

A Microsoft támogatási jegy áttelepíteni egy regionális vnetre is módosíthatja. Microsoft majd módosítást végez. Az áttelepítéshez regionális Vnetek, a hálózati konfigurációban AffinityGroup tulajdonsága módosítható. Először exportálja a PowerShell a hálózati konfigurációt, és lecseréli a **AffinityGroup** tulajdonságot a **VirtualNetworkSite** elem egy **hely** tulajdonság. Adja meg `Location = XXXX` ahol `XXXX` egy Azure-régióban van. Importálja az új konfigurációt.

Például annak eldöntéséhez, hogy a következő VNET konfigurációját:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Helyezze át a egy regionális VNETRE, Nyugat-Európában, módosítsa a konfigurációt a következő:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Tárfiókok
Prémium szintű Storage konfigurált új tárfiók létrehozásához szükséges. Figyelje meg, hogy a storage-fiók nem egyedi virtuális merevlemezek, a prémium szintű Storage használata van beállítva azonban a DS * adatsorozat virtuális gépek használatakor csatolhat a VHD-k a prémium és standard szintű tárolást fiókokhoz. A érdemes lehet, hogy lehet, ha nem szeretné helyezni a az operációs rendszer virtuális Merevlemezt a prémium szintű Storage-fiók.

A következő **New-AzureStorageAccountPowerShell** parancsot a "Premium_LRS" **típus** hoz létre a prémium szintű Storage-fiókok:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Virtuális merevlemezek gyorsítótár beállításai
Lemezek, amelyek részei a prémium szintű Storage-fiók létrehozása közötti fő különbség a lemezgyorsítótár-beállítás. Használata javasolt az SQL Server adatmennyiség lemezek azt "**olvasási gyorsítótárazás**". A tranzakció naplózási kötetek, a lemezgyorsítótár-beállítás kell állítható be "**nincs**". Ez eltér a javaslatok, Standard szintű Storage-fiókok.

A virtuális merevlemezek csatolást követően a gyorsítótár-beállítása nem módosítható. Válassza le, majd újra csatlakoztatja a VHD-t egy frissített gyorsítótár beállítású kellene.

### <a name="windows-storage-spaces"></a>Windows tárolóhelyek
Használhat [Windows tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) úgy, ahogy az előző standard szintű Storage, ez lehetővé teszi, hogy át egy virtuális Gépet, amely már van okhoz tárolóhelyek. Példa [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (9-es és az utána lépés) való kigyűjtésére, majd importálja a virtuális gép több csatlakoztatott virtuális merevlemezek és a Powershell-kódot mutatja be.

Tárolókészletek használt szabványos Azure storage-fiók átviteli sebesség növelése és a késés csökkentésére. Bizonyára hasznosnak találja érték a prémium szintű Storage Tárolókészletek tesztelése az új központi telepítéseknél, de nagyobb fokú összetettségével jár tárolási telepítés adnak hozzá.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Annak ellenőrzése, mely Azure virtuális lemezek leképezés tárolókészletek
Mivel másik gyorsítótármappa beállítás javaslatok csatolt VHD-k, dönthet, másolja a VHD-k a prémium szintű Storage-fiók. Azonban ha, csatlakoztassa újra őket az új virtuális gép DS adatsorozat, szükség lehet a gyorsítótár beállításainak módosításához. A prémium szintű Storage ajánlott gyorsítótár beállításai, ha az SQL-adatfájlok és naplófájlok (nem pedig egy virtuális Merevlemezt, amely egyaránt tartalmaz) külön virtuális merevlemezek alkalmazandó egyszerűbb.

> [!NOTE]
> Ha SQL Server adatainak és naplókönyvtárainak fájlokat ugyanazon a köteten, a gyorsítótár lehetőséget választja a IO hozzáférési minták a adatbázis munkaterhelések függ. Csak tesztelési is bemutatják, milyen gyorsítótárazás esetén ajánlott ehhez a forgatókönyvhöz.
>
>

Azonban, amelyek össze több virtuális merevlemezzel kell tekintse meg a Windows a tárolóhelyek használata a eredeti parancsfájlok azonosításához, amelyek csatlakoztatott virtuális merevlemezek olyan milyen adott készletben, így után beállíthatja a gyorsítótár beállításait ennek megfelelően az egyes lemezek.

Nincs elérhető mutatjuk be, amely a VHD-k leképezi a tárolókészlet eredeti parancsfájlt, ha az alábbi lépések segítségével határozza meg a lemez tárolási készlet leképezési.

Az egyes lemezek tegye a következőket:

1. Virtuális gép és csatlakoztatott lemezek listájának beszerzése a **Get-AzureVM** parancs:

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. Jegyezze fel a Diskname és a logikai Egységet.

    ![DisknameAndLUN][2]
3. Távoli asztali kapcsolatot a virtuális Gépet. Ezután lépjen **számítógép-kezelés** | **Eszközkezelő** | **lemezmeghajtók**. Nézze meg a "Microsoft virtuális lemezek" tulajdonságairól

    ![VirtualDiskProperties][3]
4. Itt a LUN számot egy hivatkozást a LUN számot, ha a virtuális merevlemez csatolását virtuális gép.
5. Az a "Microsoft virtuális lemez" Ugrás a **részletek** lapon ezt a a **tulajdonság** listájában keresse fel **illesztőprogram kulcs**. Az a **érték**, vegye figyelembe a **eltolás**, ez az az alábbi képernyőképen 0002. A 0002 azt jelzi, hogy a Fizikailemez2, amely a tárolókészlet hivatkozik.

    ![VirtualDiskPropertyDetails][4]
6. Minden egyes tárolókészlethez memóriakép el a társított lemezekkel:

    Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Most már használhatja ezt az információt, rendelje hozzá a VHD-k csatolva tárolókészletek a fizikai lemezek.

Virtuális merevlemezek tárolókészletek a fizikai lemezek leképezése után, majd is leválasztani és keresztül másolja őket a prémium szintű Storage-fiók, majd csatolja őket a megfelelő gyorsítótár-beállításokkal. A következő példa a a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), 8 – 12 lépést. A lépések bemutatják, hogyan bontsa ki a virtuális gép csatlakoztatott virtuális merevlemez lemezkonfigurációt CSV-fájlba, másolja a VHD-k, a lemez konfigurációs gyorsítótár beállításainak módosításához és végül telepítse újra a virtuális gép sorozataként DS VM a csatlakoztatott lemezeket.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Virtuális gép tárolási sávszélesség és a virtuális merevlemez tárolási teljesítmény
A tárolási teljesítményt mértékét megadott DS * Virtuálisgép-méret és a virtuális merevlemez méretét. A virtuális gépek különböző juttatások csatolt VHD-k számát és a maximális sávszélesség (MB/s) támogatják-e rendelkeznie. Tekintse meg az adott sávszélesség számok [virtuális gépek és Felhőszolgáltatások mérete az Azure-](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Nagyobb IOPS mérete nagyobb a érhetők el. Ez akkor érdemes megfontolni, amikor az áttelepítési útvonalának. További információkért [lásd a táblázatot IOPS és lemeztípusok](../premium-storage.md#scalability-and-performance-targets).

Végül fontolja meg, a virtuális gépek rendelkezik-e, amely támogatja a csatlakoztatott lemezek különböző maximális lemez sávszélesség. Nagy terhelés a maximális sávszélesség álljon rendelkezésre a Virtuálisgép-szerepkör méretéhez sikerült telítsük. Például egy Standard_DS14 támogatja legfeljebb 512 MB/s. három P30 lemezzel, ezért a virtuális lemez sávszélességét telítsük sikerült. De ebben a példában az átviteli sebesség korlátja sikerült túllépve, attól függően, hogy olvasási és írási IOs kombinációját.

## <a name="new-deployments"></a>Új központi telepítéséhez
A következő két szakaszok bemutatják, hogyan telepítheti az SQL Server VMs prémium szintű Storage. Ahogy korábban említettük, nem feltétlenül kell elhelyezni az operációsrendszer-lemezképet, a prémium szintű storage. Akkor célszerű használni, ha bármely intenzív IO munkaterhelések helyezze az operációs rendszer virtuális merevlemez szándékos volt ehhez.

Az első példa bemutatja, meglévő Azure-gyűjtemény lemezképei használatával. A második példában, hogy rendelkezik egy meglévő szabványos tárfiókban lévő egyéni VM-lemezkép használata.

> [!NOTE]
> Ezek a példák feltételezik, hogy már létrehozta a regionális virtuális Hálózatot.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Prémium szintű Storage gyűjtemény lemezképpel új virtuális gép létrehozása
Az alábbi példa bemutatja, hogyan helyezze el az operációs rendszer virtuális merevlemez prémium szintű storage, és a prémium szintű Storage-VHD csatolása. Azonban az operációsrendszer-lemezképet is helyez egy standard szintű tárfiókot, és majd csatolja a VHD-k, amelyek tárolása a prémium szintű Storage-fiók. Mindkét forgatókönyvet egy.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>1. lépés: A prémium szintű Storage-fiók létrehozása
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>2. lépés: Új felhőalapú szolgáltatás létrehozása
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>3. lépés: Lefoglalni egy felhőalapú szolgáltatás VIP (nem kötelező)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>4. lépés:, Hozzon létre egy Virtuálisgép-tároló
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>5. lépés: az operációs rendszer virtuális Merevlemezt a Standard vagy prémium szintű Storage helyezi el.
    #NOTE: Set up subscription and default storage account which is used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>6. lépés: Virtuális gép létrehozása
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

    #create new Avaiability Set
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


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>A prémium szintű Storage egyéni lemezképként az új virtuális gép létrehozása
Ebben a forgatókönyvben azt mutatja be, melyekben egy standard szintű tárfiókot található meglévő testre szabott lemezképet. Ahogy azt korábban említettük, ha el szeretné-e helyezni az operációs rendszer virtuális Merevlemezt a prémium szintű Storage kell, hogy létezik-e a lemezkép másolása a standard szintű tárfiók, és helyezze át a prémium szintű Storage használat előtt. Ha rendelkezik helyszíni kép, érdemes is ezt a módszert másolja, amely közvetlenül a prémium szintű Storage-fiók.

#### <a name="step-1-create-storage-account"></a>1. lépés: Tárfiók létrehozása
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>2. lépés a felhőalapú szolgáltatás létrehozása
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>3. lépés: A meglévő kép használata
Egy meglévő lemezképet is használhatja. Is [igénybe vehet egy meglévő számítógép lemezképét](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Vegye figyelembe a gépet, akkor kép nem kell Tartományi * géphez. Miután a lemezképet, a következőket mutatják be a prémium szintű Storage-fiókkal, és másolja a **Start-AzureStorageBlobCopy** PowerShell-parancsmag segítségével.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>4. lépés: Másolat Blob Storage-fiókok között
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>5. lépés: Rendszeresen ellenőrizze a példány állapotát:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>6. lépés: Lemezt Azure előfizetés-tárház kép lemez hozzáadása
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Előfordulhat, hogy annak ellenére, hogy a állapotjelentések sikeres, sikertelen továbbra is megjelenik bérleti lemezhiba. Ebben az esetben várjon körülbelül 10 percet.
>
>

#### <a name="step-7--build-the-vm"></a>7. lépés: A virtuális gép létrehozása
Itt hoz létre a virtuális Gépet a lemezkép és a VHD-k két Premium Storage:

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

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Always On rendelkezésre állási csoportok nem használó meglévő központi telepítések
> [!NOTE]
> A meglévő telepítések esetében először tekintse meg a [Előfeltételek](#prerequisites-for-premium-storage) című szakaszát.
>
>

Nincsenek az Always On rendelkezésre állási csoportok és azok, amelyek nem használó SQL Server-telepítések kapcsolatos szempontokat. Ha nem használják a mindig bekapcsolva, és rendelkezik egy meglévő önálló SQL Server, prémium szintű Storage frissíthet egy új felhőalapú szolgáltatás és a tárolási fiók használatával. Vegye figyelembe a következő lehetőségeket:

* **Hozzon létre egy új SQL Server virtuális gép**. Új SQL Server virtuális gép egy prémium szintű Storage-fiókot használó hozhat létre, az új központi megfelelően. Majd biztonsági mentéséhez, és az SQL Server konfigurációs és a felhasználó adatbázisok helyreállításához. Az alkalmazást kell frissíteni, hogy az új SQL Server hivatkoznak, ha azt kívül és belül is hozzáférnek. Minden "kívül db" objektumok másolja, mintha csinált (SxS) SQL Server párhuzamos áttelepítés kellene. Ez magában foglalja az objektumok, például a bejelentkezési adatok, a tanúsítványokat és a csatolt kiszolgálók.
* **Telepítse át a meglévő SQL Server virtuális**. Ehhez szükséges, az SQL Server virtuális gép offline állapotba helyezése, majd áthelyezte azt egy új felhőalapú szolgáltatás, mely tartalmazza, másolja a csatlakoztatott virtuális merevlemezek mindegyikét a prémium szintű Storage-fiók. A virtuális gép online állapotba kerül, ha az alkalmazás hivatkozik, a kiszolgáló állomásneve, előtt. Vegye figyelembe, hogy a meglévő lemez mérete befolyásolja a teljesítményt nyújt. Például egy 400 GB lemezterület lekérdezi kerekíti egy P20. Ha tudja, hogy nincs szüksége a lemez teljesítménye akkor képes a virtuális Gépet a DS adatsorozat virtuális gépként hozza létre, és csatolja a prémium szintű Storage VHD-k az mérete/teljesítmény specifikáció van szüksége. Ezután leválasztása sikerült, és csatlakoztassa újra az SQL-adatbázis a fájlokat.

> [!NOTE]
> Amikor kell ügyelnie méretétől függően a mérete, a virtuális merevlemez a lemezek másolásának azt jelenti, hogy a prémium szintű tároló lemez típusát esnek, ez határozza meg a lemez teljesítménye megadását. Az Azure kerekítése a legközelebbi lemez méretét, akár úgy egy 400 GB lemezterület, akkor ez felfelé kerekíti a P20. Attól függően, hogy a meglévő IO-követelményeket az operációs rendszer virtuális merevlemez nincs szükség lehet át ezt a prémium szintű Storage-fiók.
>
>

Ha az SQL Server külsőleg érhető el, majd a felhőalapú szolgáltatás VIP megváltozik. Is a záró pontot, a hozzáférés-vezérlési listák és a DNS-beállításait.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Always On rendelkezésre állási csoportok használó meglévő központi telepítések
> [!NOTE]
> A meglévő telepítések esetében először tekintse meg a [Előfeltételek](#prerequisites-for-premium-storage) című szakaszát.
>
>

Kezdetben ebben a szakaszban úgy tekintünk, hogyan Always On Azure hálózatkezelési működjön. Azt majd lebontva két olyan eset az áttelepítés: áttelepítéseket, ahol is megengedett némi állásidővel, és áttelepítéseket, ahol minimális állásidővel kell elérni.

A helyszíni SQL Server Always On rendelkezésre állási csoportok használatára a figyelő a helyi, amely egy virtuális DNS-nevet egy IP-cím, egy vagy több SQL Server-kiszolgálók között megosztott együtt regisztrálja. Ha az ügyfelek csatlakoznak a figyelő az elsődleges SQL-kiszolgáló IP-keresztül halad. Ez az a kiszolgáló, amely a mindig az IP-erőforrás tulajdonosa adott időpontban.

![A DeploymentsUseAlways][6]

A Microsoft Azure lehet hozzárendelni egy hálózati Adaptert a virtuális Gépen csak egy IP-címet így az ugyanazon a helyszínen, mint absztrakciós réteget eléréséhez Azure használja az IP-cím, amely hozzá van rendelve a belső/külső terheléselosztó (ILB/ELB). Az IP-erőforrás, amelyet a kiszolgálók között, a ILB-/ ELB azonos IP-van beállítva. Ez a DNS-ben közzétett és ügyfélforgalmat a ILB-/ ELB azt az SQL-kiszolgáló elsődleges replikára továbbítja. A ILB-/ ELB tudja, melyik SQL Server elsődleges óta mintavételt számára, hogy megvizsgálja a mindig az IP-erőforrást használ. Az előző példában azt mintavétel az üzembe helyezett ELB/ILB által hivatkozott végpont minden csomópont, a attól válaszol az elsődleges SQL-kiszolgáló.

> [!NOTE]
> A Példánynak és üzembe helyezett ELB is egy adott Azure rendelt felhőalapú szolgáltatás, ezért bármely áttelepülés a felhőbe az Azure valószínűleg azt jelenti, hogy a Load Balancer IP változik.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Áttelepítése mindig a központi telepítések engedélyezhetik bizonyos időre leállítást
Nincsenek mindig a központi telepítései áttelepítésének, amely lehetővé teszi bizonyos időre leállítást két stratégiák:

1. **Több másodlagos replika hozzáadása egy meglévő mindig a fürthöz**
2. **Új mindig a fürt áttelepítése**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Több másodlagos replika hozzáadása egy meglévő mindig a fürthöz
Egyik stratégia, hogy több másodlagos adatbázis hozzáadása az Always On rendelkezésre állási csoportnak. Meg kell vennie ezeket az új felhőalapú szolgáltatás, és frissítse a figyelő az új load balancer IP-cím.

##### <a name="points-of-downtime"></a>Állásidő pontok:
* A fürt ellenőrzése.
* Új másodlagos adatbázis-tesztelési mindig a feladatátvételt.

Ha használja a virtuális Gépen belül Windows Tárolókészletek IO nagyobb átviteli teljesítményt, akkor a rendszer offline állapotba kerül a teljes fürt ellenőrzése során. A teszttel ellenőrizheti, ha a csomópontok hozzáadása a fürthöz. A teszt futtatása szükséges idő változhat, így kell tesztelje a reprezentatív tesztkörnyezetet beolvasni egy hozzávetőleges idejét mennyi ideig Ez időt vesz igénybe.

Idő, ahol végezheti el kézi feladatátvételre és tesztelési chaos csomóponton az újonnan hozzáadott magas rendelkezésre állású mindig a funkciók a várt módon kell telepíteni.

![DeploymentUseAlways On2][7]

> [!NOTE]
> A Tárolókészletek helyének SQL Server összes példányát le kell állítani az érvényesítés futtatása előtt.
>
> ##### <a name="high-level-steps"></a>Magas szintű lépései
>

1. Hozzon létre új felhőszolgáltatás csatlakoztatott prémium szintű Storage két új SQL-kiszolgáló.
2. Másolja át a teljes biztonsági mentést, és állítsa vissza a **NORECOVERY**.
3. Másolja át a "kívül felhasználói DB" függő objektumok, például a bejelentkezéseket stb.
4. Létrehozhat új egy új belső Load Balancer (ILB), illetve egy külső Load Balancer (ELB) használja, majd állítsa be a terhelés eloszlik végpontok mindkét új csomópontjának.

   > [!NOTE]
   > Ellenőrizze minden csomópont van a megfelelő végpont-konfiguráció, a folytatás előtt
   >
   >
5. Állítsa le a felhasználó vagy alkalmazás-hozzáférés az SQL Server (ha Tárolókészletek használata).
6. SQL Server adatbázismotor-szolgáltatások leállítása az összes olyan csomóponton, (ha Tárolókészletek használata).
7. A fürt, és futtassa teljes ellenőrzést új csomópontok hozzáadása.
8. Miután az ellenőrzés nem jelez hibát, indítsa el az összes SQL Server szolgáltatás.
9. Tranzakciós naplók biztonsági mentése és visszaállítása felhasználói adatbázisokat.
10. Vegyen fel új csomópontok az Always On rendelkezésre állási csoportnak, és helyezze el a replikációs **szinkron**.
11. Adja hozzá az IP-cím erőforrás, az új felhőalapú szolgáltatás ILB-/ ELB Powershellen keresztül, az Always On többhelyes példa alapján a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). A Windows fürtszolgáltatás, állítsa be a **lehetséges tulajdonosok** , a **IP-cím** erőforrás a régi új csomóponttal. A "Hozzáadás IP-cím erőforrás ugyanazon az alhálózaton" című része a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Feladatátvétel az új csomópontok egyikére.
13. Ellenőrizze az új csomópontok automatikus feladatátvételi partnerként és a teszt feladatátvételt.
14. Távolítsa el az eredeti csomópont a rendelkezésre állási csoport.

##### <a name="advantages"></a>Előnyei
* Új SQL-kiszolgálók lehet tesztelni (SQL Server alkalmazás) előtt mindig a történő hozzáadásuk.
* Módosítsa a Virtuálisgép-méretet, és testre szabhatja a tárolót az pontos igényeinek megfelelően. Azonban hasznos lenne azonos tartani az SQL teljes elérési utat.
* Azt is szabályozhatja az adatbázis biztonsági mentések átvitele a másodlagos replikák indításakor. Ez eltér az Azure használatával **Start-AzureStorageBlobCopy** parancsmag VHD-k, másolása, mert ez egy aszinkron másolatot.

##### <a name="disadvantages"></a>Hátrányok
* Windows Tárolókészletek használata esetén nincs fürt állásidő az új további csomópontokat a teljes fürt ellenőrzése során.
* Attól függően, hogy az SQL Server verziója és a másodlagos replikák meglévő számát akkor nem feltétlenül adhat hozzá további másodlagos replikák meglévő másodlagos adatbázis eltávolítása nélkül.
* Előfordulhat, hogy hosszú SQL adatátviteli idő a másodlagos adatbázis beállítása közben.
* Nincs további költség nélkül az áttelepítés során előfordulhat, hogy a párhuzamosan futó új gépek.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Új mindig a fürt áttelepítése
Egy másik olyan stratégia, hogy hozzon létre egy új mindig a fürtöt új csomópontot az új felhőalapú szolgáltatás, és majd irányítsa át az ügyfelek számára azt.

##### <a name="points-of-downtime"></a>Állásidő pontok
Alkalmazások és a felhasználók az új mindig a figyelő az átvitel során, nincs leállás. A leállás függ:

* Végső tranzakciónapló biztonsági mentései új kiszolgálókon lévő adatbázisok visszaállítása szükséges idő.
* Ügyfél-alkalmazások új mindig a figyelő az frissítéséhez szükséges idő.

##### <a name="advantages"></a>Előnyei
* SQL Server, a tényleges éles környezetben lehet tesztelni és operációs rendszer a módosításokat.
* Lehetősége van a tárolási testreszabásához és potenciálisan a virtuális gép méretének csökkentésére. Emiatt költségek csökkentéséhez.
* A SQL Server build vagy a verziójával frissítheti a folyamat során. Az operációs rendszer is lehet frissíteni.
* Az előző mindig a fürt teli visszaállítás céljaként működhet.

##### <a name="disadvantages"></a>Hátrányok
* Módosítsa a figyelő a DNS-nevét, ha azt szeretné, hogy mindkét fut egyszerre mindig a fürtök kell. Ezzel hozzáad adminisztrációs terhet az áttelepítés során, az ügyfél alkalmazás karakterláncok tükröznie kell az új figyelő nevét.
* Meg kell valósítani a szinkronizálási mechanizmus, hogy továbbra is a végleges szinkronizálást követelmények áttelepítés előtt minimalizálása érdekében a lehető legközelebb a két környezet közötti.
* Hiba kerül költség az áttelepítés során előfordulhat, hogy az új futtató környezet.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Áttelepítése mindig a központi telepítések a minimális állásidő érdekében
Nincsenek áttelepítése mindig a központi telepítés két stratégiák a minimális állásidő érdekében:

1. **Egy létező másodlagos használják: egy helyen**
2. **Meglévő másodlagos másodpéldányt használják: többhelyes**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Egy létező másodlagos használják: egy helyen
A minimális állásidő érdekében egyik stratégia, hogy egy meglévő felhőalapú másodlagos igénybe vehet, és eltávolítja az aktuális felhőalapú szolgáltatás. Ezután másolja a virtuális merevlemezek az új prémium szintű Storage-fiókot, és a virtuális gép létrehozása az új felhőalapú szolgáltatás. A figyelő a fürtszolgáltatás és a feladatátvételi frissíteni.

##### <a name="points-of-downtime"></a>Állásidő pontok
* Amikor a végső csomópontja az elosztott terhelésű végpont, nincs leállás.
* Az ügyfél újracsatlakozás ügyfél és a DNS-konfigurációtól függően előfordulhat, hogy később.
* Ha választja, a fürt mindig a csoport offline állapotba ki az IP-címek felcserélése, nincs további állásidő. Ez a hozzáadott IP-cím erőforrás egy OR függőségi és a lehetséges tulajdonosok használatával elkerülheti a. A "Hozzáadás IP-cím erőforrás ugyanazon az alhálózaton" című része a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Ha azt szeretné, hogy a hozzáadott részt vesz a partnerként mindig a feladatátvevő csomópont kell egy hivatkozást a terhelés eloszlik beállítása az Azure végpont hozzáadása. Amikor futtatja a **Add-AzureEndpoint** ehhez parancsot, nyissa meg a jelenlegi kapcsolatok maradjon, de a figyelő új kapcsolatot nem képesek hozható létre, amíg a terheléselosztó frissítette. A tesztelés ez fordult elő az elmúlt 90-120seconds, ez kell vizsgálni.
>
>

##### <a name="advantages"></a>Előnyei
* Nincsenek további az áttelepítés során felmerülő költség.
* -Az-egyhez áttelepítés.
* Csökkentett összetettségét.
* Lehetővé teszi, hogy a megnövekedett IOPS a prémium szintű Storage SKU. Ha a lemezt leválasztani a virtuális gép, és másolja az új felhőalapú szolgáltatás, a 3. fél eszköz a virtuális merevlemez méretét, magasabb teljesítmények biztosító használható. Virtuális merevlemez mérete növekszik, lásd: a [fórum vitafórum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Hátrányok
* Nincs magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási átmenetileg megszakad az áttelepítés során.
* Mivel ez egy 1:1 áttelepítési, kell használni egy minimális Virtuálisgép-méretet, amely támogatja a virtuális merevlemezek, a száma, így nem lehet a virtuális gépek downsize.
* Ebben a forgatókönyvben szeretné használni az Azure **Start-AzureStorageBlobCopy** parancsmag, amely aszinkron. Nincs nincs SLA-t a Másolás befejezése. A példányok idő változó, amíg ez függ a várakozási sorban is függ, a mennyiségű adatot továbbít. A másolási idő növeli a Ha az adatátvitelt lesz egy másik Azure-adatközponthoz, amely támogatja a prémium szintű Storage egy másik régióban. Ha csak 2 csomópontok, inkább egy lehetséges megoldás, abban az esetben a Másolás tovább tart, mint a tesztelés. Ez magában foglalhatja a következő ötleteket.
  * Adja hozzá egy ideiglenes 3. az SQL Server-csomópont a magas rendelkezésre ÁLLÁSÚ egyeztetett állásidővel az áttelepítés előtt.
  * Futtassa az áttelepítés Azure ütemezett karbantartás kívül.
  * Győződjön meg arról, a fürt kvóruma megfelelően konfigurálta-e.  

##### <a name="high-level-steps"></a>Magas szintű lépései
Ez a dokumentum nem bemutatása teljes végpont példa, azonban a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) részletesen is javítható, ha ezt elvégezni.

![MinimalDowntime][8]

* Gyűjtse össze a lemezkonfigurációt, és távolítsa el a csomópont (ne törölje a csatolt VHD-k).
* Prémium szintű Storage-fiók létrehozása, és másolja a standard szintű tárfiók VHD-k
* Új felhőalapú szolgáltatás létrehozása, és telepítse újra a virtuális gép SQL2, amely a felhőszolgáltatás. A másolt eredeti operációs rendszer virtuális Merevlemezt használ, és a másolt VHD-virtuális gép létrehozása.
* Konfigurálja a ILB / ELB és végpont-hozzáadáshoz.
* Frissítés figyelő egyike:
  * A mindig a csoport offline állapotba helyezése, és a mindig a figyelő frissítése új ILB és üzembe helyezett ELB IP-cím.
  * Vagy az IP-cím erőforrás az új felhőalapú szolgáltatás ILB-/ ELB Powershellen keresztül történő Windows-fürtszolgáltatás hozzáadására. Ezután állítsa be az IP-cím erőforrás lehetséges tulajdonosainak áttelepített csomóponthoz, SQL2, és ez állítja be a hálózati név vagy függőséghez. A "Hozzáadás IP-cím erőforrás ugyanazon az alhálózaton" című része a [függelék](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Ellenőrizze a konfigurációs DNS propagálási az ügyfelek számára.
* Telepítse át az sql1 számítógép virtuális gép, és nyissa meg a 2 – 4. lépésben.
* Lépéseket 5ii használata, majd adja hozzá az sql1 számítógép lehetséges tulajdonosaként a hozzáadott IP-cím erőforrás
* Feladatátvétel tesztelése.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Meglévő másodlagos másodpéldányt használják: többhelyes
Ha több Azure-adatközpontban (DC) vannak olyan csomópontok, vagy ha hibrid környezettel rendelkezik, majd egy mindig a konfigurációt használhatja ebben a környezetben az állásidő minimalizálása érdekében.

A megoldás, a mindig bekapcsolva szinkronizálás átállítása szinkron a helyszíni vagy Azure a másodlagos Tartományvezérlőt, majd feladatátvétel keresztül az adott SQL Server. Ezután másolja a VHD-k a prémium szintű Storage-fiókra, és telepítse újra a gépet az új felhőalapú szolgáltatás. A figyelő frissítése, és ezután a feladat-visszavételt.

##### <a name="points-of-downtime"></a>Állásidő pontok
A leállás áll a feladatátvételt a másodlagos tartományvezérlő és vissza időt. Is függ, az ügyfél és a DNS-konfiguráció, és később, az ügyfél lehet újracsatlakozni.
Vegye figyelembe a következő példa egy hibrid mindig a konfiguráció:

![MultiSite1][9]

##### <a name="advantages"></a>Előnyei
* Úgy használhatja a meglévő infrastruktúrát.
* Lehetősége van a frissítés előtti először a vész-Helyreállítási Azure rendszerű tartományvezérlőn történő tárolása az Azure storage.
* A vész-Helyreállítási Azure DC tárolási újra kell konfigurálni.
* Nincs legalább két feladatátvételi teszteket kivéve az áttelepítés során.
* Nem kell áthelyezni az SQL Server-adatok biztonsági mentése és visszaállítása.

##### <a name="disadvantages"></a>Hátrányok
* Attól függően, hogy az ügyfél-hozzáférési az SQL-kiszolgálón lehet nagyobb késéseket amikor SQL Server egy másik tartományvezérlő futtatja az alkalmazást.
* A másolási idő a VHD-k prémium szintű Storage hosszú lehet. Ez befolyásolhatja a döntést kell-e őrizni a csomópont a rendelkezésre állási csoportban. Megfontolandó szempontok a amikor napló intenzív feladata betölti az áttelepítés során futnak, mivel az elsődleges csomópont csak a árvává tranzakciók megtartja a tranzakciós napló. Ezért ez sikerült jelentősen megnő.
* Ebben a forgatókönyvben szeretné használni az Azure **Start-AzureStorageBlobCopy** parancsmag, amely aszinkron. Nincs nincs SLA befejezését követően. A példányok idő változik, ez függ a várakozási sorban, miközben a mennyiségű adatot továbbít a is függ. Ezért csak egy csomópont van a 2. adatközpontban, abban az esetben a Másolás tovább tart, mint a tesztelés megoldás lépéseket kell tennie. A kárenyhítési lépések közé tartozik a következő tippeket:
  * Adja hozzá a 2. az SQL ideiglenes csomópontot a magas rendelkezésre ÁLLÁSÚ egyeztetett állásidővel az áttelepítés előtt.
  * Futtassa az áttelepítés Azure ütemezett karbantartás kívül.
  * Győződjön meg arról, a fürt kvóruma megfelelően konfigurálta-e.

Ez a forgatókönyv feltételezi, hogy rendelkezik saját telepítési dokumentált, és tudja, hogyan hozzá van rendelve a tárhely optimális gyorsítótár beállításainak módosításához.

##### <a name="high-level-steps"></a>Magas szintű lépései
![Multisite2][10]

* Ellenőrizze a helyszíni / Azure-tartományvezérlő az SQL Server elsődleges alternatív, és azt a más automatikus feladatátvételi Partner (AFP).
* Lemez konfigurációs adatokat gyűjtsön a SQL2, és távolítsa el a csomópont (ne törölje a csatolt VHD-k).
* Prémium szintű Storage-fiók létrehozása, és másolja a standard szintű tárfiók VHD-k.
* Hozzon létre egy új felhőalapú szolgáltatás, és hozzon létre a SQL2 virtuális Gépet a díjak tárolólemezek csatlakoztatva.
* Konfigurálja a ILB / ELB és végpont-hozzáadáshoz.
* Frissítse a mindig a figyelő új ILB / ELB IP cím és a teszt feladatátvételt.
* Ellenőrizze a DNS-beállításait.
* Módosítsa a AFP SQL2, majd telepítse át az sql1 számítógép és végrehajtania 2 – 5. lépéseket.
* Feladatátvétel tesztelése.
* Váltás a AFP SQL1 és SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Függelék: A prémium szintű Storage mindig a fürt egy többhelyes áttelepítése
Az a cikk hátralévő része a többhelyes mindig a fürt átalakítani a prémium szintű Storage egy részletes példát biztosít. Is átalakítja a figyelő a külső terheléselosztással (ELB) egy belső terheléselosztón (ILB).

### <a name="environment"></a>Környezet
* Windows 2k12 / SQL 2k12
* SP 1 DB fájlok
* Tárolókészletek csomópontonként x 2

![Appendix1][11]

### <a name="vm"></a>VM:
Ebben a példában fogjuk áthelyezése egy üzembe helyezett ELB a ILB történő bemutatása. Üzembe helyezett ELB volt elérhető ILB, mielőtt, így ez azt jelenti, hogy hogyan lehet váltani a Példánynak az áttelepítés során.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Előkészítő lépések: Csatlakozás-előfizetéshez
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>1. lépés: Új Tárfiók létrehozása, és a felhőalapú szolgáltatás
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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>2. lépés: Az erőforrás engedélyezett hibák növelése <Optional>
Bizonyos erőforrások, amelyek az Always On rendelkezésre állási csoportnak nincsenek korlátozások a hány sikertelen végrehajtása esetén, amelyek adott időszakban, ahol a fürt próbálja meg újraindítani az erőforráscsoport fordul elő. Növeli a miközben meg van útmutató alapján ez az eljárás óta Ha ezt elmulasztja manuálisan eseményindító és feladatátvételi feladatátvételek állítja le ezt a határt hamarosan kaphat gépek ajánlott.

A hiba támogatást ehhez a Feladatátvevőfürt-kezelőben, kétszeres körültekintő lenne keresse fel a mindig bekapcsolva erőforráscsoport tulajdonságait:

![Appendix3][13]

Módosítsa a maximális hibák 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>3. lépés: IP-cím hozzáadása erőforrás fürt csoport <Optional>
Ha csak egy IP-címet a fürtcsoportban, és ez a felhő alhálózati igazodik a, ügyeljen arra, ha véletlenül kapcsolat nélküli minden fürtcsomópontnak a felhőben a hálózat a fürt IP- és hálózati fürtnév rendszer nem fogja tudni online állapotba. Ebben a helyzetben a fürt más erőforrásai megakadályozza a frissítéseket.

#### <a name="step-4-dns-configuration"></a>4. lépés: DNS-konfiguráció
Végrehajtási sima átmenet, attól függ, hogyan DNS folyamatban van szükség, és frissíti.
Mindig telepítve van, ha létrehoz egy Windows fürterőforrás-csoporthoz, ha a Feladatátvevőfürt-kezelő megnyitásához arról, hogy legalább három erőforrással rendelkezik, a kettő, a dokumentum hivatkozó:

* Virtuális hálózat neve (VNN) – a DNS-nevét, amely az ügyfelek csatlakozhatnak, ha szeretne csatlakozni az SQL Server Always On keresztül.
* IP-cím erőforrás – az IP-cím, amely a VNN társított, több is van, és többhelyes konfigurációban van hely/alhálózatot / IP-címet.

Amikor csatlakozik az SQL Server, az SQL Server Client illesztőprogram kéri le a DNS-rekordokat, a figyelő társított, és megpróbál csatlakozni minden mindig a hozzárendelt IP-cím. Ezután arról lesz szó néhány befolyásoló tényezők is ez.

A figyelő neve társított egyidejű DNS-rekordok száma attól függ, nem csak kapcsolódó, IP-címek száma, de a "RegisterAllIpProviders'setting a Feladatátvételi fürtszolgáltatás az Always ON VNN erőforrás.

Ha telepít mindig az Azure-ban különböző lépést a figyelő és IP-címek létrehozása, kell manuálisan konfigurálnia a "RegisterAllIpProviders' 1, ez eltér a egy helyszíni mindig a telepítés már állítottak 1.

Ha "RegisterAllIpProviders" 0, akkor csak akkor jelenik meg egy DNS-rekordot a DNS-ben a figyelő társított:

![Appendix4][14]

Ha 'RegisterAllIpProviders' 1:

![Appendix5][15]

Az alábbi kódot ki a VNN beállítások listázása, és beállítja azt. A módosítás érvényre szeretné a VNN offline állapotba, és kapcsolja be újra online. Ez a figyelő időt vesz igénybe offline okozó ügyfél kapcsolat megszűnésének.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

Egy későbbi lépésben áttelepítési frissítenie kell a mindig bekapcsolva figyelő frissített IP-címet, amely hivatkozik a terheléselosztó, ebbe beletartozik egy IP-cím erőforrás eltávolítása és hozzáadását. Az IP-frissítés után ellenőrizze az új IP-címet a DNS-zónában frissítették, és, hogy az ügyfelek frissítése a helyi gyorsítótárban kell.

Ha az ügyfelek egy másik hálózati szegmensben találhatók, és egy másik DNS-kiszolgáló hivatkozik, fontolja meg, mi történik, DNS-zóna átviteli kapcsolatos az áttelepítés során, az alkalmazás csatlakozzon újra idő kell legalább a zóna átviteli idő bármely új IP-korlátozza a figyelő címtartományát. Ha itt időkorlát alatt, érdemes tárgyalja, és a Windows-csoportok egy növekményes zónaletöltés kényszerítése, és a DNS-állomásrekord is helyezheti a egy alacsonyabb élettartam (TTL), ezért az ügyfelek frissítése. További információkért lásd: [növekményes zónaátvitelek](https://technet.microsoft.com/library/cc958973.aspx) és [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Az élettartam a DNS-rekord, a figyelő az Always On Azure társított alapértelmezés az 1200-as másodperc. Kezdésként érdemes lehet csökkenteni Ez a korlátozás ahhoz, hogy az ügyfelek az áttelepítés során frissítse a DNS a frissített IP-cím a figyelő időpontja esetén. Tekintse meg, és a konfiguráció módosítása a VNN konfigurációjának kimenő okai:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

> [!NOTE]
> Minél alacsonyabb HostRecordTTL, a DNS-forgalom nagyobb mennyiségű következik be.

##### <a name="client-application-settings"></a>Ügyfél-Alkalmazásbeállítások
Ha az SQL-ügyfélalkalmazást támogatja a .net 4.5 SQLClient, akkor használhatja "MULTISUBNETFAILOVER = TRUE" kulcsszó. Ezt a kulcsszót kell alkalmazni, mivel lehetővé teszi a gyorsabb kapcsolat SQL Always On rendelkezésre állási csoportnak a feladatátvétel során. A mindig bekapcsolva figyelő párhuzamosan társított összes IP-címek keresztül enumerálása, és átadta szigorúbb TCP-kapcsolat újrapróbálkozási sebességét.

Az előző beállításaival kapcsolatos további információkért lásd: [MultiSubnetFailover kulcsszó és a kapcsolódó szolgáltatások](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Lásd még: [SqlClient támogatja a magas rendelkezésre állási, vészhelyreállítási](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>5. lépés: Fürt kvórumbeállításainak megadása
Legalább egy SQL Server le egyszerre tart kívánja, módosítania kell a fürt Kvórum beállítása, ha két csomópont fájl megosztási tanúsító (FSW) használatával, állítsa be a kvórum csomóponttöbbség engedélyezése és felhasználását a dinamikus szavazás , lehetővé teszi a egyetlen csomópontot állandó marad.

    Set-ClusterQuorum -NodeMajority  

Kezelését és konfigurálását a fürtkvórum további információkért lásd: [konfigurálása és kezelése a Windows Server 2012 feladatátvevő fürt kvórum](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>6. lépés: Bontsa ki a meglévő végpontok és hozzáférés-vezérlési listák
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Mentse ezt a szöveget egy fájlba.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>7. lépés: Feladatátvételi partnerként és replikációs mód módosítása
Ha több mint két SQL-kiszolgáló, "Szinkron" módosítsa a feladatátvételt egy másik másodlagos egy másik tartományvezérlő vagy a helyszíni és teszik az automatikus feladatátvételi Partner (AFP) kell, ez pedig, magas rendelkezésre ÁLLÁSÚ karbantartása, míg a módosításokat. Ezt megteheti a TSQL használatával is módosíthatja, ha a szolgáltatáshoz az SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>8. lépés: Másodlagos virtuális gép eltávolítása a felhőalapú szolgáltatás
Meg kell lennie áttelepítésének tervezése a felhő másodlagos csomópont először. Ha ez a csomópont jelenleg elsődleges, akkor kézi feladatátvételt kell kezdeményezni.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>9. lépés: Lemez gyorsítótárazási beállítások a CSV-fájlban, és mentse
A adatkötetek ezeket kell megadni csak OLVASHATÓ.

TLOG kötetek ezeket kell lehet nincs értékűre állítani.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>10. lépés: Másolat VHD-k
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



A virtuális merevlemezek a prémium szintű Storage-fiók példány állapotát ellenőrizheti:

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

![Appendix8][18]

Várjon, amíg a fenti sikeres fájl rögzíti.

Az egyes blobok információt:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>11. lépés: az operációs rendszer Register lemez
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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>12. lépés: Importálása másodlagos új felhőszolgáltatás
Az alábbi kódot is használ a hozzáadott itt importálhatja a gép és a retainable VIP használja.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>13. lépés: Hozható létre ILB új felhő Svc, vegye fel a terhelés eloszlik a végpontok és hozzáférés-vezérlési listák
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

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>14. lépés: Mindig a frissítése.
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Most távolítsa el a régi felhőalapú szolgáltatás IP-címet.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>15. lépés: A DNS-frissítési ellenőrzése
Most DNS-kiszolgálók ellenőrizze az SQL Server ügyfél hálózatokon és győződjön meg arról, hogy a fürtszolgáltatás hozzáadta a felesleges állomásrekord a hozzáadott IP-cím. Ha ezeket a DNS-kiszolgálók nem frissítették, fontolja meg a DNS zónaletöltés kényszerítése, és győződjön meg arról, hogy az ügyfelek nem alhálózati képesek mindkét mindig az IP-címekhez, ez így nem kell várnia az automatikus DNS-replikáció az.

#### <a name="step-16-reconfigure-always-on"></a>16. lépés: Mindig a újrakonfigurálása
Ezen a ponton várja meg a másodlagos teljesen szinkronizálja újra a helyi csomóponthoz és szinkron replikáció csomópont váltani, és lehetővé teszi a AFP áttelepített csomóponton.  

#### <a name="step-17-migrate-second-node"></a>17. lépés: A második csomópont áttelepítése
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>18. lépés: Lemez gyorsítótárazási beállítások a CSV-fájlban, és mentse
A adatkötetek a gyorsítótár beállításainak READONLY kell megadni.

TLOG kötetek a gyorsítótár beállításainak nincs értékre kell állítani.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>19. lépés: A másodlagos csomópont új független Storage-fiók létrehozása
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

#### <a name="step-20-copy-vhds"></a>20. lépés: Másolat VHD-k
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

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


A VHD-másolat állapota ellenőrizze, hogy minden virtuális merevlemez: ForEach (a $diskobjects $disk) {$lun = $disk. LUN $vhdname $disk.vhdname $cacheoption = = $disk. HostCaching $disklabel = $disk. Lemezcímke $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Várjon, amíg a fenti sikeres fájl rögzíti.

Az egyes blobok információt:

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>21. lépés: az operációs rendszer Register lemez
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

    #Join to existing Avaiability Set

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>22. lépés: Adja hozzá a terhelés eloszlik a végpontok és hozzáférés-vezérlési listák
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>23. lépés: Feladatátvételi teszt
Várjon, amíg az áttelepített csomópont szinkronizálja a helyszíni mindig a csomóponttal. Szinkron replikáció mód elhelyezése, és várjon, amíg azt szinkronizálva. Ezután az első csomópontot a helyszínen a feladatátvétel áttelepíti, vagyis a AFP. Után, amely működött, módosítsa a AFP áttelepített utolsó csomópontja.

Feladatátvételi tesztek összes csomópontok között kell, és bár chaos tesztek annak biztosítása érdekében feladatátvételek work várt, egy időben manor futnak.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>24. lépés: Helyezze vissza fürt kvórumbeállításainak megadása / DNS-élettartam / feladatátvételi Pntrs / szinkronizálási beállítások
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Azonos alhálózatban lévő IP-cím erőforrás hozzáadása
Ha csak két SQL-kiszolgáló és szeretne áttérni őket az új felhőalapú szolgáltatás, de szeretné, hogy továbbra is ugyanazon az alhálózaton, elkerülheti a figyelő offline véve törli az eredeti mindig az IP-címet, és adja hozzá az új IP-cím. Ha a virtuális gépeket telepít át egy másik alhálózatnak, nem kell, amíg egy fürt további hálózati által hivatkozott alhálózat ehhez.

Miután a áttelepített másodlagos felvet és hozzáadni az új IP-cím erőforrás a meglévő elsődleges feladatátvétel előtt az új felhőalapú szolgáltatás, ezeket a lépéseket belül a Feladatátvevőfürt-kezelőt kell tennie:

Adja hozzá az IP-cím, tekintse meg a [függelék](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), 14 lépésben.

1. Az aktuális IP-cím erőforrás a példában "dansqlams4" a "Meglévő elsődleges SQL Server", a lehetséges tulajdonos módosítása:

    ![Appendix13][23]
2. Az új IP-cím erőforrás "Áttelepítve másodlagos SQL-kiszolgáló", "dansqlams5" példában a lehetséges tulajdonos módosítása:

    ![Appendix14][24]
3. Miután ezt állítja be a következőket teheti feladatátvevő, és az utolsó csomópont áttelepítésekor a lehetséges tulajdonosok módosítani kell úgy, hogy a csomópont lehetséges tulajdonosa meg van adva:

    ![Appendix15][25]

## <a name="additional-resources"></a>További források
* [Prémium szintű Azure Storage](../premium-storage.md)
* [Virtuális gépek](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server Azure virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
