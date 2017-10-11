# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Készítsen biztonsági másolatot az Azure nem felügyelt méretű lemezek növekményes pillanatképek
## <a name="overview"></a>Áttekintés
Az Azure Storage lehetővé teszi a pillanatképek készítése a blobokat. Pillanatképek a blob állapot abban az idő rögzítése. Ez a cikk azt ismerteti egy olyan forgatókönyvet, amelyben akkor is fenntartható a pillanatképek használata virtuális gépek lemezeinek biztonsági másolatait. Ez a módszer segítségével használhatja, ha nem az Azure biztonsági mentési és helyreállítási szolgáltatás, és létrehozza a virtuális gépek lemezeit egyéni biztonsági mentési stratégiáját.

Azure virtuális gépek lemezeit, az Azure Storage lapblobokat tárolódnak. Azt is leíró virtuális gépek lemezeit a cikkben egy biztonsági mentési stratégiája, mivel azt tekintse meg a pillanatképek lapblobokat környezetében. A pillanatképek kapcsolatos további tudnivalókért tekintse meg [egy pillanatképet készíteni egy Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Mi az a pillanatképet?
Egy blob pillanatképet egy időben rögzített blob csak olvasható verziója telepítve. Pillanatkép létrehozása, azt is kell olvasni, másolja, vagy törölni, de nem módosított. A pillanatképek lehetőséget nyújtanak olyan biztonsági mentése blob, ahogyan megjelenik egy időben el. REST 2015-04-05-ös verzióját, amíg nem volt teljes pillanatképek másolása lehetőséget. A többi verzió 2015-07-08 és újabb verzióiban meg is másolhatja növekményes pillanatképek.

## <a name="full-snapshot-copy"></a>Teljes pillanatkép másolása
A pillanatképek másik tárolási fiókot, egy blobot alap BLOB biztonsági mentések megtartása másolhat. Pillanatkép a alap, mint a blob visszaállítása egy korábbi blob keresztül is másolhatja. Amikor a pillanatképet egy tárfiók lesz átmásolva a másikra, az ugyanazon a kiinduló lap blob lemezterületet foglal el. Ezért teljes pillanatképek másolása egy tárfiókot másik lassú, és fel a céloldali tárfiók térközt.

> [!NOTE]
> Ha az alap blob másolása másik célhelyet, a pillanatképek a BLOB nem kerülnek. Hasonlóan alap blob felülírása egy másolatot, ha az alap blob tartozó pillanatképet nem érinti és nem sérültek az alap blob neve alatt maradnak.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Készítsen biztonsági másolatot készített pillanatfelvételek segítségével történő lemezek
A virtuális gépek lemezeit a biztonsági mentési stratégia, mint a lemez vagy a lap BLOB rendszeres időközönként pillanatképek tarthat, és azokat egy másik tárolási fiókot használva másolási eszközökkel, mint például [másolási Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) művelet vagy [AzCopy](../articles/storage/common/storage-use-azcopy.md). A cél az oldalakra vonatkozó blob egy eltérő nevű pillanatkép másolhatja. Az eredményül kapott cél oldalakra vonatkozó blob írható oldalakra vonatkozó blob és a pillanatkép nem. A cikk későbbi részében pillanatképek használata virtuális gépek lemezeinek biztonsági másolatait végrehajtandó lépések azt ismertetik.

### <a name="restore-disks-using-snapshots"></a>A lemezek pillanatképek visszaállítása
Ha a lemez állítani egy stabil verzióját, a biztonsági mentési pillanatképek egyikében rögzítésének időt, pillanatkép másolhatja a kiinduló lap blob keresztül. Követően a pillanatkép van előléptetve a kiinduló lap blob, a pillanatkép marad, de a forrás, amely képes is olvashatók és írhatók másolatot felülírja. A cikk azt a pillanatkép visszaállítása egy korábbi verzióját a lemez lépéseket írja le.

### <a name="implementing-full-snapshot-copy"></a>Végrehajtási teljes pillanatkép másolása
Megvalósíthat egy teljes pillanatképet másolása a következő tevékenységek végrehajtásával

* Először készítsen pillanatképet az alap blob használatával a [pillanatkép Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) műveletet.
* A célként megadott tárolási fiók használatával, majd másolja a pillanatkép [másolási Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Ismételje meg a folyamatot, az alap blobba biztonsági másolatait.

## <a name="incremental-snapshot-copy"></a>Növekményes pillanatképet másolása
Az új funkciója a [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API készítsen biztonsági másolatot a pillanatképek a lapblobokat és lemezek javulás lehetőséget kínál. Az API-t adja vissza azokat a változásokat az alap blob és a pillanatképek között, ami csökkenti a biztonsági mentési fiók felhasznált tárterület. Az API támogatja a lapblobokat prémium szintű Storage, valamint a standard szintű tárolót. Ez az API használatával, Azure virtuális gépek biztonsági mentési megoldások gyorsabb és hatékonyabb hozhat létre. Ez az API rendelkezésre álljanak, a többi verziójával 2015-07-08 és magasabb.

Növekményes pillanatképet másolási lehetővé teszi másolhat egy tárfiók egy másikra a különbség a között,

* Alap blob és a pillanatkép vagy
* A kiinduló BLOB bármely két pillanatképek

A következő feltételek teljesülnek, a megadott

* A blob Jan-1-2016 vagy újabb hozták létre.
* A blob nem írja felül a [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) vagy [másolási Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) két pillanatképek között.

**Megjegyzés:**: Ez a funkció érhető el prémium és Standard Azure Lapblobokat.

Ha egy egyéni biztonsági mentési stratégia készített pillanatfelvételek segítségével történő, a pillanatképek másolását egy tárfiókot a másikra, és ez sok tárhelyet is felhasználhatnak. Helyett másolja át a teljes pillanatkép biztonsági mentési tárfiókba, a biztonsági mentési oldalakra vonatkozó blob egymást követő pillanatképek közötti különbség is írhat. Ezzel a módszerrel a másolási és a biztonsági mentések tárolására szolgáló terület ideje jelentősen csökken.

### <a name="implementing-incremental-snapshot-copy"></a>Végrehajtási növekményes pillanatképet másolása
A következőképpen valósíthatja növekményes pillanatképet másolása

* Pillanatkép készítése a alap blob használatával [pillanatkép Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* A cél biztonsági másolatok tárolási fiók használatával másolja a pillanatkép [másolási Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Ez az a biztonsági mentési oldalakra vonatkozó blob. Pillanatkép készítése a biztonsági mentési oldalakra vonatkozó blob, és tárolja a biztonsági mentési fiók.
* Egy másik pillanatképet, az alap blob pillanatkép Blob használatával.
* Az első és második pillanatképek alap blob használva közötti különbség beolvasása [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Használja az új paramétert **prevsnapshot**, hogy a pillanatkép le szeretné kérdezni a különbség a dátum/idő értéket adja meg. Amikor ez a paraméter meg adva, a többi válasz lapjain csak a cél pillanatkép és a korábbi pillanatképből, többek között a tiszta lapok között megváltozott.
* Használjon [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) változtatásait szeretné alkalmazni ezeket a biztonsági mentési oldalakra vonatkozó blob.
* Végezetül pillanatkép készítése a biztonsági mentési oldalakra vonatkozó blob, és tárolja a biztonsági mentési tárfiókot.

A következő szakaszban azt ismerteti, részletesebben hogyan kezelheti a biztonsági mentések lemezek növekményes pillanatképet-másolással

## <a name="scenario"></a>Forgatókönyv
Ez a szakasz azt ismerteti olyan forgatókönyvekben, amelyek a virtuális gépek lemezeit készített pillanatfelvételek segítségével történő egyéni biztonsági mentési stratégiája magában foglalja.

Vegye figyelembe a DS sorozatnak Azure virtuális gépek csatolt prémium szintű storage P30 lemezzel. A P30 lemez nevű *mypremiumdisk* tárolódik a prémium szintű storage-fiók neve *mypremiumaccount*. Standard szintű tárfiók nevű *mybackupstdaccount* biztonsági mentésének tárolásához használt *mypremiumdisk*. Azt szeretné tartani a pillanatképet *mypremiumdisk* 12 óránként.

A tárfiók és a lemezek létrehozásával kapcsolatos további tudnivalókért tekintse meg [tudnivalók az Azure storage-fiókok](../articles/storage/storage-create-storage-account.md).

Az Azure virtuális gépek biztonsági mentéséről további tudnivalókért tekintse meg [tervezze meg az Azure virtuális gép biztonsági mentések](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Ismerteti a végrehajtás lépéseit egy lemez növekményes készített pillanatfelvételek segítségével történő biztonsági mentés karbantartása
A következő lépések végrehajtásával pillanatképek készítése *mypremiumdisk* és karbantartása a biztonsági másolatok *mybackupstdaccount*. A biztonsági másolat nevű szabványos oldalakra vonatkozó blob *mybackupstdpageblob*. A biztonsági mentési oldalakra vonatkozó blob mindig tükrözi az utolsó pillanatképét a egyező állapotban *mypremiumdisk*.

1. A prémium szintű tároló lemez, a biztonsági mentési oldalakra vonatkozó blob létrehozása pillanatkép készítése a *mypremiumdisk* nevű *mypremiumdisk_ss1*.
2. Másolja a pillanatkép mybackupstdaccount nevű oldalblobként *mybackupstdpageblob*.
3. Pillanatkép készítése *mybackupstdpageblob* nevű *mybackupstdpageblob_ss1*használatával [pillanatkép Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) és tárolja a *mybackupstdaccount*.
4. A biztonsági mentési időszakban egy másik pillanatkép létrehozása a *mypremiumdisk*, azaz *mypremiumdisk_ss2*, és tárolja a *mypremiumaccount*.
5. A növekményes módosult a két pillanatképek közötti *mypremiumdisk_ss2* és *mypremiumdisk_ss1*használatával [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) a *mypremiumdisk_ ss2* rendelkező a **prevsnapshot** paraméter értéke a időbélyegzője *mypremiumdisk_ss1*. Ezek a növekményes változásokat írni a biztonsági mentési oldalakra vonatkozó blob *mybackupstdpageblob* a *mybackupstdaccount*. Ha a növekményes változásokat törölt tartománya, akkor törölni kell a biztonsági mentési oldalakra vonatkozó blob a. Használjon [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) a növekményes változásokat írni a biztonsági mentési oldalakra vonatkozó blob.
6. Pillanatkép készítése a biztonsági mentési oldalakra vonatkozó blob *mybackupstdpageblob*néven *mybackupstdpageblob_ss2*. Törölje az előző pillanatképet *mypremiumdisk_ss1* a prémium szintű storage-fiók.
7. Minden biztonsági mentés időszakának ismételje meg a 4 – 6. Így akkor is fenntartható a biztonsági másolatainak *mypremiumdisk* szabványos tárfiókokban.

![Készítsen biztonsági másolatot növekményes pillanatképek használatával](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>A lemez a visszaállítandó pillanatképek lépései
A következő lépések azt ismertetik, hogyan visszaállítása a prémium szintű lemez *mypremiumdisk* egy korábbi pillanatkép biztonsági másolatokat tároló-fiókból való *mybackupstdaccount*.

1. Azonosítsa a pont, a prémium szintű lemez visszaállítani kívánt időben. Tegyük fel a pillanatkép van *mybackupstdpageblob_ss2*, amely tárolja a biztonsági mentési tárfiók *mybackupstdaccount*.
2. A mybackupstdaccount, előléptetni a pillanatkép *mybackupstdpageblob_ss2* , az új biztonsági mentési alap oldalakra vonatkozó blob *mybackupstdpageblobrestored*.
3. Pillanatkép készítése a visszaállított biztonsági mentési oldalakra vonatkozó blob, úgynevezett *mybackupstdpageblobrestored_ss1*.
4. Másolja a visszaállított oldalakra vonatkozó blob *mybackupstdpageblobrestored* a *mybackupstdaccount* való *mypremiumaccount* az új premium tárolására *mypremiumdiskrestored*.
5. Pillanatkép készítése *mypremiumdiskrestored*néven *mypremiumdiskrestored_ss1* jövőbeli növekményes biztonsági mentést készíteni.
6. A DS adatsorozat VM mutasson a visszaállított lemez *mypremiumdiskrestored* és a régi leválasztási *mypremiumdisk* a virtuális gépről.
7. A visszaállított lemez előző szakaszban ismertetett biztonsági mentés megkezdéséhez *mypremiumdiskrestored*használatával a *mybackupstdpageblobrestored* , a biztonsági mentési oldalakra vonatkozó blob.

![A pillanatképek lemez visszaállítása](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások segítségével pillanatképeinek blob és a virtuális gép biztonsági mentési infrastruktúrájának megtervezésével kapcsolatos további.

* [Egy BLOB pillanatkép létrehozása](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [A virtuális gép biztonsági mentési infrastruktúra megtervezése](../articles/backup/backup-azure-vms-introduction.md)

