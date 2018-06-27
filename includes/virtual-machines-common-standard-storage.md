---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: 4e62342a32456787863da775ea98df178ab1d559
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806298"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Költséghatékony, Standard szintű tárolást és a felügyelt és nem felügyelt Azure virtuális gépek lemezei

Azure standard szintű tárolást nyújt a virtuális gépek megbízható, alacsony költségű lemeztámogatás késésre nem érzékeny munkaterheket futtatnak. Blobok, táblák, üzenetsorok és fájlokat is támogatja. Standard szintű tárolóval az adatok tárolási a merevlemezes (HDD) meghajtók. Virtuális gépek használatakor is szabványos SSD és HDD lemez használata a fejlesztési és tesztelési célú, illetve kevesebb mint a kritikus fontosságú munkaterhelésekhez, és a premium SSD lemezek az üzletmenet szempontjából kritikus fontosságú éles környezetben. Standard szintű tárolót minden Azure-régió nem áll rendelkezésre. 

Ez a cikk foglalkozik, a standard SSD és HDD lemezek használatát. Blobok, táblák, üzenetsorok és fájlokat a tárhely használatával kapcsolatos további információkért lásd: [Storage bemutatása](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Meghajtótípusok

Azure virtuális gépek a standard lemezek létrehozásához két módja van:

**Nem felügyelt lemezek**: Ez a lemez típus az eredeti metódus a VHD-fájlokat, hogy a Virtuálisgép-lemezek tárolására használt tárfiókok kezelhetik. VHD-fájlok, blobok a tárfiókokban tárolódnak. Nem felügyelt lemezek bármely Azure virtuális gép méretét, beleértve a virtuális gépeket, amely elsősorban az prémium szintű Storage, például a DSv2 és GS adatsorozat csatolható. Azure virtuális gépek támogatja több standard lemezek, így akár 256 TB-nyi tárhelyre virtuális gépenként.

[**Azure-lemezeket felügyelt**](../articles/virtual-machines/windows/managed-disks-overview.md): Ez a szolgáltatás kezeli a storage-fiókok, a virtuális gép lemezeivel használható. A (prémium SSD, szabványos SSD vagy Standard HDD) típusát és méretét adja meg lemez van szüksége, és Azure hoz létre, és az Ön kezeli a lemezt. Nem kell aggódnia helyezi el a lemezek több tárfiókok között, ugyanakkor a tárfiókok méretezhetőségének korlátai belül – Azure kezeli, amely az Ön biztosítása érdekében.

Annak ellenére, hogy mindkét típusú lemezek elérhetők, sok szolgáltatásaik előnyeit felügyelt lemezek használatát javasoljuk.

Ismerkedés az Azure standard szintű Storage, látogasson el a [elkezdheti használni az ingyenes](https://azure.microsoft.com/pricing/free-trial/). 

Felügyelt lemezzel rendelkező virtuális gép létrehozása módjáról további információkért lásd: a következő cikkekben.

* [Virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Linux-alapú virtuális gép létrehozása az Azure CLI 2.0-s verziójával](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Standard tárolási szolgáltatásokkal 

Vessen egy pillantást, néhány funkciója standard szintű. További részletekért lásd: [Azure Storage bemutatása](../articles/storage/common/storage-introduction.md).

**Standard szintű tárolást**: Azure standard szintű tárolást támogatja az Azure-lemezeket, Azure BLOB, Azure-fájlok, Azure-táblák és Azure várólisták. Standard szintű Storage szolgáltatást használ, indítsa el a [hozzon létre egy Azure Storage-fiók](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).

**Standard SSD-lemezek:** szabványos SSD lemezek további megbízható teljesítményt biztosít, mint szabványos HDD lemezeket, és jelenleg érhetők el az előzetes verzió. Standard SSD lemezek régiónkénti elérhetőség kapcsolatos további információkért lásd: [régiónkénti elérhetőség szabványos SSD-lemezek (előzetes verzió)](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**Standard HDD lemezek:** szabványos HDD lemezek csatolható beleértve megadva prémium szintű Storage, például a DSv2 és GS adatsorozat mérete sorozatú virtuális gépek Azure virtuális gépeken. Egy szabványos HDD lemez csak egy virtuális géphez csatlakoztatható. Azonban csatolhat egy vagy több lemezt a következő virtuális gép, akár a maximális száma az adott VM-méret meghatározás. A következő szakaszban a szabványos tárolás méretezhetőségének és teljesítménycéloknak azt ismertetik részletesen specifikációk.

**Standard oldalakra vonatkozó blob**: szabványos lapblobokat állandó lemezek virtuális gépek tárolására szolgálnak, és közvetlenül az Azure BLOB más típusú például REST is elérhető. [Lapblobok](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) véletlenszerű olvasási és írási műveletek optimalizálva 512 bájtos lapok gyűjteménye. 

**Tárolóreplikálást:** a legtöbb régióban, egy szabványos tárfiókban lévő adatokat lehet helyileg replikált vagy georeplikált több adatközpont között. A négy elérhető replikációs helyileg redundáns tárolás (LRS), a Zónaredundáns tárolás (ZRS), a Georedundáns tárolás (GRS) és az írásvédett Georedundáns tárolás (RA-GRS). Standard szintű tárolást kezelt lemezeken jelenleg támogatja a helyileg redundáns tárolás (LRS) csak. További információkért lásd: [Tárolóreplikálást](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménycélok

Ebben a szakaszban a méretezhetőségi és Teljesítménycélok figyelembe kell vennie, Standard szintű storage használata esetén azt ismertetik.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Vonatkoznak – nem vonatkozik a felügyelt lemezek

| **Erőforrás** | **Alapértelmezett korlát** |
|--------------|-------------------|
| A tárfiók TB  | 500 TB |
| Maximális érkező<sup>1</sup> tárolási fiókonként (US régió) | 10 GB/s engedélyezésekor Georedundáns/ZRS az LRS 20 GB/s |
| Maximális kilépő<sup>1</sup> tárolási fiókonként (US régió) | 20 GB/s engedélyezésekor RA-GRS vagy GRS/ZRS az LRS 30 GB/s |
| Maximális érkező<sup>1</sup> / storage-fiók (ázsiai régiók és európai) | 5 GB/s engedélyezésekor Georedundáns/ZRS az LRS 10 GB/s |
| Maximális kilépő<sup>1</sup> / storage-fiók (ázsiai régiók és európai) | 10 GB/s engedélyezésekor RA-GRS vagy GRS/ZRS az LRS 15 GB/s |
| Teljes kérelmek gyakorisága (feltéve, hogy 1 KB objektum mérete) storage-fiók / | Legfeljebb 20 000 IOPS, entitások másodpercenként vagy üzenetek / másodperc |

<sup>1</sup> érkező összes (kérelmek) küldött adatok mennyisége tárfiókba hivatkozik. Kimenő forgalom tárfiók fogadott összes adat (válasz) hivatkozik.

További információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](../articles/storage/common/storage-scalability-targets.md).

Ha az alkalmazás igényeinek túllépi a méretezhetőségi célok egyetlen tárfiók, hozzon létre az alkalmazás számára több tárfiókot használja, és az adatok particionálása adott tárfiókok között. Alternatív megoldásként Azure felügyelt lemezek is, és Azure kezeli a particionálási és az adatok az Ön elhelyezését.

### <a name="standard-disks-limits"></a>Standard lemezek korlátok

Ellentétben a Premium lemezek a / másodperc (IOPS) és átviteli sebessége (sávszélesség), a Standard lemezek bemeneti/kimeneti műveletek nem törlődnek. A Virtuálisgép-méretet, a lemez csatolva van, hogy a lemez mérete nem függ a standard lemezek teljesítményét. Az alábbi táblázatban felsorolt teljesítményszint legfeljebb eléréséhez számíthat.

**Standard lemezek korlátok (felügyelt és nem felügyelt)**

| **Virtuálisgép-réteg**            | **Az alapszintű csomag méretű VM** | **Standard szint méretű VM** |
|------------------------|-------------------|----------------------|
| A lemez maximális mérete          | 4095 GB           | 4095 GB              |
| Maximális 8 KB-os lemezenként iops teljesítményt nyújtanak | 300         | Legfeljebb 500            |
| Maximális sávszélesség lemezenként | Legfeljebb 60 MB/s     | Legfeljebb 60 MB/s        |

Ha a számítási feladatok nagy teljesítményű, alacsony késésű lemez támogatását igényli, érdemes prémium szintű Storage használ. Prémium szintű Storage további előnyei ismeri, látogasson el a [prémium szintű Storage nagy teljesítményű és az Azure virtuális gépek lemezei](../articles/virtual-machines/windows/premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>A pillanatképek és a blob másolása

A VHD-fájlt a társzolgáltatás nem oldalakra vonatkozó blob. Pillanatképek készítése a lapblobokat, és másolja őket egy másik helyre, például egy másik tárolási fiókot.

### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

Létrehozhat [növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md) ugyanúgy nem felügyelt standard lemezekhez standard szintű tárolóval használhatja pillanatképeket. Azt javasoljuk, hogy pillanatképeket, és majd másolja ezeket a pillanatképeket egy standard georedundáns tárfiókot, ha a forrás lemez helyileg redundáns tárfiókokban. További információkért lásd: [Azure tárolási redundancia lehetőségek](../articles/storage/common/storage-redundancy.md).

Ha egy lemezt a virtuális Géphez van csatolva, bizonyos API műveletek nem engedélyezettek a lemezeken. Például nem hajtható végre egy [másolási Blob](/rest/api/storageservices/Copy-Blob) , hogy a blob, amíg a lemez csatolva van egy virtuális Gépen a műveletet. Ehelyett először létre kell hoznia egy pillanatképet, hogy a blob használatával a [pillanatkép Blob](/rest/api/storageservices/Snapshot-Blob) REST API-metódusra, és utána végezze el a [másolási Blob](/rest/api/storageservices/Copy-Blob) másolni a csatlakoztatott lemez a pillanatkép. Alternatív megoldásként válassza le a lemezt, és végezze el a szükséges műveleteket.

A pillanatképek georedundáns másolatait átmásolhatja pillanatképek helyileg redundáns tárfiók egy standard georedundáns tárfiókot az AzCopy vagy a Blob másolása. További információkért lásd: [adatátvitel az AzCopy parancssori segédprogram a](../articles/storage/common/storage-use-azcopy.md) és [másolási Blob](/rest/api/storageservices/Copy-Blob).

Standard tárfiókokban elleni lapblobokat REST műveleteinek végrehajtásával részletes információkért lásd: [Azure Storage szolgáltatások REST API felülete](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezes pillanatképet egy standard szintű felügyelt lemezes tárolja a kezelt lemezen csak olvasható másolatát. Növekményes pillanatképek felügyelt lemezek jelenleg nem támogatottak, de a jövőben támogatott.

Ha egy felügyelt lemezt egy virtuális Géphez van csatolva, bizonyos API műveletek nem engedélyezettek a lemezeken. Például egy közös hozzáférésű jogosultságkód (SAS), a másolási műveletek végrehajtásához, amíg a lemez csatolva van egy virtuális gép nem hozható létre. Ehelyett először hozzon létre egy pillanatképet a lemezen, és végezze el a pillanatkép példányát. Alternatív megoldásként válassza le a lemezt, és létrehozza a közös hozzáférésű jogosultságkód (SAS), a másolási művelet végrehajtásához.

## <a name="pricing-and-billing"></a>Árak és számlázás

Standard szintű tárolást használ, amikor az alábbi számlázási szempontok érvényesek:

* Standard szintű tárolót nem felügyelt lemezek/adatok mérete 
* Standard szintű Managed Disks
* Standard szintű storage, pillanatképek
* Kimenő adatforgalom
* Tranzakciók

**Nem felügyelt tárolási lemez és az adatok mérete:** nem felügyelt lemezek és egyéb adatokat (BLOB, táblák, üzenetsorok és fájlok), van szó, csak az területet használ. Például ha egy virtuális amelynek oldalakra vonatkozó blob 127 GB, de a virtuális gép ki van építve valójában a 10 GB lemezterületet használ, csak díját is felszámítjuk a 10 GB lemezterület. Standard tárolási támogatjuk legfeljebb 8191 GB, és nem felügyelt Standard legfeljebb 4095 GB lemezterület. 

**Által kezelt lemezeken:** felügyelt lemezek számlázása kiosztott mérete. Ha a lemez ki van építve, egy 10 GB-os lemezt, és csak 5 GB használ, van szó, a 10 GB-os rendelkezés méretét.

**A pillanatképek**: standard lemezek pillanatkép-készítési számlázása a további kapacitást, a pillanatképek használják. A pillanatképek információkért lásd: [egy pillanatképet készíteni egy Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Kimenő adatátvitel**: [kimenő adatátviteli](https://azure.microsoft.com/pricing/details/data-transfers/) (az adatok Azure-adatközpont kilépő) gigabájtalapú sávszélesség-használat.

**Tranzakció**: Azure díja 0.0036 $ 100 000 standard tárolási tranzakciók száma. Tranzakciónak számít az adatok tárolóba írása és tárolóból való olvasása is.

Standard szintű tárolást, a virtuális gépek és a felügyelt lemezek árakkal kapcsolatos részletes információkért lásd:

* [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)
* [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Az Azure Backup szolgáltatás támogatása 

Nem felügyelt lemezzel rendelkező virtuális gépek Azure Backup segítségével készíthető. [További részletekért](../articles/backup/backup-azure-vms-first-look-arm.md).

Hozzon létre egy biztonsági mentési feladat idő-alapú biztonsági mentések, könnyű VM-helyreállítás és biztonsági mentési adatmegőrzési felügyelt lemezzel az Azure Backup szolgáltatás is használja. További, a [használata Azure Backup szolgáltatás felügyelt lemezzel rendelkező virtuális gépek](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure Storage bemutatása](../articles/storage/common/storage-introduction.md)

* [Tárfiók létrehozása](../articles/storage/common/storage-create-storage-account.md)

* [Managed Disks – áttekintés](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Linux-alapú virtuális gép létrehozása az Azure CLI 2.0-s verziójával](../articles/virtual-machines/linux/quick-create-cli.md)
