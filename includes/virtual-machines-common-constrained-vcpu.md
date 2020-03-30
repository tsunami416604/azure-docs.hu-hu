---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24fbab2bb637edf6c7e31215cdc7ac82bdb0ebab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179114"
---
Egyes adatbázis-számítási feladatok, például az SQL Server vagy az Oracle, nagy memóriát, tárhelyet és I/O-sávszélességet igényelnek, de nem nagy magszámot. Számos adatbázis-munkaterhelés nem cpu-igényes. Az Azure kínál bizonyos virtuálisgép-méretek, ahol korlátozhatja a virtuális gép vCPU-száma a szoftverlicencelés költségeinek csökkentése érdekében, miközben ugyanazt a memóriát, a tárhelyet és az I/O-sávszélességet.

A vCPU-szám az eredeti virtuális gép méretének felére vagy egynegyedére korlátozható. Ezek az új virtuálisgép-méretek egy utótaggal rendelkeznek, amely megadja az aktív vCPU-k számát, hogy könnyebben azonosíthatók legyenek.

Például a jelenlegi virtuális gép mérete Standard_GS5 32 vCPU-val, 448 GB RAM-mal, 64 lemezzel (legfeljebb 256 TB)-vel és 80 000 I/P-vel vagy 2 GB/s I/O-sávszélességgel rendelkezik. Az új virtuális gép méretek Standard_GS5-16 és Standard_GS5-8 jön-val 16 és 8 aktív vCPU-k, miközben a többi szemüveg a Standard_GS5 a memória, a tárolás és az I/O sávszélesség.

Az SQL Server vagy az Oracle licencelési díjai az új vCPU-számra vannak korlátozva, és más termékeket kell fizetni az új vCPU-szám alapján. Ennek eredményeképpen a virtuális gép specifikációinak és az aktív (számlázható) vCPU-khoz viszonyított aránya 50–75%-kal nő. Ezek az új virtuálisgép-méretek lehetővé teszik az ügyfelek munkaterhelései számára, hogy ugyanazt a memóriát, tárhelyet és I/O-sávszélességet használják a szoftverlicencelési költség optimalizálása mellett. Ebben az időben a számítási költség, amely magában foglalja az operációs rendszer licencelését, ugyanaz marad, mint az eredeti méret. További információ: [Az Azure virtuális gépek méretei a költséghatékonyabb adatbázis-számítási feladatokért.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)


| Név                | vCPU | Jellemzők           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Ugyanaz, mint az M8ms    |
| Standard_M8-4ms     | 4    | Ugyanaz, mint az M8ms    |
| Standard_M16-4 ms    | 4    | Ugyanaz, mint az M16ms   |
| Standard_M16-8ms    | 8    | Ugyanaz, mint az M16ms   |
| Standard_M32-8ms    | 8    | Ugyanaz, mint az M32ms   |
| Standard_M32-16ms   | 16   | Ugyanaz, mint az M32ms   |
| Standard_M64-32 ms   | 32   | Ugyanaz, mint az M64ms   |
| Standard_M64-16ms   | 16   | Ugyanaz, mint az M64ms   |
| Standard_M128-64 ms  | 64   | Ugyanaz, mint az M128ms  |
| Standard_M128-32 ms  | 32   | Ugyanaz, mint az M128ms  |
| Standard_E4-2s_v3   | 2    | Ugyanaz, mint E4s_v3  |
| Standard_E8-4s_v3   | 4    | Ugyanaz, mint E8s_v3  |
| Standard_E8-2s_v3   | 2    | Ugyanaz, mint E8s_v3  |
| Standard_E16-8s_v3  | 8    | Ugyanaz, mint E16s_v3 |
| Standard_E16-4s_v3  | 4    | Ugyanaz, mint E16s_v3 |
| Standard_E32 16s_v3 | 16   | Ugyanaz, mint E32s_v3 |
| Standard_E32-8s_v3  | 8    | Ugyanaz, mint E32s_v3 |
| 32s_v3 Standard_E64 | 32   | Ugyanaz, mint E64s_v3 |
| Standard_E64-16s_v3 | 16   | Ugyanaz, mint E64s_v3 |
| Standard_GS4-8      | 8    | Ugyanaz, mint a GS4     |
| Standard_GS4-4      | 4    | Ugyanaz, mint a GS4     |
| Standard_GS5-16     | 16   | Ugyanaz, mint a GS5     |
| Standard_GS5-8      | 8    | Ugyanaz, mint a GS5     |
| Standard_DS11-1_v2  | 1    | Ugyanaz, mint DS11_v2 |
| Standard_DS12-2_v2  | 2    | Ugyanaz, mint DS12_v2 |
| Standard_DS12-1_v2  | 1    | Ugyanaz, mint DS12_v2 |
| Standard_DS13-4_v2  | 4    | Ugyanaz, mint DS13_v2 |
| Standard_DS13-2_v2  | 2    | Ugyanaz, mint DS13_v2 |
| Standard_DS14 8_v2  | 8    | Ugyanaz, mint DS14_v2 |
| Standard_DS14 4_v2 4_v2  | 4    | Ugyanaz, mint DS14_v2 |
