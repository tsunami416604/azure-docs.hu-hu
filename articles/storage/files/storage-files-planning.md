---
title: Azure Files központi telepítésének tervezése | Microsoft Docs
description: Megtudhatja, mit érdemes figyelembe venni Azure Files központi telepítés tervezésekor.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7d11dc70a78fcec62032c2a6af168bd306c9d416
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227865"
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése

A [Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az iparági szabványnak megfelelő SMB protokollon keresztül érhető el. Mivel a Azure Files teljes körűen felügyelt, az éles környezetekben való üzembe helyezése sokkal egyszerűbb, mint a fájlkiszolgáló vagy a NAS-eszköz telepítése és kezelése. Ez a cikk azokat a témaköröket ismerteti, amelyeket figyelembe kell venni egy Azure-fájlmegosztás üzembe helyezése során a szervezeten belüli éles használatra.

## <a name="management-concepts"></a>Felügyeleti fogalmak

 Az alábbi ábra a Azure Files felügyeleti szerkezeteket szemlélteti:

![Fájlstruktúra](./media/storage-files-introduction/files-concepts.png)

* **Storage-fiók**: Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Méretezhetőségi és teljesítménycélok).

* **Megosztás**: A File Storage-megosztás egy SMB-fájlmegosztás az Azure-ban. Minden könyvtárnak és fájlnak egy szülőmegosztásban kell létrejönnie. Egy fiók korlátlan számú megosztást tartalmazhat, és egy megosztás korlátlan számú fájlt tárolhat, akár a fájlmegosztás teljes kapacitásával. A prémium és a standard fájlmegosztás teljes kapacitása 100 TiB.

* **Könyvtár**: Egy választható könyvtár-hierarchia.

* **Fájl**: A megosztásban található fájl. Egy fájl legfeljebb 1 TiB méretű lehet.

* **URL-formátum**: a fájl Rest protokollal végzett Azure-fájlmegosztás iránti kérelmek esetén a fájlok a következő URL-formátummal érhetők el:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Adatelérési módszer

A Azure Files két, beépített, kényelmes adatelérési módszert kínál, amelyeket külön vagy egymással kombinálva használhat az adatai eléréséhez:

1. **Közvetlen Felhőbeli hozzáférés**: bármely Azure-fájlmegosztás csatlakoztatható [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)és/vagy [Linux](storage-how-to-use-files-linux.md) rendszeren az iparági szabványnak megfelelő SMB protokollal vagy a REST API fájlon keresztül. Az SMB-vel a megosztásban található fájlok olvasása és írása közvetlenül az Azure-beli fájlmegosztás esetében történik. Az Azure-beli virtuális gépek csatlakoztatásához az operációs rendszer SMB-ügyfelének legalább SMB 2,1-et kell támogatnia. A helyszíni csatlakoztatáshoz (például egy felhasználó munkaállomásán) a munkaállomás által támogatott SMB-ügyfélnek támogatnia kell legalább az SMB 3,0-et (titkosítással). Az SMB-n kívül az új alkalmazások és szolgáltatások közvetlenül is hozzáférhetnek a fájlmegosztáshoz a file REST használatával, amely egyszerű és méretezhető alkalmazásprogramozási felületet biztosít a szoftverfejlesztés számára.
2. **Azure file Sync**: a Azure file Sync a megosztások replikálása a helyszíni vagy az Azure-beli Windows-kiszolgálókra is lehetséges. A felhasználók a Windows Serveren keresztül érhetik el a fájlmegosztást, például egy SMB-vagy NFS-megosztáson keresztül. Ez olyan esetekben hasznos, amikor az adatok az Azure-adatközponttól távol lesznek elérhetők és módosítva lesznek, például egy fiókirodai forgatókönyvben. Előfordulhat, hogy az adatreplikáció több Windows Server-végpont között történik, például több fiókirodában. Végezetül az adat a Azure Filesba is helyezhető, így az összes adat továbbra is elérhető a kiszolgálón keresztül, de a kiszolgáló nem rendelkezik teljes másolattal az adatról. Ehelyett a felhasználó általi megnyitáskor a rendszer zökkenőmentesen visszahívja az adatait.

Az alábbi táblázat bemutatja, hogyan érhetik el a felhasználók és az alkalmazások az Azure-fájlmegosztást:

| | Közvetlen Felhőbeli hozzáférés | Azure File Sync |
|------------------------|------------|-----------------|
| Milyen protokollokat kell használnia? | A Azure Files az SMB 2,1, az SMB 3,0 és a file REST APIt támogatja. | Az Azure-fájlmegosztás bármely támogatott protokollon keresztül elérhető a Windows Serveren (SMB, NFS, FTPS stb.) |  
| Hol fut a munkaterhelés? | **Az Azure-ban**: Azure Files közvetlen hozzáférést biztosít az adataihoz. | **Lassú hálózattal rendelkező helyszíni**környezetek: a Windows, a Linux és a MacOS rendszerű ügyfelek az Azure-fájlmegosztás gyors gyorsítótárában csatlakoztatni tudják a helyi Windows-fájlmegosztást. |
| Milyen szintű ACL-eket igényel? | Megosztás és a fájl szintje. | Megosztás, fájl és felhasználói szint. |

## <a name="data-security"></a>Adatbiztonság

Azure Files több beépített lehetőség áll rendelkezésre az adatbiztonság biztosításához:

* Titkosítás támogatása mindkét over-the-wire protokollban: SMB 3,0 encryption és file REST HTTPS-kapcsolaton keresztül. Alapértelmezés szerint: 
    * Az SMB 3,0 titkosítást támogató ügyfelek titkosított csatornán keresztül küldik és fogadják az adatfogadást.
    * Azok az ügyfelek, amelyek nem támogatják az SMB 3,0 titkosítást, titkosítás nélkül kommunikálhatnak az SMB 2,1 vagy az SMB 3,0 használatával. Az SMB-ügyfelek nem jogosultak az adatközpontok közötti kommunikációra SMB 2,1 vagy SMB 3,0 titkosítás nélkül.
    * Az ügyfelek HTTP-vagy HTTPS-kapcsolaton keresztül is kommunikálhatnak a fájlokon keresztül.
* A REST-titkosítás ([Azure Storage Service encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Storage Service encryption (SSE) engedélyezve van az összes Storage-fiókhoz. A REST-adatok titkosítása teljes mértékben felügyelt kulccsal történik. A REST-alapú titkosítás nem növelheti a tárolási költségeket, vagy csökkentheti a teljesítményt. 
* A titkosított adatforgalom opcionális követelménye: Ha be van jelölve, Azure Files elutasítja az adathoz való hozzáférést titkosítatlan csatornákon keresztül. Pontosabban csak HTTPS és SMB 3,0 engedélyezett a titkosítási kapcsolatokkal.

    > [!Important]  
    > Az adatok biztonságos átvitelének megkövetelése miatt a régebbi SMB-ügyfelek nem tudnak kommunikálni az SMB 3,0-mel, és sikertelen lesz a titkosítás. További információ: [Csatlakoztatás Windows](storage-how-to-use-files-windows.md)rendszeren, [Csatlakoztatás Linux](storage-how-to-use-files-linux.md)rendszeren, és [Csatlakoztatás MacOS rendszeren](storage-how-to-use-files-mac.md).

A maximális biztonság érdekében javasoljuk, hogy mindig engedélyezze mind a titkosítást, mind az adatforgalom titkosítását, ha modern ügyfeleket használ az adatokhoz való hozzáféréshez. Ha például egy megosztást egy Windows Server 2008 R2 rendszerű virtuális gépre kell csatlakoztatnia, amely csak az SMB 2,1-et támogatja, akkor engedélyeznie kell a titkosítatlan forgalmat a Storage-fiókjához, mivel az SMB 2,1 nem támogatja a titkosítást.

Ha Azure File Synct használ az Azure-fájlmegosztás eléréséhez, a titkosítással mindig HTTPS-t és SMB 3,0-et használunk, hogy szinkronizálja az adatait a Windows-kiszolgálókkal, függetlenül attól, hogy szükség van-e a REST-alapú adatok titkosítására.

## <a name="file-share-performance-tiers"></a>Fájlmegosztási teljesítmény szintjei

A Azure Files két teljesítményszint, a standard és a prémium szintet kínálja.

### <a name="standard-file-shares"></a>Szabványos fájlmegosztás

A standard fájlmegosztást merevlemez-meghajtók (HDD-k) végzik. A standard fájlmegosztás megbízható teljesítményt biztosít olyan IO-munkaterhelések esetében, amelyek kevésbé érzékenyek a teljesítmény változékonyságára, például az általános célú fájlmegosztás és a fejlesztési/tesztelési környezetek számára. A standard fájlmegosztás csak utólagos elszámolású számlázási modellben érhető el.

> [!IMPORTANT]
> Ha 5 TiB-nál nagyobb fájlmegosztást szeretne használni, tekintse meg a bevezetést a [nagyobb fájlmegosztás (standard szint)](#onboard-to-larger-file-shares-standard-tier) című szakaszt a bevezetéshez, valamint a regionális rendelkezésre álláshoz és korlátozásokhoz.

### <a name="premium-file-shares"></a>Prémium fájlmegosztás

A prémium fájlmegosztás SSD-meghajtókon alapul. A prémium szintű fájlmegosztás konzisztens nagy teljesítményt és kis késleltetést biztosít a legtöbb IO-művelethez, az IO-igényes munkaterhelések esetében. Ez a munkaterhelések, például az adatbázisok, a webhelyek üzemeltetése és a fejlesztői környezetek széles skálájának megfelelővé teszi. A prémium fájlmegosztás csak kiépített számlázási modellben érhető el. A prémium fájlmegosztás a standard fájlmegosztástól eltérő üzemi modellt használ.

A prémium szintű fájlmegosztás és az Azure Kubernetes szolgáltatás számára elérhető Azure Backup a 1,13-es és újabb verziókban támogatja a prémium szintű fájlmegosztást.

Ha meg szeretné tudni, hogyan hozhat létre prémium szintű fájlmegosztást, tekintse meg a tárgyat ismertető cikket: [Azure Premium file Storage-fiók létrehozása](storage-how-to-create-premium-fileshare.md).

Jelenleg nem konvertálhatók közvetlenül a standard fájlmegosztás és a prémium fájlmegosztás között. Ha bármelyik szinten szeretne váltani, új fájlmegosztást kell létrehoznia az adott szinten, és manuálisan át kell másolnia az eredeti megosztás adatait a létrehozott új megosztásra. Ezt a Azure Files támogatott másolási eszközei (például a Robocopy vagy a AzCopy) használatával teheti meg.

> [!IMPORTANT]
> A prémium szintű fájlmegosztás a legtöbb régióban LRS érhető el, amelyek a Storage-fiókokat és a ZRS-t a régiók kisebb részhalmazában kínálják. Annak megállapításához, hogy a prémium fájlmegosztás jelenleg elérhető-e az Ön régiójában, tekintse meg az Azure-ban [elérhető termékek területét](https://azure.microsoft.com/global-infrastructure/services/?products=storage) . Ha szeretné megtudni, hogy mely régiók támogatják a ZRS, tekintse meg a [támogatási lefedettség és a regionális rendelkezésre állás](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability)című témakört
>
> Kérjük, töltse ki ezt a [kérdőívet](https://aka.ms/pfsfeedback)az új régiók és prémium szintű funkciók rangsorolásához.

#### <a name="provisioned-shares"></a>Kiosztott megosztások

A prémium fájlmegosztást rögzített GiB/IOPS/átviteli sebesség alapján kell kiépíteni. Minden egyes GiB-kiosztás esetén a megosztás egy IOPS és 0,1 MiB/s átviteli sebességgel fog kiadni, a maximálisan megengedett határértékek száma szerint. A minimálisan engedélyezett kiépítés a 100 GiB és a min IOPS/átviteli sebesség.

A legjobb lehetőség az, hogy minden megosztás legfeljebb három IOPS-t tud kiosztani a kiépített tárterület 60 percen belül, a megosztás méretétől függően. Az új megosztások a kiosztott kapacitás alapján kezdődnek a teljes burst Kredittel.

A megosztásokat 1 GiB-onként kell kiépíteni. A minimális méret 100 GiB, a következő méret 101 GiB és így tovább.

> [!TIP]
> Alapterv IOPS = 1 * kiépített GiB. (Max. 100 000 IOPS).
>
> Burst határérték = 3 * alapterv IOPS. (Max. 100 000 IOPS).
>
> kimenő forgalom aránya = 60 MiB/s + 0,06 * kiépített GiB
>
> bejövő forgalom sebessége = 40 MiB/s + 0,04 * kiépített GiB

A kiosztott megosztási méretet a megosztási kvóta határozza meg. A megosztási kvóta bármikor megnövelhető, de csak a legutóbbi növekedés óta 24 óra elteltével csökkenthető. Ha a várakozás után 24 órán keresztül nem növekszik a kvóta, a megosztási kvótát tetszőleges számú időpontra csökkentheti, amíg újra nem növelné. A IOPS/átviteli sebesség változásai a méret megváltozása után néhány percen belül érvénybe lépnek.

Lehetséges, hogy csökkentse a kiosztott megosztás méretét a használt GiB alatt. Ha ezt teszi, nem veszíti el az adatvesztést, de továbbra is a felhasznált méret után kell fizetnie, és meg kell kapnia a kiépített megosztás teljesítményét (alapkonfiguráció IOPS, átviteli sebességét és burst IOPS), nem a felhasznált méretet.

Az alábbi táblázat néhány példát mutat be a kiosztott megosztási méretekre:

|Kapacitás (GiB) | Alap IOPS-érték | Burst IOPS | Kimenő forgalom (MiB/s) | Bejövő forgalom (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Akár 300     | 66   | 44   |
|500         | 500     | Akár 1 500   | 90   | 60   |
|1,024       | 1,024   | Akár 3 072   | 122   | 81   |
|5 120       | 5 120   | Akár 15 360  | 368   | 245   |
|10 240      | 10 240  | Akár 30 720  | 675 | 450   |
|33 792      | 33 792  | Akár 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Akár 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Akár 100 000 | 6 204 | 4 136   |

> [!NOTE]
> A fájlmegosztás teljesítményére a számítógép hálózati korlátai, a rendelkezésre álló hálózati sávszélesség, az IO-méretek, a párhuzamosságok számos más tényező vonatkozik. Például a 8 KiB írási/olvasási IO-méretekkel rendelkező belső tesztelésen alapuló, egyetlen Windowsos virtuális gép, *Standard F16s_v2*, a prémium szintű fájlmegosztás SMB-hez való csatlakoztatása 20000 olvasási IOPS és 15 000 FORDULAT írási IOPS. Az 512 MiB-írási/írási IO-méretekkel ugyanez a virtuális gép elérheti a 1,1 GiB/s kimenő forgalmat és a 370 MiB/s adatátviteli sebességet. A maximális teljesítmény elérése érdekében a terhelést több virtuális gép között kell elosztani. A gyakori teljesítménnyel kapcsolatos problémák és a megkerülő megoldások [hibaelhárítási útmutatójában](storage-troubleshooting-files-performance.md) olvashat.

#### <a name="bursting"></a>Tele

A prémium fájlmegosztás a IOPS akár három tényezővel is feltörte. A bursás automatizált, és kreditrendszer alapján működik. Az adatbontás a legjobb megoldási szinten működik, és a burst korlát nem garantálható, és a fájlmegosztás a korlátra is *feltört.*

A kreditek felhalmozódnak egy burst gyűjtőben, amikor a fájlmegosztás forgalma az alapszintű IOPS alatt van. Egy 100 GiB-megosztás például 100 alapterv-IOPS rendelkezik. Ha a megosztás tényleges forgalma 40 IOPS volt egy adott 1 másodperces intervallumhoz, akkor a 60 fel nem használt IOPS jóváírásra kerül egy burst gyűjtőn. Ezeket a krediteket később akkor fogjuk használni, amikor a műveletek túllépik az alapkonfiguráció IOPs.

> [!TIP]
> A burst vödör mérete = alapterv IOPS * 2 * 3600.

Ha egy megosztás túllépi az alapkonfiguráció IOPS, és a kreditek egy burst gyűjtőben vannak, akkor a rendszer kitört. A megosztások továbbra is feltörtek, amíg a kreditek megmaradnak, de az 50 TiB-nál kisebb megosztások csak a burst-korláton belül maradnak. A 50 TiB-nál nagyobb megosztások műszakilag meghaladják ezt az óránkénti korlátot, akár két óráig is, de ez a felhalmozott burst-kreditek számától függ. Az alapkonfiguráció IOPS túli minden egyes IO egy kreditet használ, és ha az összes kreditet felhasználja, a megosztás visszatér az alapkonfiguráció IOPS.

A megosztási kreditek három állapottal rendelkeznek:

- Ha a fájlmegosztás az alapkonfigurációnál kisebb IOPS használja.
- Csökkenő, ha a fájlmegosztás kitört.
- Hátralévő állandó, ha nincsenek kreditek vagy alapkonfiguráció IOPS használatban.

Az új fájlmegosztás a teljes számú Kredittel kezdődik a burst gyűjtőben. A burst kreditek nem lesznek felhalmozva, ha a megosztás IOPS az alapszintű IOPS alá esik a kiszolgáló általi szabályozás miatt.

## <a name="file-share-redundancy"></a>Fájlmegosztás-redundancia

Azure Files a standard szintű megosztások négy adatredundancia-beállítást támogatnak: helyileg redundáns tárolást (LRS), a zóna redundáns tárolását (ZRS), a Geo-redundáns tárolást (GRS) és a Geo-zóna-redundáns tárolást (előzetes verzió).

Azure Files prémium szintű megosztások támogatják mind a LRS, mind a ZRS, a ZRS jelenleg a régiók kisebb részhalmazában érhető el.

A következő szakaszok a különböző redundancia-beállítások közötti különbségeket ismertetik:

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zóna redundáns tárterülete

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Georedundáns tárolás

> [!Warning]  
> Ha az Azure-fájlmegosztást Felhőbeli végpontként használja egy GRS, a Storage-fiók feladatátvételét nem kell elindítania. Ennek hatására a szinkronizálás leáll, és az újonnan rétegű fájlok esetében váratlan adatvesztést okozhat. Egy Azure-régió elvesztése esetén a Microsoft a Azure File Syncával kompatibilis módon aktiválja a Storage-fiók feladatátvételét.

A Geo-redundáns tárolás (GRS) úgy van kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben, az adatoknak egy olyan másodlagos régióba való replikálásával, amely több száz mérföld távolságra van az elsődleges régiótól. Ha a GRS engedélyezve van, akkor az adatai tartósak maradnak, még akkor is, ha egy teljes regionális leállás vagy egy olyan katasztrófa következik be, amelyben az elsődleges régió nem helyreállítható.

Ha úgy dönt, hogy olvasási hozzáférésű geo-redundáns tárterületet (RA-GRS) választ, akkor tudnia kell, hogy az Azure-fájl jelenleg nem támogatja az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS) bármely régióban. Az RA-GRS Storage-fiókban lévő fájlmegosztás ugyanúgy működik, mint a GRS-fiókokban, és a GRS díjait számítjuk fel.

A GRS replikálja az adatait egy másik adatközpontba egy másodlagos régióban, de az adatok csak olvashatók, ha a Microsoft feladatátvételt kezdeményez az elsődlegestől a másodlagos régióba.

Az GRS-t engedélyező Storage-fiókok esetében a rendszer először a helyileg redundáns tárolással (LRS) replikálja az összes adattal. A rendszer először egy frissítést véglegesít az elsődleges helyen, és replikálja a LRS használatával. Ezt követően a rendszer aszinkron módon replikálja a frissítést a másodlagos régióba a GRS használatával. Ha az adatírás a másodlagos helyre történik, a rendszer a LRS használatával is replikálja az adott helyen belül.

Az elsődleges és a másodlagos régió is kezeli a replikákat a különböző tartalék tartományok között, és a tárolási méretezési egységen belül frissíti a tartományokat. A tárolási méretezési egység az adatközpontban található alapszintű replikációs egység. A replikáció ezen a szinten a LRS által biztosított. További információt a [helyileg redundáns tárolás (LRS): alacsony költséghatékonyságú adatredundancia az Azure Storage](../common/storage-redundancy-lrs.md)szolgáltatásban című témakörben talál.

Tartsa szem előtt ezeket a szempontokat, amikor dönti el, hogy melyik replikációs beállítást szeretné használni:

* A Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) biztosítja a magas rendelkezésre állást és a maximális tartósságot azáltal, hogy szinkronizálja az adatátvitelt három Azure rendelkezésre állási zónában, majd aszinkron módon replikálja az adatreplikálást a másodlagos régióba. Engedélyezheti a másodlagos régió olvasási hozzáférését is. A GZRS úgy lett kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben. További információ a GZRS-ről: [geo-Zone-redundáns tárolás a nagyfokú rendelkezésre állás és a maximális tartósság érdekében (előzetes verzió)](../common/storage-redundancy-gzrs.md).
* A Zone-redundáns tárolás (ZRS) lehetővé teszi a szinkron replikálást a nagyfokú rendelkezésre álláshoz, és jobb választás lehet néhány forgatókönyvnél, mint a GRS. További információ a ZRS: [ZRS](../common/storage-redundancy-zrs.md).
* Az aszinkron replikáció az adatoknak az elsődleges régióba való beírásának időpontjától számított késleltetést foglal magában, a másodlagos régióba való replikáláskor. Regionális katasztrófa esetén előfordulhat, hogy a másodlagos régióba még nem replikált módosítások elvesznek, ha az adatok nem állíthatók helyre az elsődleges régióból.
* A GRS esetében a replika nem érhető el olvasási vagy írási hozzáféréshez, kivéve, ha a Microsoft feladatátvételt kezdeményez a másodlagos régióba. Feladatátvétel esetén a feladatátvétel befejezése után olvasási és írási hozzáféréssel fog rendelkezni az adataihoz. További információ: vész- [helyreállítási útmutató](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Nagyobb fájlmegosztás beléptetése (standard szint)

Ez a szakasz csak a normál fájlmegosztás esetében érvényes. A prémium szintű fájlmegosztás 100 TiB-kapacitással érhető el.

### <a name="restrictions"></a>Korlátozások

- A LRS/ZRS és a GRS/GZRS fiók átalakítása nem lehetséges a nagyméretű fájlmegosztást engedélyező Storage-fiókok esetében.

### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A standard fájlmegosztás minden régióban 5 TiB-ig elérhető. Bizonyos régiókban 100 TiB-korláttal érhetők el, ezek a régiók a következő táblázatban láthatók:

|Régió |Támogatott redundancia |
|-------|---------|
|Kelet-Ausztrália |LRS     |
|Délkelet-Ausztrália|LRS |
|Közép-Kanada  |LRS     |
|Kelet-Kanada     |LRS     |
|Közép-India  |LRS     |
|USA középső régiója *   |LRS     |
|Kelet-Ázsia      |LRS     |
|USA keleti régiója *        |LRS     |
|USA 2. keleti régiója *      |LRS     |
|Közép-Franciaország |LRS, ZRS|
|Dél-Franciaország   |LRS     |
|Észak-Európa   |LRS     |
|Dél-India    |LRS     |
|Délkelet-Ázsia |LRS, ZRS|
|USA nyugati középső régiója|LRS     |
|Nyugat-Európa *    |LRS, ZRS|
|USA nyugati régiója *        |LRS     |
|USA nyugati régiója, 2.      |LRS, ZRS|

az új fiókok \* támogatottak, nem minden meglévő fiók végezte el a frissítési folyamatot. Megtekintheti, hogy a meglévő Storage-fiókok elvégezték-e a frissítési folyamatot a [nagyméretű fájlmegosztás engedélyezésére](storage-files-how-to-create-large-file-share.md)tett kísérlet során.

Kérjük, töltse ki ezt a [kérdőívet](https://aka.ms/azurefilesatscalesurvey)az új régiók és szolgáltatások rangsorolása érdekében.

### <a name="enable-and-create-larger-file-shares"></a>Nagyobb fájlmegosztás engedélyezése és létrehozása

A nagyobb fájlmegosztás használatának megkezdéséhez tekintse meg a [nagyméretű fájlmegosztás engedélyezésével és létrehozásával](storage-files-how-to-create-large-file-share.md)kapcsolatos cikket.

## <a name="data-growth-pattern"></a>Adatmennyiség növekedési mintája

Az Azure-fájlmegosztás maximális mérete jelenleg 100 TiB. A jelenlegi korlátozás miatt figyelembe kell vennie az adatmennyiség várható növekedését az Azure-fájlmegosztás telepítésekor.

Több Azure-fájlmegosztás egyetlen Windows-fájlkiszolgálón is szinkronizálható Azure File Sync használatával. Így biztosíthatja, hogy a régebbi, nagyméretű fájlmegosztás a helyszínen is bevihető legyen Azure File Syncba. További információ: [Azure file Sync központi telepítésének tervezése](storage-files-planning.md).

## <a name="data-transfer-method"></a>Adatátviteli módszer

Számos egyszerű lehetőség van a meglévő fájlmegosztás, például egy helyszíni fájlmegosztás adatainak tömeges átvitelére Azure Filesba. Néhány népszerű is (nem teljes lista):

* **Azure file Sync**: egy Azure-fájlmegosztás (a "Felhőbeli végpont") és a Windows Directory-névtér ("kiszolgálói végpont") közötti első szinkronizálás részeként Azure file Sync a meglévő fájlmegosztás összes adatait replikálja a Azure Filesre.
* **[Azure import/export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : az Azure import/export szolgáltatás lehetővé teszi, hogy a merevlemez-meghajtók Azure-adatközpontba való szállításával biztonságosan továbbítson nagy mennyiségű adatmennyiséget egy Azure-fájlmegosztás számára. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : a Robocopy egy jól ismert másolási eszköz, amely a Windows-és Windows Server-kiszolgálóval is rendelkezik. A Robocopy felhasználható az adatok Azure Filesba történő átvitelére a fájlmegosztás helyi csatlakoztatásával, majd a csatlakoztatott hely célként való használatával a Robocopy parancsban.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : a AzCopy egy parancssori segédprogram, amely az adatok átmásolását Azure Files, valamint az Azure Blob Storage-t használja az optimális teljesítményű egyszerű parancsok használatával.

## <a name="next-steps"></a>Következő lépések
* [Azure File Sync központi telepítésének tervezése](storage-sync-files-planning.md)
* [Azure Files üzembe helyezése](storage-files-deployment-guide.md)
* [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
