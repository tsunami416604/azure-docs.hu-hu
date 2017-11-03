---
title: "Az Azure Storage adatreplikáció |} Microsoft Docs"
description: "A Microsoft Azure Storage-fiók adatait a tartósság és magas rendelkezésre állású replikálja a rendszer. Replikációs beállítások közé tartozik a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS), a georedundáns tárolás (GRS) és az írásvédett georedundáns tárolás (RA-GRS)."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: dbc81edd24ee714fbb173ed395a2f2fc91773fff
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="azure-storage-replication"></a>Azure Storage replication (Azure Storage replikáció)
A Microsoft Azure tárfiók tartalmát mindig replikáljuk, így biztosítva az adatok tartósságát és magas rendelkezésre állását. A replikáció akár egyazon adatközponton belül, akár egy második adatközpontban készít másolatot az adatokról, a replikáció beállítástól függően. A replikáció óvja az adatokat és biztosítja az alkalmazás üzemidejét egy átmeneti hardverhiba esetén. Ha az adatok egy második adatközpontba van replikálva, az védett végzetes hibák esetén az elsődleges helyen.

A replikáció biztosítja, hogy a tárfiók még hibák esetén is teljesíti a [Storage szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) feltételeit. Tekintse át az Azure Storage tartóssági és rendelkezésre állási garanciáit a szolgáltatói szerződésben .

Tárfiók létrehozásakor választhat a következő replikációs lehetőségek közül:

* [Helyileg redundáns tárolás (LRS)](#locally-redundant-storage)
* [Zónaredundáns tárolás (ZRS)](#zone-redundant-storage)
* [Georedundáns tárolás (GRS)](#geo-redundant-storage)
* [Írásvédett georedundáns tárolás (RA-GRS)](#read-access-geo-redundant-storage)

Írásvédett georedundáns tárolás (RA-GRS) beállítás az alapértelmezett tárfiók létrehozásakor.

A következő táblázat LRS, a zrs-t, a GRS és az RA-GRS, közötti különbségek gyors áttekintést nyújt az ezt követő szakaszok részletesebben replikációs különböző típusú célja.

| Replikációs stratégia | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Több adatközpont között replikálva. |Nem |Igen |Igen |Igen |
| A másodlagos helyre, valamint az elsődleges helyre is lehet adatokat olvasni. |Nem |Nem |Nem |Igen |
| A külön csomópontokon fenntartott adatmásolatok száma. |3 |3 |6 |6 |

Lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) a díjszabásról különböző redundancia beállításokkal.

> [!NOTE]
> Prémium szintű Storage támogatja a csak a helyileg redundáns tárolás (LRS). Prémium szintű Storage kapcsolatos információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Zónaredundáns tárolás
Zónaredundáns tárolás (ZRS) az adatok tárolása három hasonló LRS, így biztosítva az LRS-nél nagyobb tartósságot biztosít replikák mellett egy vagy két régiókban üzemeltetésében aszinkron módon replikálja. A ZRS tárolt adatok állandó akkor is, ha az elsődleges adatközpont nem érhető el vagy helyreállíthatatlan.
Az ügyfelek, akik tervezi használni a zrs-t érdemes figyelembe venni, hogy:

* A ZRS csak a blokkblobokhoz az általános célú tárfiókok esetében érhető el, és csak a tárolási szolgáltatásverziók 2014-02-14-es és újabb.
* Mivel a aszinkron replikációs késés, helyi katasztrófa esetén is lehetséges, hogy még nem replikálódott a másodlagos változtatások elvesznek az adatokat nem lehet helyreállítani az elsődleges.
* A replika nem érhetők el addig, amíg a Microsoft kezdeményezi a másodlagos.
* A ZRS fiókok nem konvertálható később LRS- vagy GRS. Hasonlóképpen meglévő LRS- vagy GRS fiók nem alakítható át a ZRS fiók.
* A ZRS fiókok nem rendelkeznek, metrikákat és naplózási képesség.

## <a name="geo-redundant-storage"></a>Georedundáns tárolás
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás
Írásvédett georedundáns tárolás (RA-GRS) rendelkezésre állási a tárfiók maximalizálja a csak olvasási hozzáféréssel a másodlagos helyen mellett két régióban is GRS biztosítja a replikációs adatok megadásával.

Csak olvasási hozzáféréssel a másodlagos régióba adatai engedélyezésekor a másodlagos végponti, a tárfiók elsődleges végpont mellett az adatok érhető el. A másodlagos végponti az elsődleges végpont hasonló, de a utótag `–secondary` fióknevet. Például, ha az elsődleges végpont a Blob szolgáltatás `myaccount.blob.core.windows.net`, akkor a másodlagos végponti `myaccount-secondary.blob.core.windows.net`. A tárfiók hozzáférési kulcsainak megegyeznek az elsődleges és másodlagos végpontok.

Szempontok:

* Az alkalmazás melyik végponthoz az RA-GRS használatakor dolgozik kezelésére van.
* Mivel a aszinkron replikációs késés, regionális katasztrófa esetén is lehetséges, hogy még nem replikálódott a másodlagos régióba módosítások elvesznek az adatokat nem lehet helyreállítani az elsődleges régióban.
* Ha a Microsoft a másodlagos régióba történő feladatátvételt kezdeményez, hogy fog rendelkezik olvasási és írási engedéllyel a feladatátvételt követően befejeződött. További információkért lásd: [vész-helyreállítási útmutatást](../storage-disaster-recovery-guidance.md). 
* RA-GRS magas rendelkezésre állású célokra szolgál. Méretezhetőség útmutatásért tekintse át a [teljesítmény ellenőrzőlista](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Hogyan lehet módosítani a georedundáns replikáció típusa storage-fiókom?

   A földrajzi replikációs típusát a tárfiókja közötti LRS, GRS és RA-GRS használatával módosíthatja a [Azure-portálon](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md) vagy programozott módon, egyet a sok Storage Ügyfélkódtáraival. Vegye figyelembe, hogy a ZRS fiókok konvertált LRS- vagy GRS nem lehet. Hasonlóképpen meglévő LRS- vagy GRS fiók nem alakítható át a ZRS fiók.

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2. Lesz-e bármely állásidő storage-fiókom replikációs típusú módosításakor?

   Nem, akkor nem adható meg bármelyik állásidő.

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>3. Lesz-e minden további költség nélkül Ha a replikáció típusát storage-fiókom?

   Igen. Ha módosítja az LRS tárfiók grs-re (vagy RA-GRS), azt volna fel Önnek egy, a kimenő forgalom meglévő adatok másolása az elsődleges helyről másodlagos helyre részt a kell külön fizetni. Miután a kezdeti adatok másolásakor használata az adatokat az elsődleges másodlagos helyre történő replikálása földrajzi további további kilépő díjmentes. A sávszélesség-költségek részletes található meg a [Azure Storage szolgáltatás díjszabása lap](https://azure.microsoft.com/pricing/details/storage/blobs/). Ha átvált a Georedundáns LRS, további költség nélkül van, de az adatok törli a másodlagos helyről.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Hogyan RA-GRS segítik számomra?
   
   Georedundáns tárolás biztosítja az adatok egy másodlagos régióban, amely több száz elhagyja az elsődleges régióban miles egy elsődleges replikáció. Ebben az esetben az adatok tartósságát teljes regionális kimaradás vagy az elsődleges régióban nincs helyreállítható katasztrófa esetén is. RA-GRS-tároló tartalmazza ezt, és hozzáadja az adatokat a másodlagos helyről olvasás. Hogyan használhatók ki ez a lehetőség ötleteket, olvassa el [tervezése magas rendelkezésre álló alkalmazások RA-GRS-tárolót](../storage-designing-ha-apps-with-ragrs.md). 

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Van mód a annak eldöntésére, hogy mennyi ideig tart a másodlagos régióba replikálja a az adatokat az elsődleges?
   
   Ha RA-GRS tárhelyet használ, ellenőrizheti a legutóbbi szinkronizálásának időpontja a tárfiók. Utolsó szinkronizálás GMT dátum/idő értéket; minden elsődleges írás előtt az utolsó szinkronizálás sikeresen elkészült a másodlagos helyre, ami azt jelenti, hogy elérhetők a másodlagos helyről olvassa el. Elsődleges írása után utolsó szinkronizálásának időpontja is, vagy nem érhető el az olvasási műveletek még. Az érték használatával kérdezheti a [Azure-portálon](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), vagy programozott módon, a REST API-t vagy a Storage Ügyfélkódtáraival egyikét. 

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6. Hogyan lehet átváltani a másodlagos régióba. Ha az elsődleges régióban kimaradás?
   
   Tekintse meg a cikk [Mi a teendő, ha egy Azure Storage esetleges leálláskor](../storage-disaster-recovery-guidance.md) további részleteket.

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Mi az a helyreállítási Időkorlát és a GRS RTO?
   
   Helyreállítás időkorlát (RPO): Georedundáns és RA-GRS, a tárolás szolgáltatás aszinkron módon földrajzi-replikálja az adatokat a másodlagos hely az elsődleges. Ha nincs a regionális jelentős katasztrófa következik és a feladatátvétel végrehajtását, majd legutóbbi a változási különbözeteket, amelyek még nincsenek georeplikált elveszhetnek. A lehetséges elveszett adatok percet a helyreállítási Időkorlát (azaz a pont, amelyre az adatok helyreállíthatók időben) hivatkozunk. Általában kell az RPO kevesebb mint 15 percet, noha jelenleg nincs SLA-t a georeplikáció mennyi ideig tart.

   A helyreállítási idő célkitűzése (RTO): Ez az méri, hogy mennyi ideig tart, hajtsa végre a feladatátvételt, és a tárfiók újra online Ha feladatátvétellel kell. Az idő a feladatátvétel elvégzéséhez a következőket tartalmazza:
    * A szükséges időt, hogy vizsgálja meg, és határozza meg, ha azt állíthatja helyre az adatait az elsődleges helyen, vagy ha igazolnia kell a feladatátvétellel.
    * A fiók az elsődleges DNS-bejegyzések módosításával feladatátvételt a másodlagos helyére mutasson.

   Az adatok nagyon súlyos megőrzi az, ha az adatok helyreállítását minden alkalommal, rendszer kis türelmet a feladatátvétel és az elsődleges helyen az adatok helyreállítását összpontosítani felelőssége vesszük. A jövőben tervezzük az API-t indítás, feladatátvétel, amely lehetővé teszi a majd vezérlését a RTO saját magának, fiók szintjén is biztosítanak, de ez még nem érhető el.
   
## <a name="next-steps"></a>Következő lépések
* [RA-GRS-tárolót magas rendelkezésre állású alkalmazások tervezése](../storage-designing-ha-apps-with-ragrs.md)
* [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)
* [Az Azure storage-fiókokról](../storage-create-storage-account.md)
* [Az Azure Storage méretezhetőségi és Teljesítménycélok](storage-scalability-targets.md)
* [A Microsoft Azure tárolás redundancia beállítások és olvasási hozzáférést földrajzi redundáns tárolás](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper - Azure Storage: Egy magas rendelkezésre állású felhőalapú tárolási szolgáltatásba erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

