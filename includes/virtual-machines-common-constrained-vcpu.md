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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158487"
---
Egyes adatbázis-munkaterhelés például az SQL Server- vagy Oracle igényelnek, magas memória, tárolási, és i/o-sávszélességet, de nem egy magas magok száma. Számos adatbázis-munkaterhelés nem processzorigényes el. Az Azure biztosít bizonyos Virtuálisgép-méretek, ahol szoftverlicencelés, miközben fenntartja a memória, tárolási és i/o-sávszélességet, a költségek csökkentése érdekében a virtuális gép vCPU-számot is korlátozhatja.

A vCPU-számot is korlátozható az eredeti virtuális gép mérete fele vagy egy negyedévig. Ezek új Virtuálisgép-méretek rendelkezik, amely meghatározza, hogy könnyebben azonosíthatja azokat az active vcpu-k száma utótaggal.

Például az aktuális virtuális gép mérete 32 Vcpu, 448 GB RAM tartalmaz például a Standard_GS5, 64 lemez (akár 256 TB), és 80 000 iops-érték, vagy 2 GB/s i/o-sávszélességet. Az új virtuális gép mérete és 16 közötti Standard_GS5 és Standard_GS5-8 együttműködik a 16 és 8 active vcpu-k, miközben fenntartja az adatait tartalmazza, a memória, tárolási és i/o-sávszélességet Standard_GS5 a többi.

A licencelési díjak az SQL Server vagy az Oracle által korlátozott, az új vCPU-számot, valamint egyéb termékek kell számítunk fel díjat számítunk fel az új vCPU-számot alapján. Ennek eredményeként a Virtuálisgép-specifikációk aránya egy 50 – 75 % növelése az aktív (számlázandó) vcpu-k. Ezek új Virtuálisgép-méretek lehetővé teszik az ügyfél munkaterheléseinek, a memória, tárolási és i/o-sávszélességet használja a szoftver-licencelési költségek optimalizálását. Jelenleg a számítási költségeket, amely tartalmazza az operációs rendszer licencelési, továbbra is ugyanaz, mint az eredeti mérete. További információkért lásd: [további költséghatékony adatbázis-munkaterhelés Azure virtuális gépek méretei](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name (Név)                | vCPU | Adatok           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Ugyanaz, mint a M8ms    |
| Standard_M8-4ms     | 4    | Ugyanaz, mint a M8ms    |
| Standard_M16-4ms    | 4    | Ugyanaz, mint a M16ms   |
| Standard_M16-8ms    | 8    | Ugyanaz, mint a M16ms   |
| Standard_M32-8ms    | 8    | Ugyanaz, mint a M32ms   |
| Standard_M32-16ms   | 16   | Ugyanaz, mint a M32ms   |
| Standard_M64-32ms   | 32   | Ugyanaz, mint a M64ms   |
| Standard_M64-16ms   | 16   | Ugyanaz, mint a M64ms   |
| Standard_M128-64ms  | 64   | Ugyanaz, mint a M128ms  |
| Standard_M128-32ms  | 32   | Ugyanaz, mint a M128ms  |
| Standard_E4-2s_v3   | 2    | Ugyanaz, mint a E4s_v3  |
| Standard_E8-4s_v3   | 4    | Ugyanaz, mint a E8s_v3  |
| Standard_E8-2s_v3   | 2    | Ugyanaz, mint a E8s_v3  |
| Standard_E16-8s_v3  | 8    | Ugyanaz, mint a E16s_v3 |
| Standard_E16-4s_v3  | 4    | Ugyanaz, mint a E16s_v3 |
| Standard_E32-16s_v3 | 16   | Ugyanaz, mint a E32s_v3 |
| Standard_E32-8s_v3  | 8    | Ugyanaz, mint a E32s_v3 |
| Standard_E64-32s_v3 | 32   | Ugyanaz, mint a E64s_v3 |
| Standard_E64-16s_v3 | 16   | Ugyanaz, mint a E64s_v3 |
| Standard_GS4-8      | 8    | Ugyanaz, mint a GS4     |
| Standard_GS4-4      | 4    | Ugyanaz, mint a GS4     |
| Például a Standard_GS5 – 16     | 16   | Ugyanaz, mint a gs5 esetén     |
| Standard_GS5-8      | 8    | Ugyanaz, mint a gs5 esetén     |
| Standard_DS11-1_v2  | 1.    | Ugyanaz, mint a DS11_v2 |
| Standard_DS12-2_v2  | 2    | Ugyanaz, mint a DS12_v2 |
| Standard_DS12-1_v2  | 1.    | Ugyanaz, mint a DS12_v2 |
| Standard_DS13-4_v2  | 4    | Ugyanaz, mint a DS13_v2 |
| Standard_DS13-2_v2  | 2    | Ugyanaz, mint a DS13_v2 |
| Standard_DS14-8_v2  | 8    | Ugyanaz, mint a DS14_v2 |
| Standard_DS14-4_v2  | 4    | Ugyanaz, mint a DS14_v2 |
