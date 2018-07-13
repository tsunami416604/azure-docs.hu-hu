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
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d47981042fc13a96bdf5cb9690e4dc83a6aa0162
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932551"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Oktatóanyag: Windows rendszerű virtuális gépek létrehozása és felügyelete Azure PowerShell-lel

Az Azure-beli virtuális gépek egy teljes mértékben konfigurálható és rugalmas számítási környezetet nyújtanak. Az oktatóanyag az Azure-beli virtuális gépek üzembe helyezésének alapvető elemeit ismerteti, például a virtuális gépek méretének és rendszerképeinek kiválasztását és a virtuális gépek telepítését. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gép létrehozása és csatlakozás a virtuális géphez
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Virtuális gép átméretezése
> * Virtuális gép állapotának áttekintése és értelmezése

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. A következő példában egy *myResourceGroupVM* nevű erőforráscsoportot hozunk létre az *EastUs* régióban:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

Az erőforráscsoport meghatározására a virtuális gép létrehozásakor vagy módosításakor kerül sor, ahogy ez az oktatóanyagból is kiderül.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Egy virtuális gép létrehozásakor több lehetőség is rendelkezésre áll, például az operációsrendszer-lemezkép, a hálózati konfiguráció vagy a rendszergazdai hitelesítő adatok. Ebben a példában létrehozunk egy *myVM* nevű virtuális gépet, amely a Windows Server 2016 Datacenter alapértelmezett, legújabb verzióját futtatja.

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

```azurepowershell-interactive
New-AzureRmVm `
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

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

Futtassa a következő parancsokat a virtuális gép nyilvános IP-címének visszaadásához. Jegyezze fel ezt az IP-címet, hogy egy későbbi lépésben csatlakozhasson majd hozzá a böngészőjében a webes kapcsolat tesztelése érdekében.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali kapcsolatot szeretne létrehozni a virtuális géppel. Cserélje le az IP-címet a virtuális gépe *publicIPAddress* címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```powershell
mstsc /v:<publicIpAddress>
```

A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a virtuális géphez létrehozott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

## <a name="understand-vm-images"></a>Virtuálisgép-rendszerképek ismertetése

Az Azure Marketplace-en számos virtuálisgép-rendszerkép található, amelyekkel új virtuális gépeket lehet létrehozni. Az előző lépések során a virtuális gépet a Windows Server 2016 Datacenter rendszerképének használatával hoztuk létre. Ebben a lépésben a PowerShell-modul segítségével további Windows-rendszerképeket keresünk a piactéren, amelyek az új virtuális gépek alapjául szolgálhatnak. Ez a folyamat a rendszerkép [azonosításához](cli-ps-findimage.md#terminology) szükséges közzétevő, ajánlat, termékváltozat és esetleg verziószám megkereséséből áll. 

Használja a [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) parancsot a rendszerkép-közzétevők listájának lekéréséhez:

```powershell
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Használja a [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) parancsot a rendszerképajánlatok listájának lekéréséhez. Ezzel a paranccsal a lekért lista az adott közzétevőre lesz szűrve:

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

A [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancs ezután a közzétevő és az ajánlat neve alapján szűr a rendszerképnevek listájának lekéréséhez.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
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

Ezek az adatok egy virtuális gép adott rendszerképpel történő üzembe helyezéséhez használhatók. A lenti példa egy virtuális gépet helyez üzembe a tárolói rendszerképpel rendelkező Windows Server 2016 legfrissebb verziójának használatával.

```azurepowershell-interactive
New-AzureRmVm `
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

A virtuális gép mérete a virtuális gép által elérhető számítási erőforrások, azaz a processzor, a grafikus processzor és a memória mennyiségét határozza meg. A virtuális gépeket a várt terhelésnek megfelelő méretben kell létrehozni. Ha a számítási feladatok mennyisége nő, a meglévő virtuális gépet át is lehet méretezni.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Az alábbi táblázat a méreteket használati esetek alapján kategorizálja.  
| Típus                     | Gyakori méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Kiegyensúlyozott processzor-memória arány. Ideális választás fejlesztéshez/teszteléshez, valamint kis- és közepes méretű alkalmazásokhoz és adatkezelési megoldásokhoz.  |
| [Számításra optimalizált](sizes-compute.md)   | Fs, F             | Magas processzor-memória arány a processzor javára. Megfelelő választás a közepes forgalmú alkalmazásokhoz, hálózati berendezésekhez és kötegelt folyamatokhoz.        |
| [Memóriaoptimalizált](sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Magas memória-mag arány a memória javára. Ideális választás relációs adatbázisokhoz, közepes és nagy gyorsítótárakhoz, memóriában végzett elemzésekhez.                 |
| [Tárolásra optimalizált](sizes-storage.md)      | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Specializált virtuális gépek nagy terhelést jelentő grafikus rendereléshez és videószerkesztéshez.       |
| [Nagy teljesítmény](sizes-hpc.md) | H, A8-11          | Leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű hálózati adapterekkel (RDMA). 


### <a name="find-available-vm-sizes"></a>Elérhető virtuálisgép-méretek keresése

Egy adott régióban elérhető virtuálisgép-méretek megtekintéséhez használja a [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) parancsot.

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Virtuális gép átméretezése

A virtuális gépek az üzembe helyezésüket követően a rendelkezésre álló erőforrás-kiosztás növelése vagy csökkentése érdekében átméretezhetők.

A virtuális gép átméretezése előtt ellenőrizze, hogy a kívánt méret az aktuális virtuálisgép-fürtön elérhető-e. A [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) parancs kiadja a méretek listáját. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Ha a kívánt méret elérhető, a virtuális gépet bekapcsolt állapotban is át lehet méretezni, de a művelet során újraindul.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

Ha a kívánt méret nem érhető el az aktuális fürtön, a virtuális gépet fel kell szabadítani az átméretezés végrehajtása előtt. Vegye figyelembe, hogy a virtuális gép visszakapcsolásakor az ideiglenes lemezen lévő adatok törlődnek, és a nyilvános IP-cím is módosul, ha nem statikus IP-címet használ. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>Virtuális gépek energiaállapotai

Számos energiaállapot van, amelyek közül az Azure-beli virtuális gépek felvesznek egyet. Ez az állapot a virtuális gép aktuális állapotát tükrözi a hipervizor szempontjából. 

### <a name="power-states"></a>Energiaállapotok

| Energiaállapot | Leírás
|----|----|
| Indítás | Azt jelzi, hogy a virtuális gép indítása folyamatban van. |
| Fut | Azt jelzi, hogy a virtuális gép fut. |
| Leállítás | Azt jelzi, hogy a virtuális gép leáll. | 
| Leállítva | Azt jelzi, hogy a virtuális gép leállt. Vegye figyelembe, hogy a leállított virtuális gépekért továbbra is díjat kell fizetni.  |
| Felszabadítás | Azt jelzi, hogy a virtuális gép felszabadítása folyamatban van. |
| Felszabadítva | Azt jelzi, hogy a virtuális gép teljesen el lett távolítva a hipervizorról, a vezérlősíkon azonban továbbra is elérhető. A felszabadított állapotban lévő virtuális gépekért nem kell díjat fizetni. |
| - | Azt jelzi, hogy a virtuális gép energiaállapota ismeretlen. |

### <a name="find-power-state"></a>Energiaállapot keresése

Egy adott virtuális gép állapotát a [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) paranccsal kérheti le. Ügyeljen arra, hogy egy érvényes nevet adjon meg a virtuális géphez és az erőforráscsoporthoz. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Kimenet:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Felügyeleti feladatok

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Emellett előfordulhat, hogy szkripteket is szeretne létrehozni az ismétlődő vagy összetett feladatok automatizálására. Az Azure PowerShell használatával számos gyakori felügyeleti feladat futtatható a parancssorból vagy szkriptek segítségével.

### <a name="stop-virtual-machine"></a>Virtuális gép leállítása

A virtuális gépet a [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) paranccsal állíthatja le és szabadíthatja fel:

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

Ha regisztrált állapotban szeretné tartani a virtuális gépet, használja a -StayProvisioned paramétert.

### <a name="start-virtual-machine"></a>Virtuális gép indítása

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

Egy erőforráscsoport törlése az összes benne lévő erőforrást is törli.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
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
