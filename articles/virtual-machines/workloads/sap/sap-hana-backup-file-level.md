---
title: SAP HANA Azure Backup a fájl szintjén | Microsoft Docs
description: Az Azure Virtual Machines szolgáltatásban SAP HANA két fő biztonsági mentési lehetőség áll rendelkezésre, ez a cikk a fájlok szintjén SAP HANA Azure Backupt ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: e8ad1a9468c52fd120360827ed3a08f8f53ec3d1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426005"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup a fájl szintjén

## <a name="introduction"></a>Bevezetés

Ez egy, a SAP HANA biztonsági mentéssel kapcsolatos cikkek három részből álló sorozatának részét képezi. Az [Azure Virtual Machines SAP HANA biztonsági mentési útmutatója](./sap-hana-backup-guide.md) áttekintést nyújt az első lépésekről, és a [tárolási pillanatképek alapján SAP HANA biztonsági mentés](./sap-hana-backup-storage-snapshots.md) a Storage pillanatkép-alapú biztonsági mentési lehetőségével foglalkozik.

Az Azure-beli különböző virtuálisgép-típusok különböző számú virtuális merevlemezt tesznek lehetővé. A pontos részleteket a [Linux rendszerű virtuális gépek méreteiben dokumentáljuk az Azure-ban](../../linux/sizes.md). Az ebben a dokumentációban említett tesztek esetében egy GS5 Azure-beli virtuális gépet használunk, amely lehetővé teszi a 64-hez csatlakoztatott adatlemezek használatát. Nagyobb SAP HANA rendszerek esetében előfordulhat, hogy jelentős számú lemezre van szükség az adatok és a naplófájlok esetében, valószínűleg az optimális lemezes i/o-átviteli sebességhez. Az Azure-beli virtuális gépeken SAP HANA üzemelő példányokkal kapcsolatos javasolt lemez-konfigurációkról további részleteket az [Azure üzemeltetési útmutató SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)című cikkben olvashat. A javaslatok a helyi biztonsági mentésekre vonatkozó lemezterület-ajánlásokat is tartalmaznak.

Jelenleg nem érhető el SAP HANA biztonsági mentési integráció Azure Backup szolgáltatással. A Backup/Restore fájl szintjén való kezelésének szabványos módja a fájl-alapú biztonsági mentés SAP HANA studión keresztül vagy SAP HANA SQL-utasításokon keresztül. További információ: [SAP HANA SQL-és rendszernézetek leírása](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) .

![Ez az ábra a SAP HANA Studio Backup (biztonsági mentés) menüpontjának párbeszédpanelét jeleníti meg](media/sap-hana-backup-file-level/image022.png)

Ez az ábra a SAP HANA Studio Backup (biztonsági mentés) menüjének párbeszédpanelét jeleníti meg. Ha a Type &quot;file (típus) lehetőséget választja,&quot; egy elérési utat kell megadnia a fájlrendszerben, ahol a SAP HANA a biztonságimásolat-fájlokat írja. A visszaállítás ugyanúgy működik.

Habár ez a választás egyszerű és egyenes előre hangzik, néhány szempontot figyelembe kell venni. Ahogy azt korábban említettük, az Azure-beli virtuális gépek korlátozásával korlátozható a csatolható adatlemezek száma. Előfordulhat, hogy a virtuális gép fájlrendszerén nem lehet kapacitást tárolni SAP HANA biztonságimásolat-fájlok tárolásához, az adatbázis méretétől és a lemez átviteli sebessége követelményeitől függően, amely több adatlemezen is tartalmazhat szoftvereket. A biztonságimásolat-fájlok áthelyezésének különböző lehetőségei, valamint a fájlméretre vonatkozó korlátozások és teljesítmény kezelése a jelen cikk későbbi részében található.

Egy másik lehetőség, amely a teljes kapacitással kapcsolatos nagyobb szabadságot nyújt, az Azure Blob Storage. Habár egyetlen blob is 1 TB-ra van korlátozva, egy blob-tároló teljes kapacitása jelenleg 500 TB. Emellett a felhasználók dönthetnek úgy, hogy kiválasztják az úgynevezett &quot;ritka elérésű&quot; blob Storage-t, amely a Cost Benefit. Tekintse meg az [Azure Blob Storage: gyakori és ritka elérésű tárolási rétegeket](../../../storage/blobs/storage-blob-storage-tiers.md) a hasznos Blobok tárolásával kapcsolatos részletekért.

A további biztonság érdekében a SAP HANA biztonsági mentések tárolásához használjon földrajzilag replikált Storage-fiókot. A Storage-fiók replikálásával kapcsolatos részletekért lásd: az [Azure Storage replikációja](../../../storage/common/storage-redundancy.md) .

Az egyik dedikált virtuális merevlemezt helyezhet SAP HANA biztonsági másolatokhoz egy olyan dedikált biztonsági mentési Storage-fiókban, amely földrajzilag replikálva van. Egy másik lehetséges, hogy átmásolja a SAP HANA biztonsági másolatokat tároló virtuális merevlemezeket egy földrajzilag replikált Storage-fiókba vagy más régióban található Storage-fiókba.

## <a name="azure-backup-agent"></a>Azure Backup-ügynök

Az Azure Backup nem csak a teljes virtuális gépek biztonsági mentését, hanem a biztonsági mentési ügynökön keresztül a fájlokat és könyvtárakat is támogatja, amelyeket telepíteni kell a vendég operációs rendszerre. Ez az ügynök azonban csak Windows rendszeren támogatott (lásd: [Windows-kiszolgáló vagy-ügyfél biztonsági mentése az Azure-ba a Resource Manager](../../../backup/backup-configure-vault.md)-alapú üzemi modell használatával).

A megkerülő megoldás az, hogy először másolja SAP HANA biztonsági mentési fájlokat egy Azure-beli Windows rendszerű virtuális gépre (például a SAMBA-megosztáson keresztül), majd az Azure Backup ügynököt használja innen. A Linux és a Windows rendszerű virtuális gép közötti másolás miatt azonban technikailag lehetséges, hogy összetettséget okozna, és lelassítja a biztonsági mentési vagy visszaállítási folyamatot. Ezt a megközelítést nem ajánlott követni.

## <a name="azure-blobxfer-utility-details"></a>Az Azure blobxfer segédprogram részletei

Az Azure Storage-beli könyvtárak és fájlok tárolására a CLI vagy a PowerShell használatával, illetve az [Azure SDK](https://azure.microsoft.com/downloads/)-k egyikének használatával lehet létrehozni egy eszközt. Az adatok Azure Storage-ba történő másolásához használható használatra kész AzCopy is. (lásd: [adatok átvitele a AzCopy parancssori segédprogrammal](../../../storage/common/storage-use-azcopy.md)).

Ezért a blobxfer SAP HANA biztonságimásolat-fájlok másolására használták. Nyílt forráskód, amelyet számos ügyfél használ éles környezetben, és elérhető a [githubon](https://github.com/Azure/blobxfer). Ez az eszköz lehetővé teszi, hogy az egyiket közvetlenül az Azure Blob Storage-ba vagy az Azure-fájlmegosztásba másolja. Számos hasznos funkciót is kínál, például az MD5-kivonatot vagy az automatikus párhuzamosságot, ha több fájllal rendelkező könyvtárat másol.

## <a name="sap-hana-backup-performance"></a>Az SAP HANA biztonsági mentésének teljesítménye

![Ez a képernyőkép a SAP HANA Studióban található SAP HANA biztonsági mentési konzolról](media/sap-hana-backup-file-level/image023.png)

Ez a képernyőkép a SAP HANA Studio SAP HANA biztonsági mentési konzolján található. Körülbelül 42 percet vett igénybe a 230 GB biztonsági mentését egyetlen, a HANA virtuális géphez csatolt, a XFS fájlrendszert használó Azure standard Storage-lemezen.

![Ez a képernyőkép a SAP HANA test VM-YaST](media/sap-hana-backup-file-level/image024.png)

Ez a képernyőkép a SAP HANA test VM-YaST. Az előzőekben leírtak szerint a SAP HANA biztonsági mentés 1 TB-os lemezét láthatja. Körülbelül 42 percet vett igénybe a 230 GB biztonsági mentése. Emellett a 5 200 GB-os lemezek csatolták és a szoftveres RAID-md0 is létrehozták, és ezek az öt Azure-adatlemezre épülnek.

![Ugyanazon biztonsági másolat megismétlése a szoftveres RAID-ben az öt csatolt Azure standard Storage-adatlemezek között](media/sap-hana-backup-file-level/image025.png)

Ugyanezt a biztonsági mentést ugyanazzal a szoftveres RAID-sel megismételve, amely öt csatolt Azure standard Storage-adatlemezre mutat, a biztonsági mentés ideje 42 perctől 10 percre. A lemezeket a virtuális géphez való gyorsítótárazás nélkül csatlakoztatták. Ezért nyilvánvaló, hogy milyen fontos a lemez írási sebessége a biztonsági mentés idejére. Ezután válthat az Azure Premium Storagera az optimális teljesítmény érdekében. Az Azure Premium Storaget általában éles rendszerekhez kell használni.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA biztonságimásolat-fájlok másolása az Azure Blob Storage-ba

Egy másik lehetőség a SAP HANA biztonsági mentési fájlok gyors tárolására az Azure Blob Storage. Az egyetlen blob-tároló 500 TB-os korláttal rendelkezik, amely néhány kisebb SAP HANA rendszer esetében elegendő a M32ts, a M32ls, a M64ls és az Azure GS5 virtuálisgép-típusaival, hogy elegendő SAP HANA biztonsági mentést lehessen tartani. Az ügyfelek választhatnak, hogy &quot;a gyors&quot; és &quot;a&quot; blob Storage-tárolót (lásd [: Azure Blob Storage: gyakori és](../../../storage/blobs/storage-blob-storage-tiers.md)ritka elérésű tárolási szintek).

A blobxfer eszközzel egyszerűen másolhatók a SAP HANA biztonsági másolatok fájljai közvetlenül az Azure Blob Storage-ba.

![Itt láthatja a teljes SAP HANA fájl biztonsági mentésének fájljait](media/sap-hana-backup-file-level/image026.png)

Itt láthatja a teljes SAP HANA fájl biztonsági mentésének fájljait. Négy fájl, a legnagyobb pedig nagyjából 230 GB.

![Körülbelül 3000 másodpercet vett igénybe a 230 GB másolása Azure standard Storage-fiók blob-tárolóba](media/sap-hana-backup-file-level/image027.png)

A kezdeti tesztben nem használ MD5-kivonatot, nagyjából 3000 másodpercet vett igénybe a 230 GB másolása egy Azure standard Storage-fiók blob-tárolóba.

![Ebben a képernyőfelvételben láthatja, hogyan néz ki a Azure Portal](media/sap-hana-backup-file-level/image028.png)

Ebben a képernyőfelvételben láthatja, hogyan néz ki a Azure Portal. A rendszer létrehozta &quot;SAP-Hana-Backups&quot; nevű BLOB-tárolót, és tartalmazza a négy blobot, amely a SAP HANA biztonságimásolat-fájlokat jelképezi. Az egyik ilyen méret körülbelül 230 GB.

A HANA Studio biztonsági mentési konzol lehetővé teszi, hogy az egyik korlátozza a HANA biztonságimásolat-fájlok maximális fájlméretét. A mintavételezési környezetben a teljesítmény jobb, ha egy nagyméretű 230 GB-os fájl helyett több kisebb biztonsági mentési fájl is elérhetővé válik.

![A biztonsági mentés fájlméretének korlátozásának beállítása a HANA oldalon&#39;nem a biztonsági mentés időpontjának javítása](media/sap-hana-backup-file-level/image029.png)

A biztonsági mentési fájlméret korlátozásának beállítása a HANA oldalán&#39;nem javíthatja a biztonsági mentés idejét, mivel a fájlok egymás után, az ábrán látható módon íródnak. A fájlméret korlátja 60 GB volt, ezért a biztonsági mentés négy nagy adatfájlt hozott létre a 230 GB-os egyetlen fájl helyett. Több biztonságimásolat-fájl használata szükséges a HANA-adatbázisok biztonsági mentéséhez, amelyek kihasználják a nagyobb Azure-beli virtuális gépek memóriáját, például a M64s, a M64ms, a M128s és a M128ms.

![A blobxfer eszköz párhuzamosságának teszteléséhez a HANA biztonsági mentések maximális fájlmérete 15 GB-ra van állítva](media/sap-hana-backup-file-level/image030.png)

A blobxfer eszköz párhuzamosságának teszteléséhez a HANA biztonsági mentések maximális fájlméretét 15 GB-ra állította, ami 19 biztonsági mentési fájlt eredményezett. Ez a konfiguráció azt az időpontot hozta, hogy a blobxfer a 230 GB-ot az Azure Blob Storage-ba másolja a 3000 másodperctől a 875 másodpercig.

Ennek eredményeként az Azure-Blobok írásához a 60 MB/s korlátot kell megadni. A párhuzamosságok több blobon keresztül oldják meg a szűk keresztmetszetet, de van egy hátránya: a blobxfer eszköz teljesítményének növelése az összes ilyen HANA biztonságimásolat-fájl Azure Blob Storage-ba történő másolásával a HANA-beli virtuális gép és a hálózat is betöltődik. A HANA-rendszerek működése hatással lesz rá.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>A dedikált Azure-adatlemezek blob-példánya a Backup szoftver RAID szolgáltatásban

A virtuális gép kézi adatlemezének biztonsági mentésével ellentétben, ebben a megoldásban a virtuális gép összes adatlemezéről nem készít biztonsági mentést a teljes SAP-telepítés, beleértve a HANA-adatfájlokat, a HANA-naplófájlokat és a konfigurációs fájlokat. Ehelyett az a célja, hogy dedikált szoftveres RAID-sel rendelkezzen több Azure-beli adatvirtuális merevlemezen, amely teljes SAP HANA fájl biztonsági mentését tárolja. Az egyik a SAP HANA biztonsági másolattal rendelkező lemezeket másolja. Ezek könnyen megtekinthetők egy dedikált HANA biztonsági mentési Storage-fiókban, vagy egy dedikált &quot;biztonsági mentési felügyeleti virtuális géphez&quot; további feldolgozás céljából.

![A rendszer az összes érintett virtuális merevlemezt a * * Start-azurestorageblobcopy * * PowerShell parancs használatával másolta](media/sap-hana-backup-file-level/image031.png)

A helyi szoftveres RAID biztonsági mentésének befejezése után a rendszer az összes érintett virtuális merevlemezt a **Start-azurestorageblobcopy** PowerShell-paranccsal másolta (lásd: [Start-azurestorageblobcopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Mivel a biztonsági másolatok tárolásához csak a dedikált fájlrendszerre van hatással, a lemez SAP HANA adatai vagy a naplófájl konzisztenciája nem vonatkozik. Ennek a parancsnak az az előnye, hogy működik, miközben a virtuális gép online állapotban marad. Győződjön meg arról, hogy a folyamat nem ír a biztonsági mentési sáv készletbe, ezért a blob másolása előtt válassza le azt, majd csatlakoztassa újra. Vagy egy megfelelő módszert használhat a fájlrendszer&quot; rögzítésének &quot;. Például a XFS keresztül\_a XFS-fájlrendszer rögzítése.

![Ez a képernyőkép a Azure Portal VHD-tárolójában lévő Blobok listáját jeleníti meg](media/sap-hana-backup-file-level/image032.png)

Ez a képernyőkép a &quot;VHD-&quot; tárolóban lévő Blobok listáját jeleníti meg a Azure Portal. A képernyőképen az öt virtuális merevlemez látható, amelyek a SAP HANA Server virtuális géphez vannak csatolva, hogy a szoftveres RAID legyen a SAP HANA biztonsági mentési fájlok megtartása érdekében. Emellett a blob Copy paranccsal készített öt példányt is megjeleníti.

![Tesztelési célból a SAP HANA Backup szoftveres RAID-lemezek másolatait az App Server virtuális géphez csatolták](media/sap-hana-backup-file-level/image033.png)

Tesztelési célból a SAP HANA Backup szoftveres RAID-lemezek másolatait csatlakoztatták az App Server virtuális géphez.

![Az App Server virtuális gép le lett állítva a lemezes másolatok csatlakoztatásához](media/sap-hana-backup-file-level/image034.png)

Az App Server virtuális gép le lett állítva a lemezes másolatok csatolásához. A virtuális gép elindítása után a lemezek és a RAID megfelelően lettek felderítve (az UUID-n keresztül csatlakoztatva). Csak a csatlakoztatási pont hiányzik, amely a YaST particionáló használatával lett létrehozva. Ezt követően a SAP HANA biztonságimásolat-fájl példányai láthatóvá váltak az operációs rendszer szintjén.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>SAP HANA biztonságimásolat-fájlok másolása NFS-megosztásba

A SAP HANA rendszer teljesítmény-vagy lemezterület-perspektívából való lehetséges hatásának csökkentése érdekében érdemes lehet a SAP HANA biztonságimásolat-fájlokat egy NFS-megosztáson tárolni. Technikailag működik, de ez azt jelenti, hogy egy második Azure-beli virtuális gépet használ az NFS-megosztás gazdagépként. A virtuálisgép-hálózat sávszélessége miatt nem lehet kis méretű virtuális gép mérete. Érdemes lehet ezt a &quot;biztonsági mentési virtuálisgép-&quot; leállítani, és csak a SAP HANA biztonsági mentés végrehajtásához. Az NFS-megosztások írása betölti a hálózatot, és hatással van a SAP HANA rendszerre, de csupán a biztonságimásolat-fájlok kezelése a &quot;biztonsági mentési virtuális gépen&quot; nem befolyásolja a SAP HANA rendszerét.

![Egy másik Azure-beli virtuális gépről származó NFS-megosztás a SAP HANA-kiszolgáló virtuális géphez lett csatlakoztatva](media/sap-hana-backup-file-level/image035.png)

Az NFS-használati eset ellenőrzéséhez egy másik Azure-beli virtuális gépről származó NFS-megosztást csatlakoztattak a SAP HANA Server virtuális géphez. Nem lett alkalmazva speciális NFS-hangolás.

![A biztonsági mentés végrehajtása 1 óra és 46 perc volt.](media/sap-hana-backup-file-level/image036.png)

Az NFS-megosztás egy gyors szalagos készlet volt, például a SAP HANA-kiszolgálón. A helyi szalagos készletbe való íráskor azonban 1 óra és 46 perc is volt a biztonsági mentés közvetlenül az NFS-megosztáson, 10 perc helyett.

![Az alternatíva nem sokkal gyorsabb – 1 óra és 43 perc](media/sap-hana-backup-file-level/image037.png)

A helyi sávokra történő biztonsági mentés alternatívája, és az operációs rendszer szintjén történő másolás az NFS-megosztásba (egy egyszerű **CP-AVR-** parancs) nem sokkal gyorsabb. 1 óra és 43 perc volt.

Így működik, de a teljesítmény nem volt jó a 230 GB-os biztonsági mentési teszthez. A többszörös terabájtnál még rosszabbnak tűnik.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA biztonságimásolat-fájlok másolása Azure Files

Egy Azure Files-megosztást egy Azure Linux rendszerű virtuális gépen lehet csatlakoztatni. Az [Azure file Storage és a Linux használatáról](../../../storage/files/storage-how-to-use-files-linux.md) szóló cikk részletesen ismerteti, hogyan teheti meg. Ne feledje, hogy egy Azure-fájlmegosztás esetében jelenleg egy 5 TB-os kvóta van korlátozva, és a fájl mérete legfeljebb 1 TB lehet. További információ: [Azure Files skálázhatósági és teljesítménybeli célok](../../../storage/files/storage-files-scale-targets.md).

A tesztek azonban azt mutatták, hogy SAP HANA biztonsági másolat&#39;jelenleg nem működik közvetlenül ilyen típusú CIFS-csatlakoztatással. Emellett a 1820529-es [SAP-megjegyzésben](https://launchpad.support.sap.com/#/notes/1820529) is szerepel, hogy a CIFS nem ajánlott.

![Ez az ábra hibát jelez a SAP HANA Studióban található biztonsági mentési párbeszédablakban](media/sap-hana-backup-file-level/image038.png)

Ez az ábra hibát jelez a SAP HANA Studióban található Backup (biztonsági mentés) párbeszédpanelen, amikor közvetlenül egy CIFS-hez csatlakoztatott Azure-fájlmegosztás biztonsági mentését kísérli meg. Ezért először egy standard SAP HANA biztonsági mentést kell készítenie egy virtuálisgép-fájlrendszerre, majd a biztonsági másolat fájljait onnan másolja át az Azure file Service-be.

![Ez az ábra azt mutatja, hogy körülbelül 929 másodpercet vett át a 19 SAP HANA biztonsági mentési fájl másolásához](media/sap-hana-backup-file-level/image039.png)

Ez az ábra azt mutatja, hogy körülbelül 929 másodpercet vett igénybe a 19 SAP HANA biztonsági mentési fájlok teljes méretével, nagyjából 230 GB-ot az Azure-fájlmegosztást.

![A SAP HANA virtuális gépen lévő forrás-könyvtár szerkezete át lett másolva az Azure-fájlmegosztásba](media/sap-hana-backup-file-level/image040.png)

Ebben a képernyőfelvételben láthatja, hogy a SAP HANA virtuális gépen lévő forrás-könyvtár szerkezete át lett másolva az Azure-fájlmegosztásba: egy könyvtár (HANA\_Backup\_FSL\_15gb) és 19 egyéni biztonságimásolat-fájl.

SAP HANA biztonsági mentési fájlok tárolása az Azure Files szolgáltatásban egy érdekes lehetőség lehet a jövőben, ha SAP HANA fájl biztonsági mentései közvetlenül támogatják azt. Vagy ha lehetséges, hogy az Azure-fájlokat az NFS-en keresztül csatlakoztatja, és a kvóta maximális korlátja jelentősen meghaladja az 5 TB-ot.

## <a name="next-steps"></a>Következő lépések
* [Az Azure Virtual Machines SAP HANA biztonsági mentési útmutatója](sap-hana-backup-guide.md) áttekintést nyújt az első lépésekről.
* A [tárolási Pillanatképek alapján SAP HANA biztonsági mentés](sap-hana-backup-storage-snapshots.md) a Storage pillanatkép-alapú biztonsági mentési lehetőséget ismerteti.
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
