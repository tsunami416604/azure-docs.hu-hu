---
title: Az Azure Storage adatreplikáció |} Microsoft Docs
description: A Microsoft Azure Storage-fiók adatait a tartósság és magas rendelkezésre állású replikálja a rendszer. Replikációs beállítások közé tartozik a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS), a georedundáns tárolás (GRS) és az írásvédett georedundáns tárolás (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 600b66af3b7da24c5a40d09d5cdf76f2d5be67ac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-storage-replication"></a>Azure Storage replication (Azure Storage replikáció)

A Microsoft Azure tárfiók tartalmát mindig replikáljuk, így biztosítva az adatok tartósságát és magas rendelkezésre állását. Replikációs másolja át az adatokat, hogy átmeneti hardverhibák esetén is, hogy megőrizze-e az alkalmazás-os üzemidőt védve van. 

Ha szeretné belül azonos adatközpontba, az adatok replikálásához adatközpontokban a régión belül, vagy az régiók. Ha az adatok több különböző adatközponthoz vagy az régiók replikálódik, azt is védve van egy helyen végzetes hiba.

A replikáció biztosítja, hogy a tárfiók még hibák esetén is teljesíti a [Storage szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) feltételeit. Tekintse át az Azure Storage tartóssági és rendelkezésre állási garanciáit a szolgáltatói szerződésben .

Tárfiók létrehozásakor választhat a következő replikációs lehetőségek közül:

* [Helyileg redundáns tárolás (LRS)](#locally-redundant-storage)
* [Zónaredundáns tárolás (ZRS)](#zone-redundant-storage)
* [Georedundáns tárolás (GRS)](#geo-redundant-storage)
* [Írásvédett georedundáns tárolás (RA-GRS)](#read-access-geo-redundant-storage)

Helyileg redundáns tárolás (LRS) beállítás az alapértelmezett tárfiók létrehozásakor.

Az alábbi táblázat egy gyors áttekintést nyújt az LRS, a zrs-t, a GRS és az RA-GRS közötti különbségeket. A cikk későbbi szakaszaiban részletesebben replikációs különböző típusú cím.

| Replikációs stratégia | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Több adatközpont között replikálva. |Nem |Igen |Igen |Igen |
| A másodlagos helyre, valamint az elsődleges helyre is lehet adatokat olvasni. |Nem |Nem |Nem |Igen |
| Célja, hogy az objektumok ___ tartósságot biztosít egy adott évben képest. |legalább 99.999999999 % (11 9 meg)|legalább 99.9999999999 % (12 9 meg)|legalább 99.99999999999999 % (16 9 meg)|legalább 99.99999999999999 % (16 9 meg)|

Lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) a díjszabásról különböző redundancia beállításokkal.

> [!NOTE]
> Prémium szintű Storage támogatja a csak a helyileg redundáns tárolás (LRS). Prémium szintű Storage kapcsolatos információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../../virtual-machines/windows/premium-storage.md).
>

## <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>Redundáns tárolási zóna
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>A ZRS klasszikus fiókok

A meglévő ZRS funkció most nevezzük ZRS klasszikus. A klasszikus ZRS-fiókok csak blokkblobokhoz érhetők el az általános célú v1-tárfiókok esetén. 

A klasszikus ZRS az adatokat aszinkron módon replikálja egy vagy két régió adatközpontjaiba. Lehetséges, hogy a replika nem lesz elérhető, hacsak a Microsoft nem kezdeményezi a másodlagos példány feladatátvételét. 

A ZRS klasszikus fiókokat nem lehet konvertálni, vagy a LRS, grs-re vagy RA-GRS. A ZRS klasszikus fiókok is nem támogatják az metrikákat és naplózási.   

A ZRS régióban általánosan elérhetővé válik, nem tudnak a ZRS klasszikus-fiók létrehozása a portál az adott régióban, de létrehozhat egy más módon.  
Egy automatizált áttelepítési folyamatot a ZRS klasszikus ZRS biztosítja a jövőben.

Manuálisan áttelepítheti a ZRS fiók adatait, vagy a LRS, ZRS klasszikus, Georedundáns vagy RAGRS fiókkal. Az AzCopy, Azure Tártallózó, Azure PowerShell, az Azure parancssori felület és az Azure Storage ügyfélkódtáraival egyik manuális áttelepítés végezheti el.

> [!NOTE]
> A ZRS klasszikus fiókok érvénytelenítése és 2021 március 31 szükséges áttelepítésének tervbe van véve. Microsoft elküld további részleteket a ZRS klasszikus ügyfelek elévülése előtt.

További kérdések tárgyalja a [gyakran ismételt kérdések](#frequently-asked-questions) szakasz. 

## <a name="geo-redundant-storage"></a>Georedundáns tárolás
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás
Írásvédett georedundáns tárolás (RA-GRS) a lehető legnagyobbra növeli a tárfiók rendelkezésre állását. RA-GRS az adatokat a másodlagos helyen mellett georeplikáció két régióban is olvasási hozzáférést biztosít.

Csak olvasási hozzáféréssel a másodlagos régióba adatai engedélyezésekor az adatok érhető el a másodlagos végponti, valamint a tárfiók elsődleges végpont. A másodlagos végponti az elsődleges végpont hasonló, de a utótag `–secondary` fióknevet. Például, ha az elsődleges végpont a Blob szolgáltatás `myaccount.blob.core.windows.net`, akkor a másodlagos végponti `myaccount-secondary.blob.core.windows.net`. A tárfiók hozzáférési kulcsainak megegyeznek az elsődleges és másodlagos végpontok.

RA-GRS használatakor szem előtt tartani szempontokat:

* Az alkalmazás melyik végponthoz az RA-GRS használatakor dolgozik kezelésére van.
* Mivel aszinkron replikációs késés, a módosítások, amelyek még nem replikálódott a másodlagos régióba elveszhetnek, ha adatokat nem lehet helyreállítani az elsődleges régióban, például regionális katasztrófa esetén.
* Ha a Microsoft a másodlagos régióba történő feladatátvételt kezdeményez, hogy fog rendelkezik olvasási és írási engedéllyel a feladatátvételt követően befejeződött. További információkért lásd: [vész-helyreállítási útmutatást](../storage-disaster-recovery-guidance.md).
* RA-GRS magas rendelkezésre állású célokra szolgál. Méretezhetőség útmutatásért tekintse át a [teljesítmény ellenőrzőlista](../storage-performance-checklist.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1. Hogyan lehet módosítani a tárfiókot georeplikáció típusú?

   A tárfiók georeplikáció típusú használatával módosíthatja a [Azure-portálon](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), vagy az Azure Storage ügyfélkódtáraival egyikét.

   > [!NOTE]
   > A ZRS fiókok konvertált LRS- vagy GRS nem lehet. Hasonlóképpen meglévő LRS- vagy GRS fiók nem alakítható át a ZRS fiók.
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2. Nem a replikációs típusa eredményének a tárolási fiók az állásidő?

   Nem, a replikáció típusa a tárfiók nem eredményez állásidő.

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>3. Vannak-e további költségeket a replikáció típusa a storage-fiókom?

   Igen. Ha módosítja az LRS tárfiók grs-re (vagy RA-GRS), Ön tudomásával egy a meglévő adatok másolása az elsődleges helyről másodlagos helyre részt kilépő kell külön fizetni. Miután a kezdeti adatok másolását követően nincsenek további további kimenő költségek a georeplikációért az elsődleges másodlagos helyre. További részletek a sávszélesség-költségek: [Azure Storage szolgáltatás díjszabása lap](https://azure.microsoft.com/pricing/details/storage/blobs/).

   Ha átvált a Georedundáns LRS, további költség nélkül van, de az adatokat töröltek, amelyek a másodlagos helyre.

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4. Hogyan RA-GRS segítik számomra?

   Georedundáns tárolás biztosítja az adatok egy másodlagos régióban, amely több száz elhagyja az elsődleges régióban miles egy elsődleges replikáció. A GRS a adata tartós még a teljes regionális kimaradás vagy az elsődleges régióban nincs helyreállítható katasztrófa esetén. RA-GRS tárolási kínál a Georedundáns replikáció, és lehetővé teszik a beolvasni az adatokat a másodlagos helyre. A magas rendelkezésre állású tervezéséről a javaslatok, lásd: [tervezése magas rendelkezésre álló alkalmazások RA-GRS-tárolót](../storage-designing-ha-apps-with-ragrs.md).

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5. Van olyan módon, hogy mennyi ideig tart a másodlagos régióba replikálja a az adatokat az elsődleges?

   Ha RA-GRS tárhelyet használ, ellenőrizheti a legutóbbi szinkronizálásának időpontja a tárfiók. Utolsó szinkronizálás ideje GMT dátum/idő értéket. Minden elsődleges írás előtt a legutóbbi szinkronizálás ideje sikeresen elkészült a másodlagos helyre, ami azt jelenti, hogy elérhetők a másodlagos helyről olvasható. Elsődleges írása után utolsó szinkronizálásának időpontja is, vagy nem érhető el az olvasási műveletek még. Az érték használatával kérdezheti a [Azure-portálon](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), vagy az Azure Storage ügyfélkódtáraival egyikéből.

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6. Ha az elsődleges régióban kimaradás, átváltása a másodlagos régióba?

   További információkért lásd: [Mi a teendő, ha egy Azure Storage esetleges leálláskor](../storage-disaster-recovery-guidance.md).

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7. Mi az a helyreállítási Időkorlát és a GRS RTO?

   **Helyreállítás időkorlát (RPO):** Georedundáns és RA-GRS, a tárolás szolgáltatás aszinkron módon földrajzi-replikálja az adatokat a másodlagos hely az elsődleges. Legyen fő regionális katasztrófahelyzet esetén az elsődleges régióban Microsoft hajtja végre a feladatátvételt a másodlagos régióba. A feladatátvétel történik, ha a legutóbbi módosítások, amelyek még nincsenek georeplikált elveszhetnek. A potenciális elveszett adatok percet nevezzük a helyreállítási Időkorlát, és a pont azt jelzi, amelyhez az adatok helyreállíthatók időben. Az Azure Storage általában kevesebb mint 15 perces az RPO, akkor bár jelenleg nincs SLA georeplikáció mennyi ideig tart.

   **A helyreállítási idő célkitűzése (RTO):** a RTO azt méri, hogy mennyi ideig tart a tárfiók újra online, és végezze el a feladatátvételt. Az idő a feladatátvétel elvégzéséhez a következő műveleteket tartalmazza:

   * Az idő a Microsoft megköveteli az adatok helyreállíthatók az elsődleges helyen, vagy ha a feladatátvétel során szükséges.
   * Az idő hajtsa végre a feladatátvételt a tárfiók módosítása az elsődleges DNS-bejegyzéseket a másodlagos helyére mutasson.

   A Microsoft felelőssége súlyosan megőrzi az adatokat fogad. Ha az elsődleges régióban adatok helyreállítását minden alkalommal, Microsoft késleltetés a feladatátvételt, és összpontosítson, az adatok helyreállításáról. Egy jövőbeli verziójában a szolgáltatás lehetővé teszi a fiók szintjén feladatátvevő indul el, így felügyelheti a RTO saját maga.

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8. Milyen Azure tárolási objektum nem támogatja a zrs-t? 
Blokkblobokat, lapblobokat (kivéve a virtuális gép biztonsági lemezek), táblák, fájlok és a várólisták. 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9. A ZRS is tartalmaz a georeplikáció? 
A ZRS jelenleg nem támogatja a georeplikáció. Ha adott esetben szükség van a kereszt-régió replikációs katasztrófa utáni helyreállítás céljából, grs-re vagy RA-GRS tárolási fiókot használni.   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10. Mi történik, ha egy vagy több ZRS zónák leáll? 
Amikor az első zónára leáll, a zrs-t továbbra is replikákat az adatok írása között a két fennmaradó zónákat a régióban. Ha a második zóna leáll, olvasási és írási hozzáférést nem használható, amíg legalább két zónák újra be nem érhető el. 

## <a name="next-steps"></a>További lépések
* [RA-GRS-tárolót magas rendelkezésre állású alkalmazások tervezése](../storage-designing-ha-apps-with-ragrs.md)
* [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)
* [Az Azure storage-fiókokról](../storage-create-storage-account.md)
* [Az Azure Storage méretezhetőségi és Teljesítménycélok](storage-scalability-targets.md)
* [A Microsoft Azure tárolás redundancia beállítások és olvasási hozzáférést földrajzi redundáns tárolás ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP Paper - Azure Storage: Egy magas rendelkezésre állású felhőalapú tárolási szolgáltatásba erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
