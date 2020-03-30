---
title: Adatredundancia
titleSuffix: Azure Storage
description: A Microsoft Azure Storage-fiókban lévő adatok replikálódnak a tartósság és a magas rendelkezésre állás érdekében. A redundanciakonfigurációk közé tartozik a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS), a georedundáns tárolás (GRS), az olvasási hozzáférésű georedundáns tárolás (RA-GRS), a geozóna redundáns tárolása (GZRS) (előzetes verzió) és az olvasási hozzáférés geozónaredundáns tárolás (RA-GZRS) (előzetes verzió).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7ae5f59a1bd96362d5466b2f6363185ba168d942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255261"
---
# <a name="azure-storage-redundancy"></a>Az Azure Storage redundanciája

Az Azure Storage mindig tárolja az adatok több példányát, hogy védve legyenak a tervezett és nem tervezett eseményekkel szemben, beleértve az átmeneti hardverhibákat, a hálózati vagy áramkimaradásokat és a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a tárfiók még a hibák ellenére is megfeleljen az [Azure Storage szolgáltatásiszintű szerződésének (SLA).](https://azure.microsoft.com/support/legal/sla/storage/)

Annak eldöntésekor, hogy melyik redundancia-beállítás a legjobb a forgatókönyvhöz, vegye figyelembe az alacsonyabb költségek és a magasabb rendelkezésre állás és tartósság közötti kompromisszumokat. A következő tényezők határozzák meg, hogy melyik redundanciabeállítást válassza:  

- Az adatok replikálásának oka az elsődleges régióban
- Azt jelzi, hogy az adatok replikálása az elsődleges régiótól földrajzilag távoli második helyre történik-e a regionális katasztrófák elleni védelem érdekében
- Azt jelzi, hogy az alkalmazás olvasási hozzáférést igényel-e a másodlagos régióban lévő replikált adatokhoz, ha az elsődleges régió bármilyen okból elérhetetlenné válik

## <a name="redundancy-in-the-primary-region"></a>Redundancia az elsődleges régióban

Az Azure Storage-fiókban lévő adatok mindig háromszor replikálódnak az elsődleges régióban. Az Azure Storage két lehetőséget kínál az adatok replikálásának módjára az elsődleges régióban:

- **A helyileg redundáns tárolás (LRS)** az adatokat szinkron módon háromszor másolja át egyetlen fizikai helyen az elsődleges régióban. Az LRS a legkevésbé költséges replikációs lehetőség, de nem ajánlott a magas rendelkezésre állást igénylő alkalmazások.
- **Zónaredundáns tárolás (ZRS)** másolja az adatokat szinkron módon három Azure rendelkezésre állási zónák az elsődleges régióban. A magas rendelkezésre állást igénylő alkalmazások esetében a Microsoft a ZRS használatát javasolja az elsődleges régióban, és egy másodlagos régióra is replikálja.

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás

A helyileg redundáns tárolás (LRS) háromszor replikálja az adatokat egyetlen fizikai helyen belül az elsődleges régióban. Az LRS legalább 99,999999999999% (11 kilences) tartósságot biztosít a tárgyakszámára egy adott évben.

Az LRS a legalacsonyabb költségű redundancia lehetőség, és a legkevésbé tartósságot kínálja más lehetőségekhez képest. Az LRS megvédi az adatokat a kiszolgálóállvány- és meghajtóhibáktól. Ha azonban egy katasztrófa, például a tűz vagy az árvíz történik az adatközponton belül, az LRS-t használó tárfiók összes replikája elveszhet vagy helyreállíthatatlan lehet. A kockázat csökkentése érdekében a Microsoft [zónaredundáns tárolás](#zone-redundant-storage) (ZRS), [georedundáns tárolás](#geo-redundant-storage) (GRS) vagy [geozónaredundáns tárolás (előzetes verzió) (GZRS)](#geo-zone-redundant-storage-preview) használatát javasolja.

Az LRS-t használó tárfiók írási kérelme szinkron módon történik. Az írási művelet csak akkor ad vissza sikeresen, ha az adatok mindhárom replikába írásra került.

Az LRS jó választás a következő esetekben:

- Ha az alkalmazás olyan adatokat tárol, amelyek adatvesztés esetén könnyen rekonstruálhatók, az LRS-t választhatja.
- Ha az alkalmazás csak egy országon vagy régión belül replikálhatja az adatokat az adatkezelési követelmények miatt, választhatja az LRS-t. Bizonyos esetekben a párosított régiók, amelyeken keresztül az adatok georeplikált lehet egy másik országban vagy régióban. A párosított régiókról az [Azure-régiók](https://azure.microsoft.com/regions/)című témakörben talál további információt.

### <a name="zone-redundant-storage"></a>Zónaredundáns tárolás

Zónaredundáns tárolás (ZRS) replikálja az Azure Storage-adatok szinkron módon három Azure rendelkezésre állási zónák az elsődleges régióban. Minden rendelkezésre állási zóna egy külön fizikai hely, független áramellátással, hűtéssel és hálózatépítéssel. A ZRS legalább 99,99999999999999%-os (12 9-es) tartósságot kínál az Azure Storage-adatobjektumokhoz egy adott évben.

A ZRS-szel az adatok továbbra is elérhetők az olvasási és írási műveletekhez, még akkor is, ha egy zóna elérhetetlenné válik. Ha egy zóna elérhetetlenné válik, az Azure vállalja a hálózati frissítéseket, például a DNS újbóli rámutatását. Ezek a frissítések hatással lehetnek az alkalmazásra, ha a frissítések befejezése előtt hozzáfér az adatokhoz. A ZRS-alkalmazások tervezésekor kövesse az átmeneti hibakezelés gyakorlatait, beleértve az újrapróbálkozási szabályzatok exponenciális visszalépéssel történő megvalósítását.

A ZRS-t használó tárfiókírási kérelem szinkron módon történik. Az írási művelet csak akkor ad vissza sikeresen, ha az adatok a három rendelkezésre állási zónában lévő összes replikába írásra kerül.

A Microsoft azt javasolja, hogy a ZRS az elsődleges régióban olyan forgatókönyvek, amelyek konzisztenciát, tartósságot és magas rendelkezésre állást igénylő forgatókönyvek használata. A ZRS kiváló teljesítményt, alacsony késleltetést és rugalmasságot biztosít az adatok számára, ha átmenetileg elérhetetlenné válik. Előfordulhat azonban, hogy a ZRS önmagában nem védi meg az adatokat egy olyan regionális katasztrófa ellen, amely tartósan több zónát érint. A regionális katasztrófák elleni védelem érdekében a Microsoft a [geozóna-redundáns tárolás](#geo-zone-redundant-storage-preview) (GZRS) használatát javasolja, amely az elsődleges régióban használja a ZRS-t, és földrajzilag replikálja az adatokat egy másodlagos régióba.

Az alábbi táblázat bemutatja, hogy milyen típusú tárfiókok támogatják a ZRS-t, mely régiókban:

|    Tárfiók típusa    |    Támogatott régiók    |    Támogatott szolgáltatások    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Általános célú v2<sup>1</sup>    | Délkelet-Ázsia<br /> Kelet-Ausztrália<br /> Észak-Európa<br />  Nyugat-Európa<br /> Közép-Franciaország<br /> Kelet-Japán<br /> Dél-Afrika Észak-Afrika<br /> Az Egyesült Királyság déli régiója<br /> USA középső régiója<br /> USA keleti régiója<br /> USA 2. keleti régiója<br /> USA 2. nyugati régiója    |    Blokkblobok<br /> Lapblobok<sup>2</sup><br /> Fájlmegosztások (standard)<br /> Táblák<br /> Üzenetsorok<br /> |
|    BlockBlobStorage<sup>1</sup>    | Nyugat-Európa<br /> USA keleti régiója    |    Csak a blobok blokkolása    |
|    FileStorage (Fájltárolás)    | Nyugat-Európa<br /> USA keleti régiója    |    Csak Azure-fájlok    |

<sup>1</sup> Az archív szint jelenleg nem támogatott ZRS-fiókok esetén.<br />
<sup>2</sup> A virtuális gépekhez azure-beli felügyelt lemezeket tartalmazó tárfiókok mindig LRS-t használnak. Az Azure nem felügyelt lemezek is lrs-t kell használnia. A GRS-t használó Azure nem felügyelt lemezek hez létrehozhat egy tárfiókot, de az aszinkron georeplikációval kapcsolatos lehetséges problémák miatt nem ajánlott. Sem a felügyelt, sem a nem felügyelt lemezek nem támogatják a ZRS-t vagy a GZRS-t. A felügyelt lemezekről további információt az [Azure által felügyelt lemezek díjszabása című témakörben talál.](https://azure.microsoft.com/pricing/details/managed-disks/)

AZSE-t támogató régiókról a **Szolgáltatások régiónkénti támogatása** a [Mik azok az Azure rendelkezésre állási zónák című témakörben találhatók.](../../availability-zones/az-overview.md)

## <a name="redundancy-in-a-secondary-region"></a>Redundancia másodlagos régióban

A magas rendelkezésre állást igénylő alkalmazások esetében dönthet úgy, hogy a tárfiókban lévő adatokat egy másodlagos régióba másolja, amely több száz mérföldre van az elsődleges régiótól. Ha a tárfiók egy másodlagos régióba másolva, majd az adatok tartós a teljes regionális kimaradás vagy katasztrófa, amelyben az elsődleges régió nem helyreállítható.

Amikor létrehoz egy tárfiókot, válassza ki a fiók elsődleges régióját. A párosított másodlagos régió az elsődleges régió alapján van meghatározva, és nem módosítható. Az Azure által támogatott régiókról az [Azure-régiók című témakörben](https://azure.microsoft.com/global-infrastructure/regions/)talál további információt.

Az Azure Storage két lehetőséget kínál az adatok másodlagos régióba másolására:

- **Georedundáns tárolás (GRS)** másolja az adatokat szinkronban háromszor egyetlen fizikai helyen az elsődleges régióban az LRS használatával. Ezután aszinkron módon másolja az adatokat a másodlagos régió egyetlen fizikai helyére.
- **A geozóna redundáns tárolása (GZRS)** (előzetes verzió) szinkron módon másolja az adatokat három Azure-rendelkezésre állási zónába az elsődleges régióban a ZRS használatával. Ezután aszinkron módon másolja az adatokat a másodlagos régió egyetlen fizikai helyére.

A GRS és a GZRS közötti elsődleges különbség az adatok replikálásának módját az elsődleges régióban. A másodlagos helyen belül az adatok mindig háromszor replikálódik az LRS használatával.

GrS vagy GZRS esetén a másodlagos helyen lévő adatok nem érhetők el olvasási vagy írási hozzáféréssel, kivéve, ha a másodlagos régió feladatátvétel. A másodlagos hely olvasási hozzáféréséhez konfigurálja úgy a tárfiókot, hogy olvasási hozzáférésű georedundáns tárolást (RA-GRS) vagy olvasási hozzáférésű geozónaredundáns tárolást (RA-GZRS) használjon. További információ: [Olvasási hozzáférés a másodlagos régióban lévő adatokhoz.](#read-access-to-data-in-the-secondary-region)

Ha az elsődleges régió elérhetetlenné válik, dönthet úgy, hogy a másodlagos régióba (előzetes verzió) adja át a feladatátvételt. Miután a feladatátvétel befejeződött, a másodlagos régió lesz az elsődleges régió, és újra olvashatja és írhatja az adatokat. A vész-helyreállítási és a másodlagos régió feladatátvételi feladatátvételről további információt a [Vész-helyreállítás és a fiókfeladat-átvétel (előzetes verzió) című témakörben](storage-disaster-recovery-guidance.md)talál.

> [!IMPORTANT]
> Mivel az adatok aszinkron módon replikálódnak a másodlagos régióba, az elsődleges régiót érintő hiba adatvesztést okozhat, ha az elsődleges régió nem állítható helyre. Az elsődleges régióba írt legutóbbi írások és a másodlagos régióba történő utolsó írás közötti időközt helyreállításipont-célkitűzésnek (RPO) nevezzük. Az RPO azt az időpontot jelzi, amerre az adatok helyrevihetők. Az Azure Storage általában rendelkezik egy RPO kevesebb, mint 15 perc, bár jelenleg nincs SLA, hogy mennyi ideig tart az adatok replikálása a másodlagos régióba.

### <a name="geo-redundant-storage"></a>Georedundáns tárolás

Georedundáns tárolás (GRS) másolja az adatokat szinkronban háromszor egyetlen fizikai helyen az elsődleges régióban az LRS használatával. Ezután aszinkron módon másolja az adatokat egyetlen fizikai helyre egy másodlagos régióban, amely több száz mérföldre van az elsődleges régiótól. A GRS legalább 99,9999999999999999999999999999999999999999999999999999999999999999999999/9%-os élettartamot biztosít az Azure Storage-adatobjektumok számára egy adott évben.

Az írási művelet először véglegesítve van az elsődleges helyre, és LRS használatával replikálódik. A frissítés ezután aszinkron módon replikálódik a másodlagos régióba. Amikor az adatok a másodlagos helyre vannak írva, az LRS használatával is replikálódik az adott helyen.

### <a name="geo-zone-redundant-storage-preview"></a>Geozónaredundáns tárolás (előzetes verzió)

Geozóna-redundáns tárolás (GZRS) (előzetes verzió) egyesíti a magas rendelkezésre állási kapacitás a rendelkezésre állási zónák között a georeplikáció által biztosított regionális kimaradások elleni védelemmel kombinálható. A GZRS-tárfiókban lévő adatok az elsődleges régió három [Azure-rendelkezésre állási zónájában](../../availability-zones/az-overview.md) vannak átmásolva, és egy másodlagos földrajzi régióba is replikálódnak a regionális katasztrófák elleni védelem érdekében. A Microsoft azt javasolja, hogy a GZRS-t olyan alkalmazásokhoz használja, amelyek maximális konzisztenciát, tartósságot és rendelkezésre állást, kiváló teljesítményt és vészhelyreállítás rugalmasságát igénylik.

GZRS tárfiókkal folytathatja az adatok olvasását és írását, ha egy rendelkezésre állási zóna elérhetetlenné válik, vagy nem helyreállítható. Emellett az adatok is tartós a teljes regionális kimaradás vagy katasztrófa, amelyben az elsődleges régió nem helyreállítható. A GZRS-t úgy tervezték, hogy egy adott évben legalább 99,999999999999999999999999999%- os (16 9-es) tartósságot biztosítson.

Csak az általános célú v2-es tárfiókok támogatják a GZRS-t és az RA-GZRS-t. A tárfiók-típusokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](storage-account-overview.md) A GZRS és a RA-GZRS támogatja a blokkblobokat, a lapblobokat (a Virtuális merevlemezek kivételével), a fájlokat, a táblákat és a várólistákat.

A GZRS és a RA-GZRS jelenleg a következő régiókban érhető el előzetes verzióban:

- Délkelet-Ázsia
- Észak-Európa
- Nyugat-Európa
- Kelet-Japán
- Az Egyesült Királyság déli régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA középső régiója
- USA 2. nyugati régiója

A Microsoft továbbra is engedélyezi a GZRS-t és a RA-GZRS-t további Azure-régiókban. A támogatott régiókkal kapcsolatos információkért rendszeresen tekintse meg az [Azure-szolgáltatások frissítései](https://azure.microsoft.com/updates/) lapot.

Az előzetes verzió díjszabásáról a [GZRS-előnézeti díjszabása a Blobok,](https://azure.microsoft.com/pricing/details/storage/blobs) [fájlok,](https://azure.microsoft.com/pricing/details/storage/files/) [várólisták](https://azure.microsoft.com/pricing/details/storage/queues/)és [táblázatok](https://azure.microsoft.com/pricing/details/storage/tables/)esetében.

> [!IMPORTANT]
> A Microsoft azt javasolja, hogy ne használjon előzetes funkciókat az éles számítási feladatokhoz.

## <a name="read-access-to-data-in-the-secondary-region"></a>Olvasási hozzáférés az adatokhoz a másodlagos régióban

A georedundáns tárolás (GRS vagy GZRS segítségével) replikálja az adatokat a másodlagos régió egy másik fizikai helyére, hogy megvédje a regionális kimaradások elleni védelmet. Ezek az adatok azonban csak akkor olvashatók, ha az ügyfél vagy a Microsoft feladatátvételt kezdeményez az elsődleges és a másodlagos régió között. Ha engedélyezi az olvasási hozzáférést a másodlagos régióhoz, az adatok olvashatók lesznek, ha az elsődleges régió elérhetetlenné válik. Olvasási hozzáférés a másodlagos régió, engedélyezze az olvasási hozzáférés georedundáns tárolás (RA-GRS) vagy olvasási hozzáférés geozóna redundáns tárolás (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Tervezze meg alkalmazásait olvasási hozzáféréshez a másodlagos

Ha a tárfiók olvasási hozzáférésa a másodlagos régióhoz, majd úgy tervezheti meg az alkalmazásokat, hogy zökkenőmentesen váltson át a másodlagos régióból származó adatok olvasására, ha az elsődleges régió bármilyen okból elérhetetlenné válik. A másodlagos régió mindig elérhető olvasási hozzáférés, így tesztelheti az alkalmazást, hogy győződjön meg arról, hogy a másodlagos kimaradás esetén olvasni fog. Az alkalmazások magas rendelkezésre állásra való tervezéséről a magas [rendelkezésre állású alkalmazások tervezése olvasható georedundáns tárolással című](storage-designing-ha-apps-with-ragrs.md)témakörben olvashat bővebben.

Ha a másodlagos olvasási hozzáférés engedélyezve van, az adatok a másodlagos végpontról, valamint a tárfiók elsődleges végpontjáról is olvashatók. A másodlagos végpont hozzáfűzi az utótagot *– másodlagos* a fiók nevéhez. Ha például a Blob storage elsődleges `myaccount.blob.core.windows.net`végpontja a , `myaccount-secondary.blob.core.windows.net`majd a másodlagos végpont. A tárfiók fiókhozzáférési kulcsai megegyeznek az elsődleges és a másodlagos végpontok esetében is.

### <a name="check-the-last-sync-time-property"></a>Az Utolsó szinkronizálás időpontja tulajdonság ellenőrzése

Mivel az adatok aszinkron módon replikálódnak a másodlagos régióba, a másodlagos régió gyakran az elsődleges régió mögött van. Ha hiba történik az elsődleges régióban, valószínű, hogy az elsődleges írási műveletek még nem replikáltak a másodlagos.

Annak megállapításához, hogy mely írási műveletek lettek replikálva a másodlagos régióba, az alkalmazás ellenőrizheti a tárfiók **utolsó szinkronizálási ideje** tulajdonságát. Az utolsó szinkronizálási idő előtt az elsődleges régióba írt összes írási művelet sikeresen replikálva lett a másodlagos régióba, ami azt jelenti, hogy a másodlagosból olvashatók. Az elsődleges régióba az utolsó szinkronizálási idő után írt írási műveletek lehetnek replikálódva a másodlagos régióba, ami azt jelenti, hogy nem érhetők el olvasási műveletekhez.

Az Azure PowerShell, az Azure CLI vagy az Azure Storage-ügyfélkódtárak használatával lekérdezheti a **Last Sync Time** tulajdonság értékét. Az **Utolsó szinkronizálási idő** tulajdonság a GMT dátum/idő értéke. További információ: [Check the Last Sync Time tulajdonság egy tárfiókhoz.](last-sync-time-get.md)

## <a name="summary-of-redundancy-options"></a>A redundancialehetőségek összefoglalása

Az alábbi táblázat bemutatja, hogy egy adott esetben mennyire tartósak és elérhetők az adatok, attól függően, hogy milyen típusú redundancia van érvényben a tárfiókhoz:

| Forgatókönyv                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (előzetes verzió)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Az adatközponton belüli csomópont elérhetetlenné válik                                                                 | Igen                             | Igen                              | Igen                                  | Igen                                  |
| Egy teljes adatközpont (zónaszintű vagy nem zónaszintű) elérhetetlenné válik                                           | Nem                              | Igen                              | Igen                                  | Igen                                  |
| Az egész régióra kiterjedő kimaradás                                                                                     | Nem                              | Nem                               | Igen                                  | Igen                                  |
| Olvasási hozzáférés az adatokhoz a másodlagos régióban, ha az elsődleges régió elérhetetlenné válik | Nem                              | Nem                               | Igen (RA-GRS-sel)                                   | Igen (RA-GZRS-szel)                                 |
| Objektumok tartósságának százalékos százaléka egy adott<sup>évben 1</sup>                                          | legalább 99,99999999999% (11 9's) | legalább 99,9999999999999% (12 9's) | legalább 99,99999999999999999999% (16 9's) | legalább 99,99999999999999999999% (16 9's) |
| Támogatott tárfiók-típusok<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Rendelkezésre állási SLA olvasási kérelmekhez<sup>1</sup>  | Legalább 99,9% (99% a hűvös hozzáférési szint esetén) | Legalább 99,9% (99% a hűvös hozzáférési szint esetén) | Legalább 99,9% (99% a hűvös hozzáférési szint esetében) grs esetén<br /><br />Legalább 99,99% (99,9% a hűvös hozzáférési szint esetében) ra-GRS esetén | Legalább 99,9% (99% a hűvös hozzáférési szinthez) a GZRS esetében<br /><br />Legalább 99,99% (99,9% a hűvös hozzáférési szint esetében) ra-GZRS esetén |
| Rendelkezésre állási SLA az írási kérelmekhez<sup>1</sup>  | Legalább 99,9% (99% a hűvös hozzáférési szint esetén) | Legalább 99,9% (99% a hűvös hozzáférési szint esetén) | Legalább 99,9% (99% a hűvös hozzáférési szint esetén) | Legalább 99,9% (99% a hűvös hozzáférési szint esetén) |

<sup>1</sup> Az Azure Storage tartóssági és rendelkezésre állási garanciáiról az [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/)című témakörben talál.

<sup>2</sup> A tárfiók-típusokról a [Tárfiók áttekintése című témakörben](storage-account-overview.md)olvashat.

Minden típusú tárfiókok összes adat másolása a tárfiók redundancia beállításának megfelelően. A program átmásolja az objektumokat, például a blokkblobokat, a hozzáfűző blobokat, az oldalblobokat, a várólistákat, a táblázatokat és a fájlokat.

Az egyes redundanciabeállítások díjszabási információkért lásd: [Azure Storage díjszabása.](https://azure.microsoft.com/pricing/details/storage/)

> [!NOTE]
> Az Azure Premium Disk Storage jelenleg csak helyileg redundáns tárolást (LRS) támogat. A blokkblob-tárfiókok támogatják a helyileg redundáns tárolást (LRS) és a zónaredundáns tárolást (ZRS) bizonyos régiókban.

## <a name="data-integrity"></a>Adatintegritás

Az Azure Storage rendszeresen ellenőrzi a ciklikus redundancia-ellenőrzésekkel (CRC) tárolt adatok integritását. Ha a rendszer adatsérülést észlel, a rendszer redundáns adatok kal javítja ki. Az Azure Storage az összes hálózati forgalom ellenőrzőösszegeit is kiszámítja az adatcsomagok sérülésének észleléséhez az adatok tárolása vagy beolvasása során.

## <a name="see-also"></a>Lásd még

- [Tárfiók utolsó szinkronizálási ideje tulajdonságának ellenőrzése](last-sync-time-get.md)
- [Tárfiók redundanciabeállításának módosítása](redundancy-migration.md)
- [Magas rendelkezésre állású alkalmazások tervezése RA-GRS storage használatával](../storage-designing-ha-apps-with-ragrs.md)
- [Vész-helyreállítási és fiókfeladat-átvétel (előzetes verzió)](storage-disaster-recovery-guidance.md)
