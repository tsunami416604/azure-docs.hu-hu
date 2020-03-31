---
title: Az Azure támogatása a 2.
description: Az Azure-támogatás áttekintése a 2.
author: ju-shim
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: b68179caed4df5efd3696d7d6a3739a1e07a3c80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267299"
---
# <a name="support-for-generation-2-vms-on-azure"></a>2.000.000 virtuális gép támogatása az Azure-ban

A 2. generációs virtuális gépek (VM-ek) támogatása már elérhető az Azure-ban. A virtuális gépek generációját nem módosíthatja a létrehozásután, ezért tekintse át a lapon található szempontokat, mielőtt egy generációt választana.

A 2. generációs virtuális gépek támogatják azokat a kulcsfontosságú funkciókat, amelyeket az 1. Ezek közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX) és a virtualizált állandó memória (vPMEM). A helyszínen futó 2. További információt a [Szolgáltatások és képességek](#features-and-capabilities) című részben talál.

A 2. generációs virtuális gépek az új UEFI-alapú rendszerindítási architektúrát használják az 1. Az 1. generációs virtuális gépekhez képest a 2. A 2. generációs virtuális gépek áttekintését és az [1.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>Generációs 2 Virtuálisgép-méretek

Az 1. generációs virtuális gépeket az Azure összes virtuálisgép-mérete támogatja (kivéve az Mv2 sorozatú virtuális gépeket). Az Azure most antól 2.

* [B-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC sorozat](../dcv2-series.md)
* [DSv2 és](../dv2-dsv2-series.md) [Dsv3 sorozatú](../dv3-dsv3-series.md)
* [Esv3-sorozat](../ev3-esv3-series.md)
* [Fsv2 sorozat](../fsv2-series.md)
* [GS-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB-sorozat](../hb-series.md)
* [HC-sorozat](../hc-series.md)
* [Ls-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) és [Lsv2-sorozat](../lsv2-series.md)
* [Mv2 sorozat](../mv2-series.md)
* [NCv2 és](../ncv2-series.md) [NCv3 sorozat](../ncv3-series.md)
* [ND sorozat](../nd-series.md)
* [NVv3 sorozat](../nvv3-series.md)

> [!NOTE]
> A generációs 2 virtuálisgép-lemezképek használata az Mv2 sorozatú virtuális gépekhez általában elérhető, mivel az Mv2 sorozat kizárólag a 2. Generációs 1 virtuális gép lemezképek nem támogatottak mv2 sorozatú virtuális gépek. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>2. generációs virtuálisgép-lemezképek az Azure Marketplace-en

A 2.generációs virtuális gépek a következő Marketplace-lemezképeket támogatják:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Cent OS 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>A helyszíni és az Azure 2-es generációs virtuális gépeivel szemben

Az Azure jelenleg nem támogatja a helyszíni Hyper-V által a 2-es generációs virtuális gépekhez támogatott funkciók at.

| 2. generációs funkció                | Helyszíni Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Biztonságos rendszerindítás                         | :heavy_check_mark:  | :x:   |
| Védett virtuális gép                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Virtualizáción alapuló biztonság (VBS) | :heavy_check_mark:  | :x:   |
| VHDX formátum                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funkciók és képességek

### <a name="generation-1-vs-generation-2-features"></a>1. generációs és 2.

| Szolgáltatás | 1. generációs | 2. generációs |
|---------|--------------|--------------|
| Csomagtartó             | PCAT         | Uefi |
| Lemezvezérlők | IDE          | SCSI |
| A virtuális gépek mérete         | Minden virtuális gép méret | Csak a prémium szintű tárolást támogató virtuális gépek |

### <a name="generation-1-vs-generation-2-capabilities"></a>1. generációs és 2.

| Képesség | 1. generációs | 2. generációs |
|------------|--------------|--------------|
| Operációs rendszer > 2 TB                    | :x:                | :heavy_check_mark: |
| Egyéni lemez/kép/csere operációs rendszer         | :heavy_check_mark: | :heavy_check_mark: |
| Virtuálisgép-méretezési csoport támogatása | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Biztonsági mentés/visszaállítás                    | :heavy_check_mark: | :heavy_check_mark: |
| Megosztott képgaléria              | :heavy_check_mark: | :heavy_check_mark: |
| Azure lemeztitkosítás             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>2. generációs virtuális gép létrehozása

### <a name="marketplace-image"></a>Piactér képe

Az Azure Portalon vagy az Azure CLI-ben létrehozhat 2.

#### <a name="azure-portal"></a>Azure portál

Az alábbiakban a lépéseket, hogy hozzon létre egy generációs 2 (Gen2) virtuális gép az Azure Portalon.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Válassza az **Erőforrás létrehozása** lehetőséget.
1. Kattintson az **összes megtekintése** az Azure Piactéren a bal oldalon.
1. Jelöljön ki egy olyan lemezképet, amely támogatja a Gen2 programot.
1. Kattintson **a Létrehozás gombra.**
1. A **Speciális** lapon a **Virtuálisgép létrehozása** szakaszban válassza a **Gen 2** lehetőséget.
1. Az **Alapok lapon** a **Példány részletei**csoportban nyissa meg a **Méret** lehetőséget, és nyissa meg a **Virtuálisgép méretének kiválasztása** panelt.
1. Válasszon egy [támogatott 2.](#generation-2-vm-sizes)
1. A virtuális gép létrehozásának befejezéséhez az [Azure Portal létrehozási folyamata.](quick-create-portal.md)

![Válassza a Gen 1 vagy a Gen 2 Virtuális gép lehetőséget](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

A PowerShell segítségével virtuális gép létrehozása közvetlenül hivatkozva az 1.

Például a következő PowerShell-parancsmag használatával az `WindowsServer` ajánlatban szereplő sk-ek listájának lekérni.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Másik lehetőségként az Azure CLI segítségével megtekintheti a **Publisher**által felsorolt elérhető 2.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Ha windows Server 2012 operációs rendszerrel hoz létre virtuális gépet, akkor az 1.

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Tekintse meg a [Funkciók és képességek](#features-and-capabilities) szakaszban a támogatott Marketplace-képek aktuális listáját.

### <a name="managed-image-or-managed-disk"></a>Felügyelt lemezkép vagy felügyelt lemez

Létrehozhat egy 2.

### <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok

A 2.generációs virtuális gépeket virtuálisgép-méretezési készletek használatával is létrehozhatja. Az Azure CLI-ben azure-méretezési készletek használatával hozzon létre 2.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **A 2. generációs virtuális gépek minden Azure-régióban elérhetők?**  
    Igen. De nem minden [generációs 2 virtuális gép méret](#generation-2-vm-sizes) érhető el minden régióban. A 2.generációs virtuális gép rendelkezésre állása a virtuális gép méretének rendelkezésre állásától függ.

* **Van-e árkülönbség az 1.**  
    Nem.

* **A helyszíni 2-es generációs 2 virtuális gépről származó .vhd fájllal rendelkezem. Használhatom ezt a .vhd fájlt egy 2.**
  Igen, a 2.vhd generációs fájlt az Azure-ba viheti, és ezzel létrehozhat egy 2. Ehhez tegye a következő lépéseket:
    1. Töltse fel a .vhd egy tárfiók ugyanabban a régióban, ahol szeretné létrehozni a virtuális gép.
    1. Felügyelt lemez létrehozása a .vhd fájlból. Állítsa a Hyper-V Generation tulajdonságot V2-re. A következő PowerShell-parancsok a Hyper-V Generation tulajdonságot állították be felügyelt lemez létrehozásakor.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Miután a lemez elérhetővé válik, hozzon létre egy virtuális gép csatlakoztatásával a lemezt. A létrehozott virtuális gép egy 2.
    A 2.generációs virtuális gép létrehozásakor tetszés szerint általánosíthatja a virtuális gép lemezképét. A lemezkép általánosításával több virtuális gépet hozhat létre.

* **Hogyan növelhetem az operációs rendszer lemezméretét?**  
  A 2 TB-nál nagyobb operációsrendszer-lemezek újak a 2. Alapértelmezés szerint az operációs rendszer lemezei kisebbek, mint 2 TB a 2. A lemez méretét legfeljebb 4 TB-ra növelheti. Az Azure CLI vagy az Azure Portal használatával növelheti az operációs rendszer lemezméretét. A lemezek programozott kibontásáról a [Lemez átméretezése](expand-disks.md)című témakörben talál további információt.

  Az operációs rendszer lemezméretének növelése az Azure Portalról:

  1. Az Azure Portalon nyissa meg a virtuális gép tulajdonságai lapot.
  1. A virtuális gép leállításához és felszabadításának megszüntetéséhez kattintson a **Leállítás** gombra.
  1. A **Lemezek csoportban** jelölje ki a növelni kívánt operációsrendszert.
  1. A **Lemezek csoportban** válassza a **Konfiguráció**lehetőséget, és frissítse a **Méretet** a kívánt értékre.
  1. Lépjen vissza a virtuális gép tulajdonságai lapra, és **indítsa el** a virtuális gép.

  Előfordulhat, hogy 2 TB-nál nagyobb operációsrendszer-lemezekre vonatkozó figyelmeztetés jelenik meg. A figyelmeztetés nem vonatkozik a 2. A 4 TB-nál nagyobb operációs rendszer lemezmérete azonban *nem ajánlott.*

* **A 2-es generációs virtuális gépek támogatják a gyorsított hálózatkezelést?**  
    Igen. További információ: [Virtuális gép létrehozása gyorsított hálózatkezeléssel.](../../virtual-network/create-vm-accelerated-networking-cli.md)

* **A VHDX támogatott a 2.**  
    Nem, a 2.generációs virtuális gépek csak a virtuális merevlemezt támogatják.

* **A 2-es generációs virtuális gépek támogatják az Azure Ultra Disk Storage-ot?**  
    Igen.

* **Áttelepíthetem a virtuális gépeket az 1-es generációról a 2-es generációra?**  
    Nem, a virtuális gép létrehozása után nem módosíthatja a létrehozását. Ha váltania kell a virtuálisgép-generációk között, hozzon létre egy új virtuális gép egy másik generáció.

* **Miért nincs engedélyezve a virtuális gép mérete a méretválasztóban, amikor gen2 virtuális gép et próbálok létrehozni?**

    Ez a következőkkel oldható meg:

    1. Ellenőrizze, hogy a **virtuális gép létrehozási** tulajdonsága **Gen 2-re** **van-e** állítva a Speciális lapon.
    1. Ellenőrizze, hogy olyan virtuális gépméretet keres-e, [amely támogatja a Gen2 virtuális gépeket.](#generation-2-vm-sizes)

## <a name="next-steps"></a>További lépések

* Ismerje meg a [2 generációs virtuális gépeket a Hyper-V-ben.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
