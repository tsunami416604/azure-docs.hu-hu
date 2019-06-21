---
title: Azure-támogatás a 2. generációs virtuális gépek (előzetes verzió) |} A Microsoft Docs
description: 2\. generációs virtuális gépek Azure-támogatás áttekintése
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: d8991a6e3137953aae18b91f9cc19e81f0440c75
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151217"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Támogatása generation 2 virtuális gépen (előzetes verzió) az Azure-ban

> [!IMPORTANT]
> Azure-támogatás a 2. generációs virtuális gépek jelenleg előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosítja, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [a Microsoft Azure előzetesekre vonatkozó kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

2\. generációs virtuális gépek (VM) támogatása már elérhető az Azure-ban előzetes verzióban érhető el. Nem egy virtuálisgép-generáció azt létrehozását követően módosíthatja, így generáció kiválasztása előtt tekintse át a szempontok ezen az oldalon. 

2\. generációs virtuális gépek nem támogatottak az 1. generációs virtuális gépek fő funkcióját támogatja. Ezek a funkciók közé tartozik a nagyobb memória, Intel Guard Szoftverbővítmények (Intel SGX), és virtualizált állandó memóriával (vPMEM). 2\. generációs virtuális gépek is, hogy néhány funkció nem támogatott még az Azure-ban. További információkért lásd: a [funkciók és képességek](#features-and-capabilities) szakaszban.

2\. generációs virtuális gépek használata az új rendszerindítási UEFI-alapú architektúra, helyett generation 1 virtuális gépek használják a BIOS-alapú architektúra. 1\. generációs virtuális gépekhez képest, a 2. generációs virtuális gépek előfordulhat, hogy javult rendszerindítási és telepítési. 2\. generációs virtuális gépek áttekintése és néhány 1. és 2. generációs közötti különbséget, lásd: [kell hozhatok létre 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>2\. generációs virtuális gépek mérete

1\. generációs virtuális gépek az Azure-beli Virtuálisgép-méretek támogatottak. Az Azure kínálja a következő kiválasztott Virtuálisgép-sorozat 2. generációs előzetes verziós támogatást:

* [Dsv2 sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) és [Dsv3-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Az Esv3-adatsorok](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#gs-series)
* [Ls-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) és [Lsv2-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-sorozat](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>2\. generációs Virtuálisgép-rendszerképek az Azure Marketplace-en

2\. generációs virtuális gépek támogatják az alábbi Marketplace-rendszerképek:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>A helyi vs. Az Azure a 2. generációs virtuális gépek

Az Azure jelenleg nem támogatja, amelyek a helyszíni Hyper-V támogatja a 2. generációs virtuális gépek funkcióit.

| 2\. generációs funkció                | A helyszíni Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| A biztonságos rendszerindítás                         | :heavy_check_mark:  | :x:   |
| Védett virtuális gép                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| A virtualizálás-alapú biztonság (VBS) | :heavy_check_mark:  | :x:   |
| VHDX formátum                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funkciók és képességek

### <a name="generation-1-vs-generation-2-features"></a>Az 1. és 2. generációs funkciók összehasonlítása

| Funkció | Az 1. | 2\. generációs |
|---------|--------------|--------------|
| Rendszerindítás             | PCAT                      | UEFI                               |
| Lemez vezérlők | IDE                       | SCSI                               |
| A virtuális gépek mérete         | Virtuálisgép-méretek | Kizárólag a virtuális gépek, amelyek támogatják a premium storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Az 1. és 2. generációs képességek összehasonlítása

| Képesség | Az 1. | 2\. generációs |
|------------|--------------|--------------|
| > 2 TB az operációs rendszer lemezén                    | :x:                        | :heavy_check_mark: |
| Egyéni lemez és lemezkép/lapozófájl-kapacitás az operációs rendszer         | :heavy_check_mark:         | :heavy_check_mark: |
| Virtuális gép méretezési támogatása | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| Megosztott lemezkép-katalógusában              | :heavy_check_mark:         | :x:                |
| Az Azure disk encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Létrehozását a 2. generációs virtuális gép

### <a name="marketplace-image"></a>Piactéri lemezképhez

Az az Azure portal vagy az Azure CLI-vel létrehozhat generation 2 virtuális gép egy Piactéri rendszerképből, amely támogatja az UEFI-rendszerindítást.

A `windowsserver-gen2preview` ajánlat csak a Windows a 2. generációs lemezképet tartalmaz. A csomagolási elkerülhető az 1. és 2. generációs lemezképek zavart okozó. Hozhat létre egy 2. generációs virtuális gép, jelölje ki **lemezképek** Ez az ajánlat, és ismételje meg a standard szintű virtuális gép létrehozása.

Jelenleg Marketplace-en érhetők el a Windows következő generációs 2 lemezképek:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Tekintse meg a [funkciók és képességek](#features-and-capabilities) támogatott Piactérről származó rendszerképek szakaszában aktuális listáját.

### <a name="managed-image-or-managed-disk"></a>Felügyelt lemezkép vagy felügyelt lemez

Létrehozhat egy 2. generációs virtuális Gépet egy felügyelt rendszerképet vagy felügyelt lemez ugyanúgy akkor kell létrehoznia egy 1. generációs virtuális gép.

### <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok

Is létrehozhat generation 2 virtuális gépen a virtual machine scale sets használatával. Az Azure CLI használata az Azure méretezhető állítja be az generation 2 virtuális gép létrehozásához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **Generation 2 virtuális gépen elérhető összes Azure-régióban vannak?**  
    Igen. De nem minden [2. generációs virtuális gépek méreteit](#generation-2-vm-sizes) érhetők el minden régióban. A rendelkezésre állás, a generáció, 2 virtuális gép rendelkezésre állását a virtuális gép méretétől függ.

* **Van különbség az 1. és a generáció ár 2 virtuális gépen?**  
   Nem.

* **Hogyan növelhetem az operációsrendszer-lemez mérete?**  
  2 TB-nál nagyobb méretű operációsrendszer-lemezek nem ismeri a 2. generációs virtuális gépeket. Alapértelmezés szerint a operációsrendszer-lemezek a 2. generációs virtuális gépek 2 TB-nál kisebb. Ajánlott legfeljebb 4 TB-ig a lemez mérete növelhető. Az Azure CLI vagy az Azure portal használatával növelje az operációsrendszer-lemez mérete. Bontsa ki a lemezek programozott módon kapcsolatos információkért lásd: [lemez átméretezése](expand-os-disk.md).

  Az Azure Portalról az operációs rendszer lemez méretének növeléséhez:

  1. Az Azure Portalon lépjen a virtuális gép tulajdonságai lapon.
  1. Leállítja és felszabadítja a virtuális Gépet, jelölje be a **leállítása** gombra.
  1. Az a **lemezek** területen válassza ki az operációsrendszer-lemez bővíteni szeretné.
  1. Az a **lemezek** szakaszban jelölje be **konfigurációs**, és frissítse a **mérete** a kívánt értékre.
  1. Lépjen vissza a virtuális gép tulajdonságlapján és **Start** a virtuális Gépet.
  
  2 TB-nál nagyobb méretű operációsrendszer-lemezek esetén figyelmeztetés jelenhet meg. A figyelmeztetés a 2. generációs virtuális gépekre nem vonatkozik. Azonban a 4 TB-nál nagyobb méretű operációsrendszer-lemezméretet is *nem ajánlott.*

* **Generáció, 2 virtuális gépek által támogatott gyorsított hálózatkezelés a következő lépés?**  
    Igen. További információkért lásd: [hozzon létre egy virtuális gép gyorsított hálózatkezeléssel](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **2. generációs támogatott VHDX?**  
    Nem, a 2. generációs virtuális gépek csak a virtuális merevlemez támogatja.

* **2. generációs virtuális gépek támogatják az Azure Ultranagy Disk Storage?**  
    Igen.

* **Áttelepíthetek egy virtuális gép az 1. generációs, 2. generációs?**  
    A virtuális gép generációja nem, létrehozás után nem módosítható. Ha Virtuálisgép-generációt közötti váltáshoz van szüksége, hozzon létre egy új virtuális Gépet másik generáció.

## <a name="next-steps"></a>További lépések

* Ismerje meg [2. generációs virtuális gépek a Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Ismerje meg, hogyan [virtuális merevlemez előkészítése](prepare-for-upload-vhd-image.md) feltöltés a helyszíni rendszerek Azure-bA.
