---
title: SAP HANA Azure biztonsági mentés fájlszinten | Microsoft dokumentumok
description: Az SAP HANA két fő biztonsági mentési lehetősége van az Azure virtuális gépeken, ez a cikk az SAP HANA Azure biztonsági mentést ismerteti fájlszinten
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271382"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure biztonsági mentés fájlszinten

## <a name="introduction"></a>Bevezetés

Ez a cikk egy kapcsolódó cikket [az SAP HANA azure-beli virtuális gépek biztonsági mentési útmutatójával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)kapcsolatban, amely áttekintést és információkat nyújt az első lépésekről és az Azure Backup szolgáltatásés a tárolási pillanatképek további részleteiről. 

Az Azure-ban különböző virtuálisgép-típusok különböző számú virtuális géphez csatlakoztatott lehetővé teszik. A pontos részleteket linuxos méretű virtuális gépek ben dokumentálja [az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) Az ebben a dokumentációban említett tesztekhez egy GS5 Azure virtuális gép, amely lehetővé teszi a 64 csatlakoztatott adatlemezek. A nagyobb SAP HANA-rendszerek esetében előfordulhat, hogy már jelentős számú lemezt kell készíteni az adatokhoz és a naplófájlokhoz, esetleg szoftvercsíkozással kombinálva az optimális lemez I/O-átviteli teljesítmény érdekében. Az SAP HANA-telepítések azure-beli virtuális gépeken javasolt lemezkonfigurációiról további információt az [SAP HANA Azure virtuálisgép-tároló konfigurációi című](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)cikkben talál. A javaslatok a helyi biztonsági mentések lemezterület-javaslatait is tartalmazják.

A fájlszintű biztonsági mentés/visszaállítás kezelésének szabványos módja az SAP HANA Studio-n keresztül vagy az SAP HANA SQL utasításokon keresztül történő fájlalapú biztonsági mentés. További információ: SAP [HANA SQL és System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Ez az ábra az SAP HANA Studio biztonsági másolat menüelemének párbeszédablakát mutatja](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Ez az ábra az SAP HANA Studio biztonsági mentési menüelemének párbeszédablakát mutatja be. A típusfájl &quot;&quot; kiválasztásakor meg kell adni egy elérési utat a fájlrendszerben, ahol az SAP HANA írja a biztonsági másolat fájljait. A visszaállítás ugyanúgy működik.

Bár ez a választás egyszerűnek és egyenesen előre hangzik, van néhány szempont. Az Azure virtuális gép a csatolható adatlemezek száma korlátozott. Előfordulhat, hogy az adatbázis és a lemezátviteli követelmények méretétől függően nincs kapacitás az SAP HANA biztonsági másolat fájlfájljaiban való tárolásra, ami szoftveres csíkozást vonhat maga után több adatlemezen. A biztonsági másolatfájlok áthelyezésének, valamint a fájlméret-korlátozások és a teljesítmény kezelésének különböző lehetőségei a terabájtnyi adatok kezelésekor a cikk későbbi részében találhatók.

Egy másik lehetőség, amely nagyobb szabadságot biztosít a teljes kapacitás tekintetében, az Azure blob storage. Míg egy blob is korlátozódik 1 TB-os, egy blob tároló teljes kapacitása jelenleg 500 TB. Emellett lehetővé teszi az ügyfelek számára, &quot;&quot; hogy válassza ki az úgynevezett hűvös blob storage, amely költségelőnnyel jár. Tekintse meg [az Azure Blob storage: gyakori elérésű, ritka elérésű és archív hozzáférési szintek](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) a ritka elérésű blob-tároló részleteiért.

A nagyobb biztonság érdekében használjon egy georeplikált tárfiókot az SAP HANA biztonsági mentések tárolására. A tárolóredundanciával és a tárreplikációval kapcsolatos részletekért tekintse meg az [Azure Storage redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy) című témakörét.

Lehet elhelyezni dedikált Virtuális gépek az SAP HANA biztonsági mentések egy dedikált biztonsági mentési tárfiók, amely georeplikált. Vagy lehet másolni a Virtuálisnd-k, amelyek az SAP HANA biztonsági mentések egy georeplikált tárfiók, vagy egy másik régióban lévő tárfiókba.

## <a name="azure-blobxfer-utility-details"></a>Az Azure blobxfer segédprogram részletei

Könyvtárak és fájlok tárolására az Azure storage-ban, használhatja a CLI vagy a PowerShell, vagy az [Azure SDK-k](https://azure.microsoft.com/downloads/)egyikhasználatával egy eszköz. Van is egy használatra kész segédprogram, AzCopy, adatok másolására az Azure storage-ba. [(lásd: Adatátvitel átvitele az AzCopy parancssori segédprogrammal).](../../../storage/common/storage-use-azcopy.md)

Ezért blobxfer sap HANA biztonsági másolat fájlok másolásához használták. Nyílt forráskódú, számos ügyfél használja éles környezetben, és elérhető a [GitHubon.](https://github.com/Azure/blobxfer) Ez az eszköz lehetővé teszi, hogy az adatok másolása közvetlenül az Azure blob storage vagy az Azure fájlmegosztás. Ez is felajánl egy sor -ból hasznos jellegét meghatározza, szeret md5 hash, vagy önműködő párhuzamosság mikor másolás egy címtár -val sokszoros fájlokat.

## <a name="sap-hana-backup-performance"></a>Az SAP HANA biztonsági mentésének teljesítménye
Ebben a fejezetben a teljesítményre vonatkozó szempontokat tárgyaljuk. Előfordulhat, hogy az elért számok nem jelentik a legutóbbi állapotot, mivel folyamatos fejlesztés van az Azure storage jobb átviteli kapacitásának elérése érdekében. Ennek eredményeképpen egyéni teszteket kell végrehajtania a konfigurációhoz és az Azure-régióhoz.

![Ez a képernyőkép az SAP HANA biztonsági másolat konzoljáról készült az SAP HANA Studióban](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Ez a képernyőkép az SAP HANA Studio SAP HANA biztonsági mentési konzolját mutatja. Körülbelül 42 percet vett igénybe egy 230 GB-os biztonsági mentés egyetlen Azure Standard HDD-tárolólemezen, amely a HANA virtuális géphez csatlakozik az xfs-fájlrendszer használatával az egyik lemezen.

![Ez a képernyőkép az SAP HANA teszt virtuális gépyi](media/sap-hana-backup-file-level/one-backup-disk.png)

Ez a képernyőkép a YaST az SAP HANA teszt virtuális gép. Láthatja az 1-TB egyetlen lemez az SAP HANA biztonsági mentés. Ez elvitt körülbelül 42 jegyzőkönyv -hoz hát 230 GB. Emellett öt 200 GB-os lemezt csatoltak, és szoftveres RAID md0-t hoztak létre, és az öt Azure-adatlemez tetején csíkozást is.

![Ugyanaza biztonsági mentés ismétlése a szoftveres RAID-en öt csatlakoztatott Azure-szabványos tárolóadat-lemez csíkozással](media/sap-hana-backup-file-level/five-backup-disks.png)

Ha ugyanazt a biztonsági mentést ismételgeti a szoftveres RAID-en, és öt csatlakoztatott Azure-szintű tárolóadat-lemezen csíkozással a biztonsági mentési időt 42 percről 10 percre csökkentve. A lemezek a virtuális géphez való gyorsítótárazás nélkül voltak csatlakoztatva. Ez a gyakorlat bemutatja a lemez írási átviteli értékének fontosságát a jó biztonsági mentési idő eléréséhez. Az optimális teljesítmény érdekében átválthat az Azure Standard SSD-tárhelyre vagy az Azure Premium Storage-ra, hogy tovább gyorsítsa a folyamatot. Általában az Azure szabványos HDD-tároló nem ajánlott, és csak szemléltetési célokra használták. Javasoljuk, hogy az Azure Standard SSD-tárterület vagy az Azure Premium Storage éles rendszerekhez legalább egy minimális használatát használja.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA biztonsági másolatfájlok másolása az Azure blob storage-ba
Előfordulhat, hogy a teljesítményszámok, a biztonsági mentés időtartamának száma és az időtartamszámok másolása nem az Azure-technológia legújabb állapotát tükrözik. A Microsoft folyamatosan javítja az Azure-tárhelyet, hogy nagyobb átviteli és kisebb késéseket biztosítson. Ezért a számok csak demonstrációs célokat szolgálnak. Az Ön által választott Azure-régióban az egyéni igényeknek való megfelelést kell tesztelnie ahhoz, hogy a módszerrel a legjobb az Ön számára.

Egy másik lehetőség az SAP HANA biztonsági másolat fájlok gyors tárolására az Azure blob storage. Egyetlen blob tároló egy korlát körülbelül 500 TB, elegendő SAP HANA-rendszerek m32ts, M32ls, M64ls és GS5 virtuális gép típusú Azure használatával elegendő SAP HANA biztonsági mentések. Az ügyfelek választhatnak&quot; &quot;a&quot; gyakori és ritka elérésű blobtárolók között &quot;(lásd: [Azure Blob storage: gyakori elérésű, ritka elérésű és archív hozzáférési szintek).](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

A blobxfer eszközzel könnyen másolhatja az SAP HANA biztonsági másolat fájljait közvetlenül az Azure blob storage-ba.

![Itt látható a fájlokat a teljes SAP HANA fájl biztonsági mentés](media/sap-hana-backup-file-level/list-of-backups.png)

Megtekintheti a fájlokat a teljes SAP HANA fájl biztonsági mentés. A négy fájl közül a legnagyobb nagyjából 230 GB méretű.

![Nagyjából 3000 másodpercbe telt a 230 GB-os másolás egy Azure standard szintű tárfiók blobtárolóba](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Nem md5-kivonat ot a kezdeti teszt, nagyjából 3000 másodpercalatt másolja a 230 GB-os egy Azure standard szintű tárfiók blob tárolóba.

A HANA Studio biztonsági másolat konzol lehetővé teszi, hogy korlátozza a HANA biztonsági másolat fájlok maximális fájlméretét. A mintakörnyezetben egy nagy 230 GB-os fájl helyett több kisebb biztonsági másolat talalható.

A biztonsági másolat fájlméret-korlátja a HANA oldalon való beállítása nem&#39;a biztonsági mentési idő, mivel a fájlok egymás után vannak írva. A fájlméret-korlát 60 GB-ra volt állítva, így a biztonsági másolat négy nagy adatfájlt hozott létre a 230 GB-os egyetlen fájl helyett. Több biztonsági másolat fájlok használatával szükségessé válhat a HANA-adatbázisok biztonsági mentése, ha a biztonsági mentési célok korlátozásokat a fájl mérete blob méretű.

![A blobxfer eszköz párhuzamosságának teszteléséhez a HANA biztonsági mentések maximális fájlméretét 15 GB-ra állították](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

A blobxfer eszköz párhuzamosságának teszteléséhez a HANA biztonsági mentések maximális fájlmérete 15 GB-ra volt állítva, ami 19 biztonsági mentési fájlt eredményezett. Ez a konfiguráció hozta az idő blobxfer másolni a 230 GB-os Azure blob storage 3000 másodperc le 875 másodperc.

A helyi lemezeken végrehajtott biztonsági mentések más helyekre, például az Azure blob storage-ba történő másolása során ne feledje, hogy egy esetleges párhuzamos másolási folyamat által használt sávszélesség az egyes virtuálisgép-típusok hálózatának vagy tárolási kvótájának számlázása. Ennek eredményeképpen egyensúlyba kell hoznia a másolat időtartamát a hálózattal és a tárolási sávszélességgel, amelyet a virtuális gépben futó normál munkaterhelés igényel. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>SAP HANA biztonsági másolatfájlok másolása NFS-megosztásra

A Microsoft Azure natív NFS-megosztásokat kínál [az Azure NetApp-fájlokon](https://azure.microsoft.com/services/netapp/)keresztül. A biztonsági mentések tárolására és kezelésére kapacitásként több tucat TB-t hozhat létre. Ezeket a köteteket a NetApp technológiája alapján is pillanatkészítheted. Az Azure NetApp Files (ANF) három különböző szolgáltatási szinten érhető el, amelyek különböző tárolási átviteli kapacitást biztosítanak. További részletekért olvassa el a cikk [Ben Az Azure NetApp-fájlok szolgáltatási szintjei című cikket.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) NFS-kötetet hozhat létre és csatlakoztathat az ANF-ből a [rövid útmutató: Az Azure NetApp-fájlok beállítása és nfs-kötet létrehozása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal)című cikkben leírtak szerint.

Az ANF-en keresztül az Azure natív NFS-köteteinek használata mellett számos lehetőség kínál különböző lehetőségeket olyan saját telepítések létrehozására, amelyek NFS-megosztásokat biztosítanak az Azure-ban. Mindegyiknek megvan a hátránya, hogy ezeket a megoldásokat saját kezűleg kell üzembe helyeznie és kezelnie. E lehetőségek közül néhányat a következő cikkek dokumentálnak:

- [Magas rendelkezésre állás az NFS számára az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server rendszeren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

A fent leírt eszközökkel létrehozott NFS-megosztások segítségével közvetlenül futtathatók hana biztonsági mentések ellen, vagy a helyi lemezeken végrehajtott biztonsági mentések másolása az okra az NFS-megosztásokra.

> [!NOTE]
> Az SAP HANA támogatja az NFS v3-at és az NFS v4.x-et. Bármely más formátum, például a CIFS fájlrendszerű SMB nem támogatott hana biztonsági mentések írásához. Lásd még: [SAP támogatási megjegyzés #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA biztonsági másolatfájlok másolása az Azure Files-ba

Lehetőség van egy Azure Files-megosztás csatlakoztatására egy Azure Linux-alapú virtuális gépen belül. Az [Azure File storage linuxos használata](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) című cikk a konfiguráció végrehajtásának részleteit tartalmazza. Az Azure Files vagy az Azure Premium-fájlok korlátozásáról olvassa el az [Azure Files méretezhetőségi és teljesítménycélok című cikkét.](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)

> [!NOTE]
> A CIFS fájlrendszerű SMB-t az SAP HANA nem támogatja hana biztonsági mentések írásához. Lásd még: [AZ SAP támogatási megjegyzése #1820529](https://launchpad.support.sap.com/#/notes/1820529). Ennek eredményeképpen ezt a megoldást csak a hana-adatbázis biztonsági mentésének végső céljaként használhatja, amely közvetlenül a helyi csatolt lemezeken került sor.
> 

Az Azure Files, nem az Azure Premium Files tesztsorán körülbelül 929 másodpercbe telt 19 biztonsági másolat 230 GB-os teljes kötettel történő másolása. Arra számítunk, hogy az Azure Premium Files használata sokkal jobb lesz. Azonban meg kell szem előtt tartani, hogy meg kell egyensúlyt az érdekeit egy gyors másolatot a terhelést a hálózati sávszélességre vonatkozó követelményekkel. Mivel minden Azure virtuális gép típus kényszeríti a hálózati sávszélesség-kvóta, meg kell maradnia a tartományon belül, hogy a kvóta a számítási feladatok, valamint a biztonsági mentési fájlok másolatát.

![Ez az ábra azt mutatja, hogy körülbelül 929 másodpercig tartott 19 SAP HANA biztonsági mentési fájl másolása](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Sap HANA biztonsági mentési fájlok tárolása az Azure-fájlokban lehet egy érdekes lehetőség. Különösen az Azure Premium Files jobb késésével és átviteli képességével.

## <a name="next-steps"></a>További lépések
* [Az Sap HANA azure-beli virtuális gépeken című biztonsági mentési útmutatója](sap-hana-backup-guide.md) áttekintést és információkat nyújt az első lépésekről.
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure-ban (nagy példányok), tekintse meg az [SAP HANA (nagy példányok) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md)
