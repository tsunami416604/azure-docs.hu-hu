---
title: A foglalási kedvezmény alkalmazása az Azure Storage-ra | Microsoft Docs
description: Megismerheti, hogyan alkalmazható a fenntartott Azure Storage-kapacitási kedvezményt a blokkblobokra és az Azure Data Lake Storage Gen2-erőforrásokra.
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: tamram
ms.openlocfilehash: b6360619b7666ab7836a751491e05e379df32d93
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199194"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>A foglalási kedvezmény alkalmazása az Azure Storage-ra

Miután megvásárolta a fenntartott Azure Storage-kapacitást, a foglalási kedvezmény automatikusan érvényesítve lesz azokra a blokkblobokra és Azure Data Lake Storage Gen2-erőforrásokra, amelyek megfelelnek a foglalás feltételeinek. A foglalási kedvezmény csak a tárolási kapacitásra vonatkozik. A sávszélességért és a kérelmekért használatalapú díjat kell fizetnie.

A fenntartott Azure Storage-kapacitással kapcsolatos további információkért tekintse meg [a fenntartott kapacitású Blob Storage költségeinek optimalizálását](../../storage/blobs/storage-blob-reserved-capacity.md) bemutató cikket.

Az Azure Storage-foglalás díjszabásával kapcsolatos további információkért tekintse meg a [blokkblobok díjszabását](https://azure.microsoft.com/pricing/details/storage/blobs/) és [az Azure Data Lake Storage Gen 2 díjszabását](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazásának menete

A fenntartott Azure Storage-kapacitásokra érvényes kedvezmény érvényesítése óránként történik a blokkblobok és az Azure Data Lake Storage Gen2-erőforrások esetében.

A fenntartott Azure Storage-kapacitásra vonatkozó kedvezmény csak akkor érvényes, ha folyamatosan igénybe veszi. Ha egy bizonyos órában nem rendelkezik olyan blokkblobokkal vagy Azure Data Lake Storage Gen2-erőforrásokkal, amelyek megfelelnek a foglalás feltételeinek, akkor arra az órára elveszíti a foglalási mennyiséget. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás törlésekor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák elvesznek.

## <a name="discount-examples"></a>Példák a kedvezményre

Az alábbi példák bemutatják, hogy a rendszer hogyan alkalmazza a fenntartott Azure Storage-kapacitásra érvényes kedvezményt az üzemelő példányoktól függően.

Tegyük fel, hogy 100 TB fenntartott kapacitást vásárolt az USA 2. nyugati régiójában 1 éves időtartamra. A foglalása helyileg redundáns tárolásra (LRS) vonatkozik a gyakori hozzáférési szinten.

Tegyük fel, hogy a példa foglalás ára 18 540 dollár. Dönthet úgy, hogy a teljes összeget előre kifizeti, vagy fizetheti a következő tizenkét hónapban 1 545 dolláros fix havi részletekben is.

Tegyük fel, hogy feliratkozott egy havi foglalási fizetési konstrukcióra. A következő forgatókönyvek ismertetik, mi történik, ha a fenntartott kapacitás kihasználatlan vagy túlhasznált.

### <a name="underusing-your-capacity"></a>A kapacitás kihasználatlansága

Tegyük fel, hogy a foglalási időszak egy adott órájában a fenntartott 100 TB-os kapacitásából csak 80 TB-ot használt fel. A fennmaradó 20 TB-ot arra az órára nem használja fel, és nem viheti tovább.

### <a name="overusing-your-capacity"></a>A kapacitás túlzott mértékű használata

Tegyük fel, hogy a foglalási időszak egy adott órájában 101 TB-nyi tárolási kapacitást használt fel. A foglalási kedvezmény az Ön adatainak 100 TB-jára vonatkozik, a fennmaradó 1 TB-ért pedig használatalapú díjat kell fizetnie az adott órára. Ha a következő órában a használat mértéke 100 TB-ra módosul, akkor az összes használatot fedezi a foglalás.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A fenntartott kapacitású Blob Storage költségeinek optimalizálása](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
