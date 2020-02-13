---
title: A foglalási kedvezmény Azure Disk Storage-ra való alkalmazásának megismerése
description: Megtudhatja, hogyan érvényesül az Azure-beli fenntartott lemezekre vonatkozó kedvezmény a prémium szintű felügyelt SSD-k esetében.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902146"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>A foglalási kedvezmény Azure Disk Storage-ra való alkalmazásának megismerése

Miután megvásárolta az Azure-beli fenntartott lemezkapacitást, a foglalási kedvezmény automatikusan érvényesítve lesz azokra a lemezerőforrásokra, amelyek megfelelnek a foglalás feltételeinek. A foglalási kedvezmény csak a lemezkapacitásra vonatkozik, a lemezpillanatképek számlázása használatalapú díjszabás szerint történik.

Az Azure-beli lemezfoglalásokkal kapcsolatos további információ: [Költségek megtakarítása Azure-beli lemezfoglalásokkal](../../virtual-machines/linux/disks-reserved-capacity.md).
Az Azure-beli lemezfoglalások díjszabásával kapcsolatos információ: [Az Azure-lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazásának menete

Az Azure-beli lemezfoglalási kedvezmény igénybe vételére egy alkalommal van lehetőség, és a rendszer óránként alkalmazza a felügyeltlemez-erőforrásokra. Ha egy bizonyos órában nem rendelkezik olyan felügyeltlemez-erőforrásokkal, amelyek megfelelnek a foglalás feltételeinek, akkor arra az órára elveszíti a foglalási mennyiséget, és a fel nem használt órák nem vihetők tovább.

Egy erőforrás törlésekor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="discount-examples"></a>Példák a kedvezményre

Az alábbi példák bemutatják, hogy a rendszer hogyan alkalmazza a fenntartott Azure-beli lemezfoglalásokra érvényes kedvezményt az üzemelő példányoktól függően:

Tegyük fel, hogy 100 P30 lemeznyi (lemezenként ~1 TiB) fenntartott kapacitást vásárolt az USA 2. nyugati régiójában 1 éves időtartamra. Tegyük fel, hogy a példa foglalás ára 140 100 dollár. Dönthet úgy, hogy a teljes összeget előre kifizeti, vagy fizetheti a következő 12 hónapban 11 675 dolláros fix havi részletekben is.
Tegyük fel, hogy feliratkozott egy havi foglalási fizetési konstrukcióra. A következő forgatókönyvek ismertetik, mi történik, ha a fenntartott kapacitás kihasználatlan, túlhasznált vagy rétegezve van.

### <a name="underusing-your-capacity"></a>A kapacitás kihasználatlansága

Tegyük fel, hogy a foglalási időszak egy adott órájában a 100 P30 lemeznyi foglalásából csak 99 P30 prémium szintű SSD-t helyezett üzembe. A fennmaradó 1 P30 lemezt abban az órában nem használja fel, és nem viheti tovább.

### <a name="overusing-your-capacity"></a>A kapacitás túlzott mértékű használata

Tegyük fel, hogy a foglalási időszak egy adott órájában 101 P30 prémium szintű SSD-t használ. A foglalási kedvezmény csak 100 P30 lemezre vonatkozik, a fennmaradó egy P30 lemezért pedig használatalapú díjat kell fizetnie az adott órára. Ha a következő órában a használat mértéke 100 P30 lemezre csökken, akkor az összes használatot fedezi a foglalás.

### <a name="tiering-your-capacity"></a>A kapacitás rétegzése

Tegyük fel, hogy a foglalási időszak egy adott órájában összesen 200 P30 prémium szintű SSD-t szeretne használni. Az első 30 percben csak 100-at használ. Ebben az időszakban a használat teljes mértékben le van fedve, mivel 100 P30 lemezt foglalt le. Ha ezt követően abbahagyja az első 100 használatát (tehát egyet sem használ), majd elkezdi használni a másik 100-at a fennmaradó 30 percre, a foglalása ezt a használatot is lefedi.

![A kihasználatlanság, a túlhasználat és a rétegzési kapacitás leírását bemutató példa](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Költségek csökkentése Azure-beli lemezfoglalással (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Költségek csökkentése Azure-beli lemezfoglalással (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)