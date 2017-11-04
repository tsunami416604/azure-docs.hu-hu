---
title: "A fájl szintjén SAP HANA Azure biztonsági mentés |} Microsoft Docs"
description: "Két fő biztonsági mentési lehetőség SAP Hana az Azure virtuális gépeken, ez a cikk foglalkozik SAP HANA Azure biztonsági mentési fájl szinten"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-azure-backup-on-file-level"></a>A fájl szintjén SAP HANA Azure biztonsági mentés

## <a name="introduction"></a>Bevezetés

Ez egy SAP HANA biztonsági másolaton kapcsolódó cikkek háromrészes sorozatát része. [Biztonsági mentési útmutató SAP Hana Azure virtuális gépeken](./sap-hana-backup-guide.md) áttekintése és információkat nyújt a kezdeti lépések, és [SAP HANA biztonsági másolat alapján storage-pillanatfelvételekkel](./sap-hana-backup-storage-snapshots.md) magában foglalja a tárolási pillanatkép-alapú biztonsági mentési beállítás.

Az Azure Virtuálisgép-méretek megnézi, egy látható, hogy egy GS5 lehetővé teszi a mellékelt adatok 64 lemez. A nagyméretű SAP HANA-rendszerek esetében lemezek jelentős számú már adatainak és naplókönyvtárainak fájlokat, valószínűleg szoftveres RAID optimális lemez I/O kapacitása együtt kerülhet sor. A kérdés majd az SAP HANA biztonsági mentési fájlokat, amelyek idővel sikerült feltöltve a mellékelt adatok lemezek tárolására szolgáló? Lásd: [Azure Linux virtuális gépek méretei](../../linux/sizes.md) az Azure virtuális gép mérete táblákhoz.

Nincs nincs biztonsági mentési SAP HANA-integráció az Azure Backup szolgáltatás jelenleg. Standard úgy kezelheti a biztonsági mentés/visszaállítás a fájlok szintjén van egy fájl alapú biztonsági mentéssel keresztül SAP HANA Studio vagy SAP HANA SQL-utasítások segítségével. Lásd: [SAP HANA-SQL és a rendszer nézetek hivatkozás](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) további információt.

![Az ábrán látható, a párbeszédpanelen, a biztonsági mentési menüpont SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Az ábrán látható, a párbeszédpanelen, a biztonsági mentési menüpont SAP HANA Studio. Ha hálózatiadapter-típus &quot;fájl,&quot; egy rendelkezik a fájlrendszerbeli elérési útja amelybe SAP HANA írja a biztonságimásolat-fájlokat. Visszaállítás ugyanúgy működik.

Ez a választás hangvételére egyszerű és egyszerű, amíg nincsenek szempontokat. Ahogy korábban említettük, az Azure virtuális gép csatolható adatlemezek száma korlátozás van érvényben. Nem lehet kapacitása a virtuális gép, a lemez- és adatbázis-átviteli követelmények, amelyek során előfordulhat, hogy a szoftver méretétől függően a fájlrendszerek SAP HANA biztonsági mentési fájlok tárolására RAID-lemezek között több adat szétosztott használatával. Különböző lehetőségek áthelyezésére e biztonságimásolat-fájlokat, és kezelését fájl mérete korlátozások és teljesítmény terabájtos adatkészleteket, kezelésekor a cikk későbbi részében találhatók.

Egy másik lehetőség, mely rugalmasabban vonatkozó teljes kapacitás biztosítja, az Azure blob Storage tárolóban. 1 TB méretű is korlátozódik egyetlen blob, amíg a teljes kapacitás egyetlen blob-tároló jelenleg 500 TB. Emellett biztosít az ügyfelek választhatja úgynevezett &quot;lassú&quot; tárolóról, ami egy költség előnye blob. Lásd: [Azure Blob Storage: közbeni és a tárolási rétegek cool](../../../storage/blobs/storage-blob-storage-tiers.md) ritkán használt adatok a blob storage vonatkozó további információért.

A további biztonsági georeplikált tárfiók segítségével az SAP HANA biztonsági másolatok tárolására. Lásd: [Azure Storage replikációs](../../../storage/common/storage-redundancy.md) tárfiók replikációjának vonatkozó további információért.

Egy SAP HANA-biztonsági mentések dedikált virtuális merevlemezek sikerült helyez egy dedikált biztonsági másolatok tárolási fiók georeplikált. Ellenkező esetben az egyik másolja a VHD-k, amely a SAP HANA biztonsági másolatokat tárolni, a georeplikált storage-fiók, vagy a storage-fiók, amely egy másik régióban.

## <a name="azure-backup-agent"></a>Az Azure Backup szolgáltatás ügynöke

Azure biztonsági mentés lehetővé tárerőforrások készítsen biztonsági másolatot a teljes virtuális gépeken, de is fájlok és könyvtárak keresztül a biztonságimásolat-készítő ügynök, amely a vendég operációs rendszer telepítve van. 2016. December, ez az ügynök csak akkor támogatott a Windows, de (lásd: [biztonsági mentése a Windows Server vagy az Azure Resource Manager telepítési modellel ügyfél](../../../backup/backup-configure-vault.md)).

A probléma megoldásához először másolja az SAP HANA biztonságimásolat-fájlt egy Windows virtuális gépre az Azure-on (például keresztül SAMBA megosztás), majd az Azure Backup szolgáltatás ügynöke ott. Bár ez technikailag lehetséges, azt volna összetettebbé és lelassul a biztonsági mentési vagy visszaállítási folyamat túl kicsit miatt a példányt a Linux és a Windows virtuális gép között. Ezt a módszert követve nem ajánlott.

## <a name="azure-blobxfer-utility-details"></a>Az Azure blobxfer segédprogram részletei

Könyvtárak és fájlok az Azure storage tárolja, egy sikerült CLI vagy a PowerShell vagy fejlesszen ki egy eszközt egyikével a [Azure SDK-k](https://azure.microsoft.com/downloads/). Szerepel továbbá egy használatra kész segédprogram AzCopy, az adatok másolása az Azure storage, de Windows csak (lásd: [adatátvitel az AzCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy.md)).

Ezért blobxfer használt biztonsági mentési SAP HANA-fájlok másolása. Nyílt forráskódú, éles környezetben számos ügyfél használja, és elérhető legyen [GitHub](https://github.com/Azure/blobxfer). Ez az eszköz lehetővé teszi, hogy egy adatok közvetlenül az Azure blob-tároló vagy Azure fájlmegosztás másolása. Számos hasznos szolgáltatáshoz, például az md5 kivonatoló vagy automatikus párhuzamossági, ha egy könyvtárat a több fájl is biztosít.

## <a name="sap-hana-backup-performance"></a>Biztonsági mentési SAP HANA-teljesítmény

![A biztonsági mentési SAP HANA-konzol SAP HANA Studio van ezen a képernyőfelvételen látható](media/sap-hana-backup-file-level/image023.png)

Ezen a képernyőfelvételen látható a biztonsági mentési SAP HANA-konzol SAP HANA Studio van. A XFS fájlrendszerrel HANA virtuális Géphez csatlakozik, egyetlen Azure standard tárolási lemezen 230 GB mentés végrehajtásának körülbelül 42 perc.

![Ezen a képernyőfelvételen látható verziója YaST SAP HANA teszteléshez használt virtuális Gépen](media/sap-hana-backup-file-level/image024.png)

YaST a SAP HANA teszteléshez használt virtuális gép nem ezen a képernyőfelvételen látható. Az 1 TB méretű egyetlen lemez SAP HANA biztonsági mentés egy láthatja, ahogy korábban említettük. Körülbelül 42 percig tartott a biztonsági mentési 230 GB. Ezenkívül öt 200 GB-os lemezeken lenne csatlakoztatva, és a szoftver RAID md0 létre, csíkozást fölött öt az Azure data köteteit.

![A szoftver ugyanazt biztonsági másolatának ismétlődő rendelkező csíkozást öt különböző RAID csatolt Azure standard szintű tárolást adatlemezek](media/sap-hana-backup-file-level/image025.png)

Ismétlődő ugyanazt a biztonsági másolatnak a szoftver a csíkozást öt különböző RAID csatolt Azure standard szintű tárolást adatlemezek a biztonságimásolat-készítési időpont állapotba 42 perces le 10 perc. A virtuális gép gyorsítótárazása nélkül volt csatlakoztatva a lemezek. Ezért ezt a nyilvánvaló mennyire fontos lemezírás teljesítménye van a biztonsági mentés ideje. Egy sikerült váltson prémium szintű Azure storage további gyorsító az optimális teljesítmény folyamatát. Prémium szintű Azure storage általában éles rendszerek esetén használandó.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Másolja az SAP HANA biztonságimásolat-fájlt az Azure blob storage

2016. December a legjobb lehetőség gyorsan az SAP HANA biztonságimásolat-fájlok tárolására az Azure blob Storage tárolóban. Több egyetlen blob-tároló rendelkezik egy legfeljebb 500 TB, elég a legtöbb SAP HANA-rendszerek esetén GS5 virtuális gépen futó Azure, a megfelelő SAP HANA-biztonsági mentések megtartása. Az ügyfelek közötti választhatnak &quot;forró&quot; és &quot;cold&quot; blob-tároló (lásd: [Azure Blob Storage: közbeni és a tárolási rétegek cool](../../../storage/blobs/storage-blob-storage-tiers.md)).

A blobxfer eszközzel is könnyen közvetlenül az Azure blob storage a SAP HANA biztonságimásolat-fájlokat másolni.

![Itt látható egy SAP HANA fájl teljes biztonsági mentés fájljait](media/sap-hana-backup-file-level/image026.png)

Itt láthatja egy SAP HANA fájl teljes biztonsági mentés fájljait. Négy fájlok vannak, és a legnagyobb nincs nagyjából 230 GB.

![A 230 GB átmásolása egy Azure standard fiók tárolóra nagyjából 3000 másodpercig tartott](media/sap-hana-backup-file-level/image027.png)

Nem használja az md5 kivonatoló a kezdeti teszt, tartott a 230 GB átmásolása egy Azure standard fiók tárolóra nagyjából 3000 másodperc.

![Ezen a képernyőfelvételen látható egyik látható megjelenését az Azure portálon](media/sap-hana-backup-file-level/image028.png)

Ezen a képernyőfelvételen látható egy láthatja, hogyan nézzen ki az Azure portálon. A következő nevű blobtárolóban &quot;sap-hana-biztonsági mentések&quot; hozta létre, és a négy blobokat, és a biztonsági mentési SAP HANA-fájlokat tartalmazza. Az egyik legyen egy korlátja nagyjából 230 GB.

A biztonsági mentési HANA Studio konzol lehetővé teszi egy HANA biztonságimásolat-fájlok maximális méretének korlátozására. A minta környezetben teljesítményét azáltal, hogy előfordulhat, hogy több kisebb biztonságimásolat-fájl egy nagy 230 GB-os fájl helyett javult.

![A biztonságimásolat-fájl méretkorlátot beállítása a HANA ügyféloldali nem &#39; t javítása a biztonságimásolat-készítési időpont](media/sap-hana-backup-file-level/image029.png)

A biztonságimásolat-fájl méretkorlátot beállítása a HANA ügyféloldali nem &#39; t javíthatja a biztonságimásolat-készítési időpont, mert egymás után, az ábrán látható módon írja a fájlt. A fájlok méretkorlátját beállított 60 GB, így a biztonsági másolat négy nagy fájlok 230 GB-os egyetlen fájl helyett.

![A blobxfer eszköz párhuzamosságát teszteléséhez a maximális fájlméret HANA biztonsági másolatok majd beállítása 15 GB](media/sap-hana-backup-file-level/image030.png)

A blobxfer eszköz párhuzamosságát teszteléséhez a maximális fájlméret HANA biztonsági másolatok majd beállított 15 GB, ami 19 biztonságimásolat-fájlokat. Ez a konfiguráció állapotba másolása az Azure blob storage a 230 GB 875 másodperc le 3000 másodperc blobxfer időpontját.

Ennek az az oka 60 MB/s írásra, az Azure blob korlátot. Több blobok keresztül párhuzamossági megoldja a szűk keresztmetszetek, de a hátránya: HANA virtuális gép és a hálózati terhelést növelését a teljesítmény a blobxfer eszköz másolja a következő HANA biztonsági mentési fájlokat az Azure blob storage helyezi. HANA rendszer működésének hatással lesz.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Biztonsági mentési szoftver RAID dedikált Azure adatlemezek BLOB másolata

Ellentétben a manuális méretű lemez biztonsági mentését az ezt a módszert használja egy nem készítsen biztonsági másolatot a virtuális gép mentése az egész SAP a telepítés HANA adatokat, beleértve az összes adatlemezek HANA naplózza fájljai és konfigurációs fájlok. Ehelyett a lényege, a dedikált szoftveres RAID csíkozást a több Azure adatok VHD-k között SAP HANA fájl teljes biztonsági mentés tárolásához. Csak ezek lemezek, amelyek az SAP HANA biztonsági másolat egy másolja. Azok könnyen dedikált HANA biztonsági mentési tárfiók tartott, vagy egy dedikált csatolva &quot;biztonsági mentése a felügyeleti virtuális gép&quot; további feldolgozásra.

![Érintett összes VHD-k használatával másolása a ** start-azurestorageblobcopy ** PowerShell-paranccsal](media/sap-hana-backup-file-level/image031.png)

A helyi szoftveres RAID a biztonsági mentés után minden érintett virtuális másolása használatával a **start-azurestorageblobcopy** PowerShell-parancsot (lásd: [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Ez csak a dedikált fájlrendszere a biztonságimásolat-fájlok érinti, nincsenek nem kétségei SAP HANA adat- vagy naplófájl fájl konzisztencia a lemezen. Ez a parancs előnye, hogy működik a miközben a virtuális gép online marad. Ahhoz, hogy bizonyos, hogy egyetlen folyamat sem ír a biztonsági mentési stripe-készlet, ügyeljen arra, hogy a blob másolása előtt válassza le azt, és utána ismét csatlakoztatni. Vagy a megfelelő módon használhatja egy &quot;rögzítése&quot; a fájlrendszerben. Például keresztül xfs\_rögzítése a XFS fájlrendszerhez.

![Ezen a képernyőfelvételen látható BLOB listáját jeleníti meg a virtuális merevlemezek tárolóban az Azure portálon](media/sap-hana-backup-file-level/image032.png)

Ezen a képernyőfelvételen látható megjelennek a blobot, amely a &quot;VHD-k&quot; tároló az Azure portálon. A képernyőfelvételen látható az öt VHD-k, amelyek szolgáljanak, a szoftveres RAID a biztonsági mentési fájlok SAP HANA-virtuális gép SAP HANA-kiszolgálóhoz lenne csatlakoztatva. Emellett az öt másolatát, és a blob másolási parancs keresztül elvégzett jelenít meg.

![Tesztelési célokra, a SAP HANA biztonsági mentési szoftver RAID lemezek példányait VM alkalmazáskiszolgálóra lenne csatlakoztatva](media/sap-hana-backup-file-level/image033.png)

Tesztelési célokra, a SAP HANA biztonsági mentési szoftver RAID lemezek példányait VM alkalmazáskiszolgálóra lenne csatlakoztatva.

![Másolja át a virtuális gép le volt állítva, a lemez csatlakoztatásához alkalmazáskiszolgáló](media/sap-hana-backup-file-level/image034.png)

A virtuális gép alkalmazáskiszolgálóra le volt állítva, a lemez csatlakoztatásához másolja át. Után a virtuális gép indítása, a lemezek és a RAID derített fel megfelelően (csatlakoztatott UUID keresztül). Csak a csatlakoztatási pont hiányzott, amely a YaST particionáló lett létrehozva. Ezt követően az SAP HANA biztonságimásolat-fájlt másolja vált látható az operációs rendszer szintjén.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS-megosztás SAP HANA biztonságimásolat-fájl másolása

Lehetséges keresik a teljesítmény vagy a szabad terület perspektíva SAP HANA rendszeren, egy esetleg érdemes tárolni a SAP HANA biztonságimásolat-fájlok az NFS-megosztások. Technikailag működik, de az azt jelenti, hogy a második Azure virtuális gépek használata a gazdagép az NFS-megosztás. Nem lehet egy kis Virtuálisgép-méret miatt a virtuális gép hálózati sávszélességet. Ez leállítása lenne logika majd &quot;biztonsági mentése a virtuális gép&quot; és csak emelni feliratkozott a SAP HANA biztonsági mentés végrehajtásakor. Az NFS írás megosztás terhelés helyezi a hálózaton és nem lesz hatással az SAP HANA rendszert, de pusztán kezelése a biztonságimásolat-fájlok ezt követően a a &quot;biztonsági mentése a virtuális gép&quot; volna nem befolyásolják a SAP HANA-rendszer minden.

![Az NFS-megosztások egy másik Azure virtuális gép csatlakoztatása a virtuális gép SAP HANA-kiszolgálóhoz](media/sap-hana-backup-file-level/image035.png)

Az NFS-és nagybetűhasználattal ellenőrzéséhez egy NFS-megosztás egy másik Azure virtuális gép csatlakoztatása a virtuális gép SAP HANA-kiszolgálóhoz. Hiba történt az semmilyen különleges NFS hangolása alkalmazza.

![1 óra és 46 perc közvetlenül ehhez a biztonsági mentés végrehajtásának](media/sap-hana-backup-file-level/image036.png)

Az NFS-megosztás lett gyors paritásos készletként, például egy, a SAP HANA-kiszolgálón. 1 óra és 46 perc ettől függetlenül az NFS-megosztás 10 perc, ha a helyi paritásos írás beállítása helyett közvetlenül a Mentés végrehajtásának.

![A tulajdonos alternatív nem sokkal gyorsabb, 1 óra 43 perc](media/sap-hana-backup-file-level/image037.png)

A tulajdonos alternatív helyi stripe-készlethez biztonságimásolat-készítést, és az NFS-megosztás, az operációs rendszer szintjén másolása (egy egyszerű **cp - avr** parancs) nem sokkal gyorsabb. Végrehajtásának 1 óra 43 perc.

Így működik, de a teljesítmény nem volt helyes 230 GB-os biztonsági mentési vizsgálat. Lenne a több terabájt még rosszabb.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Az Azure file service SAP HANA biztonságimásolat-fájl másolása

Akkor lehet csatlakoztatni az Azure fájlmegosztások egy Azure Linux virtuális Gépen belül. A cikk [Azure File storage használata Linux](../../../storage/files/storage-how-to-use-files-linux.md) menete részleteit. Ne feledje, hogy jelenleg egy 5-TB-os kvótakorlátot egy Azure fájlmegosztás és a fájl mérete legfeljebb 1 TB-os fájlonként. Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](../../../storage/common/storage-scalability-targets.md) tárhelyet olvashat.

Tesztek kimutatták, azonban SAP HANA biztonsági mentési állapotszolgáltatáson &#39; t közvetlenül az ilyen típusú CIFS csatlakoztatási jelenleg működik. Azt is van megadva a [SAP Megjegyzés 1820529](https://launchpad.support.sap.com/#/notes/1820529) CIFS nem ajánlott.

![Az ábrán látható hiba az SAP HANA Studio a biztonsági mentés párbeszédpanelen](media/sap-hana-backup-file-level/image038.png)

Ez a szám SAP HANA Studio, a biztonsági mentés párbeszédpanelen jelez hibát, közvetlenül csatlakoztatott CIFS Azure fájlmegosztás biztonsági mentés közben. Tehát egy SAP HANA szabványos biztonsági először tegye a virtuális gép operációs rendszer, és másolja a biztonságimásolat-fájlok onnan az Azure file szolgáltatással.

![Az ábrán látható, hogy hamarosan 929 másodpercig tartott a 19 SAP HANA biztonságimásolat-fájl másolása](media/sap-hana-backup-file-level/image039.png)

Az ábrán látható, körülbelül 929 másodpercig tartott a 19 SAP HANA biztonságimásolat-fájlok a teljes méret nagyjából 230 GB másolása az Azure-fájlmegosztáshoz.

![A forrás mappastruktúrát a SAP HANA-virtuális gép Azure fájlmegosztás lett másolva](media/sap-hana-backup-file-level/image040.png)

Ezen a képernyőfelvételen látható egy láthatja, hogy a forrás mappastruktúrát a SAP HANA-virtuális gép Azure fájlmegosztás lett másolva: egy könyvtárat (hana\_biztonsági mentési\_fsl\_15 gb) és egyes biztonsági mentési fájlok 19.

SAP HANA biztonságimásolat-fájlok az Azure files tárolása érdekes lehetősége lehet a jövőben, ha a SAP HANA-fájlok biztonsági másolatait közvetlenül támogatja. Vagy ha csatlakoztatási Azure fájlok NFS keresztül lehetővé válik, és a maximális kvótakorlát jelentősen nagyobb, mint 5 TB.

## <a name="next-steps"></a>Következő lépések
* [Biztonsági mentési útmutató SAP Hana Azure virtuális gépeken](sap-hana-backup-guide.md) áttekintése és bevezető információkat biztosít.
* [A storage-pillanatfelvételekkel alapján SAP HANA biztonsági mentés](sap-hana-backup-storage-snapshots.md) ismerteti a tárolási pillanatkép-alapú biztonsági mentési beállítás.
* Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md).
