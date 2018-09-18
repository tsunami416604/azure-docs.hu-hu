---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88a9348ea7d6282b7410d5a323fd482dc82416c6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979578"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Készítsen biztonsági másolatot az Azure nem felügyelt Virtuálisgép-lemezek növekményes pillanatképekkel
## <a name="overview"></a>Áttekintés
Az Azure Storage lehetővé teszi, hogy a blobok pillanatképek. A pillanatképek a blob állapot rögzítése abban az időpontban. Ebben a cikkben egy olyan forgatókönyvet, amely akkor is fenntartható a pillanatképek használata virtuális gépek lemezeinek biztonsági másolatait ismertetünk. Ezt a módszert is használhat, ha nem szeretné használni az Azure biztonsági mentési és helyreállítási, és létrehoz egy egyéni virtuálisgép-lemezek biztonsági mentési stratégia.

Az Azure virtuális gépek lemezei és az Azure Storage szolgáltatásban tárolódnak. Azt is leíró Ez a cikk a virtuálisgép-lemezek biztonsági mentési stratégiája, mivel a lapblobok keretében pillanatképek nevezzük. A pillanatképek kapcsolatos további információkért tekintse meg [létrehozása egy pillanatképet egy Blobról](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Mi az pillanatképet?
Blob-pillanatkép egy BLOB egy időben rögzített csak olvasható verziója is. Pillanatkép létrehozása után azt is olvassa el, másolja, vagy törölni, de nem módosulnak. A pillanatképek biztonsági mentése egy blobot, ahogyan megjelenik egy időben jelenleg úgy adja meg. REST verziója 2015-04-05, amíg a teljes pillanatképek másolásának képességét rendelkezett. A többi verziója 2015-07-08 és újabb, akkor is másolhatja a növekményes pillanatképek.

## <a name="full-snapshot-copy"></a>Teljes pillanatkép másolása
A pillanatképek alap BLOB biztonsági mentések megtartása blobként átmásolható egy másik tárfiókba. Az alap BLOB, amely úgy működik, mint a blob visszaállítása egy korábbi pillanatkép is másolhatja. Pillanatkép másolódik egyik tárfiókból a másikba, amikor az ugyanazon a kiinduló lap blob szóközt foglal el. Ezért teljes pillanatkép másolása egyik tárfiókból a másikba lassú, és a célként megadott tárfiók mennyi lemezterületet használ fel.

> [!NOTE]
> Ha egy másik célhelyre másolja az alap blob, a pillanatképek a BLOB nem kerülnek, együtt. Ehhez hasonlóan Ha alap blob egy másolatot felülírja, az alap blob tartozó pillanatképek nem érinti és alap blob neve alatt maradjon.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Pillanatképekkel lemezek biztonsági mentése
Egy virtuálisgép-lemezek biztonsági mentési stratégia, mint a rendszeres időközönként pillanatképek a lemez vagy lap BLOB elvégezhető, és azokat egy másik Storage-fiók használatával másolási eszközökkel, mint például [a Blob másolásához](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) művelet vagy [AzCopy](../articles/storage/common/storage-use-azcopy.md). A cél lapblob, más néven pillanatkép másolhatja. Az eredményül kapott cél lapblob egy írható lapblob és nem egy pillanatkép. Ez a cikk későbbi részében ismertetünk lépéseket kell tennie a pillanatképek használata virtuális gépek lemezeinek biztonsági másolatait.

### <a name="restore-disks-using-snapshots"></a>A pillanatképek a lemezek visszaállítása
Ha a lemez egy stabil verzióra, amely egy, a biztonsági mentési pillanatképek rögzítésének helyreállításához szükséges időt, a kiinduló lap BLOB pillanatkép másolhatja. A pillanatkép lett előléptetve, a kiinduló lap után blob, a pillanatkép marad, de a forrás, amely képes is olvashatók és írhatók másolatot felülírja. A cikk későbbi részében ismertetünk lépéseket a pillanatkép visszaállítása egy korábbi állapotba a lemezt.

### <a name="implementing-full-snapshot-copy"></a>Végrehajtási teljes pillanatkép másolása
Az alábbiak szerint valósítható meg a teljes pillanatkép másolása

* Először készítsen pillanatképet az alap blob használatával a [Blob pillanatkép](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) műveletet.
* Ezután másolja a pillanatkép a cél tárfiók tárfiókkulcsait [a Blob másolásához](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Ismételje meg az eljárást az alap blob biztonsági másolatait karbantartása.

## <a name="incremental-snapshot-copy"></a>Növekményes pillanatkép-másolás
Az új szolgáltatása a [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API lehetővé teszi a sokkal jobb biztonsági mentése a pillanatképek a lapblobok és lemezek. Az API-t adja vissza azokat a változásokat az alap blob és a pillanatképek közötti, ami csökkenti a biztonsági mentési fiók felhasznált lemezterület mennyiségét. Az API támogatja a lapblobokat a prémium szintű Storage, valamint a standard szintű tárolást. Az API-t használ, hozhat létre biztonsági mentési megoldások gyorsabb és hatékonyabb Azure-beli virtuális gépek. Ez az API elérhető a 2015-07-08 REST-verzióval és magasabb lesz.

Növekményes pillanatkép-másolás lehetővé teszi, hogy másolása egyik tárfiókból a másikba a különbség a között,

* Alap blob és a pillanatkép vagy
* A kiinduló BLOB bármely két pillanatképek

A következő feltételek teljesülnek, a megadott

* A blob a 1 – 2016. január, vagy később hozta létre.
* A blob nem írja felül a [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) vagy [a Blob másolásához](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) két-pillanatképek közötti.

**Megjegyzés:**: Ez a funkció a prémium és standard szintű Azure-Lapblobokkal érhető el.

Ha egy egyéni biztonsági mentési stratégia pillanatképekkel rendelkezik, másol a pillanatképek egyik tárfiókból a másikba lassú lehet, és mennyi tárhelyet használhatnak. Helyett a teljes pillanatkép másolása egy biztonsági mentési tárfiókot, írhat egy biztonsági mentési lapblob egymást követő pillanatképek közötti különbség. Ily módon a másolás és a biztonsági mentések tárolására szolgáló terület idő jelentősen csökken.

### <a name="implementing-incremental-snapshot-copy"></a>Növekményes pillanatkép-másolás végrehajtási
Növekményes pillanatkép-másolás valósítható meg az alábbiak szerint

* Pillanatkép készítése a kiinduló blob használatával [Blob pillanatkép](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* A pillanatkép másolása a célként megadott biztonsági mentési tárfiók tárfiókkulcsait [a Blob másolásához](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Ez az a biztonsági mentési lapblob. Pillanatkép készítése a biztonsági mentési lapblob, és tárolja a biztonsági mentési fiók.
* Készítsen másik pillanatképet, az alap blob pillanatkép Blob használatával.
* A különbség az alapszintű blob használatával az első és második pillanatképek beolvasása [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Az új paraméterrel **prevsnapshot**, a pillanatkép szeretne kapni a különbség az a dátum/idő értéket adjon meg. Ha a paraméter meg lett adva, a REST-válasz csak a cél pillanatkép és az előző pillanatképet, beleértve az oldalak törlése közötti módosított lapok magában foglalja.
* Használat [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) a alkalmazni ezeket a módosításokat a biztonsági mentési lapblob.
* Végül készítsen pillanatképet a biztonsági mentési lapblob, és tárolja a biztonsági mentési tárfiók.

A következő szakaszban bemutatunk néhányat részletesebben hogyan kell karbantartani növekményes pillanatkép-másolás használatával lemezeinek biztonsági másolatait

## <a name="scenario"></a>Forgatókönyv
Ebben a szakaszban egy forgatókönyvet, amely magában foglalja a virtuális gép lemezeinek pillanatképekkel egy egyéni biztonsági mentési stratégia ismertetünk.

Fontolja meg a DS sorozatú Azure virtuális gép és a egy prémium szintű storage P30 lemez csatolva. A típus P30 lemez nevű *mypremiumdisk* nevű premium storage-fiók tárolva van *mypremiumaccount*. A standard szintű tárfiók nevű *mybackupstdaccount* a biztonsági másolatának tárolásához használt *mypremiumdisk*. Azt szeretnénk, hogy a pillanatkép, *mypremiumdisk* 12 óránként.

Storage-fiók létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Azure virtuális gépek biztonsági mentésével kapcsolatos további információkért tekintse meg [megtervezése az Azure virtuális gép biztonsági mentéseinek](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Lépések egy növekményes pillanatképekkel lemezt biztonsági másolatainak kezelése
Az alábbi lépések bemutatják, hogyan pillanatképek *mypremiumdisk* és karbantartása a biztonsági másolatok *mybackupstdaccount*. A biztonsági mentés egy standard szintű lapblob nevű *mybackupstdpageblob*. A biztonsági mentési lapblob mindig ugyanazt az állapotot, a legutóbbi pillanatképet tükrözi *mypremiumdisk*.

1. Hozzon létre a biztonsági mentési lapblob a prémium szintű storage lemez pillanatképének elkészítése *mypremiumdisk* nevű *mypremiumdisk_ss1*.
2. A pillanatkép másolása mybackupstdaccount nevű lapblobként *mybackupstdpageblob*.
3. Pillanatkép készítése *mybackupstdpageblob* nevű *mybackupstdpageblob_ss1*révén [Blob pillanatkép](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) , és tárolja a *mybackupstdaccount*.
4. A biztonsági mentési időszakban egy másik pillanatkép létrehozása a *mypremiumdisk*, például: *mypremiumdisk_ss2*, és tárolja a *mypremiumaccount*.
5. A növekményes változásokat a két-pillanatképek közötti első *mypremiumdisk_ss2* és *mypremiumdisk_ss1*révén [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) a *mypremiumdisk_ ss2* együtt a **prevsnapshot** paraméterkészlet az időbélyeghez ugorhatnak *mypremiumdisk_ss1*. Ezek a növekményes változásokat írni a biztonsági mentési lapblob *mybackupstdpageblob* a *mybackupstdaccount*. Ha a növekményes változásokat törlése címtartományokat, azok a biztonsági mentési lapblob a bude vymazán. Használat [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) a növekményes változásokat írni a biztonsági mentési lapblob.
6. Pillanatkép készítése a biztonsági mentési lapblob *mybackupstdpageblob*néven *mybackupstdpageblob_ss2*. Az előző pillanatképet törölni *mypremiumdisk_ss1* prémium szintű storage-fiókból.
7. Minden biztonsági mentés időszakának ismételje meg a 4 – 6. Így akkor is fenntartható a biztonsági másolatainak *mypremiumdisk* a standard szintű storage-fiókban.

![Biztonsági mentése növekményes pillanatképekkel lemezt](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>A pillanatképek a lemez visszaállításához lépések
A következő lépések azt ismertetik, hogyan a prémium szintű lemez visszaállítása *mypremiumdisk* , a biztonsági mentési tárfiókból egy korábbi pillanatkép *mybackupstdaccount*.

1. Azon pont azonosítása, állítsa vissza a prémium szintű lemezt a kívánt időben. Tegyük fel, hogy azt pillanatkép *mybackupstdpageblob_ss2*, amely a biztonsági mentési tárfiókban tárolt *mybackupstdaccount*.
2. A mybackupstdaccount, a pillanatkép előléptetése *mybackupstdpageblob_ss2* , az új biztonsági mentési kiinduló lap blob *mybackupstdpageblobrestored*.
3. Pillanatkép készítése a visszaállított biztonsági mentési lapblob, nevű *mybackupstdpageblobrestored_ss1*.
4. Másolja a visszaállított lapblob *mybackupstdpageblobrestored* a *mybackupstdaccount* való *mypremiumaccount* , az új prémium szintű lemez  *mypremiumdiskrestored*.
5. Pillanatkép készítése *mypremiumdiskrestored*néven *mypremiumdiskrestored_ss1* a jövőbeli növekményes biztonsági mentések készítése.
6. A DS sorozatú virtuális gép átirányítása a visszaállított lemez *mypremiumdiskrestored* le a régi *mypremiumdisk* a virtuális gépről.
7. A visszaállított lemezt az előző szakaszban ismertetett biztonsági mentési megkezdéséhez *mypremiumdiskrestored*révén a *mybackupstdpageblobrestored* , a biztonsági mentési lapblob.

![A pillanatképek a lemez visszaállítása](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások segítségével további tudnivalók a blobokról pillanatképek létrehozása és a virtuális gép biztonsági infrastruktúrájának megtervezése.

* [Egy Blob pillanatképének létrehozása](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Virtuális gép biztonsági infrastruktúrájának megtervezése](../articles/backup/backup-azure-vms-introduction.md)

