---
title: Az Azure Storage-ban adatredundancia |} A Microsoft Docs
description: A Microsoft Azure Storage-fiókban lévő adatok a rendszer replikálja a tartósság és magas rendelkezésre állás. Adatredundanciával kapcsolatos lehetőségek közé tartozik a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS), a georedundáns tárolás (GRS) és az írásvédett georedundáns tárolás (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 0ba36042d02d0b4101f1e80c63af232717bcf4ca
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506787"
---
# <a name="azure-storage-redundancy"></a>Az Azure Storage-redundancia

A Microsoft Azure tárfiók tartalmát mindig replikáljuk, így biztosítva az adatok tartósságát és magas rendelkezésre állását. Az Azure Storage készít másolatot az adatokról, így a tervezett és nem tervezett események, például átmeneti hardverhibák, hálózati vagy áramkimaradások és hatalmas természeti katasztrófák védve van. Ha szeretné, ugyanabban az adatközpontban, az adatok replikálása a zónaszintű adatközpontokon ugyanazon a régión belül, vagy földrajzilag különálló régiók között elosztva.

A replikáció biztosítja, hogy a tárfiók még hibák esetén is teljesíti a [Storage szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) feltételeit. Tekintse át az Azure Storage tartóssági és rendelkezésre állási garanciáit a szolgáltatói szerződésben .

## <a name="choosing-a-redundancy-option"></a>Egy adatredundáns tárolási mód kiválasztása

Amikor létrehoz egy tárfiókot, a következő adatredundanciával kapcsolatos lehetőségek közül választhat:

* [Helyileg redundáns tárolás (LRS)](storage-redundancy-lrs.md)
* [Zónaredundáns tárolás (ZRS)](storage-redundancy-zrs.md)
* [Georedundáns tárolás (GRS)](storage-redundancy-grs.md)
* [Írásvédett georedundáns tárolás (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Az alábbi táblázat a tartóssággal és rendelkezésre állással, amely minden replikációs stratégiát biztosít Önnek egy adott típusú esemény (vagy esemény hasonló gyakorolt hatás) körét gyors áttekintést nyújt.

| Forgatókönyv                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Csomópont hiányában egy adatközponton belül                                                                 | Igen                             | Igen                              | Igen                                  | Igen                                  |
| (A zónaszintű vagy nem zónaszintű) teljes adatközpont elérhetetlenné válik.                                           | Nem                              | Igen                              | Igen                                  | Igen                                  |
| Egy régióra kiterjedő szolgáltatáskimaradás                                                                                     | Nem                              | Nem                               | Igen                                  | Igen                                  |
| Olvasási hozzáférés az adatokhoz (a távoli, a georeplikált régiónként) régióra kiterjedő elérhetetlensége esetén | Nem                              | Nem                               | Nem                                   | Igen                                  |
| Nyújtson \_ \_ tárolt objektumok számára egy adott évben                                          | legalább 99,999999999 %-os (11 9-es) | legalább 99,9999999999 %-os (12 9-es) | 99,99999999999999 %-ában (16 9-es) | 99,99999999999999 %-ában (16 9-es) |
| Támogatott tárfióktípusok                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| Rendelkezésre állási SLA olvasási kérelmek esetén | Legalább 99,9 %-os (a ritka elérésű hozzáférési szint esetében 99 %-os) | Legalább 99,9 %-os (a ritka elérésű hozzáférési szint esetében 99 %-os) | Legalább 99,9 %-os (a ritka elérésű hozzáférési szint esetében 99 %-os) | Legalább 99,99 %-át (a lassú elérési szint esetében 99,9 %) |
| Rendelkezésre állási SLA-kérésekre vonatkozó | Legalább 99,9 %-os (a ritka elérésű hozzáférési szint esetében 99 %-os) | Legalább 99,9 %-os (a ritka elérésű hozzáférési szint esetében 99 %-os) | Legalább 99,9 %-os (a ritka elérésű hozzáférési szint esetében 99 %-os) | Legalább 99,9 %-os (a ritka elérésű hozzáférési szint esetében 99 %-os) |

Minden egyes adatredundáns tárolási mód díjszabásról lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/). 

Információkat szeretne megtudni az Azure Storage tartóssági és rendelkezésre állási garanciák, lásd: a [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> A Premium Storage támogatja a csak a helyileg redundáns tárolás (LRS). Prémium szintű Storage szolgáltatással kapcsolatos további információkért lásd: [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Replikációs stratégia módosítása
Módosíthatja a tárfiók replikációs stratégia használatával lehetővé tesszük a [az Azure portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), vagy egy a többhöz [ Azure-ügyfélkönyvtárak](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). A storage-fiók replikációs típusának módosítása eredményez leállás.

   > [!NOTE]
   > Jelenleg a portál vagy API nem használható a fiók konvertálása zrs-t. Ha azt szeretné, a fiók replikációs átalakítása zrs-t, tekintse meg a [zónaredundáns tárolás (ZRS)](storage-redundancy-zrs.md) részleteiről.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Vannak-e bármilyen módosítás, a fiók replikációs stratégiát költségek?
Attól függ, az átalakítás elérési útra. LRS, ZRS, GRS és RA-GRS rendezése a legolcsóbb legköltségesebb redundancia előfizetésért rendelkezünk. Például fog *a* semmit az LRS többletköltségekkel jár, mivel egy kifinomultabb tárhelyredundancia-szint kívánja. Fog *való* GRS vagy RA-GRS számítjuk egy kimenő sávszélesség mert (az elsődleges régióban) az adatok replikálódnak a távoli másodlagos régióba. Ez a kezdeti telepítéskor egyszeri díjat. Miután az adatok másolását követően nem számítunk további átalakítás fel díjat. Csak kell díjat fizetnie replikálásához bármely új vagy meglévő adatok frissítése. További információ a sávszélesség-költségek: [Azure Storage díjszabását ismertető weblapon](https://azure.microsoft.com/pricing/details/storage/blobs/).

Ha módosítjuk GRS az LRS, nem kell további költenie, de a másodlagos helyről a replikált adatok törlődnek.

## <a name="see-also"></a>Lásd még

- [Helyileg redundáns tárolás (LRS): Az Azure Storage alacsony költségű adatredundancia](storage-redundancy-lrs.md)
- [Zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások](storage-redundancy-zrs.md)
- [Georedundáns tárolás (GRS): Az Azure Storage-régiók közti replikáció](storage-redundancy-grs.md)
- [Az Azure Storage méretezhetőségi és teljesítménycéljai](storage-scalability-targets.md)
- [RA-GRS tároló használatával magas rendelkezésre állású alkalmazások tervezése](../storage-designing-ha-apps-with-ragrs.md)
- [A Microsoft Azure Storage redundancia lehetőségeket és olvasási hozzáférés georedundáns tárolás ](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP tanulmány – Azure Storage: Magas rendelkezésre állású felhőbeli tárolószolgáltatásba erős konzisztencia](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
