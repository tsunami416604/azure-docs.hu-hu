---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76837561"
---
| Prémium SSD-méretek | 1. | 2. év et tő) | 3. p.* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Lemezméret a GiB-ben | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2048 | 4,096 | 8,192 | 16,384 | 32 767 |
| IOPS-érték lemezenként | 120 | 120 | 120 | 120 | 240 | 500 | 1100 | 2300 | 5000 | 7500 | 7500 | 16000 | 18000 | 20000 |
| Adattovábbítás lemezenként | 25 MiB/mp | 25 MiB/mp | 25 MiB/mp | 25 MiB/mp | 50 MiB/mp | 100 MiB/mp | 125 MiB/mp | 150 MiB/mp | 200 MiB/mp | 250 MiB/mp | 250 MiB/mp| 500 MiB/mp | 750 MiB/mp | 900 MiB/mp |
| Maximális sorozatos IOPS lemezenként** | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 |
| Maximális burst átviteli sebesség lemezenként** | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp |
| Maximális sorozathossz** | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |
| Foglalásra jogosult | Nem  | Nem  | Nem  | Nem  | Nem  | Nem  | Nem  | Nem  | Igen, legfeljebb egy év | Igen, legfeljebb egy év | Igen, legfeljebb egy év | Igen, legfeljebb egy év | Igen, legfeljebb egy év | Igen, legfeljebb egy év |

\*A jelenleg előzetes verzióban lévő lemezméretet jelöli a területi rendelkezésre állási információkhoz: [Új lemezméretek: Felügyelt és nem felügyelt](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).  
\*\*Az előzetes verzióban lévő szolgáltatást jelöli, további információt a [Lemezfelszakítás](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) című témakörben talál.
