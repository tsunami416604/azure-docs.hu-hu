---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935823"
---
Jelenleg az ultra-lemezek további korlátozásokkal rendelkeznek, ezek a következők:

- A következő régiókban támogatott, régiónként eltérő számú rendelkezésre állási zónával:
    - USA 2. keleti régiója
    - USA keleti régiója
    - USA 2. nyugati régiója
    - Délkelet-Ázsia
    - Észak-Európa
    - Nyugat-Európa
    - Egyesült Királyság déli régiója 
- Csak a rendelkezésre állási zónákkal használható (rendelkezésre állási csoportok és a zónákon kívüli önálló virtuálisgép-telepítések esetén nem lehet Ultra lemez csatlakoztatása)
- Csak a következő virtuálisgép-sorozatokban támogatott:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem minden virtuálisgép-méret érhető el minden olyan támogatott régióban, ahol az ultra Disks
- Csak adatlemezként érhetők el, és csak a 4k fizikai szektor méretének támogatásához. Az ultra-lemez 4K-os natív szektorának mérete miatt bizonyos alkalmazások nem lesznek kompatibilisek az ultra Disks szolgáltatással. Az ultra-lemezek támogatásához az egyik példa a Oracle Database, amely a 12,2-as vagy újabb kiadást igényli.  
- Csak üres lemezként hozható létre  
- Még nem támogatja a lemezes pillanatképeket, a virtuálisgép-lemezképeket, a rendelkezésre állási csoportokat és az Azure Disk encryptiont
- Még nem támogatja az integrációt Azure Backup vagy Azure Site Recovery
- A GA virtuális gépek IOPS jelenlegi maximális korlátja 80 000.
- Ha szeretne részt venni egy olyan virtuális gép korlátozott előnézetében, amely az 160 000 IOPS-t az ultra Disks használatával tudja elérni, kérjük, küldjön e-mailt UltraDiskFeedback@microsoft. com