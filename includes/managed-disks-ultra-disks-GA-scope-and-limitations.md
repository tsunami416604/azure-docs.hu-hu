---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600975"
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
- Nem minden virtuálisgép-méret érhető el minden olyan támogatott régióban, amely Ultra-lemezekkel rendelkezik.
- Csak adatlemezként érhetők el, és csak a 4k fizikai szektor méretének támogatása  
- Csak üres lemezként hozható létre  
- Még nem támogatja a lemezes pillanatképeket, a virtuálisgép-lemezképeket, a rendelkezésre állási csoportokat és az Azure Disk encryptiont
- Még nem támogatja az integrációt Azure Backup vagy Azure Site Recovery
- A GA virtuális gépek IOPS jelenlegi maximális korlátja 80 000.