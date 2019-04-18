---
title: A nagyszámítógépes tárolóját áthelyezheti az Azure Storage
description: Rugalmasan méretezhető Azure storage-erőforrások megkönnyíti a nagygépes-alapú szervezetek számára telepítse át, és IBM z14 alkalmazásokat.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4fea787e7fe20d60de91761811e1b69bebf3a010
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896349"
---
# <a name="move-mainframe-storage-to-azure"></a>A nagyszámítógépes tár áthelyezése az Azure-bA

A nagyszámítógépes számítási feladatok futtatásához a Microsoft Azure-ban, a nagyszámítógépes funkciók hogyan hasonlítsa össze az Azure-bA ismernie kell. A nagy mértékben méretezhető tárolási erőforrások megkönnyíti a szervezetek számára elhagyása nélkül modernizálhat támaszkodnak az alkalmazások megkezdéséhez.

Az Azure biztosít a nagygépes-rendszergazdai műveletekhez hasonló funkciók és a tárolási kapacitás, amely hasonló, mint az IBM z14-alapú rendszerekhez (a jelen cikk írásakor az aktuális modell). Ez a cikk bemutatja, hogyan lehet hasonló eredményt ad az Azure-ban.

## <a name="mainframe-storage-at-a-glance"></a>Egyetlen pillantással nagyszámítógépes storage

Az IBM Nagyszámítógépek kétféleképpen tárolási jellemzi. Az első az közvetlen hozzáférést tárolóeszköz (DASD). A második egymást követő tárolási. Tárolás kezelése, a nagyszámítógépes biztosítja az adatok létesítmény Storage Management alrendszer (DFSMS). Kezeli a különböző tárolóeszközök adatokhoz való hozzáférést.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) másodlagos (nem memóriában) tároló, amely lehetővé teszi az adatok közvetlen elérésére szolgáló egyedi címmel egy külön eszközt jelenti. Eredetileg az előfizetési időszak DASD lemezek, mágneses dob vagy adatcelláiban működtetésével alkalmazza. Azonban most kifejezés is alkalmazhat az SSD-tárolóinak eszközök (SSD-kkel), a tárolóhálózatok (SAN), hálózati tárolóeszközök (NAS), és optikai meghajtók csatolva. Ez a dokumentum az alkalmazásában DASD lemezek, a San-OK és SSD-k működtetésével hivatkozik.

Szakembereket DASD tárolására a nagyszámítógépes a szekvenciális tárolási eszközök, például a szalagos meghajtók, ahol a adatok érhető el, amely kiindulási pontot, majd olvassa el, vagy sor írása hivatkozik.

Tárolóeszközök általában csatolt fiber kapcsolattal (FICON), vagy közvetlenül a a nagyszámítógépes i/o-busz használatával elért [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), az IBM technológiát kínál a nagy sebességű kommunikáció partíciók kiszolgáló között egy hipervizor.

A legtöbb nagyszámítógépes rendszer tárolási szét két típusa:

- *Online tárhelyen* (mint a gyakran használt adatok tárolási is ismert) van szükség a napi műveletekhez. DASD storage általában erre a célra szolgál. Egymást követő tárolókba, például napi szalagos biztonsági mentések (logikai vagy fizikai) is használható erre a célra.

- *Archív tárolási* (más néven a ritka elérésű tárolási) csatlakoztatnia kell egy adott időpontban nem garantált. Ehelyett csatlakoztatva van, és igény szerint elérhető. Az Archive storage gyakran Storage szekvenciális szalagos biztonsági mentések (logikai vagy fizikai) segítségével van megvalósítva.

## <a name="mainframe-versus-io-latency-and-iops"></a>A nagyszámítógépes és IO-késés és IOPS

Nagyszámítógépek gyakran használják az alkalmazásokat, amelyek nagy teljesítményű i/o- és alacsony késésű IO-műveletekre van szükség. Ehhez az i/o-eszközök és HiperSockets FICON-kapcsolatokat használja. Amikor HiperSockets alkalmazásokból és eszközökről közvetlenül csatlakozhat a nagyszámítógépes i/o-csatornát használ, a mikroszekundumban késés érhető el.

## <a name="azure-storage-at-a-glance"></a>Az Azure storage gyors áttekintése

Az Azure infrastruktúra--szolgáltatásként ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) tárolási lehetőségei összehasonlítható nagyszámítógép-kapacitást biztosítanak.

A Microsoft több petabájtnyi visszamenőleg az Azure-ban üzemeltetett alkalmazásaikon tárhelyet kínál, és számos tárolási lehetőség van. Ezek a nagy teljesítményű SSD-tárolóval és eső alacsony költségű blob storage-háttértár és archívumok. Emellett az Azure biztosít a tárolás adatredundáns tárolási mód – további erőfeszítésekre nagyszámítógépes környezetben beállítása eljáró.

Az Azure storage szolgáltatás érhető el, [Azure Disks](/azure/virtual-machines/windows/managed-disks-overview), [Azure Files](/azure/storage/files/storage-files-introduction), és [Azure-Blobok](/azure/storage/blobs/storage-blobs-overview) , az alábbi táblázat foglalja össze. Tudjon meg többet [használata minden egyes](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typo</th><th>Leírás</th><th>A következő esetekben használja:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Biztosít egy SMB-kapcsolatot, ügyfél szalagtárakban és a egy <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> felület, amely lehetővé teszi a hozzáférést bárhonnan tárolt fájlokhoz.
</td>
<td><ul>
<li>Lift- and -shift-alkalmazását a felhőbe, ha az alkalmazás a natív fájlrendszer API-k segítségével, és más Azure-ban futó alkalmazások közötti adatmegosztást.</li>
<li>Store fejlesztés és hibakeresés eszközöket, amelyek a több virtuális gépet kell hozzáférniük.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blobs
</td>
<td>Ügyfélkódtárak biztosít és a egy <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> felület, amely lehetővé teszi a strukturálatlan adatok tárolása és elérése a blokkblobok nagy méretű. Emellett támogatja <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> vállalati big Data típusú adatok elemzési megoldásokat.
</td>
<td><ul>
<li>Streamelési és a véletlenszerű hozzáférés forgatókönyveket támogatja az alkalmazások.</li>
<li>Az alkalmazás adatokat bárhonnan elérheti.</li>
<li>Az enterprise data lake létrehozása az Azure-ban, és végezze el a big data-elemzés.</li>
</ul></td>
</tr>
<tr><td>Azure Disks
</td>
<td>Ügyfélkódtárak biztosít és a egy <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> felület, amely lehetővé teszi az adatok állandó tárolása és elérése a csatlakoztatott virtuális merevlemezről.
</td>
<td><ul>
<li>Alkalmazások átemelése, amely olvasási és írási adatok állandó lemezt natív fájlrendszer API-k használatával.</li>
<li>Nem szükséges a virtuális gép, amelyhez a lemez csatlakozik kívülről adatok Store.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure (online) gyakori és ritka elérésű (archív) tárolási

A tárolás egy adott rendszerhez típusát attól függ, hogy a rendszer, beleértve a tároló mérete, átviteli sebesség és iops-érték követelményeinek. DASD típusú tároláshoz a nagyszámítógépes az Azure-beli alkalmazások általában inkább az Azure Disks meghajtó storage. A nagyszámítógépes az archive storage blob storage-bA az Azure-ban használnak.

SSD-k biztosítanak a legmagasabb szintű tárolási teljesítmény az Azure-ban. A következő lehetőségek állnak rendelkezésre (írásakor a jelen dokumentum):

| Typo         | Méret           | IO                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB-os 64 TB-ig  | 1200 való 160,000 IOPS |
| Prémium SSD  | 32 GB-os 32 TB-ig | 12-15 000 iops-érték     |
| Standard SSD | 32 GB-os 32 TB-ig | 12, 2 000 iops-érték      |

A BLOB storage tárolási legnagyobb mennyisége biztosít az Azure-ban. Azure storage mérete, felügyelt és a nem felügyelt tárolási lehetőséget biztosít. A felügyelt tárfiókkal az Azure gondoskodik az alapul szolgáló storage-fiókok kezelése. Nem felügyelt tárolóval a felhasználó felelőssége a tárolási követelmények teljesítéséhez a megfelelő méretű Azure storage-fiókok beállításával kapcsolatos vesz igénybe.

## <a name="next-steps"></a>További lépések

- [Nagyszámítógépek migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [A nagyszámítógépes újratárolása az Azure Virtual machines szolgáltatásban](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [A nagyszámítógépes számítási áthelyezése az Azure-bA](mainframe-compute-Azure.md)
- [Annak eldöntése, mikor érdemes használni az Azure-Blobok, az Azure Files és az Azure-lemezek](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standard SSD Managed Disks-Azure virtuális gépek számítási feladataihoz](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM-erőforrások

- [Az IBM Z párhuzamos Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS és a kapcsoló konstrukció: Alapvető túl](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Egy Db2-pureScale szolgáltatás telepítése a szükséges felhasználók létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - példány parancs létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2-pureScale fürtözött adatbázis-megoldás](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nagyszámítógépes alkalmazások a Microsoft Azure Government-felhőben](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft és a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>További források a migráláshoz

- [Platform Modernization Alliance: IBM DB2-höz, az Azure-ban](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Az Azure virtuális adatközpont Lift és Shift útmutató](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
