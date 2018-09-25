---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dcd94cbd7963c13bd739b1b004fe5ac506d3ea9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006488"
---
**Standard felügyelt virtuálisgép-merevlemez**

| Standard lemez típusa  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60              | S70              | S80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Lemez mérete GiB-ban          | 32             | 64             | 128            | 256  | 512            | 1,024    | a 2048     | 4095    | 8,192     | 16384     | 32 767     |
| IOPS-érték lemezenként       | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500 | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500             | Legfeljebb 500              | Akár 1300              | Legfeljebb 2000              | Legfeljebb 2000              |
| Adattovábbítás lemezenként | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp | Akár 60 MiB/mp| Legfeljebb 300 MiB/mp | Legfeljebb 500 MiB/mp | Legfeljebb 500 MiB/mp |

**Standard szintű felügyelt virtuális gépek SSD-k**

| Standard SSD-lemez típusa  | E10               | E15               | E20             | E30 | E40              | E50              | E60              | E70              | E80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Lemez mérete GiB-ban           | 128             | 256             | 512            | 1,024  | a 2048            | 4095     | 8,192     | 16384     | 32 767    |
| IOPS-érték lemezenként       | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500 | Legfeljebb 500              | Legfeljebb 500              | Legfeljebb 500             | Legfeljebb 500              | Akár 1300              | Legfeljebb 2000              | Legfeljebb 2000              |
| Adattovábbítás lemezenként | Legfeljebb 60 MB/mp | Legfeljebb 60 MB/mp | Legfeljebb 60 MB/mp | Legfeljebb 60 MB/mp | Legfeljebb 60 MB/mp | Legfeljebb 60 MB/mp | Legfeljebb 60 MB/mp | Legfeljebb 60 MB/mp| Legfeljebb 300 MiB/mp |  Legfeljebb 500 MiB/mp | Legfeljebb 500 MiB/mp |

**Prémium felügyelt virtuálisgép-lemezek: lemezenkénti korlátok**

| Prémium szintű lemeztípus  | P4               | P6               | P10             | P15 | P20              | S30              | P40              | P50              | P60              | P70              | P80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Lemez mérete GiB-ban           | 32             | 64             | 128            | 256  | 512            | 1,024    | a 2048     | 4095    | 8,192     | 16384     | 32 767     |
| IOPS-érték lemezenként       | Legfeljebb 120 | Legfeljebb 240              | Legfeljebb 500              | Akár 1100 | Akár 2,300              | Legfeljebb 5000              | Akár 7500             | Akár 7500              | Akár 12,500              | Legfeljebb 15 000              | Legfeljebb 20 000              |
| Adattovábbítás lemezenként | Legfeljebb 25 MiB/mp | Legfeljebb 50 MiB/mp | Legfeljebb 100 MiB/mp | Akár 125 MiB/mp | Legfeljebb 150 MiB/mp | Akár 200 MiB/mp | Legfeljebb 250 MiB/mp | Legfeljebb 250 MiB/mp| Legfeljebb 480 MiB/mp | Legfeljebb 750 MiB/mp | Legfeljebb 750 MiB/mp |

**Prémium felügyelt virtuálisgép-lemezek: virtuális gépenkénti korlátok**

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Virtuális gépenkénti maximális IOPS |80 000 IOPS GS5 virtuális géppel |
| Virtuális gépenkénti maximális átviteli sebesség |2000 MB/s GS5 virtuális géppel |
