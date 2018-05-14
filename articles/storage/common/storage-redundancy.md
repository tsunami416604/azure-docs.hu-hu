---
title: Az Azure Storage adatreplikáció |} Microsoft Docs
description: A Microsoft Azure Storage-fiók adatait a tartósság és magas rendelkezésre állású replikálja a rendszer. Replikációs beállítások közé tartozik a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS), a georedundáns tárolás (GRS) és az írásvédett georedundáns tárolás (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 6c2c6979d56eb19ff2ba4fb647c7c51e52e51ac6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="azure-storage-replication"></a>Azure Storage replication (Azure Storage replikáció)

A Microsoft Azure tárfiók tartalmát mindig replikáljuk, így biztosítva az adatok tartósságát és magas rendelkezésre állását. Az Azure Storage replikációs másolja át az adatokat, hogy a tervezett és nem tervezett események közötti átmeneti hardverhibák esetén, hálózati vagy áramkimaradások, nagy természeti katasztrófa, és így tovább védve van. Ha szeretné belül azonos adatközpontba, az adatok replikálásához zonal adatközpontokban a régión belül, és még régiók között.

A replikáció biztosítja, hogy a tárfiók még hibák esetén is teljesíti a [Storage szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) feltételeit. Tekintse át az Azure Storage tartóssági és rendelkezésre állási garanciáit a szolgáltatói szerződésben .

## <a name="choosing-a-replication-option"></a>A replikációs beállítás

Tárfiók létrehozásakor választhat a következő replikációs lehetőségek közül:

* [Helyileg redundáns tárolás (LRS)](storage-redundancy-lrs.md)
* [Zónaredundáns tárolás (ZRS)](storage-redundancy-zrs.md)
* [Georedundáns tárolás (GRS)](storage-redundancy-grs.md)
* [Írásvédett georedundáns tárolás (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

A következő táblázat a tartósság és a rendelkezésre állási, amely minden replikációs stratégiájának biztosítja azokat az esemény (vagy hasonló hatással lehet az esemény) egy adott típusú hatókörét gyors áttekintést.

| Forgatókönyv                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Csomópont elérhetetlensége adatközponton belül                                                                 | Igen                             | Igen                              | Igen                                  | Igen                                  |
| Nem érhető el a teljes adatközpont (zonal vagy nem zonal)                                           | Nem                              | Igen                              | Igen                                  | Igen                                  |
| Egy terület kiterjedő leállás                                                                                     | Nem                              | Nem                               | Igen                                  | Igen                                  |
| Olvasási hozzáférés a adatokat (a távoli, a georeplikált régiónként) régió kiterjedő elérhetetlensége esetén | Nem                              | Nem                               | Nem                                   | Igen                                  |
| Az objektumok ___ tartósságot biztosít egy adott évben                                          | legalább 99.999999999 % (11 9 meg) | legalább 99.9999999999 % (12 9 meg) | legalább 99.99999999999999 % (16 9 meg) | legalább 99.99999999999999 % (16 9 meg) |
| Támogatott tárfióktípusokat                                                                   | GPv1, GPv2, Blob                | GPv2                             | GPv1, GPv2, Blob                     | GPv1, GPv2, Blob                     |

Lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) a díjszabásról különböző redundancia a beállítások.

> [!NOTE]
> Prémium szintű Storage támogatja a csak a helyileg redundáns tárolás (LRS). Prémium szintű Storage kapcsolatos információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Replikációs stratégiájának módosítása
Azt teszik lehetővé a tárfiók replikációs stratégiájának módosítása használatával a [Azure-portálon](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), vagy egy a többhöz [ Azure ügyfélkódtárai](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools). A tárfiók replikációs típusának módosítása nem eredményez állásidő.

   > [!NOTE]
   > Jelenleg a portál vagy az API nem használható a fiók átalakítása zrs-t. Ha azt szeretné, a fiók replikációs átalakítása zrs-t, tekintse meg a [zónaredundáns tárolás (ZRS)](storage-redundancy-zrs.md) részleteiről.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Vannak-e a változó a fiók replikációs stratégiájának költségeket?
Az átalakítás útvonalának függ. A legköltségesebb redundancia ajánlat való legolcsóbb a rendezés kell LRS, a zrs-t, a GRS és az RA-GRS. Például fog *a* hozzá semmihez LRS fog többletköltségei mert fog kifinomultabb redundanciájának szintjét. Fog *való* grs-re vagy RA-GRS tájékozódnia egy kimenő sávszélesség kell fizetni, mert az adatok (az elsődleges régió) replikálódnak. a távoli másodlagos régióba. Ez az első telepítéskor egyszeri járnak. Után az adatok másolását követően nincsenek további átalakítás költségek. Csak fizetnie replikálásához bármely új vagy adatok frissítése. További részletek a sávszélesség-költségek: [Azure Storage szolgáltatás díjszabása lap](https://azure.microsoft.com/pricing/details/storage/blobs/).

Ha átvált a Georedundáns LRS, további költség nélkül van, de a replikált adatokat töröltek, amelyek a másodlagos helyre.

## <a name="see-also"></a>Lásd még

- [Helyileg redundáns tárolás (LRS): az Azure Storage alacsony költségű adatredundanciát](storage-redundancy-lrs.md)
- [Zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások](storage-redundancy-zrs.md)
- [Georedundáns tárolás (GRS): az Azure Storage kereszt-területi replikáció](storage-redundancy-grs.md)
- [Az Azure Storage méretezhetőségi és Teljesítménycélok](storage-scalability-targets.md)
- [RA-GRS-tárolót magas rendelkezésre állású alkalmazások tervezése](../storage-designing-ha-apps-with-ragrs.md)
- [A Microsoft Azure tárolás redundancia beállítások és olvasási hozzáférést földrajzi redundáns tárolás ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper - Azure Storage: Egy magas rendelkezésre állású felhőalapú tárolási szolgáltatásba erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
