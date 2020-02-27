---
title: Adatredundancia
titleSuffix: Azure Storage
description: A rendszer a Microsoft Azure Storage fiókjában lévő adatait a tartósság és a magas rendelkezésre állás érdekében replikálja. A redundancia-konfigurációk közé tartozik a helyileg redundáns tárolás (LRS), a zóna-redundáns tárolás (ZRS), a Geo-redundáns tárolás (GRS), a Read-Access geo-redundáns tárolás (RA-GRS), a Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) és az olvasási hozzáférés Geo-Zone-redundáns tárolás (RA-GZRS) (előzetes verzió).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6b36694c2fe1bf264c876944b054d39371db616c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614279"
---
# <a name="azure-storage-redundancy"></a>Azure Storage-redundancia

Az Azure Storage mindig több példányban tárolja az adatait, így védve van a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardver meghibásodását, a hálózati vagy áramkimaradást, valamint a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a Storage-fiókja a meghibásodások előtt is teljesítse az [Azure Storage szolgáltatói szerződését (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) .

Ha eldönti, hogy melyik redundancia-lehetőség a legmegfelelőbb a forgatókönyvéhez, vegye figyelembe az alacsonyabb költségek és a magasabb rendelkezésre állás és a tartósság közötti kompromisszumokat. Az alábbi tényezők segítenek meghatározni, hogy melyik redundancia-beállítást kell választania:  

- Hogyan replikálódnak az adatai az elsődleges régióban
- Azt jelzi, hogy a rendszer replikálja-e az adatait egy második helyre, amely földrajzilag az elsődleges régióban van, a regionális katasztrófák elleni védelem érdekében
- Azt jelzi, hogy az alkalmazásnak van-e olvasási hozzáférése a másodlagos régióban lévő replikált értékekhez, ha az elsődleges régió bármilyen okból elérhetetlenné válik

## <a name="redundancy-in-the-primary-region"></a>Redundancia az elsődleges régióban

Az Azure Storage-fiókban lévő adategységek mindig háromszor replikálódnak az elsődleges régióba. Az Azure Storage két lehetőséget kínál az adatai replikálására az elsődleges régióban:

- A **helyileg redundáns tárolás (LRS)** az adatfeldolgozást az elsődleges régióban lévő egyetlen fizikai helyen belül háromszor másolja. A LRS a legkevésbé költséges replikálási lehetőség, de nem ajánlott magas rendelkezésre állást igénylő alkalmazásokhoz.
- A **Zone-redundáns tárolás (ZRS)** az elsődleges régió három Azure-beli rendelkezésre állási zónájában szinkron módon másolja az adatait. A magas rendelkezésre állást igénylő alkalmazások esetében a Microsoft javasolja a ZRS használatát az elsődleges régióban, valamint egy másodlagos régióba való replikálást is.

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás

A helyileg redundáns tárolás (LRS) háromszor replikálja az adatait az elsődleges régióban található egyetlen fizikai helyen belül. A LRS legalább 99,999999999%-os (11 kilenc) tartósságot biztosít az objektumok számára egy adott évben.

A LRS a legalacsonyabb árú redundancia-beállítás, és a lehető legkevesebb tartósságot kínálja a többi lehetőséghez képest. A LRS megvédi adatait a Server rack és a meghajtó meghibásodása ellen. Ha azonban az adatközponton belül egy sérülés, például a tűz vagy az árvíz történik, a LRS használó Storage-fiókok összes replikája elveszhet vagy helyreállíthatatlan lehet. A kockázat enyhítése érdekében a Microsoft a [zóna-redundáns](#zone-redundant-storage) tárolás (ZRS), a [geo-redundáns tárolás](#geo-redundant-storage) (GRS) vagy a [geo-zóna-redundáns tárolás (előzetes verzió)](#geo-zone-redundant-storage-preview) (GZRS) használatát javasolja.

A LRS-t használó Storage-fiókra vonatkozó írási kérelem szinkron módon történik. Az írási művelet csak akkor tér vissza sikeresen, ha az adatok mindhárom replikára íródnak.

A LRS jó választás a következő esetekben:

- Ha az alkalmazás adatvesztés esetén könnyen újraépíthető adatait tárolja, a LRS is dönthet.
- Ha az alkalmazás az adatirányítási követelmények miatt csak az országon vagy régión belül replikálja az adatreplikálást, a LRS is dönthet. Bizonyos esetekben előfordulhat, hogy a párosított régiók, amelyeken az adatföldrajzi hely replikálása megtörténik, egy másik országban vagy régióban lehet. A párosított régiókkal kapcsolatos további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zónaredundáns tárolás

A Zone-redundáns tárolás (ZRS) az Azure Storage-adatait szinkron módon replikálja az elsődleges régió három Azure-beli rendelkezésre állási zónáján belül. Az egyes rendelkezésre állási zónák különálló, független energiaellátással, hűtéssel és hálózatkezeléssel ellátott fizikai helyet foglalnak magukban. A ZRS az Azure Storage-adatobjektumok tartósságát kínálja legalább 99,9999999999%-ban (12 9) az adott évben.

A ZRS esetében az adatok továbbra is elérhetők olvasási és írási műveletekhez, még akkor is, ha egy zóna elérhetetlenné válik. Ha egy zóna elérhetetlenné válik, az Azure hálózati frissítéseket végez, például a DNS-re. Ezek a frissítések hatással lehetnek az alkalmazásra, ha a frissítések befejeződése előtt hozzáférnek az adataihoz. A ZRS alkalmazások tervezésekor kövesse az átmeneti hibák kezelésének eljárásait, beleértve az újrapróbálkozási szabályzatok az exponenciális visszatartással történő megvalósítását.

A ZRS-t használó Storage-fiókra vonatkozó írási kérelem szinkron módon történik. Az írási művelet csak akkor ad eredményül, ha az adatok a három rendelkezésre állási zónában lévő összes replikára íródnak.

A Microsoft a ZRS használatát javasolja az elsődleges régióban olyan forgatókönyvek esetében, amelyek következetességet, tartósságot és magas rendelkezésre állást igényelnek. A ZRS kiváló teljesítményt, kis késleltetést és rugalmasságot biztosít az adatai számára, ha átmenetileg elérhetetlenné válik. A ZRS önmagában azonban nem nyújt védelmet az adatainak olyan regionális katasztrófák ellenében, ahol a rendszer több zónát tartósan érint. A regionális katasztrófák elleni védelem érdekében a Microsoft azt javasolja, hogy a [geo-Zone-redundáns tárolást](#geo-zone-redundant-storage-preview) (GZRS) használja, amely az elsődleges régióban lévő ZRS használja, és földrajzilag replikálja az adatait egy másodlagos régióba.

A következő táblázat azt mutatja be, hogy milyen típusú Storage-fiókok támogatják a ZRS, amelyekben a régiók:

|    Tárfiók típusa    |    Támogatott régiók    |    Támogatott szolgáltatások    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Általános célú v2<sup>1</sup>    | Délkelet-Ázsia<br /> Észak-Európa<br />  Nyugat-Európa<br /> Közép-Franciaország<br /> Kelet-Japán<br /> Az Egyesült Királyság déli régiója<br /> USA középső régiója<br /> USA keleti régiója<br /> USA 2. keleti régiója<br /> USA 2. nyugati régiója    |    Blokkblobok<br /> <sup>2</sup> . oldal Blobok<br /> Fájlmegosztás (standard)<br /> Táblák<br /> Üzenetsorok<br /> |
|    <sup>1</sup> . BlockBlobStorage    | Nyugat-Európa<br /> USA keleti régiója    |    Csak Blobok letiltása    |
|    FileStorage    | Nyugat-Európa<br /> USA keleti régiója    |    Csak Azure Files    |

<sup>1</sup> az archiválási szint jelenleg nem támogatott a ZRS-fiókok esetében.<br />
<sup>2</sup> a virtuális gépekhez készült Azure Managed Disks-t tartalmazó Storage-fiókok mindig a LRS-t használják. Az Azure Unmanaged Disks szolgáltatásnak a LRS is használnia kell. Létrehozhat egy Storage-fiókot az Azure nem felügyelt, GRS használó lemezek számára, de az aszinkron geo-replikációval kapcsolatos lehetséges problémák miatt nem ajánlott. Sem a felügyelt, sem a nem felügyelt lemezek támogatják a ZRS vagy a GZRS. A felügyelt lemezekkel kapcsolatos további információkért lásd: [Az Azure Managed Disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

További információ arról, hogy mely régiók támogatják a ZRS-t: a **szolgáltatások támogatása régiók szerint** a [Mi a Azure Availability Zones?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundancia egy másodlagos régióban

A magas rendelkezésre állást igénylő alkalmazások esetében választhatja azt is, hogy a Storage-fiókban lévő adatok egy olyan másodlagos régióba másolható, amely több száz kilométerre van az elsődleges régiótól. Ha a Storage-fiókot egy másodlagos régióba másolják, akkor az adatai tartósak maradnak, még akkor is, ha egy teljes regionális leállás vagy egy olyan katasztrófa következik be, amelyben az elsődleges régió nem helyreállítható.

A Storage-fiók létrehozásakor ki kell választania a fiók elsődleges régióját. A párosított másodlagos régió az elsődleges régió alapján van meghatározva, és nem módosítható. Az Azure által támogatott régiókkal kapcsolatos további információkért lásd: [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/).

Az Azure Storage két lehetőséget kínál az adatok másodlagos régióba történő másolására:

- A **geo-redundáns tárolás (GRS)** a LRS használatával háromszor másolja az adatait az elsődleges régióban található egyetlen fizikai helyen belül. Ezután aszinkron módon másolja át az adatait a másodlagos régió egyetlen fizikai helyére.
- A **geo-Zone-redundáns tárolás (GZRS)** (előzetes verzió) az elsődleges régió három Azure-beli rendelkezésre állási zónájában szinkronizálja az adatait az ZRS használatával. Ezután aszinkron módon másolja át az adatait a másodlagos régió egyetlen fizikai helyére.

A GRS és a GZRS közötti elsődleges különbség az, hogy az elsődleges régióban hogyan replikálódnak az adathalmazok. A másodlagos helyen a LRS használatával a rendszer mindig szinkron módon replikálja az adataikat.

A GRS vagy a GZRS esetében a másodlagos helyen lévő információk nem érhetők el olvasási vagy írási hozzáférés esetén, kivéve, ha feladatátvétel történik a másodlagos régióval. Ha olvasási hozzáférést szeretne adni a másodlagos helyhez, konfigurálja a Storage-fiókot az olvasási hozzáférésű geo-redundáns tárolás (RA-GRS) vagy az olvasási hozzáférésű földrajzi zóna – redundáns tárolás (RA-GZRS) használatára. További információ: [olvasási hozzáférés az adatokhoz a másodlagos régióban](#read-access-to-data-in-the-secondary-region).

Ha az elsődleges régió elérhetetlenné válik, dönthet úgy, hogy átadja a feladatátvételt a másodlagos régiónak (előzetes verzió). A feladatátvétel befejeződése után a másodlagos régió lesz az elsődleges régió, és újra elolvashatja és írhatja az adataikat. A vész-helyreállítással és a másodlagos régióba való feladatátvételsel kapcsolatos további információkért tekintse meg a vész- [helyreállítás és a fiók feladatátvétele (előzetes verzió)](storage-disaster-recovery-guidance.md)című témakört.

> [!IMPORTANT]
> Mivel az adatreplikáció a másodlagos régióba aszinkron módon történik, az elsődleges régiót érintő hibák adatvesztést okozhatnak, ha az elsődleges régió nem állítható helyre. Az elsődleges régióba irányuló legutóbbi írások és a másodlagos régió utolsó írása közötti intervallum a helyreállítási pont célkitűzése (RPO). A RPO azt az időpontot jelöli, ameddig az Adathelyreállítás visszaállítható. Az Azure Storage szolgáltatásnak jellemzően 15 percnél rövidebb RPO van, bár jelenleg nem áll rendelkezésre SLA, hogy mennyi ideig tart az adatreplikálás a másodlagos régióba.

### <a name="geo-redundant-storage"></a>Georedundáns tárolás

A Geo-redundáns tárolás (GRS) a LRS használatával háromszor másolja az adatait az elsődleges régióban található egyetlen fizikai helyen belül. Ezután aszinkron módon másolja az adatait egy olyan másodlagos régióba, amely több száz mérföld távolságra van az elsődleges régiótól. A GRS az Azure Storage-adatobjektumok tartósságát kínálja legalább 99.99999999999999%-ban (16 9) az adott évben.

A rendszer először egy írási műveletet véglegesít az elsődleges helyen, és replikálja a LRS használatával. A rendszer ezután aszinkron módon replikálja a frissítést a másodlagos régióba. Ha az adatírás a másodlagos helyre történik, a rendszer a LRS használatával is replikálja az adott helyen belül.

### <a name="geo-zone-redundant-storage-preview"></a>Geo-Zone-redundáns tárolás (előzetes verzió)

A Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) a rendelkezésre állási zónákon keresztüli redundancia által biztosított magas rendelkezésre állást ötvözi a földrajzi replikálás által biztosított regionális kimaradások elleni védelemmel. A GZRS-fiókban lévő adatok az elsődleges régióban három Azure-beli [rendelkezésre állási zónába](../../availability-zones/az-overview.md) másolódnak át, és a regionális katasztrófák elleni védelem érdekében a másodlagos földrajzi régióba is replikálódnak. A Microsoft a GZRS használatát javasolja a maximális következetességet, tartósságot és rendelkezésre állást, a kiváló teljesítményt és a vész-helyreállítási rugalmasságot igénylő alkalmazások számára.

GZRS-fiókkal folytathatja az adatok olvasását és írását, ha a rendelkezésre állási zónák elérhetetlenné válnak, vagy nem állíthatók helyre. Emellett az adatai tartósak is maradnak a teljes regionális leállás vagy egy olyan katasztrófa esetén, amelyben az elsődleges régió nem helyreállítható. A GZRS úgy lett kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben.

Csak az általános célú v2 tároló-fiókok támogatják a GZRS és az RA-GZRS. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](storage-account-overview.md). A GZRS és RA-GZRS támogatja a blokk blobokat, az oldal blobokat (kivéve a VHD-lemezeket), a fájlokat, a táblákat és a várólistákat.

A GZRS és az RA-GZRS jelenleg előzetes verzióként érhető el a következő régiókban:

- Délkelet-Ázsia
- Észak-Európa
- Nyugat-Európa
- Az Egyesült Királyság déli régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA középső régiója

A Microsoft továbbra is engedélyezi a GZRS és az RA-GZRS további Azure-régiókban. A támogatott régiókkal kapcsolatos információkért olvassa el rendszeresen az [Azure szolgáltatás frissítéseinek](https://azure.microsoft.com/updates/) oldalát.

Az előzetes verzió díjszabásával kapcsolatos információkért tekintse meg a [Blobok](https://azure.microsoft.com/pricing/details/storage/blobs), [fájlok](https://azure.microsoft.com/pricing/details/storage/files/), [várólisták](https://azure.microsoft.com/pricing/details/storage/queues/)és [táblák](https://azure.microsoft.com/pricing/details/storage/tables/)GZRS előzetes verziójának díjszabását.

> [!IMPORTANT]
> A Microsoft az előzetes verziójú funkciók használatát javasolja az éles számítási feladatokhoz.

## <a name="read-access-to-data-in-the-secondary-region"></a>Olvasási hozzáférés a másodlagos régióban lévő adatelérési ponthoz

A Geo-redundáns tárolás (GRS vagy GZRS) replikálja az adatait a másodlagos régió egy másik fizikai helyére a regionális kimaradások elleni védelem érdekében. Ezek az adatok azonban csak olvashatók, ha az ügyfél vagy a Microsoft feladatátvételt kezdeményez az elsődlegesről a másodlagos régióba. Ha engedélyezi az olvasási hozzáférést a másodlagos régióhoz, az adatai olvashatók lesznek, ha az elsődleges régió elérhetetlenné válik. Ha olvasási hozzáférést szeretne a másodlagos régióhoz, engedélyezze az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS) vagy az olvasási hozzáférésű földrajzi zóna-redundáns tárolást (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Alkalmazások tervezése a másodlagoshoz való olvasási hozzáféréshez

Ha a Storage-fiókja olvasási hozzáférésre van konfigurálva a másodlagos régióhoz, akkor megtervezheti, hogy az alkalmazások zökkenőmentesen átálljanak a másodlagos régió adatainak olvasására, ha az elsődleges régió bármilyen okból elérhetetlenné válik. A másodlagos régió mindig olvasási hozzáféréshez érhető el, így tesztelheti az alkalmazást, és ellenőrizheti, hogy a másodlagos esemény leáll-e leállás esetén. További információ az alkalmazások magas rendelkezésre állásra való tervezéséről: [magas rendelkezésre állású alkalmazások tervezése olvasási hozzáférésű geo-redundáns tárolással](storage-designing-ha-apps-with-ragrs.md).

Ha a másodlagos olvasási hozzáférés engedélyezve van, az adatok a másodlagos végpontból és a Storage-fiók elsődleges végpontján is olvashatók. A másodlagos végpont hozzáfűzi az utótagot *– a másodlagos* nevet a fiók nevéhez. Ha például a blob Storage elsődleges végpontja `myaccount.blob.core.windows.net`, akkor a másodlagos végpont `myaccount-secondary.blob.core.windows.net`. A Storage-fiókhoz tartozó fiók-hozzáférési kulcsok mind az elsődleges, mind a másodlagos végpont esetében azonosak.

### <a name="check-the-last-sync-time-property"></a>Az Utolsó szinkronizálás időpontja tulajdonság ellenőrzése

Mivel a rendszer aszinkron módon replikálja az adatfájlokat a másodlagos régióba, a másodlagos régió gyakran az elsődleges régió mögött van. Ha hiba történik az elsődleges régióban, akkor valószínű, hogy az elsődlegesnél az összes írás még nem lett replikálva a másodlagosra.

Annak megállapításához, hogy mely írási műveletek lettek replikálva a másodlagos régióba, az alkalmazás megtekintheti a Storage-fiók **utolsó szinkronizálási idő** tulajdonságát. Az elsődleges régióba az utolsó szinkronizálás időpontja előtt írt összes írási műveletet sikeresen replikálták a másodlagos régióba, ami azt jelenti, hogy elérhetők a másodlagos helyről való olvasáshoz. Az elsődleges régióba az utolsó szinkronizálás időpontja után írt írási műveletek nem replikálódtak a másodlagos régióra, ami azt jelenti, hogy nem lesznek elérhetők olvasási műveletekhez.

A **Legutóbbi szinkronizálási idő** tulajdonság értékét a Azure PowerShell, az Azure CLI vagy az Azure Storage ügyféloldali kódtárai használatával kérdezheti le. Az **utolsó szinkronizálás időpontja** tulajdonság egy GMT dátum/idő érték. További tudnivalókért tekintse [meg a Storage-fiók utolsó szinkronizálási idejének tulajdonságát](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Redundancia-beállítások összefoglalása

Az alábbi táblázat azt mutatja be, hogy milyen tartós és elérhető az adatai egy adott helyzetben, attól függően, hogy milyen típusú redundancia van érvényben a Storage-fiókhoz:

| Forgatókönyv                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (előzetes verzió)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Az adatközpontban lévő csomópont elérhetetlenné válik                                                                 | Igen                             | Igen                              | Igen                                  | Igen                                  |
| Egy teljes adatközpont (Zona vagy nem zónák) elérhetetlenné válik                                           | Nem                              | Igen                              | Igen                                  | Igen                                  |
| Az egész régióra kiterjedő leállás következik be                                                                                     | Nem                              | Nem                               | Igen                                  | Igen                                  |
| Olvasási hozzáférés a másodlagos régióban lévő adateléréshez, ha az elsődleges régió elérhetetlenné válik | Nem                              | Nem                               | Igen (az RA-GRS-vel)                                   | Igen (az RA-GZRS-vel)                                 |
| Objektumok tartóssága az adott évben<sup>1</sup>                                          | legalább 99,999999999% (11 9) | legalább 99,9999999999% (12 9) | legalább 99.99999999999999% (16 9) | legalább 99.99999999999999% (16 9) |
| Támogatott Storage-fiók típusai<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Rendelkezésre állási SLA az olvasási kérelmekhez<sup>1</sup>  | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) a GRS<br /><br />Legalább 99,99% (99,9% a lassú elérési szinthez) az RA-GRS | Legalább 99,9% (99% a lassú elérési szinthez) a GZRS<br /><br />Legalább 99,99% (99,9% a lassú elérési szinthez) az RA-GZRS |
| Az írási kérelmek rendelkezésre állási SLA-ja<sup>1</sup>  | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) | Legalább 99,9% (99% a lassú elérési szinthez) |

<sup>1</sup> az Azure Storage szolgáltatás tartósságának és rendelkezésre állásának garantálása érdekében az [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/)-ban talál további információt.

<sup>2</sup> a Storage-fiókok típusaival kapcsolatos információkért lásd: a [Storage-fiók áttekintése](storage-account-overview.md).

A rendszer az összes típusú Storage-fiókhoz tartozó összes adatmennyiséget a Storage-fiók redundancia beállításának megfelelően másolja. Az objektumok, például a Blobok, a Blobok, a Blobok, a várólisták, a táblák és a fájlok másolása történik.

Az egyes redundancia-lehetőségek díjszabásáról az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/)ismertető cikk nyújt tájékoztatást.

> [!NOTE]
> Az Azure Premium Disk Storage jelenleg csak a helyileg redundáns tárolást (LRS) támogatja. A blob Storage-fiókok nem támogatják a helyileg redundáns tárolást (LRS) és a Zone redundáns tárolást (ZRS) bizonyos régiókban.

## <a name="data-integrity"></a>Adatok integritása

Az Azure Storage rendszeresen ellenőrzi a ciklikus redundancia-ellenőrzések (FCSF) használatával tárolt adatok integritását. Ha a rendszer az adatsérülést észleli, a redundáns adatvesztéssel kerül kijavításra. Az Azure Storage az összes hálózati forgalom ellenőrzőösszegét is kiszámítja, hogy észlelje az adatcsomagok sérülését az adatok tárolása vagy beolvasása során.

## <a name="see-also"></a>Lásd még

- [A Storage-fiók utolsó szinkronizálási ideje tulajdonságának megtekintése](last-sync-time-get.md)
- [Storage-fiók redundancia beállításának módosítása](redundancy-migration.md)
- [Magasan elérhető alkalmazások tervezése RA-GRS Storage használatával](../storage-designing-ha-apps-with-ragrs.md)
- [Vész-helyreállítás és fiók feladatátvétele (előzetes verzió)](storage-disaster-recovery-guidance.md)
