---
title: Oktatóanyag – Windows rendszerű virtuális gépek létrehozása és felügyelete Azure PowerShell-lel | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Windows rendszerű virtuális gépeket az Azure-ban az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/28/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 94858a147dc0383376a54bc94685d855d37e12d5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991200"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Oktatóanyag: Létrehozása és kezelése Windows virtuális gépek az Azure PowerShell használatával

Az Azure-beli virtuális gépek egy teljes mértékben konfigurálható és rugalmas számítási környezetet nyújtanak. Az oktatóanyag az Azure-beli virtuális gépek (VM) üzembe helyezésének alapvető feladatait ismerteti, például a virtuális gépek méretének és rendszerképeinek kiválasztását és a virtuális gépek üzembe helyezését. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gép létrehozása és csatlakozás a virtuális géphez
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Virtuális gép átméretezése
> * Virtuális gép állapotának áttekintése és értelmezése

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsot.

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. A következő példában egy *myResourceGroupVM* nevű erőforráscsoportot hozunk létre az *EastUs* régióban:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Az erőforráscsoport meghatározására a virtuális gép létrehozásakor vagy módosításakor kerül sor, ahogy ez az oktatóanyagból is kiderül.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Virtuális gép létrehozásakor több lehetőség is elérhető, mint például az operációs rendszer lemezképét, a hálózati konfiguráció vagy a rendszergazdai hitelesítő adatokkal. Ez a példa létrehoz egy *myVM* nevű virtuális gépet, amely a Windows Server 2016 Datacenter alapértelmezett verzióját futtatja.

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-6) paranccsal:

```azurepowershell-interactive
$cred = Get-Credential
```

A virtuális Gépet a létrehozása [új AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Kapcsolódás egy virtuális géphez

Miután az üzembe helyezés befejeződött, hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

Futtassa a következő parancsokat a virtuális gép nyilvános IP-címének visszaadásához. Jegyezze fel ezt az IP-címet, hogy egy későbbi lépésben csatlakozhasson majd hozzá a böngészőjében a webes kapcsolat tesztelése érdekében.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Használja az alábbi parancsot a helyi gépen, ha távoli asztali kapcsolatot szeretne létrehozni a virtuális géphez. Cserélje le az IP-címet a virtuális gép *publicIPAddress* címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```powershell
mstsc /v:<publicIpAddress>
```

A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a virtuális géphez létrehozott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

## <a name="understand-marketplace-images"></a>A Marketplace-beli rendszerképek megismerése

Az Azure Marketplace-en számos rendszerkép található, amelyekkel új virtuális gépeket lehet létrehozni. Az előző lépések során a virtuális gépet a Windows Server 2016 Datacenter rendszerképének használatával hoztuk létre. Ebben a lépésben a PowerShell-modul segítségével további Windows-rendszerképeket keresünk a piactéren, amelyek az új virtuális gépek alapjául szolgálhatnak. Ez a folyamat a rendszerkép [azonosításához](cli-ps-findimage.md#terminology) szükséges közzétevő, ajánlat, termékváltozat és esetleg verziószám megkereséséből áll.

Használja a [Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) parancsot a rendszerkép-közzétevők listájának lekéréséhez:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Használja a [Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) a rendszerképajánlatok listájának lekéréséhez. Ezzel a paranccsal a lekért lista az adott közzétevőre lesz szűrve, amelynek neve `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Az eredmény az alábbi példában láthatóhoz hasonló lesz: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

A [Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) parancs majd szűrést a rendszerképnevek listájának lekéréséhez a közzétevő és az ajánlat nevét.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Az eredmény az alábbi példában láthatóhoz hasonló lesz: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Ezek az adatok egy virtuális gép adott rendszerképpel történő üzembe helyezéséhez használhatók. Ez a példa egy virtuális gépet helyez üzembe a tárolói rendszerképpel rendelkező Windows Server 2016 legfrissebb verziójának használatával.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Az `-AsJob` paraméter háttérfeladatként létrehozza a virtuális gépet, így használni tudja a PowerShell-parancssorokat. A háttérfeladatok részleteit a `Get-Job` parancsmaggal tekintheti meg.

## <a name="understand-vm-sizes"></a>Virtuális gépek méreteinek ismertetése

A virtuális gép mérete határozza meg a számítási erőforrások, például a Processzor, a grafikus Processzor és memória, amely a virtuális gép számára elérhetővé válnak. Virtuális gépek a számítási feladathoz megfelelő Virtuálisgép-mérettel kell létrehozni. Ha a számítási feladatok mennyisége nő, a meglévő virtuális gépet át is lehet méretezni.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Az alábbi táblázat a méreteket használati esetek alapján kategorizálja.  

| Typo                     | Gyakori méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Kiegyensúlyozott processzor-memória arány. Ideális választás fejlesztéshez/teszteléshez, valamint kis- és közepes méretű alkalmazásokhoz és adatkezelési megoldásokhoz.  |
| [Számításra optimalizált](sizes-compute.md)   | Fsv2, Fs, F             | Magas processzor-memória arány a processzor javára. Megfelelő választás a közepes forgalmú alkalmazásokhoz, hálózati berendezésekhez és kötegelt folyamatokhoz.        |
| [Memóriaoptimalizált](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Magas memória-mag arány a memória javára. Ideális választás relációs adatbázisokhoz, közepes és nagy gyorsítótárakhoz, memóriában végzett elemzésekhez.                 |
| [Tárolásra optimalizált](sizes-storage.md)      | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Specializált virtuális gépek nagy terhelést jelentő grafikus rendereléshez és videószerkesztéshez.       |
| [Nagy teljesítmény](sizes-hpc.md) | H        | Leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű hálózati adapterekkel (RDMA). |

### <a name="find-available-vm-sizes"></a>Elérhető virtuálisgép-méretek keresése

Egy adott régióban elérhető VM méretek megtekintéséhez használja a [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) parancsot.

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Virtuális gép átméretezése

A virtuális gépek az üzembe helyezésüket követően a rendelkezésre álló erőforrás-kiosztás növelése vagy csökkentése érdekében átméretezhetők.

A virtuális gép átméretezése előtt ellenőrizze a kívánt elérhető-e az aktuális virtuális gép fürtön. A [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) parancs a méretek listáját adja vissza.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

A méret érhető el, ha a virtuális gép is át lehet méretezni a bekapcsolt állapotban, azonban a művelet során újraindul.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Ha a kívánt méret nem érhető el az aktuális fürtön, a virtuális Gépet fel kell szabadítani az átméretezés végrehajtása előtt kell. A virtuális gép felszabadítása törli az ideiglenes lemezen található adatokat, és a nyilvános IP-cím is módosul, ha nem statikus IP-címet használ.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Virtuális gépek energiaállapotai

Számos energiaállapot van, amelyek közül az Azure-beli virtuális gépek felvesznek egyet. 


| Energiaállapot | Leírás
|----|----|
| Indítás | A virtuális gép indítása folyamatban van. |
| Fut | Még fut a virtuális gép. |
| Leállítás | A virtuális gép leáll. |
| Leállítva | A virtuális gép leállt. A leállított virtuális gépekért továbbra is díjat kell fizetni.  |
| Felszabadítás | A virtuális gép felszabadítása folyamatban van. |
| Felszabadítva | Azt jelzi, hogy a virtuális gép törlődik a hipervizor, de továbbra is elérhető a vezérlősík a. A `Deallocated` állapotban lévő virtuális gépekért nem kell díjat fizetni. |
| - | A virtuális gép energiaállapota ismeretlen. |


Egy adott virtuális gép állapotának lekérdezéséhez használja a [Get-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) parancsot. Ügyeljen arra, hogy érvényes nevet adjon meg a virtuális gép és az erőforráscsoport számára.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

A kimenet az alábbi példában láthatóhoz hasonló lesz:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Felügyeleti feladatok

Virtuális gép élettartama során érdemes futtatni a felügyeleti feladatokat, mint indítása, leállítása vagy törlése a virtuális gép. Emellett előfordulhat, hogy szkripteket is szeretne létrehozni az ismétlődő vagy összetett feladatok automatizálására. Az Azure PowerShell használatával számos gyakori felügyeleti feladat futtatható a parancssorból vagy szkriptek segítségével.

### <a name="stop-a-vm"></a>Virtuális gép leállítása

Leállítás és felszabadítás rendelkező virtuális gép [Stop-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm):

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Ha regisztrált állapotban szeretné tartani a virtuális gépet, használja a -StayProvisioned paramétert.

### <a name="start-a-vm"></a>Virtuális gép elindítása

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

Minden belül egy erőforráscsoport törlésekor a rendszer az erőforráscsoport törlése.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a virtuális gépek létrehozásának és kezelésének alapvető műveleteivel ismerkedett meg, például:

> [!div class="checklist"]
> * Virtuális gép létrehozása és csatlakozás a virtuális géphez
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Virtuális gép átméretezése
> * Virtuális gép állapotának áttekintése és értelmezése

Folytassa a következő oktatóanyaggal, amely a virtuálisgép-lemezeket ismerteti.  

> [!div class="nextstepaction"]
> [Virtuálisgép-lemezek létrehozása és kezelése](./tutorial-manage-data-disk.md)
