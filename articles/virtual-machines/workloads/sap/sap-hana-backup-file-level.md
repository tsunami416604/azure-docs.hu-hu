---
title: Az SAP HANA az Azure Backup a fájlok szintjére |} A Microsoft Docs
description: Két lehetőség közül választhat jelentős biztonsági mentés SAP Hana Azure-beli virtuális gépeken, ez a cikk ismerteti az SAP HANA az Azure Backup a fájlok szintjén
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: d3d1769766053b513a98df153cb635ae148f26b1
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867370"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Az SAP HANA az Azure Backup a fájlok szintjén

## <a name="introduction"></a>Bevezetés

Ez egy háromrészes sorozat kapcsolódó cikkekben az SAP HANA biztonsági másolat része. [Azure virtuális gépeken futó SAP Hana biztonsági mentési útmutató](./sap-hana-backup-guide.md) áttekintése és információt nyújt az első lépések, és [SAP HANA biztonsági mentés tárolási pillanatképeken alapuló](./sap-hana-backup-storage-snapshots.md) magában foglalja a pillanatkép-alapú biztonsági mentési beállítást.

Virtuális gépek különböző típusairól, az Azure-ban lehetővé teszik a csatlakoztatott virtuális merevlemezek oszlopaik száma különböző. Pontos részleteiért vannak dokumentálva [az Azure-ban Linux rendszerű virtuális gépek méretei](../../linux/sizes.md). A tesztek ebben a dokumentációban hivatkozott, amely lehetővé teszi, hogy 64 csatlakoztatott adatlemezekkel GS5 Azure virtuális gép használtuk. Olyan nagyobb rendszerekhez, az SAP HANA jelentős számú lemez már előfordulhat, hogy kell venni adathoz és naplófájlhoz, valószínűleg a szoftver csíkozást optimális lemezteljesítmény i/o-kombinációját. Az Azure virtuális gépeken futó SAP HANA-telepítéshez javasolt lemezkonfigurációja további részletekért olvassa el a cikket [SAP HANA az Azure-műveletek útmutatójában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Javaslatok útmutatóként van többek között a lemez terület ajánlásokat, valamint a helyi biztonsági mentésekhez.

Érhető nincs biztonsági mentés SAP HANA-integráció az Azure Backup szolgáltatás jelenleg. Standard úgy kezelheti a biztonsági mentési és visszaállítási a fájlok szintjén van egy fájl alapú biztonsági mentéssel vagy SAP HANA SQL-utasítások az SAP HANA Studio keresztül. Lásd: [SAP HANA SQL és a rendszer nézetek hivatkozási](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) további információt.

![Ezen az ábrán a párbeszédpanelen, a biztonsági mentési menüelem az SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Ezen az ábrán a párbeszédpanelen, a biztonsági mentési menüelem az SAP HANA Studio. Típus kiválasztásakor &quot;fájl&quot; adatáthelyezések a fájlrendszerbeli elérési útja, az SAP HANA ír a biztonságimásolat-fájlokat. Visszaállítás ugyanúgy működik.

Ez a választás úgy érzi, egyszerű és nagyon egyszerű, amíg nincsenek szempontokat. Ahogy korábban említettük, egy Azure virtuális gép korlátozott számú adatlemez csatolható. Nem tárolja az SAP HANA biztonsági mentési fájlokat a virtuális gép, a lemez és az adatbázis átviteli sebességet megkövetelő, amelyek között több adatlemez szoftver csíkozást is járhat méretétől függően a fájl rendszereken kapacitás lehet. Különböző lehetőség e biztonságimásolat-fájlokat, és kezelését fájl mérete korlátozások és teljesítmény kezelésekor a terabájtnyi adatot, a cikk későbbi részében találhatók.

Egy másik lehetőség, amellyel kapcsolatban teljes kapacitás nagyobb szabadságot kínál, az Azure blob storage-bA. Egy blob is 1 TB-os korlátozva, amíg a teljes kapacitás egyetlen blob-tároló jelenleg 500 TB. Ezenkívül biztosít ügyfeleink választhatja úgynevezett &quot;ritkán használt adatok&quot; a blob storage, amely rendelkezik egy költségmegtakarítást jelent. Lásd: [Azure Blob Storage: gyakran és ritkán használt adatok tárolási rétege](../../../storage/blobs/storage-blob-storage-tiers.md) ritka elérésű blobtároló részleteit.

További biztonsági georeplikált storage-fiók használatával az SAP HANA biztonsági másolatok tárolására. Lásd: [Azure Storage replikáció](../../../storage/common/storage-redundancy.md) tárfiók replikációjának részleteit.

SAP HANA biztonsági mentések dedikált virtuális merevlemezek egy tudta elhelyezni, amely georeplikált dedikált backup storage-fiókban található. Ellenkező esetben az egyik sikerült másolni a virtuális merevlemezeket, hogy az SAP HANA biztonsági mentés egy georeplikált tárfiókba, vagy egy másik régióban lévő tárfiókhoz.

## <a name="azure-backup-agent"></a>Az Azure Backup szolgáltatás ügynöke

Az Azure backup emellett lehetőséget biztosít a nemcsak a biztonsági másolatot a teljes virtuális gépeket, de is a fájlok és könyvtárak keresztül a biztonságimásolat-készítő ügynök, amely a vendég operációs rendszer telepítve van. Ez az ügynök csak a Windows támogatott, de (lásd: [biztonsági mentése a Windows Server vagy az ügyfél az Azure-bA a Resource Manager üzemi modell használatával](../../../backup/backup-configure-vault.md)).

Egy megkerülő megoldás, hogy először az SAP HANA biztonsági mentési fájlok másolása az Azure-beli Windows virtuális gép (például keresztül SAMBA megosztás), majd az Azure Backup szolgáltatás ügynöke onnan. Bár technikailag lehetséges, lenne összetettséget és lelassul a biztonsági mentési vagy visszaállítási folyamat meglehetősen egy kicsit miatt a példányt a Linux és a Windows virtuális gép között. Nem ajánlott ezt a megközelítést.

## <a name="azure-blobxfer-utility-details"></a>Az Azure blobxfer segédprogram részletei

Az Azure storage található könyvtárak és fájlok tárolásához, egy sikerült CLI vagy a PowerShell használatával, vagy fejlesszen ki egy eszközt, az egyik a [Azure SDK-k](https://azure.microsoft.com/downloads/). Emellett van egy használatra kész, az AzCopy segédprogram az adatok másolása az Azure storage-ba, de Windows csak (lásd: [adatátvitel az AzCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy.md)).

Ezért blobxfer használták az SAP HANA biztonsági mentési fájlokat. Nyílt forráskódú, termelési környezetben számos ügyfél által használt és elérhető legyen [GitHub](https://github.com/Azure/blobxfer). Ez az eszköz lehetővé teszi adatok közvetlenül az Azure blob storage vagy Azure-fájlmegosztás másolása egyik. Azt is számos hasznos funkciókat, például az md5-kivonat vagy automatikus párhuzamosság egy könyvtárat a több fájl másolásakor.

## <a name="sap-hana-backup-performance"></a>SAP HANA biztonsági mentés teljesítményét

![Az SAP HANA biztonsági mentési konzol, az SAP HANA Studio van ezen a képernyőfelvételen látható](media/sap-hana-backup-file-level/image023.png)

Ezen a képernyőfelvételen a SAP HANA biztonsági mentési konzol, az SAP HANA Studio van. Ehhez a biztonsági mentés XFS fájlrendszert használ, a HANA virtuális Géphez csatolt Azure standard szintű tárolást egyetlen lemezen 230 GB végrehajtásának készül 42 percet.

![Ezen a képernyőfelvételen látható az YaST, a SAP HANA-teszt virtuális gép](media/sap-hana-backup-file-level/image024.png)

A SAP HANA teszteléshez használt virtuális gép YaST nem ezen a képernyőfelvételen látható. Ahogy korábban említettük, egy SAP HANA biztonsági mentés egyetlen 1 TB-os lemezt láthatja. Körülbelül 42 percet tartott a biztonsági mentési 230 GB. Emellett öt 200 GB-os lemezt lenne csatlakoztatva, és a szoftver RAID md0 hozott létre, az alábbi öt Azure-adatlemezek felett szétosztottsága befolyásolhatja.

![Ismételje meg a szoftver azonos biztonsági másolatának öt szétosztottsága befolyásolhatja a RAID csatlakoztatott adatlemezeket az Azure standard szintű storage](media/sap-hana-backup-file-level/image025.png)

Ismételje meg a szoftver azonos biztonsági másolatának öt szétosztottsága befolyásolhatja a RAID csatlakoztatott állapotba a biztonsági mentés időpontja 42 percet lefelé 10 perc vagy az Azure standard szintű storage adatlemezek. A lemezek csatolása a virtuális géphez gyorsítótárazása nélkül történt. Így a biztonsági mentés ideje van nyilvánvaló mennyire fontos a lemezírás teljesítménye. Egy lehetett váltani az Azure Premium Storage tovább gyorsíthatja a folyamatot az optimális teljesítmény érdekében. Az Azure Premium Storage általában használandó éles rendszerek esetén.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA biztonsági mentési fájlok másolása az Azure blob storage-bA

Egy másik lehetőség, gyorsan az SAP HANA biztonsági másolatok tárolására, az Azure blob storage-bA. Egy egyetlen blobtároló 500 TB-os, néhány kisebb az SAP HANA-rendszerek esetén elegendő M32ts, M32ls, M64ls és GS5 virtuális gép típusú Azure-használatával, hogy elegendő az SAP HANA biztonsági másolatok esetében. Az ügyfelek választhatnak között &quot;gyakori elérésű&quot; és &quot;hideg&quot; blob storage-ban (lásd: [Azure Blob Storage: gyakran és ritkán használt adatok tárolási rétege](../../../storage/blobs/storage-blob-storage-tiers.md)).

A blobxfer eszközzel az SAP HANA biztonsági mentési fájlok másolása a közvetlenül az Azure blob storage-könnyebbé vált.

![Itt látható egy teljes SAP HANA biztonsági mentés fájljai](media/sap-hana-backup-file-level/image026.png)

Itt láthatja egy teljes SAP HANA biztonsági mentés fájljait. Négy fájlok vannak, és a legnagyobb már nagyjából 230 GB.

![A 230 GB-os átmásolása egy az Azure standard szintű storage-fiók blobtároló körülbelül 3000 másodpercig tartott](media/sap-hana-backup-file-level/image027.png)

A kezdeti teszt nem használja az md5-kivonat, ideig tartott a 230 GB-os átmásolása egy az Azure standard szintű storage-fiók blobtároló körülbelül 3000 másodperc.

![Ezen a képernyőfelvételen látható egy látható hogy néz ki az Azure Portalon](media/sap-hana-backup-file-level/image028.png)

Ezen a képernyőfelvételen látható egy tekintheti meg, hogy néz ki az Azure Portalon. Egy blobtárolót &quot;sap-hana – biztonsági másolatok&quot; lett létrehozva, és tartalmazza a négy blobok, amelyek tartalmazzák az SAP HANA biztonsági másolatok. Az egyiket van egy nagyjából 230 GB méretű.

A HANA Studio biztonsági mentési konzol lehetővé teszi egy HANA biztonsági másolatok maximális méretének korlátozására. Minta a környezetben így lehetséges, hogy több kisebb méretű biztonsági mentési fájl helyett egy nagy 230 GB-os fájlt, továbbfejlesztett teljesítmény.

![A biztonságimásolat-fájl maximális mérete beállítást a HANA ügyféloldali nem&#39;t javíthatja a biztonsági mentés időpontja](media/sap-hana-backup-file-level/image029.png)

A biztonságimásolat-fájl maximális mérete beállítást a HANA ügyféloldali nem&#39;t javítása a biztonsági mentés időpontja, mert a fájlok írt egymás után, az ábrán látható módon. A fájl mérete legfeljebb 60 GB lett beállítva, ezért a biztonsági másolat létre négy nagyméretű adatfájlok 230 GB-os egyetlen fájl helyett. Több biztonsági mentési fájl használata áttelepítésére, az Azure-beli például M64s, M64ms, M128s és M128ms nagyobb méretű virtuális memória HANA-adatbázisok biztonsági mentése.

![Párhuzamosság a blobxfer eszköz teszteléséhez a HANA biztonsági másolatok maximális fájlméret majd beállíthatja a 15 GB](media/sap-hana-backup-file-level/image030.png)

Párhuzamosság a blobxfer eszköz teszteléséhez a HANA biztonsági másolatok maximális fájlméret majd állították be 15 GB, 19 biztonságimásolat-fájlok eredményezett. Ez a konfiguráció az idő a 230 GB-os az Azure blob storage-másol le 875 másodperc 3000 másodperc blobxfer állapotba.

Ez az eredmény a 60 MB/s írásához egy Azure-blobból miatt van. Párhuzamosság keresztül több blobok megoldja a szűk keresztmetszetet, de ennek a hátránya van: növelje a teljesítményt a blobxfer eszköz ezek HANA biztonsági mentési fájlok másolása az Azure blob storage-be illeszti be a terhelés a HANA-Virtuálisgép-és a hálózati. HANA rendszer működésének negatív lesz.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>BLOB másolása a biztonsági mentési szoftver RAID dedikált Azure-beli adat-lemezek

A manuális virtuális lemez biztonsági mentését, ellentétben ebben a megközelítésben az egyik nem készítsen biztonsági másolatot a menteni a teljes SAP-telepítés, a HANA-adatokat, beleértve a virtuális gép összes adatlemezét HANA naplózza fájlok és a konfigurációs fájlok. Ehelyett a cél, hogy rendelkezik dedikált szoftveres RAID szétosztottsága befolyásolhatja a több Azure-beli adat VHD-k között egy teljes SAP HANA biztonsági mentés tárolásához. Csak ezek a lemezek, amelyek rendelkeznek a SAP HANA biztonsági mentés egy másolja. Azok egyszerűen egy dedikált fiókot a HANA biztonsági mentési tár tárolt, vagy egy dedikált csatolt &quot;felügyeleti virtuális gép biztonsági mentése&quot; további feldolgozás céljából.

![Érintett összes virtuális merevlemez használatával lettek másolva a ** start-azurestorageblobcopy ** PowerShell-paranccsal](media/sap-hana-backup-file-level/image031.png)

Miután befejeződött a biztonsági mentés a helyi szoftveres RAID, részt vevő összes virtuális merevlemez másolása használatával a **start-azurestorageblobcopy** PowerShell-parancsot (lásd: [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Csak befolyásolja a dedikált fájlrendszer a biztonságimásolat-fájlok, nincsenek nincs aggodalmai vannak az SAP HANA adat- vagy naplófájl fájl konzisztencia a lemezen. Ez a parancs előnye, hogy működés közben a virtuális gép online marad. Ahhoz, hogy bizonyos, hogy egyetlen folyamat ír a biztonsági mentési stripe-készlet, mindenképpen válassza le azt a blob másolása előtt, és csatlakoztassa újra később. Vagy egy lehetne a megfelelő módon &quot;rögzítése&quot; a fájlrendszerben. Például keresztül xfs\_befagyasztani a fájlrendszer esetén XFS.

![Ezen a képernyőfelvételen látható a blobok listáját a VHD-ket tároló az Azure Portalon](media/sap-hana-backup-file-level/image032.png)

Ezen a képernyőfelvételen a blobok listáját jeleníti meg a &quot;VHD-k&quot; tárolót az Azure Portalon. A képernyőfelvételen a öt virtuális merevlemezek, az SAP HANA-kiszolgáló virtuális Géphez, a szoftveres RAID a SAP HANA biztonsági mentési fájlok egyikükön lett csatolva. Azt is bemutatja a öt példányban, és a blob másolása parancsot keresztül származnak.

![Tesztelési célokra, az SAP HANA biztonsági mentési szoftver RAID lemezek példányait lenne csatlakoztatva az alkalmazás-kiszolgáló virtuális Géphez](media/sap-hana-backup-file-level/image033.png)

Tesztelési célokra az alkalmazás-kiszolgáló virtuális Géphez az SAP HANA biztonsági mentési szoftver RAID lemezek példányait lenne csatlakoztatva.

![Másolja át a virtuális gép le lett állítva, a lemez csatlakoztatásához alkalmazáskiszolgáló](media/sap-hana-backup-file-level/image034.png)

Az alkalmazás-kiszolgáló virtuális gép le lett állítva, a lemez csatlakoztatásához másolja. Miután a virtuális gép indításával, a lemezek és a RAID felderített megfelelően (csatlakoztatott UUID-n keresztül). Csak a csatlakoztatási pont hiányzott, amely a YaST partitioner lett létrehozva. Ezt követően az SAP HANA biztonsági mentési Fájlmásolások vált, látható az operációs rendszer szintjén.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>NFS-megosztások SAP HANA biztonsági mentési fájlok másolása

Lehetséges keresik a teljesítmény vagy lemez terület perspektíva SAP HANA rendszerben, előfordulhat, hogy figyelembe venni az NFS-megosztások az SAP HANA biztonsági mentési fájlok tárolására. Technikailag működik, de azt jelenti, hogy a gazdagép az NFS-megosztás egy második Azure virtuális gép használatával. Nem lehet egy kisméretű Virtuálisgép-méretet a virtuális gép hálózati sávszélesség miatt. Akkor lenne értelme majd állítsa le ezt a &quot;biztonsági másolatot készíteni a virtuális gép&quot; és csak a SAP HANA biztonsági mentés végrehajtása regisztrálásához. Az NFS írás megosztás terhelés helyezi a hálózaton és hatással van az SAP HANA rendszer, de pusztán a biztonsági mentési fájlok kezelése ezt követően a a &quot;biztonsági másolatot készíteni a virtuális gép&quot; lenne nem befolyásolja az SAP HANA rendszer egyáltalán.

![Az SAP HANA-kiszolgáló virtuális Géphez csatlakoztatása egy NFS-megosztás egy másik Azure virtuális gépből](media/sap-hana-backup-file-level/image035.png)

Annak ellenőrzéséhez, hogy az NFS használati eset, az SAP HANA-kiszolgáló virtuális Géphez csatlakoztatása egy NFS-megosztás egy másik Azure virtuális gépből. Hiba történt a semmilyen különleges NFS finomhangolása a alkalmazni.

![1 óra és 46 perc közvetlenül ehhez a biztonsági mentés szükséges](media/sap-hana-backup-file-level/image036.png)

Az NFS-megosztás egy gyors stripe-csoportba, például egy, az SAP HANA-kiszolgáló a volt. Mindazonáltal tartottak 1 óra 46 perc ehhez a biztonsági mentést közvetlenül az NFS-megosztás 10 perc, ha írnak a helyi paritásos beállítása helyett.

![A tulajdonos alternatív nem sokkal gyorsabb, 1 óra 43 perc](media/sap-hana-backup-file-level/image037.png)

A tulajdonos alternatív helyi stripe-készlethez biztonságimásolat-készítést, és másolja az NFS-megosztás, az operációs rendszer szintjén (egy egyszerű **cp - avr** parancs) nem sokkal gyorsabb. 1 óra 43 perc szükséges.

Úgy működik, de a teljesítmény nem volt helyes a 230 GB-os biztonsági mentési teszthez. Lenne a több terabájt még rosszabb.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA biztonsági mentési fájlok másolása az Azure Files

Akkor lehet csatlakoztatni egy Azure-fájlmegosztást az Azure Linux virtuális Gépen belül. A cikk [Azure File storage használata linuxszal](../../../storage/files/storage-how-to-use-files-linux.md) mindez ismerteti. Ne feledje, hogy jelenleg egy 5 TB-os kvótát egy Azure-fájlmegosztást, és a fájl mérete legfeljebb 1 TB-os fájlonként. Lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../../../storage/common/storage-scalability-targets.md) tárhelykorlátok tájékoztatást.

Tesztek rendelkezik is látható, azonban az SAP HANA biztonsági mentési állapotszolgáltatáson&#39;t jelenleg munkahelyi közvetlenül a CIFS csatlakoztatási az ilyen típusú. Azt is van megadva a [SAP Megjegyzés 1820529](https://launchpad.support.sap.com/#/notes/1820529) CIFS nem javasolt.

![Ezen az ábrán hiba az SAP HANA Studio biztonsági mentési párbeszédpanel](media/sap-hana-backup-file-level/image038.png)

Ez az ábra az SAP HANA Studio, a biztonsági mentési párbeszédpanelen jelez hibát, közvetlenül a CIFS csatlakoztatott Azure-fájlmegosztás biztonsági mentése közben. Így az egyik szabványos SAP HANA biztonsági VM fájl rendszerbe először, és majd onnan másolja a biztonságimásolat-fájlokat az Azure file Storage-szolgáltatás.

![Ez az ábra bemutatja, hogy körülbelül 929 másodpercig tartott a 19 SAP HANA biztonsági mentési fájlok másolása](media/sap-hana-backup-file-level/image039.png)

Ez az ábra azt mutatja, körülbelül 929 másodpercig tartott a 19 SAP HANA biztonságimásolat-fájlok a teljes méret nagyjából 230 GB másolása az Azure-fájlmegosztást.

![A forrás könyvtárstruktúrát a SAP HANA virtuális gépen a rendszer átmásolta-Azure-fájlmegosztás](media/sap-hana-backup-file-level/image040.png)

Ezen a képernyőfelvételen látható egy láthatja, hogy a forrás könyvtárstruktúrát a SAP HANA virtuális gépen az Azure-fájlmegosztás másolta: egy könyvtárat (hana\_biztonsági mentési\_fsl\_15 gb) és egyes biztonsági mentési fájlok 19.

SAP HANA az Azure files biztonsági mentési fájlok tárolására lehet egy érdekes beállítást, a jövőben, ha az SAP HANA biztonsági másolatainak közvetlenül támogatja azt. Vagy ha Azure files csatlakoztatása az NFS-n keresztül is, és a maximális kvótahatár: jelentősen nagyobb, mint 5 TB-os.

## <a name="next-steps"></a>További lépések
* [Azure virtuális gépeken futó SAP Hana biztonsági mentési útmutató](sap-hana-backup-guide.md) áttekintése és első lépések információkat biztosít.
* [SAP HANA biztonsági mentés tárolási pillanatképeken alapuló](sap-hana-backup-storage-snapshots.md) ismerteti a pillanatkép-alapú biztonsági mentési beállítást.
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
