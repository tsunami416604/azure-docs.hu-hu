---
title: 2\. generációs virtuális gépek (előzetes verzió) az Azure-ban |} A Microsoft Docs
description: Az Azure a 2. generációs virtuális gépek – áttekintés
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 183e2144317bf3f1c9a60443d393bdcb3fd7c04a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66390551"
---
# <a name="generation-2-vms-preview-on-azure"></a>2\. generációs virtuális gépek (előzetes verzió) az Azure-ban

> [!IMPORTANT]
> 2\. generációs virtuális gépek jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

2\. generációs virtuális gépek (VM) támogatása mostantól elérhető nyilvános előzetes verzióban elérhető az Azure-ban. Egy virtuálisgép-generáció azt létrehozását követően nem módosítható. Ezért azt javasoljuk, hogy tekintse át a szempontok [Itt](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) és a generáció kiválasztása előtt ezen az oldalon az információkat.

2\. generációs virtuális gépek támogatási fő funkcióját nem támogatottak az 1. generációs virtuális gépek, mint például: memória, az Intel® szoftver Guard Extensions (SGX) és a virtuális állandó memóriával (vPMEM). 2\. generációs virtuális gépek is, hogy néhány funkció nem támogatott még az Azure-ban. További információkért lásd: a [funkciók és képességek](#features-and-capabilities) szakaszban.

2\. generációs virtuális gépek használata az új rendszerindítási UEFI-alapú architektúra és a BIOS-alapú architektúra, 1. generációs virtuális gépek által használt. 1\. generációs virtuális gépekhez képest, a 2. generációs virtuális gépek előfordulhat, hogy javult rendszerindítási és telepítési. 2\. generációs virtuális gépek áttekintése és néhány lényeges különbség az 1. és 2. generációs: [kell hozhatok létre 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>2\. generációs virtuális gépek mérete

1\. generációs virtuális gépek az Azure-beli Virtuálisgép-méretek támogatottak. Az Azure kínálja a következő kiválasztott Virtuálisgép-sorozatok nyilvános előzetes verzióban elérhető 2. generációs támogatása:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) és [Dsv3-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Az Esv3-adatsorok](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Ls-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) és [Lsv2-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2-sorozat](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>2\. generációs Virtuálisgép-rendszerképek az Azure Marketplace-en

2\. generációs virtuális gépek támogatják a következő Azure Marketplace-rendszerképek:

* 2019\. Windows server Datacenter
* Windows server 2016 Datacenter
* Windows server 2012 R2 Datacenter
* Windows server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>A helyszíni és az Azure a 2. generációs virtuális gépek

Az Azure jelenleg nem támogatja, amelyek a helyszíni Hyper-V támogatja a 2. generációs-beli virtuális gépek funkcióit.

| 2\. generációs funkció                | A helyszíni Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| A biztonságos rendszerindítás                         | :heavy_check_mark:  | :x:   |
| Védett virtuális gép                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| A virtualizálás-alapú biztonság (VBS) | :heavy_check_mark:  | :x:   |
| VHDX formátum                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funkciók és képességek

### <a name="generation-1-vs-generation-2-features"></a>Az 1. és 2. generációs funkciók

| Funkció | Az 1. | 2\. generációs |
|---------|--------------|--------------|
| Rendszerindítás             | PCAT                      | UEFI                               |
| Lemez vezérlők | IDE                       | SCSI                               |
| A virtuális gépek mérete         | Elérhető Virtuálisgép-méretek | A Premium storage virtuális gépek csak támogatott |

### <a name="generation-1-vs-generation-2-capabilities"></a>Az 1. és 2. generációs képességek

| Képesség | Az 1. | 2\. generációs |
|------------|--------------|--------------|
| > 2 TB az operációs rendszer lemezén                    | :x:                        | :heavy_check_mark: |
| Egyéni lemez vagy rendszerkép/felcserélés operációs rendszer         | :heavy_check_mark:         | :heavy_check_mark: |
| Virtuális gép méretezési támogatása | :heavy_check_mark:         | :heavy_check_mark: |
| Az ASR/biztonsági mentés                        | :heavy_check_mark:         | :x:                |
| Megosztott rendszerkép-katalógus              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Létrehozását a 2. generációs virtuális gép

### <a name="marketplace-image"></a>Piactéri lemezképhez

2\. generációs virtuális gépek a marketplace-lemezképből (mely támogatja a UEFI-rendszerindítás) az Azure Portalon vagy az Azure CLI-n keresztül lehet létrehozni.

A `windowsserver-gen2preview` ajánlat csak a Windows a 2. generációs lemezképet tartalmaz. Ezzel elkerülhető a keveredési meg az 1. és 2. generációs lemezképek. Generation 2 virtuális gép létrehozásához válassza **lemezképek** Ez az ajánlat, és kövesse a normál virtuális gép létrehozási folyamata.

Jelenleg a következő Windows-generation 2 lemezképek közzétett az Azure piactéren:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

További tájékoztatást képességek listáját támogatott marketplace-rendszerképek, továbbra is, amelyek támogatják a 2. generációs további lemezképek hozzáadása.

### <a name="managed-image-or-managed-disk"></a>Felügyelt lemezkép vagy felügyelt lemez

2 virtuális gépen hozható létre felügyelt lemezkép vagy felügyelt lemez ugyanúgy akkor kell létrehoznia egy az 1. generációs virtuális Gépet.

### <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok

Generáció, 2 virtuális gépen a virtual machine scale sets használatával is létrehozhatók. Generation 2 virtuális gép használata az Azure-beli virtuálisgép-méretezési csoportok Azure CLI-n keresztül hozhat létre.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **Generation 2 virtuális gépen elérhető összes Azure-régióban vannak?**  
    Igen; azonban nem minden [2. generációs virtuális gépek méreteit](#generation-2-vm-sizes) érhetők el minden régióban. A rendelkezésre állási generáció, 2 virtuális gép áll rendelkezésre állását a virtuális gép méretétől függ.

* **Van különbség az 1. és a generáció ár 2 virtuális gépen?**  
    Nincs a díjszabás az 1. és 2. generációs virtuális gépek közötti különbség.

* **Hogyan növelhetem az operációsrendszer-lemez mérete?**  
  2 TB-nál nagyobb méretű operációsrendszer-lemezek nem ismeri a 2. generációs virtuális gépeket. Alapértelmezés szerint a legtöbb operációsrendszer-lemez kevesebb, mint 2 TB az 2. generációs virtuális gépek, de ajánlott legfeljebb 4 TB-ig a lemez mérete növelhető. Az Azure CLI vagy az Azure Portalon keresztül, az operációs rendszer lemez mérete növelhető. Programozott módon bővülő lemezek további információkért lásd: [lemez átméretezése](expand-disks.md).

  Azure-portálon az operációs rendszer lemez méretének növeléséhez:

  * Keresse meg a virtuális gép tulajdonságlapján az Azure Portalon.

  * Állítsa le és szabadítsa fel a virtuális gépet a **leállítása** gombra.

  * Az a **lemezek** területen válassza ki az operációsrendszer-lemez, amely szeretné növelni.

  * Válassza ki **konfigurációs** a a **lemezek** szakaszt, és frissítse a **mérete** a kívánt értékre.
  
  * Lépjen vissza a virtuális gép tulajdonságlapján és **Start** a virtuális Gépet.

  2 TB-nál nagyobb méretű operációsrendszer-lemezek esetén figyelmeztetés jelenhet meg. A figyelmeztetés nem vonatkozik a 2. generációs virtuális gépek; azonban a 4 TB-nál nagyobb méretű operációsrendszer-lemezméretet is **nem ajánlott.**

* **2. generációs virtuális gépek támogatják a gyorsított hálózatkezelés?**  
    Igen, a 2. generációs virtuális gépek által támogatott [gyorsított hálózatkezelés](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **2. generációs támogatott .vhdx?**  
    Nem, csak a .vhd 2. generációs virtuális gépek esetén támogatott.

* **2. generációs virtuális gépek támogatják az Ultranagy tartós állapot-meghajtók (SSD)?**  
    Igen, a 2. generációs virtuális gépek Ultranagy SSD támogatja.

* **Át tudom telepíteni az 1. a 2. generációs virtuális gépekre?**  
    A virtuális gép generációja nem, akkor létrehozását követően nem módosítható. Ha Virtuálisgép-generációt közötti váltáshoz van szüksége, hozzon létre egy új virtuális Gépet másik generáció szeretné.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [2. generációs virtuális gépek a Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
