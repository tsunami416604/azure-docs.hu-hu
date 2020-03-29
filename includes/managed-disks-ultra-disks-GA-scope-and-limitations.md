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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935823"
---
Most, ultra lemezek további korlátozások, ezek a következők:

- A következő régiókban támogatottak, régiónként eltérő számú rendelkezésre állási zónával:
    - USA 2. keleti régiója
    - USA keleti régiója
    - USA nyugati régiója, 2.
    - Délkelet-Ázsia
    - Észak-Európa
    - Nyugat-Európa
    - Az Egyesült Királyság déli régiója 
- Csak rendelkezésre állási zónákkal használható (a rendelkezésre állási csoportok és a zónákon kívüli egyetlen virtuális gép-telepítések nem képesek ultralemezcsatolására)
- Csak a következő virtuálisgép-sorozatban támogatottak:
    - [ESv3 között](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3 között](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 között](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem minden virtuális gép méret érhető el minden támogatott régióban ultralemezekkel
- Csak adatlemezként érhetők el, és csak a 4k fizikai szektorméretet támogatják. Az Ultra Disk 4K natív szektormérete miatt vannak olyan alkalmazások, amelyek nem kompatibilisek az ultralemezekkel. Erre példa az Oracle Database, amely 12.2-es vagy újabb kiadást igényel az ultralemezek támogatásához.  
- Csak üres lemezként hozható létre  
- Még nem támogatja a lemezpillanatképeket, a virtuálisgép-lemezképeket, a rendelkezésre állási készleteket és az Azure lemeztitkosítást
- Még nem támogatja az integrációt az Azure Backup vagy az Azure Site Recovery szolgáltatással
- A ga-virtuális gépeki IOPS jelenlegi maximális korlátja 80 000.
- Ha szeretne részt venni egy virtuális gép korlátozott előzetes verziójában, amely 160 000 IOPS-t képes elérni ultra lemezekkel, kérjük, írjon e-mailt.com UltraDiskFeedback@microsoft