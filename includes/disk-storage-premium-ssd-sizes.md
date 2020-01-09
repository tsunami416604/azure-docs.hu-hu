---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c88cba57ed987074c0dde3c8b251f54393667740
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75002700"
---
| prémium SSD méretek | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Lemez mérete GiB-ban | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1 024 | 2048 | 4 096 | 8,192 | 16 384 | 32 767 |
| Percenkénti IO-műveletek lemezenként | 120 | 120 | 120 | 120 | 240 | 500 | 1100 | 2300 | 5000 | 7500 | 7500 | 16000 | 18000 | 20 000 |
| Adattovábbítás lemezenként | 25 MiB/mp | 25 MiB/mp | 25 MiB/mp | 25 MiB/mp | 50 MiB/mp | 100 MiB/mp | 125 MiB/mp | 150 MiB/mp | 200 MiB/mp | 250 MiB/mp | 250 MiB/mp| 500 MiB/mp | 750 MiB/mp | 900 MiB/mp |
| A burst IOPS maximális száma (* *) | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 |
| Adatforgalom maximális átviteli sebessége (lemez * *) | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp | 170 MiB/mp |
| Maximális burst időtartam * * | 30 perc  | 30 perc  | 30 perc  | 30 perc  | 30 perc  | 30 perc  | 30 perc  | 30 perc  |

\*a jelenleg előzetes verzióban elérhető lemezterületet jelöli, a regionális rendelkezésre állási információkért lásd [: új lemezek mérete: felügyelt és nem felügyelt](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\*\*a jelenleg előzetes verzióban elérhető funkciót jelöli, további információért lásd a [lemez kitörése](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) című részt.
