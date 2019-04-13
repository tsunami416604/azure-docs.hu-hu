---
title: Az Azure Files üzembe helyezésének megtervezése |} A Microsoft Docs
description: Ismerje meg az Azure Files üzembe helyezésének tervezése során megfontolandó szempontokat.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d4361fc37d01b351d20a273aa39f558e9b00faa4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525925"
---
# <a name="planning-for-an-azure-files-deployment"></a>Az Azure Files üzembe helyezésének megtervezése

[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely az iparági szabvány SMB protokollon keresztül érhető el a felhőben. Mivel az Azure Files teljes körűen felügyelt, üzembe helyezés éles forgatókönyvekben, sokkal egyszerűbb, mint az üzembe helyezése és kezelése a fájlkiszolgáló vagy NAS-eszköz. Ez a cikk foglalkozik a témakörök figyelembe kell venni üzembe helyezése az Azure-fájlmegosztás a szervezeten belül éles környezetben való használatra.

## <a name="management-concepts"></a>Tartománykezelési fogalmaival

 Az alábbi ábra az Azure Files felügyeleti eszközökkel:

![Fájlstruktúra](./media/storage-files-introduction/files-concepts.png)

* **Storage-fiók**: Minden, az Azure Storage-hozzáférés tárfiókon keresztül történik. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Méretezhetőségi és teljesítménycélok).

* **Megosztás**: A File Storage-megosztás egy SMB-fájlmegosztás az Azure-ban. Minden könyvtárnak és fájlnak egy szülőmegosztásban kell létrejönnie. Egy fiók korlátlan számú megosztást tartalmazhat, és a egy megosztás korlátlan számú fájl legfeljebb 5 TiB teljes kapacitását a fájlmegosztás tárolhatja.

* **Directory**: Egy választható hierarchia könyvtárak.

* **Fájl**: A megosztásban található fájl. Előfordulhat, hogy a fájl mérete legfeljebb 1 Tib-ra.

* **URL-formátum**: Az Azure-fájlmegosztások a fájl REST protokoll végrehajtott kérelmek a fájlok a következő URL-formátummal:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Adat-hozzáférési módszer

Az Azure Files ajánlatok két, beépített és kényelmes adat-hozzáférési módszereit, hogy önállóan vagy együtt egymással, az adatok eléréséhez:

1. **A közvetlen felhőhozzáférést**: Bármely Azure-fájlmegosztás által is csatlakoztathatók [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), és/vagy [Linux](storage-how-to-use-files-linux.md) az iparági szabványos Server Message Block (SMB) protokollt vagy a fájl REST API-n keresztül. SMB, az olvasási és írási műveletek a megosztáson található fájlok készülnek el közvetlenül a fájlmegosztás az Azure-ban. Csatlakoztatása egy Azure-beli virtuális gép által, az operációs rendszerben az SMB-ügyfél legalább kell támogatja SMB 2.1. A helyszíni, csatlakoztatni, például a felhasználói munkaállomáson, a munkaállomás által támogatott SMB-ügyfél legalább támogatnia SMB 3.0 (a titkosítás). SMB, valamint új alkalmazások vagy szolgáltatások előfordulhat, hogy tud közvetlenül hozzáférni a fájl REST, amely egy egyszerű és méretezhető alkalmazásprogramozási felületet biztosít a szoftverfejlesztést fájlmegosztást.
2. **Az Azure File Sync**: Az Azure File Sync a megosztások replikálható a helyszíni Windows-kiszolgálók vagy az Azure-ban. A felhasználók számára szeretné hozzáférnie a fájlmegosztás Windows-kiszolgálón keresztül például az SMB vagy NFS-megosztások. Ez akkor hasznos, amelyben az adatok elérni és módosítani milyen távolságra egy Azure-adatközpontban, mint például a fiókirodák esetében forgatókönyvekhez. Előfordulhat, hogy lehet replikálja az adatokat között több Windows Server-végpontot, például több fiókirodák között. Végül előfordulhat, hogy lehet rétegzett adatok az Azure Files, úgy, hogy az összes adat a kiszolgáló továbbra is elérhetők, de a kiszolgáló nem rendelkezik az adatok teljes másolata. Adatok, zökkenőmentesen idézni, amikor a felhasználó megnyitja.

Az alábbi táblázat mutatja be, hogy a felhasználók és alkalmazások férhet hozzá az Azure-fájlmegosztásba:

| | Közvetlen felhőhozzáférést | Azure File Sync |
|------------------------|------------|-----------------|
| Milyen protokollokat szeretné használni? | Az Azure Files SMB 2.1, az SMB 3.0-s és a fájl REST API támogatja. | Hozzáférés az Azure-fájlmegosztás segítségével bármely támogatott protokoll a Windows Server (SMB, NFS, FTPS, stb.) |  
| Hol futnak az alkalmazások és szolgáltatások? | **Az Azure-ban**: Az Azure Files az adatok közvetlen hozzáférést biztosít. | **A helyszínen a lassú hálózati**: Windows, Linux és MacOS rendszerű ügyfelek is egy helyi Windows-fájlmegosztás csatlakoztatása az Azure-fájlmegosztás gyors gyorsítótárként. |
| Milyen szintű hozzáférés-vezérlési listák van szüksége? | Megosztás és a fájl szintje. | Megosztás, a fájl és a felhasználói szintű. |

## <a name="data-security"></a>Adatbiztonság

Az Azure Files számára biztosítja az adatok biztonságát több beépített lehetőséggel rendelkezik:

* Mindkét keresztüli átvitel közbeni protokollt a titkosítás támogatása: Az SMB 3.0 titkosítás és a fájl REST HTTPS-kapcsolaton keresztül. Alapértelmezés szerint: 
    * Ügyfelek, amelyek támogatják az SMB 3.0 titkosítás adatokat küldeni és fogadni egy titkosított csatornán keresztül.
    * Az ügyfelek, amelyek nem támogatják az SMB 3.0-s titkosítással kommunikálhatnak belüli adatközpontot SMB 2.1 vagy SMB 3.0 titkosítás nélkül. SMB-ügyfelek nem engedélyezettek az inter-datacenter SMB 2.1 vagy SMB 3.0 protokollt használó kommunikációra titkosítás nélkül.
    * A HTTP vagy HTTPS fájl REST az ügyfelek kommunikálhatnak.
* Titkosítás inaktív ([az Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Az összes storage-fiókok Storage Service Encryption (SSE) engedélyezve van. Inaktív adatok teljes körűen felügyelt kulcsokkal van titkosítva. Titkosítás inaktív nem növelje a tárolási költségeket, és csökkentheti a teljesítményt. 
* A titkosított adatokat átvitel nem kötelező követelménye: kiválasztásakor az Azure Files elutasítja a hozzáférési adatokat nem titkosított csatornákon keresztül. Pontosabban a csak HTTPS és SMB 3.0 titkosítás kapcsolatok engedélyezettek.

    > [!Important]  
    > Az adatok biztonságos átvitel megkövetelése eredményezi, hogy a régebbi SMB-ügyfelek nem képes kommunikálni az SMB 3.0, a titkosítás a nem sikerült. További információkért lásd: [Windows csatlakoztassa](storage-how-to-use-files-windows.md), [csatlakoztatás linuxon](storage-how-to-use-files-linux.md), és [macOS rendszeren csatlakoztatási](storage-how-to-use-files-mac.md).

A maximális biztonság érdekében javasoljuk, hogy mindig mindkét titkosítás inaktív engedélyezése és az adatok átvitel titkosításának engedélyezésével, amikor az ügyfelek a modern használ az adatok eléréséhez. Például ha van szüksége a Windows Server 2008 R2 rendszerű, csak az SMB 2.1 támogatja, amelyek a megosztás csatlakoztatásához szüksége a tárfiók nem titkosított forgalom engedélyezésére, mivel az SMB 2.1 nem támogatja a titkosítást.

Ha az Azure File Sync használatával az Azure-fájlmegosztás eléréséhez, mindig használjuk HTTPS és SMB 3.0-s titkosítással szinkronizálja az adatokat a Windows-kiszolgálók, függetlenül attól, inaktív adatok titkosításának megkövetelése.

## <a name="file-share-performance-tiers"></a>A fájl megosztási teljesítményszintek

Az Azure Files két teljesítményszinttel kínál: standard és prémium szintű.

* **Standard fájlmegosztások** élvezik forgó merevlemez-meghajtók (HDD), amely megbízható teljesítményt, amelyek kevésbé érzékenyek a teljesítményingadozásra, például az általános célú fájlmegosztások és a fejlesztési és tesztelési környezetek i/o-munkaterhelések esetében. Standard fájlmegosztások csak egy használatalapú számlázási modell érhető el.
* **Prémium szintű fájlmegosztások (előzetes verzió)** élvezik tartós állapotú lemezeket (SSD-kkel), amely egységes nagy teljesítményű és kis késésű, belül a legtöbb i/o-műveletek, a legtöbb i/o-igényes munkaterhelések esetében egy számjegyű ideje ezredmásodpercben. Ez teszi őket a megfelelő számos különböző számítási feladatokhoz – például adatbázisokat, webhelyszolgáltatás, fejlesztőkörnyezetet, stb. Prémium szintű fájlmegosztások csak egy üzembe helyezett számlázási modell érhető el. Prémium szintű fájlmegosztások a egy külön, standard fájlmegosztásokból telepítési modellt használja. Ha szeretné, hogyan hozzon létre egy prémium szintű fájlmegosztást, tekintse meg a cikk a tárgyban: [A prémium szintű Azure file storage-fiók létrehozása](storage-how-to-create-premium-fileshare.md).

> [!IMPORTANT]
> Prémium szintű fájl megosztások továbbra is előzetes verzióban érhetők el, csak az LRS, érhető el, és csak egy részhalmazát régióban érhető el legyenek elérhetők az Azure Backup-támogatással rendelkező régiók kiválasztása:

|Rendelkezésre álló terület  |Az Azure Backup-támogatás  |
|---------|---------|
|USA 2. keleti régiója      | Igen|
|USA keleti régiója       | Igen|
|USA nyugati régiója       | Nem |
|USA 2. nyugati régiója      | Nem |
|USA középső régiója    | Nem |
|Észak-Európa  | Nem |
|Nyugat-Európa   | Igen|
|Délkelet-ázsiai       | Igen|
|Kelet-Japán    | Nem |
|Korea középső régiója | Nem |
|Kelet-Ausztrália| Nem |

### <a name="provisioned-shares"></a>Kiépített megosztások

Prémium szintű fájlmegosztások (előzetes verzió) egy rögzített GiB/IOPS/teljesítmény arány alapján vannak üzembe helyezve. Az egyes üzembe helyezett GiB a megosztás fogja kiállítani egy IOPS és 0,1 MiB/s átviteli legfeljebb fájlmegosztás maximális vonatkozó korlátok. Kiépítés engedélyezett minimum 100 GB, a minimális IOPS és átviteli sebesség. Megosztás méretének bármikor, minden alkalommal és csökkent növelhető, de 24 óránként óta az utolsó növelését a csökkenthető.

Az elérhető legjobb lehetőség alapján minden megosztás is megnövelheti arra GiB kiosztott tárolás három IOPS legfeljebb 60 percet vagy hosszabb ideig a megosztás méretétől függően. Új megosztások indítsa el a teljes burst kreditet kap, a kiépített lemezkapacitás alapján.

Minden megosztás is megnövelheti arra legfeljebb legalább 100 IOPS és a cél 100 MiB/s adatforgalomból. Megosztások ki kell építeni az 1 GIB-ra kerekítve. Minimális mérete 100 GB, a következő méret 101-es GIB és így tovább.

> [!TIP]
> Alapkonfiguráció IOPS = 100 + 1 * GiB kiépítve. (Legfeljebb egy legfeljebb 100 000 iops-érték).
>
> Átmenetileg megnövelhető a korlát = 3 * alapkonfiguráció iops-t. (Legfeljebb egy legfeljebb 100 000 iops-érték).
>
> kilépő üzenetek gyakorisága = 60 MiB/s + 0,06 * kiosztott GiB
>
> bejövő forgalom = 40 MiB/s + 0,04 * kiosztott GiB

Megosztás méretének bármikor, minden alkalommal és csökkent növelhető, de 24 óránként óta az utolsó növelését a csökkenthető. IOPS/átviteli sebesség a módosítások méretének módosítása után 24 órán belül től lép érvénybe.

Az alábbi táblázatban néhány példa az ezekben a képletekben a létesített megosztás méretek mutatja be:

(Méretek kimaradásával egy * következők korlátozott nyilvános előzetes verzióban érhető el)

|Kapacitás (GB) | Alapkonfiguráció IOPS | Hirtelen korlát | Kimenő forgalom (MiB/s) | Bejövő (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Legfeljebb 300     | 66   | 44   |
|500         | 500     | Legfeljebb 1500 felhasználóval   | 90   | 60   |
|1,024       | 1,024   | Akár 3072   | 122   | 81   |
|5,120       | 5,120   | Akár 15,360  | 368   | 245   |
|10,240 *     | 10,240  | Akár 30 720  | 675 | 450   |
|33,792 *     | 33,792  | Legfeljebb 100 000 | 2,088 | 1,392   |
|51,200 *     | 51,200  | Legfeljebb 100 000 | 3,132 | 2,088   |
|102,400 *    | 100 000 | Legfeljebb 100 000 | 6,204 | 4,136   |

Megosztás méretét legfeljebb 5 TiB jelenleg nyilvános előzetes verzióban érhető el, amíg akár 100 Tib-ra korlátozott nyilvános előzetes verzióként, a korlátozott nyilvános előzetes verzió teljes körű hozzáférési kérelmek [felmérésre.](https://aka.ms/azurefilesatscalesurvey)

### <a name="bursting"></a>Tartalékkapacitás

Prémium szintű fájlmegosztásokat is megnövelheti arra az iops-érték legfeljebb három tényező. Tartalékkapacitás automatikusan végbemegy, és működik, a rendszer alapján. Tartalékkapacitás akkor működik a legjobb lehetőség alapján, és a hirtelen korlát nem garantálja, fájlmegosztások is megnövelheti arra *akár* korlátot.

Kreditek öszesítés az adatlöketek kérelemegységeket, minden alkalommal, amikor a fileshares forgalmát alapkonfiguráció iops-érték alatt van. Például egy 100 GB-megosztás rendelkezik 100 alapkonfiguráció iops-t. Ha a megosztáson tényleges forgalom a meghatározott 1 másodperces intervallumban 40 iops-érték volt, majd a fel nem használt iops-érték 60-számlán burst kérelemegységeket. A kreditek majd később során alkalmazandó műveletek túllépné az alapkonfiguráció iops-érték.

> [!TIP]
> A hirtelen korlát gyűjtő mérete = Baseline_IOPS * 2 * 3600.

Amikor egy megosztást az alapkonfiguráció iops-érték meghaladja, és hirtelen kérelemegységeket krediteket tartalmaz, a rendszer burst. Megosztások továbbra is folyamatosan mindaddig, amíg vannak hátralévő a kreditek, bár kisebb, mint 50 tiB megosztásokat csak fog maradni, akár egy órára adatlöketek maximális. Nagyobb, mint 50 TiB megosztások technikailag lépheti túl ezt a határt egy óra, fel, ez azonban két óra alapján a feladatokkal kapcsolatos adatlöketek kreditek száma. Minden egyes IO túl az alapkonfiguráció IOPS használ fel egy, és miután az összes kreditet használnak fel a megosztás alaptervhez IOPS adna vissza.

Megosztás-jóváírások három állapota van:

- Halmoz fel, ha a fájlmegosztás kevesebb, mint az alapkonfiguráció iops-t.
- Csökkenő, ha a fájlmegosztás tartalékkapacitás van.
- Fennmaradó nullára állítja, amikor nem krediteket vagy a referenciakonfiguráció IOPS van használatban.

Új fájlmegosztások alapdíja a krediteket az adatlöketek gyűjtőhöz a teljes számát.

## <a name="file-share-redundancy"></a>Fájl megosztási redundancia

Az Azure standard szintű fájlmegosztásokat három adatredundanciával kapcsolatos lehetőségek támogatja: a helyileg redundáns tárolás (LRS), a zónaredundáns tárolás (ZRS) és a georedundáns tárolás (GRS).

Fájlok a prémium szintű Azure-fájlmegosztások csak támogatja a helyileg redundáns tárolás (LRS).

A következő szakaszok ismertetik a különböző adatredundanciával kapcsolatos lehetőségek közötti különbségek:

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Zónaredundáns tárolás

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Georedundáns tárolás

> [!Warning]  
> Ha GRS tárfiók a felhőbeli végpont, használja az Azure-fájlmegosztást, storage-fiók feladatátvétele nem kezdeményezheti. Ezzel OK sync leállításához működő és május is művelettel váratlan adatvesztés esetén újonnan rétegzett fájlok. Egy Azure-régióban, adatvesztés esetén a Microsoft a tárolási fiók feladatátvétel úgy, hogy a kompatibilis az Azure File Sync aktivál.

Georedundáns tárolás (GRS) célja, hogy 99,99999999999999 %-ában (16 9-es) tartós objektumok egy adott évben által az adatok replikálása egy másodlagos régióba, amely több száz mérföld a forrásadatok elsődleges. Ha a tárfiók GRS engedélyezve van, az adatok a tartós még esetén teljes regionális kimaradás vagy vészhelyzet, amelyben az elsődleges régió nem állítható helyre.

Ha úgy dönt, az írásvédett georedundáns tárolás (RA-GRS), kell ismernie, hogy az Azure File nem támogatja az írásvédett georedundáns tárolás (RA-GRS) bármelyik régióban jelenleg. Az RA-GRS tárfiókban lévő fájlmegosztások működnek, mint a GRS fiókok ugyanúgy és felszámított GRS díjszabása.

GRS azonban, hogy adatokat érhető el lehet csak olvasható, ha a Microsoft kezdeményezi egy feladatátvétel az elsődleges kiszolgálóról a másodlagos régióba replikálja az adatokat egy másodlagos régióban, egy másik adatközpontba.

A helyileg redundáns tárolás (LRS) először replikálja a grs típusú, engedélyezett tárfiók összes adatot. Frissítés először elkötelezte magát az elsődleges helyre, és replikálja az LRS használata. A frissítés majd aszinkron módon replikálja a másodlagos régióba, GRS használatával. A másodlagos helyre írja az adatokat, amikor is replikált LRS használatával adott helyen belül.

Az elsődleges és másodlagos régiók kezelheti a replikák külön tartalék tartományokban és frissítési tartományokban lévő tárolóskálázási egységben. A tárolási skálázási egység az egyszerű replikációs egység az adatközponton belül. LRS; által biztosított replikáció ezen a szinten További információkért lásd: [helyileg redundáns tárolás (LRS): Az Azure Storage alacsony költségű adatredundancia](../common/storage-redundancy-lrs.md).

Tartsa szem ezeken a pontokon, ha a replikációs beállítás használata:

* Zónaredundáns tárolás (ZRS) szinkron replikációt a magas rendelkezésre állást biztosít, és lehet, hogy bizonyos helyzetekben, mint a GRS jobb választás. A zrs-t további információkért lásd: [ZRS](../common/storage-redundancy-zrs.md).
* Aszinkron replikációs késleltetés az adatok írása az elsődleges régióra, ha azt a rendszer a másodlagos régióba replikálja az idő foglalja magában. Regionális katasztrófa esetén módosítások, amelyek még nem lett replikálása még a másodlagos régióba lehetséges, hogy elvesznek, ha az adatok nem állíthatók vissza az elsődleges régióból.
* A grs Tárolással a replika nem érhető el olvasási vagy írási hozzáférést, kivéve, ha a Microsoft kezdeményezi egy feladatátvételt a másodlagos régióba. A feladatátvétel esetén sem fog rendelkezik olvasási, és befejeződött a feladatátvétel után az adatok írási hozzáféréssel. További információkért tekintse meg [vészhelyreállítási útmutató](../common/storage-disaster-recovery-guidance.md).

## <a name="data-growth-pattern"></a>Növekedési adatmintát

Még ma, a maximális Azure-fájlmegosztások mérete 5 Tib-ra (100 TiB-prémium szintű fájl megosztása korlátozott nyilvános előzetes verzió). Ezen jelenlegi korlátozás miatt meg kell fontolnia a várható adatmennyiség növekedését, Azure-fájlmegosztások üzembe helyezésekor.

Több Azure-fájlmegosztások az Azure File Sync használatával egyetlen Windows fájlkiszolgálóhoz szinkronizálási lehetőség. Ez lehetővé teszi, hogy győződjön meg arról, hogy a régebbi, nagy mennyiségű fájlmegosztások, hogy előfordulhat, hogy a helyszíni az Azure File Sync tehető. További információkért lásd: [Azure File Sync üzembe helyezésének megtervezése](storage-files-planning.md).

## <a name="data-transfer-method"></a>Adatátviteli módszer

Átviteli adatokat egy már létező fájlt oszt meg, például egy helyszíni fájlmegosztás, az Azure Files tömeges számos egyszerű lehetőség van. Néhány népszerű eszközök közé tartozik a (nem teljes lista):

* **Az Azure File Sync**: Egy Azure-fájlmegosztások ("Felhőbeli végpont") és a egy Windows-könyvtár névtér ("kiszolgálói végpont") között első szinkronizálási részeként az Azure File Sync replikálja az összes adat a meglévő fájlmegosztást a az Azure Files.
* **[Az Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Az Azure Import/Export szolgáltatás lehetővé teszi, hogy biztonságosan továbbíthat nagy mennyiségű adatot az Azure-fájlmegosztások merevlemezes meghajtók egy Azure-adatközpontban való szállításával. 
* **[A Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: A Robocopy egy olyan jól ismert másolás eszköz, Windows és Windows Server részét képező. A Robocopy adatok átviteléhez az Azure Files helyileg a fájlmegosztás csatlakoztatása és a csatlakoztatott helyet használva a Robocopy parancsot a célként használható.
* **[Az AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: Az AzCopy egy parancssori segédprogram, és az Azure Files, valamint az Azure Blob storage, az adatok másolása szolgál az optimális teljesítmény érdekében egyszerű parancs használatával. Az AzCopy Windows és Linux rendszerekhez érhető el.

## <a name="next-steps"></a>További lépések
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
* [Az Azure Files üzembe helyezése](storage-files-deployment-guide.md)
* [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
