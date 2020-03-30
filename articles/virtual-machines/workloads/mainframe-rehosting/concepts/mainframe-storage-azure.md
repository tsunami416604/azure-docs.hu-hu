---
title: Nagyszámítógépes tárhely áthelyezése az Azure Storage-ba
description: A nagymértékben méretezhető Azure-tárolási erőforrások segítségével a nagyszámítógépes szervezetek áttelepíthetik és modernizálhatják az IBM z14-alkalmazásokat.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288914"
---
# <a name="move-mainframe-storage-to-azure"></a>Nagyszámítógépes tárhely áthelyezése az Azure-ba

A nagyszámítógépes számítási feladatok futtatásához a Microsoft Azure-ban tudnia kell, hogy a nagyszámítógép képességei hogyan viszonyulnak az Azure-hoz. A nagymértékben méretezhető tárolási erőforrások segítségével a szervezetek úgy kezdhetik el a modernizációt, hogy nem hagyják el a rájuk támaszkodó alkalmazásokat.

Az Azure az IBM z14-alapú rendszerekhez hasonló nagyszámítógép-szerű funkciókat és tárolási kapacitást biztosít (ez az írás jelenlegi modellje). Ez a cikk bemutatja, hogyan kaphat összehasonlítható eredményeket az Azure-ban.

## <a name="mainframe-storage-at-a-glance"></a>Nagyszámítógépes tárolás egy pillantással

Az IBM nagyszámítógépe kétféleképpen jellemzi a tárolást. Az első egy közvetlen hozzáférésű tárolóeszköz (DASD). A második a szekvenciális tárolás. A tárolás kezeléséhez a nagyszámítógép biztosítja az adatlétesítmény-tárolókezelési alrendszert (DFSMS). Kezeli az adatokhoz való hozzáférést a különböző tárolóeszközökhöz.

[A DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) egy külön eszközre utal a másodlagos (nem a memóriában lévő) tárolókszámára, amely lehetővé teszi egy egyedi cím használható az adatok közvetlen eléréséhez. Eredetileg a DASD kifejezés forgó lemezekre, mágneses dobokra vagy adatcellákra vonatkozott. Most azonban a kifejezés alkalmazható az SSD-kre, a tárolóhálózatokra (SANs), a hálózati tárolókra (NAS) és az optikai meghajtókra is. Ennek a dokumentumnak az alkalmazásában a DASD forgó lemezekre, san-okra és SSD-kre vonatkozik.

A DASD-tárolóval ellentétben a nagyszámítógép szekvenciális tárolása olyan eszközökre vonatkozik, mint például a szalagos meghajtók, ahol az adatok egy kiindulási pontról érhetők el, majd egy sorban olvashatók vagy írnak.

A tárolóeszközök általában szálas kapcsolaton (FICON) keresztül vannak csatlakoztatva, vagy közvetlenül a nagyszámítógép IO-buszán érhetők el [a HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), egy IBM technológia segítségével, amely a hipervizorral rendelkező kiszolgáló partíciói közötti nagy sebességű kommunikációhoz szolgál.

A legtöbb nagyszámítógépes rendszer két típusra bontja a tárolást:

- *Online tárolás* (más néven forró tárolás) szükséges a napi műveletekhez. A DASD-tárolót általában erre a célra használják. Erre a célra azonban szekvenciális tárolás, például napi szalagos biztonsági mentések (logikai vagy fizikai) is használhatók.

- *Az archív tárolás* (más néven hűtőtárolás) nem garantált, hogy egy adott időpontban kell felszerelni. Ehelyett van szerelve, és szükség szerint elérhető. Az archív tárolás tévhett videóbiztonsági mentések (logikai vagy fizikai) használatával történő megvalósítása gyakran történik.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus IO késés és IOPS

Nagyszámítógépek gyakran használják az alkalmazások, amelyek megkövetelik a nagy teljesítményű IO és alacsony I/késés. Ezt az IO-eszközök és a HiperSockets FICON-kapcsolataival tehetik meg. Ha a HiperSockets-t az alkalmazások és eszközök közvetlenül a nagyszámítógép IO-csatornához való csatlakoztatására használják, a mikroszekundumok késése érhető el.

## <a name="azure-storage-at-a-glance"></a>Az Azure-tárhely áttekintése

Az Azure infrastruktúra-szolgáltatásként[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)a tárolási lehetőségek hasonló nagyszámítógépes kapacitást biztosítanak.

A Microsoft az Azure-ban üzemeltetett alkalmazások számára petabájt értékű tárhelyet kínál, és számos tárolási lehetőség közül választhat. Ezek a nagy teljesítményű SSD-tárolóktól az alacsony költségű blob-tárolásig terjednek a tömeges tárolás és az archívumok számára. Emellett az Azure adatredundancia-lehetőséget biztosít a tároláshoz – ami nagyobb erőfeszítést igényel a nagyszámítógépes környezetben való beállításhoz.

Az Azure storage [azure-lemezként,](/azure/virtual-machines/windows/managed-disks-overview) [Azure-fájlokként](/azure/storage/files/storage-files-introduction)és [Azure Blobként](/azure/storage/blobs/storage-blobs-overview) érhető el, ahogy az alábbi táblázat összegzi. További információ [arról, hogy mikor kell használni az egyes](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)eket.

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Típus</th><th>Leírás</th><th>Akkor használja, ha:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
SMB-összeköttetést, ügyféltárakat és <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST-felületet</a> biztosít, amely bárhonnan hozzáférést biztosít a tárolt fájlokhoz.
</td>
<td><ul>
<li>Akkor emelje fel és helyezze át az alkalmazást a felhőbe, amikor az alkalmazás a natív fájlrendszer API-kat használja az adatok megosztására közte és az Azure-ban futó más alkalmazások között.</li>
<li>Tárolja a fejlesztési és hibakeresési eszközöket, amelyeket számos virtuális gépről kell elérni.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blobs
</td>
<td>Ügyfélkódtárakat <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> és REST-felületet biztosít, amely lehetővé teszi a strukturálatlan adatok tömeges tárolását és elérését blokkblobokban. Támogatja az <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> vállalati big data-elemzési megoldásokat is.
</td>
<td><ul>
<li>Támogatja a streamelési és véletlenszerű hozzáférésű forgatókönyvek egy alkalmazásban.</li>
<li>Bárhonnan hozzáférhet az alkalmazásadatokhoz.</li>
<li>Hozzon létre egy nagyvállalati adattavat az Azure-on, és végezzen big data-elemzéseket.</li>
</ul></td>
</tr>
<tr><td>Azure Disks
</td>
<td>Ügyféltárakat <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> és REST-felületet biztosít, amely lehetővé teszi az adatok állandó tárolását és elérését egy csatlakoztatott virtuális merevlemezről.
</td>
<td><ul>
<li>A natív fájlrendszer API-kat használó alkalmazások at emelheti és áthelyezi az állandó lemezekre történő adatok olvasásához és írásához.</li>
<li>Olyan adatokat tárol, amelyek hez nem szükséges, hogy a lemezt csatlakoztató virtuális gépen kívülről lehessen hozzáférni.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Az Azure hot (online) és hideg (archív) tárolása

Egy adott rendszer tárolási típusa a rendszer követelményeitől függ, beleértve a tárolási méretet, az átviteli kapacitást és az IOPS-t. A DASD-típusú tárolás nagygépen, alkalmazások az Azure-ban általában az Azure Disks meghajtó tároló helyett. Nagyszámítógépes archív tárolás esetén a blobstorage az Azure-ban használatos.

Az SSD-k biztosítják a legmagasabb tárolási teljesítményt az Azure-ban. A következő lehetőségek állnak rendelkezésre (a dokumentum írásáttekintőkor):

| Típus         | Méret           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB és 64 TB között  | 1 200 –160 000 IOPS között |
| Prémium SSD  | 32 GB–32 TB | 12 –15 000 IOPS     |
| Standard SSD | 32 GB–32 TB | 12–2000 IOPS      |

A Blob Storage biztosítja a legnagyobb mennyiségű tárhelyet az Azure-ban. A tárhely mérete mellett az Azure felügyelt és nem felügyelt tárhelyet is kínál. A felügyelt tárral az Azure gondoskodik az alapul szolgáló tárfiókok kezeléséről. A nem felügyelt tárolás, a felhasználó felelősséget vállal az Azure storage-fiókok beállítása a megfelelő méretű, hogy megfeleljen a tárolási követelményeknek.

## <a name="next-steps"></a>További lépések

- [Nagyszámítógép migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting az Azure virtuális gépek](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Nagyszámítógépes számítás áthelyezése az Azure-ba](mainframe-compute-Azure.md)
- [Az Azure Blobok, az Azure Files vagy az Azure Disks használatának eldöntése](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standard SSD-felügyelt lemezek az Azure virtuális gép számítási feladatához](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM források

- [Párhuzamos Sysplex az IBM Z-n](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS és a kapcsolórendszer: Az alapokon túl](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [A Db2 pureScale funkció telepítéséhez szükséges felhasználók létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – Példány létrehozása parancs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale fürtözött adatbázismegoldás](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government felhő nagyszámítógépes alkalmazásokhoz](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft és a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>További áttelepítési erőforrások

- [Az Azure virtuális adatközpontok –– fuvar- és műszakútmutató](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
