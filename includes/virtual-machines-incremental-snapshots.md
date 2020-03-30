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
ms.openlocfilehash: f30518c3bfc9876cbddaf8295ff9e8b667a70200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74014560"
---
## <a name="overview"></a>Áttekintés
Az Azure Storage lehetővé teszi a blobok pillanatképeinek pillanatképeit. A pillanatképek rögzítik a blob állapotát abban az időpontban. Ebben a cikkben egy forgatókönyvet, amelyben a virtuális gép lemezek biztonsági mentések pillanatképek használatával karbantartása. Ezt a módszert akkor használhatja, ha úgy dönt, hogy nem használja az Azure biztonsági mentési és helyreállítási szolgáltatást, és egyéni biztonsági mentési stratégiát szeretne létrehozni a virtuális gép lemezeihez.

Az Azure virtuálisgép-lemezek lapblobként tárolódnak az Azure Storage-ban. Mivel ebben a cikkben a virtuálisgép-lemezek biztonsági mentési stratégiáját ismertetjük, a lapblobok környezetében lévő pillanatképekre hivatkozunk. Ha többet szeretne megtudni a pillanatképekről, olvassa el a [Blob pillanatképének létrehozása.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)

## <a name="what-is-a-snapshot"></a>Mi az a pillanatfelvétel?
A blob pillanatkép egy blob, amely egy adott időpontban rögzített csak olvasható verziója. A pillanatkép létrehozása után olvasható, másolható vagy törölhető, de nem módosítható. Pillanatképek lekínálnak egy lehetőséget, hogy biztonsági másolatot egy blob, ahogy megjelenik egy pillanatban. A REST 2015-04-05-ös verziójáig képes volt teljes pillanatképekmásolására. A 2015-07-08-as és újabb verzióval növekményes pillanatképek et is másolhat.

## <a name="full-snapshot-copy"></a>Teljes pillanatkép-másolat
Pillanatképek átmásolhatók egy másik tárfiókba blobként az alap blob biztonsági mentések megőrzése. A pillanatképet is másolhatja az alap blobon keresztül, amely olyan, mint a blob visszaállítása egy korábbi verzióra. Amikor egy pillanatképet egyik tárfiókból a másikba másol, ugyanazt a helyet foglalja el, mint az alaplap blob. Ezért a teljes pillanatképek másolása egyik tárfiókból a másikba lassú, és sok helyet foglal a céltárfiókban.

> [!NOTE]
> Ha másolja az alap blob egy másik helyre, a blob pillanatképei nem másolt együtt. Hasonlóképpen, ha felülírja az alapblob egy másolattal, az alap blobhoz társított pillanatképek nem érinti, és érintetlen marad az alap blob neve alatt.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Lemezek biztonsági és biztonsági felvétele pillanatképek használatával
A virtuális gép lemezeinek biztonsági mentési stratégiájaként rendszeres pillanatképeket készíthet a lemezről vagy a lapblobról, és átmásolhatja őket egy másik tárfiókba olyan eszközökkel, mint a [Blob másolása](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) művelet vagy az [AzCopy.](../articles/storage/common/storage-use-azcopy.md) A pillanatképet más néven más céllapblobba másolhatja. Az eredményül kapott céllap blob egy írható lap blob, és nem egy pillanatkép. A cikk későbbi részében bemutatjuk a virtuális gép lemezeinek biztonsági mentésepillanatok használatával történő biztonsági mentéseit.

### <a name="restore-disks-using-snapshots"></a>Lemezek visszaállítása pillanatképek használatával
Ha itt az ideje, hogy visszaállítsa a lemezt egy stabil verzióra, amelyet korábban rögzített az egyik biztonsági mentési pillanatképek, átmásolhatja a pillanatképet az alaplap blob. Miután a pillanatkép előléptetése az alaplap blob, a pillanatkép marad, de a forrás felülírja egy másolatot, amely olvasható és írható. A cikk későbbi részében ismertetjük a lemez korábbi verziójának pillanatképből való visszaállításának lépéseit.

### <a name="implementing-full-snapshot-copy"></a>Teljes pillanatkép-másolat megvalósítása
A teljes pillanatfelvétel másolatát a következőkkel valósíthatja meg:

* Először pillanatképet készíthet az alapblobról a [Pillanatkép blobművelet](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) használatával.
* Ezután másolja a pillanatképet egy céltárfiókba a [Blob másolása](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)használatával.
* Ismételje meg ezt a folyamatot az alapblob biztonsági másolatainak karbantartásához.

## <a name="incremental-snapshot-copy"></a>Növekményes pillanatkép-másolás
A [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API új szolgáltatása sokkal jobb módot biztosít a lapblobok vagy lemezek pillanatképeinek biztonsági készítésére. Az API az alapblob és a pillanatképek közötti módosítások listáját adja vissza, ami csökkenti a biztonsági mentési fiókban használt tárterület mennyiségét. Az API támogatja a lapblobokat a prémium szintű storage-ban és a standard storage-ban. Ezzel az API-val gyorsabb és hatékonyabb biztonsági mentési megoldásokat hozhat létre az Azure virtuális gépekhez. Ez az API a REST 2015-07-08-as és újabb verziójával lesz elérhető.

A növekményes pillanatkép-másolás lehetővé teszi, hogy az egyik tárfiókból a másikba másolja a

* Alapblob és pillanatképvagy
* Az alapblob két pillanatképe

Feltéve, hogy a következő feltételek teljesülnek,

* A blob jött létre a jan-1-2016 vagy újabb.
* A blob nem felülírta [a PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) vagy [másolásblob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) két pillanatkép között.

**Megjegyzés:** Ez a funkció prémium és standard Szintű Azure-lapblobok esetén érhető el.

Ha egy egyéni biztonsági mentési stratégia segítségével pillanatképek, a pillanatképek másolása az egyik tárfiókból a másikba lassú lehet, és sok tárhelyet. Ahelyett, hogy a teljes pillanatkép másolása egy biztonsági mentési tárfiókba, írhat a különbség az egymást követő pillanatképek egy biztonsági mentési lap blob. Ily módon a másolási idő és a biztonsági mentések tárolására szükséges hely jelentősen csökken.

### <a name="implementing-incremental-snapshot-copy"></a>Növekményes pillanatkép-másolás megvalósítása
A növekményes pillanatkép-másolást a következőkkel valósíthatja meg:

* Pillanatkép készítése az alapblobról a [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)használatával.
* Másolja a pillanatképet a célbiztonsági tárfiókba ugyanabban vagy bármely más Azure-régióban a [Blob másolása](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)használatával. Ez a biztonsági mentési lap blob. Készítsen pillanatképet a biztonsági mentési lap blobjáról, és tárolja azt a biztonsági mentési fiókban.
* A Snapshot Blob használatával egy újabb pillanatképet készíthet az alapblobról.
* A GetPageRanges használatával az alapblob első és második pillanatképe közötti különbség [beszerezése.](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) Az új **prevsnapshot**paraméter rel megadhatja annak a pillanatképnek a DateTime értékét, amelyhez a különbséget szeretné elérni. Ha ez a paraméter jelen van, a REST válasz csak azokat az oldalakat tartalmazza, amelyek a cél pillanatkép és az előző pillanatkép között megváltoztak, beleértve a tiszta oldalakat is.
* A [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) segítségével alkalmazza ezeket a módosításokat a biztonsági másolat lapblobra.
* Végül készítsen pillanatképet a biztonsági mentési lap blobjáról, és tárolja azt a biztonsági mentési tárfiókban.

A következő szakaszban részletesebben ismertetjük, hogyan őrizheti meg a lemezek biztonsági mentését a növekményes pillanatkép-másolás sal

## <a name="scenario"></a>Forgatókönyv
Ebben a szakaszban egy forgatókönyvet, amely magában foglalja az egyéni biztonsági mentési stratégia a virtuális gép lemezei pillanatképek használatával.

Fontolja meg egy DS-sorozatú Azure virtuális gép prémium szintű tárolási P30 lemez csatlakoztatva. A P30 lemez nevű *mypremiumdisk* tárolja a prémium tárfiók nevű *mypremiumaccount*. A *mypremiumdisk*biztonsági másolatának tárolására egy *mybackupstdaccount* nevű szabványos tárfiókot használnak. Szeretnénk 12 óránként pillanatképet készíteni a *mypremiumdisk-ről.*

A tárfiók létrehozásáról a [Tárfiók létrehozása című](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)témakörben olvashat.

Az Azure virtuális gépek biztonsági mentéséről az [Azure virtuális gépek biztonsági mentésének megtervezése.](../articles/backup/backup-azure-vms-introduction.md)

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>A lemez biztonsági másolatainak karbantartása növekményes pillanatképek használatával
A következő lépések bemutatják, hogyan készíthet pillanatképeket a *mypremiumdisk* lemezről, és hogyan őrizze meg a biztonsági másolatokat a *mybackupstdaccount ban.* A biztonsági mentés egy standard lapblob, a *mybackupstdpageblob.* A biztonsági másolat lapblobja mindig ugyanazt az állapotot tükrözi, mint a *mypremiumdisk*utolsó pillanatképe.

1. Hozza létre a biztonsági mentési lap blobját a prémium szintű tárolólemezhez, készítsen pillanatképet a *mypremiumdisk_ss1*nevű *mypremiumdisk* lemezről.
2. Másolja ezt a pillanatképet a mybackupstdaccount ba *egy mybackupstdpageblob*nevű lapblobként.
3. Készítsen pillanatképet a *mybackupstdpageblobról,* *amelyet mybackupstdpageblob_ss1*néven használnak, a [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) használatával tárolja azt *a mybackupstdaccountában.*
4. A biztonsági mentési ablakban hozzon létre egy másik pillanatképet a *mypremiumdisk-* ről , mondja *mypremiumdisk_ss2*, és tárolja a *mypremiumaccount alkalmazásban.*
5. A két pillanatkép, a *mypremiumdisk_ss2* és a *mypremiumdisk_ss1*növekményes módosításait a [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) használatával *mypremiumdisk_ss2* a *mypremiumdisk_ss1*időbélyegére beállított **prevsnapshot** paraméterrel. Írja be ezeket a növekményes módosításokat a biztonsági másolat lap *blobmybackupstdpageblob* a *mybackupstdaccount*. Ha vannak törölt tartományok a növekményes módosításokat, törölni kell a biztonsági mentési lap blob. A [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) segítségével növekményes módosításokat írhat a biztonságimásolat-lap blobjába.
6. Készítsen pillanatképet a biztonsági másolat lapblobról, amelyet *mybackupstdpageblobnak*hívnak, *mybackupstdpageblob_ss2.* Törölje az előző pillanatkép *mypremiumdisk_ss1* prémium szintű tárfiókból.
7. Ismételje meg a 4-6. Ily módon a *mypremiumdisk* biztonsági mentéseit egy normál tárfiókban tarthatja fenn.

![Lemez biztonsági és biztonsági másolatot készíteni növekményes pillanatképek használatával](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Lemez pillanatképekből történő visszaállításának lépései
A következő lépések, ismerteti, hogyan lehet visszaállítani a prémium lemez, *mypremiumdisk* egy korábbi pillanatképet a biztonsági mentési tárfiók *mybackupstdaccount*.

1. Azonosítsa azt az időpontot, amelybe vissza szeretné állítani a prémium szintű lemezt. Tegyük fel, hogy ez a pillanatkép *mybackupstdpageblob_ss2*, amely a *mybackupstdaccount*biztonsági mentési tárfiókban van tárolva.
2. A mybackupstdaccount, előkell mozdítania a pillanatkép *mybackupstdpageblob_ss2,* mint az új biztonsági mentés alaplap blob *mybackupstpageblobrestored*.
3. Készítsen pillanatképet a visszaállított biztonsági másolat lapblobról, *amelyet mybackupstdpageblobrestored_ss1.*
4. Másolja a visszaállított lapblob *ot mybackupstdpageblobrestored a* *mybackupstdaccount* *a mypremiumaccount* az új prémium lemez *mypremiumdiskrestored*.
5. Készítsen pillanatképet a *mypremiumdiskrestored*ról, *amelyet mypremiumdiskrestored_ss1* a jövőbeli növekményes biztonsági mentések készítéséhez.
6. Irányítsa a DS sorozatú virtuális gép a visszaállított lemez *mypremiumdiskrestored* és válassza le a régi *mypremiumdisk* a virtuális gép.
7. Kezdje el a biztonsági mentési folyamat leírt előző szakaszban a visszaállított lemez *mypremiumdiskrestored*, a *mybackupstdpageblobrestored a* biztonsági mentési lap blob.

![Lemez visszaállítása pillanatképekből](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások segítségével további információval rendelkezik a blob pillanatképeinek létrehozásáról és a virtuális gép biztonsági mentési infrastruktúrájának megtervezéséről.

* [Blob pillanatképének létrehozása](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [A virtuális gép biztonsági mentési infrastruktúrájának megtervezése](../articles/backup/backup-azure-vms-introduction.md)

