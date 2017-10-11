---
title: "Hozzon létre egy Azure virtuális gép a hálózat elérését gyorsítja fel |} Microsoft Docs"
description: "Útmutató: virtuális gép létrehozása a hálózat elérését gyorsítja fel."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 449425189a3b42dcb2c31316c1c8e38fac69d761
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>A hálózat elérését gyorsítja fel a virtuális gép létrehozása

Ebben az oktatóanyagban elsajátíthatja, hogyan hozzon létre egy Azure virtuális gép (VM) a hálózat elérését gyorsítja fel. Gyorsított hálózatkezelés a Windows és a nyilvános előzetes verziójában az adott Linux terjesztésekről GA. Gyorsított hálózatkezelés lehetővé teszi, hogy az egygyökerű i/o-virtualizálás (SR-IOV) egy virtuális géphez, a hálózati teljesítmény nagy mértékben javítva. A nagy teljesítményű elérési út nincs hatással a gazdagép a datapath, így csökkentve a késést, a jitter és a CPU felhasználását, a legnagyobb igénybevételt jelentő munkaterheléseit támogatott Virtuálisgép-típusokon való használatra. Az alábbi képen látható két virtuális gép (VM) közötti kommunikációt, és anélkül gyorsított hálózat:

![Összehasonlítása](./media/virtual-network-create-vm-accelerated-networking/image1.png)

Gyorsított hálózatkezelés nélkül, az összes hálózati forgalom mindkét a virtuális Gépet a virtuális kapcsoló és a gazdagép be kell járnia. A virtuális kapcsoló betartatja az összes, például a hálózati biztonsági csoportok, hozzáférés-vezérlési listák, elkülönítési és egyéb hálózati szempontból virtualizált szolgáltatások a hálózati forgalmat. Virtuális kapcsolók kapcsolatos további tudnivalókért olvassa el a [Hyper-V hálózatvirtualizálás és a virtuális kapcsoló](https://technet.microsoft.com/library/jj945275.aspx) cikk.

Gyorsított hálózattal, a hálózati forgalom érkezik a virtuális hálózati adapter (NIC), és a virtuális gép ezután kerül. Minden hálózati házirendek, a virtuális kapcsolót alkalmazó gyorsított hálózatkezelés nélkül kiszervezett és hardver alkalmazva. A hardver-házirend alkalmazását lehetővé teszi, hogy a hálózati adapter a hálózati forgalom erre a virtuális Gépet, a gazdagép és a virtuális kapcsolót, akkor alkalmazza, a fogadó minden házirend fenntartásával kihagyásával.

A gyorsított hálózat előnyeit, hogy engedélyezve van a virtuális gép csak vonatkoznak. A legjobb eredmény elérése érdekében az ideális legalább két virtuális gépeken, az azonos Azure Virtual Network (VNet) csatlakozik a funkció engedélyezése érdekében. Ha kommunikál a Vnetek vagy az összekötő a helyszíni, ez a funkció csak minimális befolyással van általános késleltetésű.

> [!WARNING]
> A Linux Public Preview nem rendelkezhet azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. A szolgáltatás nem támogatott, van, korlátozott képességeket, és előfordulhat, hogy nem érhető el az összes Azure helyét. A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések tekintse meg az Azure Virtual Network frissítések lap.

## <a name="benefits"></a>Előnyök
* **Késés csökkentése / magasabb csomag / másodperc (pps):** a virtuális kapcsoló eltávolítása a datapath eltávolítja a csomagok kiosztására szánt idejét a gazdagép a házirend-feldolgozás, a virtuális Gépen belül feldolgozható csomagok száma.
* **Alacsonyabb jitter:** virtuális kapcsoló feldolgozása függ a házirendet, szükség van, és a CPU-t, amely a feldolgozási terhelését. A házirendek betartatását, hogy a hardver kiszervezésével megszüntetéséhez, hogy a variancia közvetlenül a virtuális gép eltávolítása a gazdagép VM kommunikáció és az összes szoftver megszakítások és a környezet kapcsolók kézbesíti a csomagokat.
* **Csökkent a CPU-felhasználás:** kevesebb hálózati forgalom feldolgozása a CPU-használatot a virtuális kapcsoló a gazdagép megkerülésével vezet.

## <a name="Limitations"></a>Korlátozások
A következő korlátozások vonatkoznak az e funkció használata esetén:

* **A hálózati illesztő létrehozása:** gyorsított hálózat csak akkor engedélyezhető, az új hálózati Nem engedélyezhető az egy meglévő hálózati adaptert.
* **Virtuális gép létrehozása:** A hálózati adapter engedélyezve gyorsított hálózattal csak akkor csatolható a virtuális géphez a virtuális gép létrehozásakor. A hálózati adapter nem lehet csatolni, egy meglévő virtuális gépre.
* **Régiók:** Windows-alapú virtuális gépek gyorsított hálózattal érhető el az Azure-régióban. Linux virtuális gépek gyorsított hálózattal több régióba érhető el. Ez a funkció érhető el a régiók növekszik. Tekintse meg az Azure virtuális hálózat frissítések blog alatt a legfrissebb információkat.   
* **Támogatott operációs rendszerekről:** Windows: Microsoft Windows Server 2012 R2 Datacenter és a Windows Server 2016. Linux: Ubuntu Server 16.04 kernel 4.4.0-77 vagy annál újabb LTS, SLES 12 SP2, RHEL 7.3 és CentOS 7.3 (közzétett "Engedélyezetlen Wave szoftver").
* **Virtuálisgép-méret:** általános célú és nyolc vagy több maggal rendelkező számítási optimalizált példány mérete. További információkért lásd: a [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretének cikkeket. A Virtuálisgép-példány támogatott méretek készletét a jövőben bont ki.
* **Telepítés csak Azure Resource Managerrel (ARM) keresztül:** az elérését gyorsítja fel hálózati nincs elérhető a telepítéshez a címterület-kezelés/RDFE keresztül.

Ezek a korlátozások módosításai keresztül történik bejelentés a [Azure virtuális hálózat frissítése](https://azure.microsoft.com/updates/accelerated-networking-in-preview) lap.

## <a name="create-a-windows-vm"></a>Windows rendszerű virtuális gép létrehozása
Használhatja az Azure-portálon vagy az Azure [PowerShell](#windows-powershell) a virtuális gép létrehozásához.

### <a name="windows-portal"></a>Portál

1. Egy webböngészőben nyissa meg az Azure [portal](https://portal.azure.com) és jelentkezzen be a Azure [fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha már nincs fiókja, regisztrálhat az egy [ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kattintson a portál **+ új** > **számítási** > **Windows Server 2016 Datacenter**. 
3. Az a **Windows Server 2016 Datacenter** panel, amely akkor jelenik meg, hagyja *erőforrás-kezelő* a kijelölt **telepítési modell kiválasztása**, és kattintson a **létrehozása** .
4. Az a **alapjai** panel, amelyen megjelenik, adja meg a következő értékeket, hagyja a további alapértelmezett beállításokat, vagy válassza ki vagy adja meg a saját értékeit, majd kattintson a **OK** gombra:

    |Beállítás|Érték|
    |---|---|
    |Név|MyVm|
    |Erőforráscsoport|Hagyja **hozzon létre új** kiválasztva, és írja be *MyResourceGroup*|
    |Hely|USA nyugati régiója, 2.|

    Ha most ismerkedik az Azure-ba, további információ [erőforráscsoportok](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [előfizetések](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), és [helyek](https://azure.microsoft.com/regions) (amely is nevezzük régiók).
5. Az a **méret kiválasztása** panel, amelyen megjelenik, írja be *8* a a **minimális magszámra** mezőbe, majd kattintson az **összes**.
6. Kattintson a **DS4_V2 szabványos**, vagy bármely támogatott virtuális gép, majd kattintson a **válasszon** gombra.
7. A a **beállítások** panelen megjelenő összes beállításokat hagyja-van, kivéve, kattintson **engedélyezve** alatt **elérését gyorsítja fel a hálózat**, majd kattintson a **OK** gombra. **Megjegyzés:** , ha előző lépésben kiválasztott értékek a virtuális gép mérete, az operációs rendszer vagy helyre, amely nem szerepel a [korlátozások](#Limitations) című szakaszát, **elérését gyorsítja fel a hálózat** nem látható.
8. Az a **összegzés** panel, amelyen megjelenik, kattintson a **OK** gombra. Azure elindítja a virtuális gép létrehozása. Virtuális gép létrehozásának folyamata eltart néhány percig.
9. A Windows a gyorsított hálózati illesztőprogram telepítéséhez hajtsa végre a lépéseit a [konfigurálása Windows](#configure-windows) című szakaszát.

### <a name="windows-powershell"></a>PowerShell
1. Az Azure PowerShell legújabb verziójának telepítéséhez [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modul. Ha most ismerkedik az Azure PowerShell, olvassa el a [Azure PowerShell áttekintése](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk.
2. A PowerShell-munkamenet indításához kattintson a Start gombra, írja be **powershell**, majd kattintson a **PowerShell** a keresési eredmények.
3. A PowerShell-ablakban írja be a `login-azurermaccount` parancs futtatásával jelentkezzen be a Azure [fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha már nincs fiókja, regisztrálhat az egy [ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p).
4. A böngészőben másolja a következő:
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. A PowerShell-ablakban kattintson a jobb gombbal illessze be a parancsfájlt, és futtatnia kell elindítani. Felhasználónév és jelszó megadását kéri. Ezek a hitelesítő adatok használatát a bejelentkezéshez a virtuális géphez, amikor csatlakozik a következő lépésben. Ha a parancsfájl futása sikertelen, és azt végrehajtása előtt megváltoztatta a parancsfájlban szereplő értékeket, ellenőrizze a virtuális gép méretét, operációs rendszer használt értékeket, és a helyen találhatók a [korlátozások](#Limitations) című szakaszát.
6. A Windows a gyorsított hálózati illesztőprogram telepítéséhez hajtsa végre a lépéseit a [konfigurálása Windows](#configure-windows) című szakaszát.

### <a name="configure-windows"></a>A Windows beállítása
Miután létrehozta a virtuális Gépet az Azure-ban, telepítenie kell a Windows a gyorsított hálózati illesztőprogram. Az alábbi lépések elvégzése előtt kell korábban létrehozott egy Windows virtuális gép gyorsított hálózat használatával a [portal](#windows-portal) vagy [PowerShell](#windows-powershell) ebben a cikkben ismertetett visszaállítási lépésekkel. 

1. Egy webböngészőben nyissa meg az Azure [portal](https://portal.azure.com) és jelentkezzen be a Azure [fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha már nincs fiókja, regisztrálhat az egy [ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p).
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *MyVm*. Ha **MyVm** jelenik meg a keresési eredmények között kattintson rá.
3. Az a **MyVm** panel, amelyen megjelenik, kattintson a **Connect** a panel bal felső sarkában található gombra. **Megjegyzés:** Ha **létrehozása** alatt látható a **Connect** gomb, Azure még nem fejeződött be a virtuális gép létrehozása. Kattintson a **Connect** csak után többé nem láthatja **létrehozása** alatt a **Connect** gombra.
4. Engedélyezze a böngészőben a letöltés a **MyVm.rdp** fájlt.  Miután letöltötte, kattintson a fájl megnyitásához. 
5. Kattintson a **Connect** gombra a **távoli asztali kapcsolat** panelen, amely tájékoztat arról, hogy a távoli kapcsolat közzétevője nem azonosítható.
6. Az a **Windows biztonsági** meg, kattintson a **több lehetőséget**, majd kattintson a **használjon más fiókot**. Írja be a felhasználónevét és a 4. lépésben megadott jelszót, majd kattintson a **OK** gombra.
7. Kattintson a **Igen** gombra a távoli asztali kapcsolat mezőbe, amely tájékoztat arról, hogy a távoli számítógép identitása nem ellenőrizhető.
8. Kattintson a jobb gombbal a Start gombra, és kattintson a **Eszközkezelő**. Bontsa ki a **hálózati adapterek** csomópont. Ellenőrizze, hogy a **Mellanox ConnectX-3 virtuális függvény Ethernet-Adapter** az alábbi ábrán látható módon jelenik meg:
   
    ![Eszközkezelő](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. Gyorsított hálózatkezelés engedélyezve van a virtuális gép számára.

## <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása
Használhatja az Azure-portálon vagy az Azure [PowerShell](#linux-powershell) egy Ubuntu vagy SLES virtuális gép létrehozásához. Az RHEL és CentOS virtuális gépeket egy másik munkafolyamat van.  Ellenőrizze az alábbi utasításokat.

### <a name="linux-portal"></a>Portál
1. Linux preview; Ehhez hajtsa végre az 1-5 lépések a gyorsított hálózati regisztrálható a [hozzon létre egy Linux virtuális Gépet - PowerShell](#linux-powershell) című szakaszát.  A portál előnézethez nem regisztrálható.
2. Végezze el a lépéseket 1-8 a [hozzon létre egy Windows virtuális Gépet - portál](#windows-portal) című szakaszát. A 2. lépésben, kattintson a **Ubuntu Server 16.04 LTS** helyett **Windows Server 2016 Datacenter**. A jelen oktatóanyag esetében jelölje ki a jelszót, nem pedig egy SSH-kulcsot, abban az esetben, ha az üzemi környezetek is használhatja. Ha **elérését gyorsítja fel a hálózat** nem jelenik meg, amikor befejezte a 7. lépés a [hozzon létre egy Windows virtuális Gépet - portál](#windows-portal) szakasz ezen cikk valószínű, a következő okok valamelyike:
    - Meg vannak még nincs regisztrálva az előzetes verziójára. Győződjön meg arról, hogy a regisztrációs állapotát **regisztrált**, 4. lépésében leírtak szerint a [hozzon létre egy Linux virtuális Gépet - Powershell](#linux-powershell) című szakaszát. **Megjegyzés:** Amennyiben Ön részt vett a gyorsított hálózatkezelés a Windows virtuális gépek preview (már nem szükséges Windows hálózat virtuális gépek gyorsított használandó regisztrálása), akkor nem automatikusan regisztrálva vannak a Linux virtuális gépek hálózati gyorsított előzetes verzió. Regisztrálnia kell a gyorsított hálózati Linux virtuális gépek Preview részt.
    - Nem jelölt ki egy virtuális gép méretét, az operációs rendszer, vagy a hely szerepel a [korlátozások](#limitations) című szakaszát.
3. Gyorsított hálózati illesztőprogram telepítése Linux, lépéseinek végrehajtásához a [konfigurálása Linux](#configure-linux) című szakaszát.

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>Ha a Linux virtuális gépek létrehozása egy előfizetésben gyorsított hálózattal, és próbálja meg egy Windows virtuális gép létrehozása az ugyanahhoz az előfizetéshez gyorsított hálózattal, a Windows virtuális gépek létrehozása sikertelen lehet. Ez az előzetes ajánlott, hogy tesztelje a Linux és a Windows virtuális gépek külön előfizetésekhez gyorsított hálózattal.
>

1. Az Azure PowerShell legújabb verziójának telepítéséhez [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modul. Ha most ismerkedik az Azure PowerShell, olvassa el a [Azure PowerShell áttekintése](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk.
2. A PowerShell-munkamenet indításához kattintson a Start gombra, írja be **powershell**, majd kattintson a **PowerShell** a keresési eredmények.
3. A PowerShell-ablakban írja be a `login-azurermaccount` parancs futtatásával jelentkezzen be a Azure [fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha már nincs fiókja, regisztrálhat az egy [ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Regisztráció az Azure-gyorsított hálózati preview az alábbi lépések végrehajtásával:
    - E-mail küldése [ axnpreview@microsoft.com ](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) az Azure-előfizetése Azonosítóját és a tervezett használattól. Várjon, amíg egy e-mailes megerősítés tudnivalók az előfizetés engedélyezése a Microsoft.
    - Adja meg annak ellenőrzéséhez, hogy az előzetes regisztrálta a következő parancsot:
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        Ne folytassa az 5. lépés-ig **regisztrált** kimenet jelenik meg, miután megadta az előző parancsot. A kimeneti folytatása előtt a következő kimeneti hasonlóan kell kinéznie:
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >Amennyiben Ön részt vett a gyorsított hálózatkezelés a Windows virtuális gépek preview (már nem szükséges Windows virtuális gépek hálózati gyorsított használandó regisztrálása), akkor nincsenek automatikusan regisztrálva a gyorsított hálózatkezelési a Linux virtuális gépek megtekintése. Regisztrálnia kell a gyorsított hálózati Linux virtuális gépek Preview részt.
      >
5. A böngészőben másolja a következő Ubuntu vagy SLES és a kívánt módon működjenek.  Ebben az esetben Redhat és CentOS van egy másik munkafolyamat, az alábbiakban leírt:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. A PowerShell-ablakban kattintson a jobb gombbal illessze be a parancsfájlt, és futtatnia kell elindítani. Felhasználónév és jelszó megadását kéri. Ezek a hitelesítő adatok használatát a bejelentkezéshez a virtuális géphez, amikor csatlakozik a következő lépésben. Ha a parancsfájl futása sikertelen, ellenőrizze, hogy:
    - Regisztrálva az előzetes a 4. lépésben leírtak szerint
    - Ha megváltoztatta virtuális gép méretét, operációs rendszer típusa vagy a parancsfájl értékei előtt futtatnia kell, hogy az értékek láthatók a [korlátozások](#Limitations) című szakaszát.
7. Gyorsított hálózati illesztőprogram telepítése Linux, lépéseinek végrehajtásához a [konfigurálása Linux](#configure-linux) című szakaszát.

### <a name="configure-linux"></a>Linux konfigurálása

Miután létrehozta a virtuális Gépet az Azure-ban, telepítenie kell a gyorsított hálózati illesztőprogram Linux. Az alábbi lépések elvégzése előtt kell korábban létrehozott Linux virtuális gép gyorsított hálózat használatával a [portal](#linux-portal) vagy [PowerShell](#linux-powershell) ebben a cikkben ismertetett visszaállítási lépésekkel. 

1. Egy webböngészőben nyissa meg az Azure [portal](https://portal.azure.com) és jelentkezzen be a Azure [fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Ha már nincs fiókja, regisztrálhat az egy [ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p).
2. A bal felső, a portál jobb oldalán a *keresési erőforrások* sávban kattintson a **> _** ikonra kattintva indítsa el a felhő rendszerhéjakba (előzetes verzió). Megadja a lap alján, a portál, és néhány másodpercen belül megjelenik a Bash felhő rendszerhéj ablaktábla a  **username@Azure:~ $** kérdés. Bár a számítógépre, nem pedig a felhő rendszerhéj a virtuális gép is SSH, ebben az oktatóanyagban az utasítások feltételezik, hogy a felhő rendszerhéj használata.
3. A portál felső részén a mezőbe, amely tartalmazza a szöveg *keresési erőforrások*, típus *MyVm*. Ha **MyVm** jelenik meg a keresési eredmények között kattintson rá.
4. Az a **MyVm** panel, amelyen megjelenik, kattintson a **Connect** a panel bal felső sarkában található gombra. **Megjegyzés:** Ha **létrehozása** alatt látható a **Connect** gomb, Azure még nem fejeződött be a virtuális gép létrehozása. Kattintson a **Connect** csak után többé nem láthatja **létrehozása** alatt a **Connect** gombra.
5. Azure megnyitása felszólító adja meg a `ssh adminuser@<ipaddress>`. Adja meg a parancs a felhő rendszerhéj (vagy azt a listából, hogy megjelent 4. lépés:, és illessze be a felhő rendszerhéj másolás), majd az ENTER billentyűt.
6. Adjon meg **Igen** kéri, hogy ha folytatja a kapcsolódás, majd nyomja le az Enter a kérdésre.
7. Adja meg a virtuális gép létrehozásakor megadott jelszó. Egyszer sikeresen bejelentkezett a virtuális gép, megjelenik egy adminuser@MyVm:~ $ kérdés. Most jelentkezett a virtuális gép a felhőben rendszerhéj-kapcsolaton keresztül. **Megjegyzés:** felhő rendszerhéj munkamenetek időkorlátja lejárt 10 perc inaktivitás után.

Ezen a ponton utasításokat a telepítési módjától függően változhat. 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. A parancssorba írja be a `uname -r` , majd erősítse meg a verzió:

    * Ubuntu "4.4.0-77-generic," vagy nagyobb
    * SLES "4.4.59-92.20-default" vagy nagyobb

2. Az alábbi parancsok futtatásával hozzon létre egy kötés a szabványos hálózati virtuális hálózati és a gyorsított hálózati vNIC között. A hálózati forgalom használ a magasabb végrehajtása az elérését gyorsítja fel hálózati vNIC, amíg a kötés biztosítja, hogy hálózati adatforgalom nem szakad meg bizonyos konfigurációs változások során.
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. A parancsfájl futtatása után a virtuális gép újraindul, 60 másodperc felfüggesztése után.
4. Miután a virtuális gép újraindul, kapcsolódjon újra azt újra 5-7 lépések elvégzésével.
5. Futtassa a `ifconfig` parancsot, és győződjön meg róla, hogy bond0 merült fel, és a rendszer azt jelzi, fel a kapcsolat. 
 
 >[!NOTE]
      >Gyorsított hálózatkezelés használó alkalmazások kell protokollt használó kommunikációra a *bond0* nem felület *eth0*.  A kapcsolat neve előtt gyorsított hálózatkezelés eléri az általánosan rendelkezésre álló módosíthatja.

#### <a name="rhelcentos"></a>RHEL vagy CentOS

A Red Hat Enterprise Linux vagy a CentOS 7.3 VM létrehozásához meg néhány további lépést betölteni a legújabb illesztőprogramokkal SR-iov-t és a virtuális funkciójú (VF) illesztőprogram hálózati kártya számára szükséges. Az első fázisban a utasítás előkészíti egy olyanra, amely egy vagy több virtuális gépeken, amelyek az előre betöltött illesztőprogramokat is használhatók.

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>1. fázis: készítse elő a Red Hat Enterprise Linux vagy a CentOS 7.3 alapjául szolgáló lemezképhez. 

1.  Egy nem - PORTPROFIL CentOS 7.3 virtuális Gépet az Azure telepítéséhez

2.  Telepíteni azokat 4.2.2:
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  Konfigurációs fájljainak letöltése
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  Ez a virtuális gép kiosztásának megszüntetése

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  Azure-portálon állítsa le a virtuális gép; Ugrás a virtuális gép "lemez", az OSDisk URI Azonosítójú virtuális merevlemez rögzítése. Ezt az URI és az alapjául szolgáló lemezképhez VHD-t a tárfiók tartalmazza. 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>2. fázis: Azure-alapú virtuális gépek új kiépítése

1.  Kiépítés új virtuális gépek és az alapjául szolgáló lemezképhez engedélyezve a virtuális hálózati AcceleratedNetworking, szakaszban rögzített virtuális merevlemez használatával új AzureRMVMConfig alapján:

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  Után indítsa el a virtuális gépek, ellenőrizze a VF eszköz által "lspci", és ellenőrizze a Mellanox bejegyzést. Például azt kell látnia a lspci kimeneti ezt az elemet:
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  A ragasztás parancsprogrammal szerint:

    ```bash
    sudo bondvf.sh
    ```

4.  Indítsa újra az új virtuális gépek:

    ```bash
    sudo reboot
    ```

A virtuális gép konfigurált bond0 és az elérését gyorsítja fel hálózati elérési utat engedélyezve kell kezdődnie.  Futtatás `ifconfig` megerősítéséhez.
