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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279165"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). Ha a GB-ban (1000^3 bájt) mért lemezeket hasonlítja össze a GiB-ben (1024^3) mért lemezekkel, ne feledje, hogy a GiB-ben megadott kapacitásszámok kisebbnek tűnhetnek. Például 1023 GiB = 1098,4 GB.
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Az adatlemezek gyorsítótárazott és gyorsítótárazás nélküli módban üzemelhetnek. Gyorsítótárazott adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **ReadOnly** (Csak olvasás) vagy **ReadWrite** (Írás és olvasás) beállításra van konfigurálva.  Gyorsítótárazás nélküli adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **None** (Nincs) beállításra van konfigurálva.
- Ha azt szeretné, hogy a legjobb teljesítményt a virtuális gépek, az adatlemezek számát kettő re vCPU-nként.
- **A várt hálózati sávszélesség** a virtuális géptípusonként az összes hálózati adapteren leosztott maximális összesített sávszélesség az összes cél számára. További információ: [Virtual machine network bandwidth](../articles/virtual-network/virtual-machine-network-throughput.md).

  A felső határok nem garantáltak. A korlátok útmutatást nyújtanak a megfelelő virtuálisgép-típus kiválasztásához a tervezett alkalmazáshoz. A tényleges hálózati teljesítmény több tényezőtől függ, például a hálózati torlódástól, az alkalmazásterheléstől és a hálózati beállításoktól. A hálózati átviteli hálózat optimalizálásáról a [Hálózati átviteli hálózat optimalizálása az Azure virtuális gépekhez](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)című témakörben talál további információt. A várt hálózati teljesítmény eléréséhez Linux vagy Windows, előfordulhat, hogy ki kell választania egy adott verziót, vagy optimalizálja a virtuális gép. További információ: [Bandwidth/Throughput testing (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



