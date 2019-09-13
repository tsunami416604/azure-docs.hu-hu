---
title: Azure-támogatás a 2. generációs virtuális gépekhez (előzetes verzió) | Microsoft Docs
description: A 2. generációs virtuális gépek Azure-támogatásának áttekintése
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/10/2019
ms.author: lahugh
ms.openlocfilehash: 5dbd13775bd91a2bab3a7a4989cb14f4d7b44fa8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900718"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>2\. generációs virtuális gépek (előzetes verzió) támogatása az Azure-ban

> [!IMPORTANT]
> A 2. generációs virtuális gépekhez tartozó Azure-támogatás jelenleg előzetes verzióban érhető el.
> Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A 2. generációs virtuális gépek (VM-EK) támogatása már előzetes verzióban érhető el az Azure-ban. A virtuális gép generációját a létrehozása után nem módosíthatja, ezért a létrehozás előtt tekintse át az ezen a lapon található szempontokat.

A 2. generációs virtuális gépek támogatják az 1. generációs virtuális gépek által nem támogatott főbb funkciókat. A szolgáltatások közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és a virtualizált állandó memória (vPMEM). A 2. generációs virtuális gépek olyan szolgáltatásokat is tartalmazhatnak, amelyek még nem támogatottak az Azure-ban. További információ: [szolgáltatások és képességek](#features-and-capabilities) szakasz.

A 2. generációs virtuális gépek az új UEFI-alapú rendszerindítási architektúrát használják az 1. generációs virtuális gépek által használt BIOS-alapú architektúra helyett. Az 1. generációs virtuális gépekhez képest a 2. generációs virtuális gépeknél a rendszerindítási és a telepítési idő is javult. A 2. generációs virtuális gépek, valamint az 1. és a 2. generáció közötti különbségek némelyikének áttekintését lásd: [1. vagy 2. generációs virtuális gépek létrehozása a Hyper-V-ben?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>2\. generációs VM-méretek

Az 1. generációs virtuális gépeket minden virtuálisgép-méret támogatja az Azure-ban. Az Azure mostantól 2. generációs támogatást nyújt a következő kiválasztott virtuálisgép-sorozatokhoz:

* [B sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dc-series)
* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) és [Dsv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hb-series)
* [HC sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hc-series)
* Az [ls-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) és a [Lsv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [NCv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series) és [NCv3 sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [ND sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)
* [NVv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>2\. generációs VM-lemezképek az Azure Marketplace-en

A 2. generációs virtuális gépek a következő Piactéri rendszerképeket támogatják:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4

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
| OPERÁCIÓSRENDSZER-lemez > 2 TB                    | x                        | :heavy_check_mark: |
| Egyéni lemez/lemezkép/swap operációs rendszer         | :heavy_check_mark:         | :heavy_check_mark: |
| Virtuálisgép-méretezési csoport támogatása | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/biztonsági mentés                        | :heavy_check_mark:         | x                |
| Megosztott lemezképkatalógus              | :heavy_check_mark:         | x                |
| Azure Disk Encryption             | :heavy_check_mark:         | x                |

## <a name="creating-a-generation-2-vm"></a>2\. generációs virtuális gép létrehozása

### <a name="marketplace-image"></a>Piactéri rendszerkép

A Azure Portal vagy az Azure CLI-ben létrehozhat 2. generációs virtuális gépeket az UEFI rendszerindítást támogató piactér-rendszerképből.

Az `windowsserver-gen2preview` ajánlat csak a Windows 2. generációs lemezképeit tartalmazza. Ez a csomagolás elkerüli az 1. generáció és a 2. generációs képek összekeveredését. A 2. generációs virtuális gépek létrehozásához válassza az ajánlat **képei** lehetőséget, majd a normál eljárást követve hozza létre a virtuális gépet.

A piactér jelenleg a következő Windows 2. generációs rendszerképeket kínálja:

* 2019-datacenter-gen2
* 2016 – Datacenter – Gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

A támogatott Piactéri rendszerképek aktuális listáját a [szolgáltatások és képességek](#features-and-capabilities) című szakaszban találja.

### <a name="managed-image-or-managed-disk"></a>Felügyelt lemezkép vagy felügyelt lemez

A 2. generációs virtuális gépeket ugyanúgy hozhatja létre felügyelt lemezképből vagy felügyelt lemezről, mint az 1. generációs virtuális gépeket.

### <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok

A 2. generációs virtuális gépeket virtuálisgép-méretezési csoportok használatával is létrehozhatja. Az Azure CLI-ben a 2. generációs virtuális gépek létrehozásához használja az Azure-méretezési csoportokat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **A 2. generációs virtuális gépek az összes Azure-régióban elérhetők?**  
    Igen. A 2. [generációs VM-méretek](#generation-2-vm-sizes) azonban nem minden régióban érhetők el. A 2. generációs virtuális gép rendelkezésre állása a virtuális gép méretétől függ.

* **Van-e árkülönbség az 1. és 2. generációs virtuális gépek között?**  
    Nem.

* **Van egy. vhd-fájlom a helyszíni 2. generációs virtuális gépről. Használhatom ezt a. vhd-fájlt egy 2. generációs virtuális gép létrehozásához az Azure-ban?**
  Igen, a 2. vhd-fájlt átviheti az Azure-ba, és ezzel létrehozhatja a 2. generációs virtuális gépeket. A következő lépések végrehajtásával teheti meg:
    1. Töltse fel a. vhd fájlt egy olyan Storage-fiókba, amely ugyanabban a régióban található, ahol létre szeretné hozni a virtuális gépet.
    1. Hozzon létre egy felügyelt lemezt a. VHD fájlból. Állítsa a HyperV Generation tulajdonságot a v2 értékre. A következő PowerShell-parancsok HyperV generálási tulajdonságot állítanak be a felügyelt lemez létrehozásakor.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Ha a lemez elérhetővé válik, hozzon létre egy virtuális gépet a lemez csatolásával. A létrehozott virtuális gép egy 2. generációs virtuális gép lesz.
    Ha létrehozta a 2. generációs virtuális gépet, lehetősége van a virtuális gép rendszerképének általánosítására. A rendszerkép általánosításával több virtuális gép létrehozására is használható.

* **Hogyan az operációsrendszer-lemez méretének növelését?**  
  A 2 TB-nál nagyobb operációsrendszer-lemezek újak a 2. generációs virtuális gépekhez. Alapértelmezés szerint az operációsrendszer-lemezek 2 TB-nál kisebbek a 2. generációs virtuális gépeknél. A lemez mérete legfeljebb 4 TB ajánlott lehet. Az operációsrendszer-lemez méretének növeléséhez használja az Azure CLI-t vagy a Azure Portal. További információ a lemezek programozott kibontásáról: [lemez átméretezése](expand-disks.md).

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
