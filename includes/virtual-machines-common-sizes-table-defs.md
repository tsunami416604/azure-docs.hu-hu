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
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "74279165"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). Ha a GB-ban (1000 ^ 3 bájtban) mért lemezeket hasonlítja össze a GiB (1024 ^ 3) értékben mért lemezekkel, ne feledje, hogy a GiB-ban megadott kapacitások száma kisebb lehet. Például 1023 GiB = 1098,4 GB.
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Az adatlemezek gyorsítótárazott és gyorsítótárazás nélküli módban üzemelhetnek. Gyorsítótárazott adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **ReadOnly** (Csak olvasás) vagy **ReadWrite** (Írás és olvasás) beállításra van konfigurálva.  Gyorsítótárazás nélküli adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **None** (Nincs) beállításra van konfigurálva.
- Ha a legjobb teljesítményt szeretné használni a virtuális gépek számára, akkor a vCPU két lemezre kell korlátoznia az adatlemezek számát.
- A **várt hálózati sávszélesség** a virtuálisgép-típusokban lefoglalt maximális összesített sávszélesség az összes hálózati adapteren, minden célhelynél. További információ: [virtuális gép hálózati sávszélessége](../articles/virtual-network/virtual-machine-network-throughput.md).

  A felső határértékek nem garantáltak. A határértékek útmutatást nyújtanak a megfelelő virtuálisgép-típus kiválasztásához a kívánt alkalmazáshoz. A tényleges hálózati teljesítmény számos tényezőtől függ, beleértve a hálózati torlódást, az alkalmazások terhelését és a hálózati beállításokat. A hálózati átviteli sebesség optimalizálásával kapcsolatos információkért lásd: [Az Azure-beli virtuális gépek hálózati teljesítményének optimalizálása](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Előfordulhat, hogy a Linux vagy Windows rendszeren a várt hálózati teljesítmény eléréséhez ki kell választania egy adott verziót, vagy optimalizálnia kell a virtuális gépet. További információ: [sávszélesség/átviteli sebesség tesztelése (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



