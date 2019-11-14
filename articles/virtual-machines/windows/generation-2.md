---
title: Azure-támogatás a 2. generációs virtuális gépekhez
description: A 2. generációs virtuális gépek Azure-támogatásának áttekintése
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/04/2019
ms.author: lahugh
ms.openlocfilehash: d6fcbcb7f5d1b6bb25840ad1bddc5327b068c15d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075467"
---
# <a name="support-for-generation-2-vms-on-azure"></a>2\. generációs virtuális gépek támogatása az Azure-ban

A 2. generációs virtuális gépek (VM-EK) támogatása már elérhető az Azure-ban. A virtuális gép generációját a létrehozása után nem módosíthatja, ezért a létrehozás előtt tekintse át az ezen a lapon található szempontokat.

A 2. generációs virtuális gépek támogatják az 1. generációs virtuális gépek által nem támogatott főbb funkciókat. A szolgáltatások közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és a virtualizált állandó memória (vPMEM). A 2. generációs virtuális gépek a helyszínen futnak, az Azure-ban még nem támogatott funkciók vannak. További információ: [szolgáltatások és képességek](#features-and-capabilities) szakasz.

A 2. generációs virtuális gépek az új UEFI-alapú rendszerindítási architektúrát használják az 1. generációs virtuális gépek által használt BIOS-alapú architektúra helyett. Az 1. generációs virtuális gépekhez képest a 2. generációs virtuális gépeknél a rendszerindítási és a telepítési idő is javult. A 2. generációs virtuális gépek, valamint az 1. és a 2. generáció közötti különbségek némelyikének áttekintését lásd: [1. vagy 2. generációs virtuális gépek létrehozása a Hyper-V-ben?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>2\. generációs VM-méretek

Az 1. generációs virtuális gépeket minden virtuálisgép-méret támogatja az Azure-ban. Az Azure immár 2. generációs támogatást nyújt a következő kiválasztott virtuálisgép-sorozatokhoz:

* [B sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DC sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) és [Dsv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [HC sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* Az [ls-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) és a [Lsv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* [NCv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) és [NCv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [ND sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [NVv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

> [!NOTE]
> A 2. generációs virtuálisgép-lemezképek használata a Mv2 sorozatú virtuális gépek esetében általánosan elérhető, mivel a Mv2 sorozat csak a 2. generációs virtuálisgép-rendszerképekkel működik együtt. Az 1. generációs virtuálisgép-lemezképek nem támogatottak a Mv2 sorozatú virtuális gépeken. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>2\. generációs VM-lemezképek az Azure Marketplace-en

A 2. generációs virtuális gépek a következő Piactéri rendszerképeket támogatják:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19,04, 19,10
* RHEL 8,0


## <a name="on-premises-vs-azure-generation-2-vms"></a>Helyszíni és Azure 2. generációs virtuális gépek

Az Azure jelenleg nem támogatja a 2. generációs virtuális gépekhez a helyszíni Hyper-V által támogatott funkciókat.

| 2\. generációs funkció                | Helyszíni Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Biztonságos rendszerindítás                         | :heavy_check_mark:  | x   |
| Védett virtuális gép                         | :heavy_check_mark:  | x   |
| vTPM                                | :heavy_check_mark:  | x   |
| Virtualizálás-alapú biztonság (VBS) | :heavy_check_mark:  | x   |
| VHDX formátuma                         | :heavy_check_mark:  | x   |

## <a name="features-and-capabilities"></a>Funkciók és képességek

### <a name="generation-1-vs-generation-2-features"></a>1\. generációs, 2. generációs funkciók

| Funkció | 1\. generáció | 2\. generáció |
|---------|--------------|--------------|
| Rendszerindítás             | PCAT                      | UEFI                               |
| Lemezvezérlő-vezérlők | IDE                       | SCSI                               |
| A virtuális gépek mérete         | Minden VM-méret | Csak a Premium Storage-t támogató virtuális gépek |

### <a name="generation-1-vs-generation-2-capabilities"></a>1\. generációs, 2. generációs képességek

| Képesség | 1\. generáció | 2\. generáció |
|------------|--------------|--------------|
| OPERÁCIÓSRENDSZER-lemez > 2 TB                    | x                | :heavy_check_mark: |
| Egyéni lemez/lemezkép/swap operációs rendszer         | :heavy_check_mark: | :heavy_check_mark: |
| Virtuálisgép-méretezési csoport támogatása | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Biztonsági mentés/visszaállítás                    | :heavy_check_mark: | :heavy_check_mark: |
| Közös Képtár              | :heavy_check_mark: | :heavy_check_mark: |
| Azure Disk Encryption             | :heavy_check_mark: | x                |

## <a name="creating-a-generation-2-vm"></a>2\. generációs virtuális gép létrehozása

### <a name="marketplace-image"></a>Piactéri rendszerkép

A Azure Portal vagy az Azure CLI-ben létrehozhat 2. generációs virtuális gépeket az UEFI rendszerindítást támogató piactér-rendszerképből.

#### <a name="azure-portal"></a>Azure Portal

A 2. generációs rendszerképek a Windows-és a SLES a Gen1-lemezképekkel megegyező kiszolgálói ajánlatban szerepelnek. A folyamat szempontjából ez azt jelenti, hogy az ajánlatot és az SKU-t a virtuális gép portálján kell kiválasztani. Ha az SKU az 1. és a 2. generációs rendszerképeket is támogatja, kiválaszthatja, hogy létrehoz egy 2. generációs virtuális gépet a virtuális gép létrehozási folyamatának *speciális* lapján.

Jelenleg a következő SKU-támogatás az 1. és a 2. generációs rendszerképeket támogatja:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

Ha a Windows Server SKU-t ajánlatként választja, a **speciális** lapon lehetőség van egy **Gen 1** (BIOS) vagy **Gen 2** (UEFI) virtuális gép létrehozására. Ha a **2**. generációs lehetőséget választja, győződjön meg arról, hogy a 2. [generációs virtuális gépeknél](#generation-2-vm-sizes)az **alapok** lapon kiválasztott virtuálisgép-méret támogatott.

![Válassza ki az 1. generációs vagy a 2. generációs virtuális gépet](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

A PowerShell használatával is létrehozhatja a virtuális gépet úgy, hogy közvetlenül az 1. generációs vagy 2. generációs SKU-ra hivatkozik.

Például a következő PowerShell-parancsmaggal kérheti le a `WindowsServer` ajánlatban szereplő SKU-ket.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Ha operációs rendszert futtató virtuális gépet hoz létre a Windows Server 2012-es verziójával, akkor az 1. generáció (BIOS) vagy a 2. generációs (UEFI) VM SKU lesz kiválasztva, amely a következőképpen néz ki:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

A támogatott Piactéri rendszerképek aktuális listáját a [szolgáltatások és képességek](#features-and-capabilities) című szakaszban találja.

### <a name="managed-image-or-managed-disk"></a>Felügyelt lemezkép vagy felügyelt lemez

A 2. generációs virtuális gépeket ugyanúgy hozhatja létre felügyelt lemezképből vagy felügyelt lemezről, mint az 1. generációs virtuális gépeket.

### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

A 2. generációs virtuális gépeket virtuálisgép-méretezési csoportok használatával is létrehozhatja. Az Azure CLI-ben a 2. generációs virtuális gépek létrehozásához használja az Azure-méretezési csoportokat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **A 2. generációs virtuális gépek az összes Azure-régióban elérhetők?**  
    Igen. A 2. [generációs VM-méretek](#generation-2-vm-sizes) azonban nem minden régióban érhetők el. A 2. generációs virtuális gép rendelkezésre állása a virtuális gép méretétől függ.

* **Van-e árkülönbség az 1. és 2. generációs virtuális gépek között?**  
   Nem.

* **Van egy. vhd-fájlom a helyszíni 2. generációs virtuális gépről. Használhatom ezt a. vhd-fájlt egy 2. generációs virtuális gép létrehozásához az Azure-ban?**
  Igen, a 2. vhd-fájlt átviheti az Azure-ba, és ezzel létrehozhatja a 2. generációs virtuális gépeket. A következő lépések végrehajtásával teheti meg:
    1. Töltse fel a. vhd fájlt egy olyan Storage-fiókba, amely ugyanabban a régióban található, ahol létre szeretné hozni a virtuális gépet.
    1. Hozzon létre egy felügyelt lemezt a. VHD fájlból. Állítsa a Hyper-V Generation tulajdonságot v2-re. A következő PowerShell-parancsok a Hyper-V Generation tulajdonságot a felügyelt lemez létrehozásakor határozzák meg.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Ha a lemez elérhetővé válik, hozzon létre egy virtuális gépet a lemez csatolásával. A létrehozott virtuális gép egy 2. generációs virtuális gép lesz.
    Ha létrehozta a 2. generációs virtuális gépet, lehetősége van a virtuális gép rendszerképének általánosítására. A rendszerkép általánosítása használatával több virtuális gépet is létrehozhat.

* **Hogyan az operációsrendszer-lemez méretének növelését?**  
  A 2 TB-nál nagyobb operációsrendszer-lemezek újak a 2. generációs virtuális gépekhez. Alapértelmezés szerint az operációsrendszer-lemezek 2 TB-nál kisebbek a 2. generációs virtuális gépeknél. A lemez mérete legfeljebb 4 TB ajánlott lehet. Az operációsrendszer-lemez méretének növeléséhez használja az Azure CLI-t vagy a Azure Portal. További információ a lemezek programozott kibontásáról: [lemez átméretezése](expand-os-disk.md).

  Az operációsrendszer-lemez méretének növeléséhez a Azure Portal:

  1. A Azure Portal nyissa meg a virtuális gép tulajdonságai lapot.
  1. A virtuális gép leállításához és felszabadításához kattintson a **Leállítás** gombra.
  1. A **lemezek** szakaszban válassza ki a bővíteni kívánt operációsrendszer-lemezt.
  1. A **lemezek** szakaszban válassza a **Konfigurálás**lehetőséget, majd frissítse a **méretet** a kívánt értékre.
  1. Lépjen vissza a virtuális gép tulajdonságai lapra, és **indítsa el** a virtuális gépet.
  
  Előfordulhat, hogy a 2 TB-nál nagyobb operációsrendszer-lemezekre vonatkozó figyelmeztetés jelenik meg. A figyelmeztetés nem vonatkozik a 2. generációs virtuális gépekre. A 4 TB-nál nagyobb méretű operációsrendszer-lemezek azonban *nem ajánlottak.*

* **A 2. generációs virtuális gépek támogatják a gyorsított hálózatkezelést?**  
    Igen. További információ: [virtuális gép létrehozása gyorsított hálózatkezeléssel](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **A VHDX támogatott a 2. generáción?**  
    Nem, a 2. generációs virtuális gépek csak VHD-t támogatnak.

* **A 2. generációs virtuális gépek támogatják az Azure Ultra Disk Storaget?**  
    Igen.

* **Áttelepíthetek egy virtuális gépet az 1. generációról a 2. generációra?**  
    Nem, a létrehozás után nem módosítható a virtuális gép generációja. Ha váltania kell a virtuálisgép-generációk között, hozzon létre egy másik generációs új virtuális gépet.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a [2. generációs virtuális gépekkel a Hyper-V-ben](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Megtudhatja, hogyan [készítheti elő a virtuális merevlemezeket](prepare-for-upload-vhd-image.md) a helyszíni rendszerekről az Azure-ba való feltöltéshez.
