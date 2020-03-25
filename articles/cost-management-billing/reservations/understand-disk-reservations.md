---
title: A foglalási kedvezmény Azure Disk Storage-ra való alkalmazásának megismerése
description: Megtudhatja, hogyan érvényesül az Azure-beli fenntartott felügyelt lemezekre vonatkozó kedvezmény a prémium szintű felügyelt SSD-k esetében.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 18fdda3e28761fcf912b716f51b5e270a9b224d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77586648"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>A foglalási kedvezmény Azure Disk Storage-ra való alkalmazásának megismerése

Miután megvásárolta az Azure-beli fenntartott lemezkapacitást, a foglalási kedvezmény automatikusan érvényesítve lesz azokra a lemezerőforrásokra, amelyek megfelelnek a foglalás feltételeinek. A foglalási kedvezmény csak a lemez-termékváltozatokra vonatkozik. A lemezpillanatképekért használatalapú díjat kell fizetnie.

Az Azure-beli lemezfoglalásokkal kapcsolatos további információért lásd a [költségmegtakarítást Azure-beli lemezfoglalásokkal](../../virtual-machines/linux/disks-reserved-capacity.md). Az Azure-beli lemezfoglalások díjszabásával kapcsolatos információért lásd [az Azure Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazásának menete

Az Azure-beli lemezfoglalási kedvezmény igénybevételére egy alkalommal van lehetőség. A rendszer a kedvezményt óránként alkalmazza a felügyeltlemez-erőforrásokra. Ha egy bizonyos órában nem rendelkezik olyan felügyeltlemez-erőforrásokkal, amelyek megfelelnek a foglalás feltételeinek, akkor arra az órára elveszíti a foglalási mennyiséget. A fel nem használt órák nem vihetők tovább.

Egy erőforrás törlésekor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem található egyező erőforrás, akkor a lefoglalt órák elvesznek.

## <a name="discount-examples"></a>Példák a kedvezmény alkalmazására

Az alábbi példák bemutatják, hogy a rendszer hogyan alkalmazza a fenntartott Azure-beli lemezfoglalásokra érvényes kedvezményt az üzemelő példánytól függően.

Tegyük fel, hogy 100 P30-lemezt vásárolt és foglalt le az USA 2. nyugati régiójában egyéves időtartamra. Az egyes lemezek tárterületének mérete körülbelül 1 TiB. Tegyük fel, hogy a példafoglalás ára 140 100 dollár. Dönthet úgy, hogy a teljes összeget előre kifizeti, vagy fizetheti a következő 12 hónapban 11 675 dolláros fix havi részletekben is.

A következő forgatókönyvek ismertetik, mi történik, ha a fenntartott kapacitás kihasználatlan, túlhasznált vagy rétegezve van. Tegyük fel, hogy havi foglalási fizetési konstrukcióra iratkozott fel.

### <a name="underusing-your-capacity"></a>A kapacitás kihasználatlansága

Tegyük fel, hogy a foglalási időszak egy adott órájában a lefoglalt 100 prémium szintű Azure P30 SSD-lemezből csak 99-et helyezett üzembe. A fennmaradó P30-lemezt abban az órában nem használja fel. A lemez nem vihető át a következő órára.

### <a name="overusing-your-capacity"></a>A kapacitás túlzott mértékű használata

Tegyük fel, hogy a foglalási időszak egy adott órájában 101 prémium szintű P30 SSD-lemezt használ. A foglalási kedvezmény csak 100 P30-lemezre vonatkozik. A fennmaradó P30-lemezért használatalapú díjat kell fizetnie az adott órára. Ha a következő órában a használat mértéke 100 P30-lemezre csökken, akkor az összes használatot fedezi a foglalás.

### <a name="tiering-your-capacity"></a>A kapacitás rétegzése

Tegyük fel, hogy a foglalási időszak egy adott órájában összesen 200 prémium szintű P30 SSD-t szeretne használni. Tegyük fel azt is, hogy az első 30 percben csak 100-at használ. Ebben az időszakban a használat teljes mértékben le van fedve, mivel 100 P30-lemezt foglalt le. Ha ezt követően abbahagyja az első 100 használatát (tehát egyet sem használ), majd elkezdi használni a másik 100-at a fennmaradó 30 percre, a foglalása ezt a használatot is lefedi.

![Példa a kihasználatlanságra, a túlhasználatra és a rétegzési kapacitásra](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések

- [Költségek csökkentése Azure-beli lemezfoglalással (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Költségek csökkentése Azure-beli lemezfoglalással (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
