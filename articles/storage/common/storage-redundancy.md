---
title: Adatredundancia az Azure Storage-ban | Microsoft Docs
description: A rendszer a Microsoft Azure Storage fiókjában lévő adatait a tartósság és a magas rendelkezésre állás érdekében replikálja. A redundancia lehetőségei közé tartozik a helyileg redundáns tárolás (LRS), a zóna-redundáns tárolás (ZRS), a Geo-redundáns tárolás (GRS), az olvasási hozzáférésű geo-redundáns tárolás (RA-GRS), a Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) és az olvasási hozzáférésű földrajzi zóna – redundáns Storage (RA-GZRS) (előzetes verzió).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3640d2d88fc679b78395472c667fcde39979728a
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71074354"
---
# <a name="azure-storage-redundancy"></a>Azure Storage-redundancia

A Microsoft Azure tárfiók tartalmát mindig replikáljuk, így biztosítva az adatok tartósságát és magas rendelkezésre állását. Az Azure Storage átmásolja az adatait, hogy védve legyen a tervezett és nem tervezett eseményekkel szemben, beleértve az átmeneti hardverhiba, a hálózati vagy áramkimaradások, valamint a súlyos természeti katasztrófákat. Dönthet úgy is, hogy ugyanazon az adatközponton belül replikálja az adatait, többek között az azonos régión belüli és a földrajzilag elkülönített régiók között.

A replikáció biztosítja, hogy a tárfiók még hibák esetén is teljesíti a [Storage szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) feltételeit. Tekintse át az Azure Storage tartóssági és rendelkezésre állási garanciáit a szolgáltatói szerződésben .

Az Azure Storage rendszeresen ellenőrzi a ciklikus redundancia-ellenőrzések (FCSF) használatával tárolt adatok integritását. Ha a rendszer az adatsérülést észleli, a redundáns adatvesztéssel kerül kijavításra. Az Azure Storage az összes hálózati forgalom ellenőrzőösszegét is kiszámítja, hogy észlelje az adatcsomagok sérülését az adatok tárolása vagy beolvasása során.

## <a name="choosing-a-redundancy-option"></a>Redundancia-beállítás kiválasztása

A Storage-fiók létrehozásakor a következő redundancia-lehetőségek közül választhat:

- [Helyileg redundáns tárolás (LRS)](storage-redundancy-lrs.md)
- [Zónaredundáns tárolás (ZRS)](storage-redundancy-zrs.md)
- [Georedundáns tárolás (GRS)](storage-redundancy-grs.md)
- [Írásvédett georedundáns tárolás (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)
- [Geo-Zone-redundáns tárolás (GZRS)](storage-redundancy-gzrs.md)
- [Olvasási hozzáférés – földrajzi zóna – redundáns tárolás (RA-GZRS)](storage-redundancy-gzrs.md)

Az alábbi táblázat gyors áttekintést nyújt a tartósság és a rendelkezésre állás hatóköréről, amelyet az egyes replikációs stratégiák egy adott típusú esemény (vagy hasonló hatás esetén) számára biztosítanak.

| Forgatókönyv                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (előzetes verzió)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Csomópontok nem rendelkezésre állása az adatközponton belül                                                                 | Igen                             | Igen                              | Igen                                  | Igen                                  |
| Egy teljes adatközpont (Zona vagy nem zónák) elérhetetlenné válik                                           | Nem                              | Igen                              | Igen                                  | Igen                                  |
| Az egész régióra kiterjedő leállás                                                                                     | Nem                              | Nem                               | Igen                                  | Igen                                  |
| Olvasási hozzáférés az adataihoz (távoli, földrajzilag replikált régióban) az egész régióra kiterjedő elérhetetlenség esetén | Nem                              | Nem                               | Igen (az RA-GRS-vel)                                   | Igen (az RA-GZRS-vel)                                 |
| Az objektumok tartósságának biztosítására \_ \_ szolgál az adott évben                                          | legalább 99,999999999% (11 9) | legalább 99,9999999999% (12 9) | legalább 99.99999999999999% (16 9) | legalább 99.99999999999999% (16 9) |
| Támogatott Storage-fiókok típusai                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2                     |
| Rendelkezésre állási SLA az olvasási kérelmekhez | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) a GRS<br /><br />Legalább 99,99% (99,9% a lassú elérési szinthez) az RA-GRS | Legalább 99,9% (99% a lassú elérési szinthez) a GZRS<br /><br />Legalább 99,99% (99,9% a lassú elérési szinthez) az RA-GZRS |
| Az írási kérelmek rendelkezésre állási SLA-ja | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) |

A rendszer a Storage-fiók összes adatait replikálja, beleértve a Blobok blokkolását és a Blobok, az oldal Blobok, a várólisták, a táblák és a fájlok hozzáfűzését. Minden típusú Storage-fiók replikálva van, bár a ZRS általános célú v2 Storage-fiókot igényel.

Az egyes redundancia-lehetőségek díjszabásáról az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/)ismertető cikk nyújt tájékoztatást. 

További információ az Azure Storage szolgáltatás tartósságáról és rendelkezésre állásáról: az [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/)-ja.

> [!NOTE]
> Az Azure Premium Storage csak a helyileg redundáns tárolást (LRS) támogatja.

## <a name="changing-replication-strategy"></a>Replikációs stratégia módosítása

A Storage-fiók replikációs stratégiáját a [Azure Portal](https://portal.azure.com/), az [Azure PowerShell](storage-powershell-guide-full.md), az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)vagy az [Azure Storage ügyféloldali kódtárai](https://docs.microsoft.com/azure/index#pivot=sdkstools)segítségével módosíthatja. A Storage-fiók replikálási típusának módosítása nem eredményezi az időkorlátot.

> [!NOTE]
> Jelenleg nem használhatja a Azure Portal vagy az Azure Storage ügyféloldali kódtárait, hogy a fiókját ZRS, GZRS vagy RA-GZRS alakítsa át. A fiók ZRS-re való áttelepítéséhez tekintse meg a következő témakört: [Zone-redundáns Storage (ZRS), amely a részletes Azure Storage-alkalmazások létrehozására](storage-redundancy-zrs.md) használható. A GZRS vagy RA-GZRS áttelepítéséhez tekintse meg a [geo-Zone-redundáns tárterületet a nagyfokú rendelkezésre állás és a maximális tartósság (előzetes verzió)](storage-redundancy-zrs.md) a részletekért.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Vannak költségek a fiókom replikációs stratégiájának módosításához?

Ez a konverziós útvonaltól függ. Rendeljen legalább a legdrágább, Azure Storage redundancia-ajánlatokat a LRS, a ZRS, a GRS, az RA-GRS, a GZRS és az RA-GZRS. Ha például *a LRS-ből bármilyen* más típusú replikációra kerül, további díjakat számítunk fel, mert összetettebb redundancia-szintre vált. A GRS-RA vagy RA-GRS-re *való* Migrálás esetén a kimenő sávszélességért fizetendő díj várható, mert az elsődleges régiójában lévő adatai replikálódnak a távoli másodlagos régióba. Ez a díj egy egyszeri költség a kezdeti beállításnál. Az adatok másolása után nincsenek további áttelepítési díjak. Csak az új vagy a meglévő adatfrissítések replikálására kell fizetni. A sávszélességgel kapcsolatos további információkért lásd az [Azure Storage díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/blobs/).

Ha a Storage-fiókot a GRS-ből a LRS-be telepíti át, akkor nem jár további díjszabással, de a replikált adatok törlődnek a másodlagos helyről.

Ha a Storage-fiókját az RA-GRS-ről a GRS vagy a LRS-re telepíti át, akkor ez a fiók az RA-GRS-ként lesz kiszámlázva az RA-tól számított további 30 nap után.

## <a name="see-also"></a>Lásd még

- [Helyileg redundáns tárolás (LRS): Alacsony költséghatékonyságú adatredundancia az Azure Storage szolgáltatásban](storage-redundancy-lrs.md)
- [Zóna – redundáns tárolás (ZRS): Magasan elérhető Azure Storage-alkalmazások](storage-redundancy-zrs.md)
- [Geo-redundáns tárolás (GRS): Régiók közötti replikáció az Azure Storage-ban](storage-redundancy-grs.md)
- [Geo-Zone-redundáns tárolás (GZRS) a nagyfokú rendelkezésre állás és a maximális tartósság érdekében (előzetes verzió)](storage-redundancy-gzrs.md)
- [Az Azure Storage skálázhatósági és teljesítménybeli céljai](storage-scalability-targets.md)
- [Magasan elérhető alkalmazások tervezése RA-GRS Storage használatával](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundancia-beállítások és az olvasási hozzáférés földrajzi redundáns tárolás](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP-papír – Azure Storage: Magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
