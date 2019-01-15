---
title: Azure virtuális gépek biztonsági mentéséről
description: További tudnivalók az Azure virtuális gépek biztonsági mentését, és jegyezze fel néhány ajánlott eljárást.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: 09464342bd39e57f6e637ce90adc7190d08340a9
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265413"
---
# <a name="about-azure-vm-backup"></a>Azure virtuális gépek biztonsági mentéséről

Ez a cikk azt ismerteti, hogyan a [Azure Backup szolgáltatás](backup-introduction-to-azure-backup.md) Azure virtuális gépek biztonsági mentését.

## <a name="backup-process"></a>Biztonsági mentési folyamat

Itt látható, hogyan Azure Backup befejeződött a biztonsági mentés az Azure virtuális gépek.

1. Az Azure virtuális gépek biztonsági mentésre kijelölt az Azure Backup szolgáltatás biztonsági mentési feladat a megadott biztonsági mentési ütemterv szerint indítja el.
2. A szolgáltatás aktiválása, a biztonsági mentési bővítményt.
    - Windows virtuális gépek használata a _VMSnapshot_ bővítmény.
    - Linux rendszerű virtuális gépek használata a _VMSnapshotLinux_ bővítmény.
    - A bővítmény telepítve van a virtuális gép első biztonsági mentés során.
    - A bővítmény telepítéséhez a virtuális Gépen kell futnia.
    - Ha a virtuális gép nem fut, a Backup szolgáltatás az alapul szolgáló tárolóról készít pillanatképet (mivel nem történik alkalmazásírás, amikor a virtuális gép le van állítva).
4. A biztonsági mentési bővítményt pillanatképet tárolási szintű összeomlási konzisztens/fájlkonzisztens.
5. A pillanatkép elkészítésének után az adatok átkerülnek a tárolóba. Maximális hatékonyság érdekében a szolgáltatás azonosítja és továbbítja, amelyek az előző (különbözeti) biztonsági mentés óta változtak csak azokat az adatblokkokat.
5. Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

![Az Azure virtuális gép biztonsági mentési architektúra](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Adattitkosítás

Az Azure Backup nem titkosítja az adatokat a biztonsági mentési folyamatának részeként. Az Azure Backup támogatja az Azure Disk Encryption használatával titkosított Azure virtuális gépek biztonsági mentését.

- A Bitlocker titkosítást Key(BEK) csak titkosított virtuális gépek biztonsági mentését, és a Blokktitkosítási kulcs titkosítási Key(KEK) együtt támogatott, felügyelt és nem felügyelt Azure-beli virtuális gépek.
- A BEK(secrets) és a biztonsági mentés KEK(keys) ahhoz, hogy olvassa el, illetve csak akkor, ha a visszaállított vissza a key vaulttal a jogosult felhasználók által használt titkosított.
- A blokktitkosítási kulcsot is az olyan forgatókönyvekben, ahol rendelkeznek BEk-KEL elveszett, biztonsági mentését, mert a hitelesített felhasználók visszaállítása a blokktitkosítási kulcsot az KeyVault és a titkosított virtuális gép helyreállítása. Kulcsok és titkos kulcsok a titkosított virtuális gépek készül biztonsági másolat titkosított formában, hogy jogosulatlan felhasználók és az Azure nem tudja olvasni, vagy használja a kulcsok és titkos kulcsok készíteni. Csak a megfelelő szintű engedélyeket is biztonsági mentése és visszaállítása titkosított virtuális gépek, valamint a kulcsok és titkok.

## <a name="snapshot-consistency"></a>Pillanatkép konzisztencia

A pillanatképek igénybe vehet, amíg alkalmazások futnak, az Azure Backup elkészítette alkalmazáskonzisztens pillanatképeket.

- **Windows virtuális gépek**: Windows virtuális gépek a Backup szolgáltatás koordinálja a a kötet árnyékmásolata másolási szolgáltatás (VSS), a Virtuálisgép-lemezek alkalmazáskonzisztens pillanatképet.
    - Alapértelmezés szerint az Azure Backup elkészítette a teljes VSS biztonsági mentést. [További információk](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Ha szeretné módosítani a beállítást, hogy az Azure biztonsági mentések VSS másolásos biztonsági mentéshez szükséges, állítsa be a következő beállításkulcsot:
        ```
        [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
        ""USEVSSCOPYBACKUP"="TRUE"
        ```
        - Futtassa az alábbi parancsot (rendszergazdaként) emelt szintű parancssorból, a fenti beállításkulcs beállítása:
          ```
          REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f
          ```
- **Linux rendszerű virtuális gépek**: Győződjön meg arról, hogy a Linux rendszerű virtuális gépek alkalmazáskonzisztens-e az Azure Backup által készített pillanatképet, a Linux szkript előtti és utáni keretrendszerrel használható. Írhat saját egyéni parancsfájlok konzisztencia biztosításához, ha a virtuális gép pillanatképének elkészítése.
    -  Az Azure Backup csak meghívja az üzem előtti és utáni parancsfájlokat, Ön által írt.
    - A szkript előtti és utáni szkriptek végrehajtása sikertelen, ha az Azure Backup, alkalmazáskonzisztens helyreállítási pontot jelöli meg. Azonban Ön végső soron felelős az alkalmazáskonzisztencia egyéni parancsfájlok használata esetén.
    - [További](backup-azure-linux-app-consistent.md) parancsfájlok konfigurálásáról.


#### <a name="consistency-types"></a>Konzisztencia-típusok

A következő táblázat ismerteti a különböző típusú konzisztencia.

**Snapshot** | **VSS-based** | **Részletek** | **Helyreállítás**
--- | --- | --- | ---
**Application-consistent** | Igen (csak Windows) | Alkalmazáskonzisztens biztonsági mentések memória a tartalom és a függőben lévő i/o-műveleteket rögzíti. Alkalmazáskonzisztens pillanatképek használja a VSS-író (vagy Linux-előkészítő/utólagos parancsfájl), amely az alkalmazás adatok konzisztenciájának biztosítása előtt a biztonsági másolat. | Az alkalmazáskonzisztens pillanatkép helyreállításakor a virtuális gép elindul. Nincs, adatvesztés vagy adatsérülés. Az alkalmazások konzisztens állapotban indítsa el.
**Fájlrendszerkonzisztens** | Igen (csak Windows) |  Alkalmazáskonzisztens biztonsági másolatainak tartalmazó fájlokat alkalmazáskonzisztens biztonsági mentései szolgáltatása pillanatképének az összes fájl egyszerre.<br/><br/> Az Azure Backup helyreállítási pontok számára egységes fájl:<br/><br/> – Linux rendszerű virtuális gépek, amelyek nem rendelkeznek előtti és utáni parancsfájlokat, vagy ha biztonsági mentései parancsfájlt, amely nem sikerült.<br/><br/> – Windows virtuális gép biztonsági mentése, a VSS nem sikerült. | A fájlkonzisztens pillanatképek helyreállításakor a virtuális gép elindul. Nincs, adatvesztés vagy adatsérülés. Alkalmazások kell megvalósítani a saját "javítás felfelé" mechanizmus, amellyel győződjön meg arról, hogy a visszaállított adatok összhangban-e.
**Crash-consistent** | Nem | Az összeomlási konzisztencia gyakran akkor történik, ha egy Azure virtuális gép leáll a biztonsági mentés idején.  Csak azokat az adatokat, amely már létezik a lemezen a biztonsági mentés időpontjában rögzített, és biztonsági mentés.<br/><br/> Összeomlás-konzisztens helyreállítási pont létrehozása nem garantálja az operációs rendszer vagy az alkalmazás az adatok konzisztenciáját. | Nincsenek nem garantálja, de általában a virtuális gép indítása, és a egy lemezt a következőképpen ellenőrizze, hogy a sérülés hibák javítása. Memóriában lévő adatokat vagy írási, nem továbbított lemez elvesznek. Alkalmazás valósít meg saját adatainak ellenőrzése. Például egy adatbázis-alkalmazások esetén a tranzakciónapló olyan bejegyzéseket, amelyek nem szerepelnek az adatbázisban, ha az adatbázis-szoftver állapotfigyelőket amíg adatok nem konzisztens.


## <a name="service-and-subscription-limits"></a>Szolgáltatás és az előfizetések korlátai

Az Azure Backup az előfizetések és a tárolók korlátok több rendelkezik.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Biztonsági mentés teljesítményét

### <a name="disk-considerations"></a>Lemez-megfontolások

Biztonsági mentési művelet optimalizálja biztonsági másolatot készíteni a virtuális gép lemezét párhuzamosan. Például ha egy virtuális gép lemezei négy, a szolgáltatás megkísérli párhuzamosan összes négy lemezek biztonsági mentése. Az egyes lemezek biztonsági mentés alatt álló, az Azure Backup azokat az adatblokkokat a lemez beolvassa és csak a módosított adatokat (növekményes biztonsági mentés) tárolja.


### <a name="scheduling-considerations"></a>Ütemezési szempontok

Biztonsági mentés ütemezését hatással van a teljesítményre.

- Ha konfigurál szabályzatok, így minden virtuális gép készül biztonsági másolat egy időben, mint a biztonsági mentési folyamat megpróbálja készítsen biztonsági másolatot az összes lemez párhuzamos ütemezett a forgalom csávából.
- A biztonsági mentési forgalom csökkentése érdekében különböző virtuális gépek biztonsági mentése nélkül, a nap másik időpontban.


### <a name="time-considerations"></a>Idő kapcsolatos szempontok

Bár a legtöbb biztonsági mentési időt beolvasása, illetve az adatok másolását, más műveletek hozzáadása egy virtuális gép biztonsági másolatának szükséges teljes idő:

- **Telepítse a tartalék mellék**: A szükséges idő telepíteni vagy frissíteni a biztonsági mentési bővítményt.
- **Az eseményindító pillanatkép**: Pillanatkép-trigger ideje. A pillanatképek aktivált közelében az ütemezett biztonsági mentés időpontja.
- **Várólista várakozási idő**: A biztonsági mentési szolgáltatás folyamatok feladatok több ügyfél tárfiókokban egy időben, mert a pillanatkép adatainak előfordulhat, hogy nem azonnal másolja a Recovery Services-tároló. Betöltés időszakokban előtt a biztonsági mentések feldolgozása legfeljebb nyolc órát is igénybe vehet. Viszont a teljes virtuális gép biztonsági mentésének ideje, a napi biztonsági mentési szabályzatok legfeljebb 24 óra.
- **Kezdeti biztonsági mentés**: Bár a teljes biztonsági mentési idő kevesebb, mint 24 órányi növekményes biztonsági mentések esetében érvényes, nem lehet az első biztonsági mentés. Szükséges idő mérete az adatok és a biztonsági mentés készül függ.
- **Adatátviteli idő**: Biztonsági mentési szolgáltatásban a növekményes változásokat előző biztonsági mentés a számítási és a változások átvitele a storage-tároló szükséges idő.

### <a name="factors-affecting-backup-time"></a>Biztonsági mentés idejét befolyásoló tényezők

Biztonsági mentés két fázisból áll, pillanatképeket és a pillanatképek átvitele a tárba. A Backup szolgáltatás optimalizálja a tároláshoz.

- A pillanatkép adatainak továbbítson egy tárolót, a szolgáltatás csak továbbítja a növekményes változásokat az előző pillanatképet.
- Annak megállapításához, a növekményes változásokat, a szolgáltatás kiszámítja a blokkok ellenőrzőösszeget.
    - Ha módosul egy, a blokk kell küldeni a tároló egy blokk azonosítja.
    - A szolgáltatás működése során további minden egyes azonosított blokkok, minimalizálása érdekében az adatok átvitele lehetőségeket keres.
    - Után minden megváltozott blokkokat kiértékelését, a szolgáltatás számmá egyesíti a módosításokat, és elküldi azokat a tárolóban.

Olyan helyzetekben, amelyek hatással lehetnek a biztonsági mentés időpontja a következők:

- **Már védett virtuális gépekhez újonnan hozzáadott lemezek esetében a kezdeti biztonsági mentés**: Ha egy virtuális Gépet a növekményes biztonsági mentés alatt áll, és a egy új lemez hozzáadása a virtuális géphez, majd a biztonsági mentési időtartam is túlmutató 24 óra, mivel az újonnan hozzáadott lemezt együtt a meglévő lemezt a változások replikálása a kezdeti replikálás mennek keresztül.
- **Töredezettség**: Biztonsági mentési termék megvizsgálja a növekményes változásokat két biztonsági műveletek között. Biztonsági mentési műveletek a következők: gyorsabb, amikor a módosításokat a lemezen vannak közös elhelyezésű képest a módosítások megállítását között, akkor a lemez. 
- **Vásárlói lemorzsolódás**: Napi adatváltozás (a inkrementális replikálási) nagyobb, mint 200 GB-os lemezenként nagyobb, mint a művelet végrehajtásához ~ 8 órát is igénybe vehet. Ha virtuális gépen egynél több lemezt, és ezeket a lemezeket egyik Backup tart a hosszabb ideig, majd ez hatással lehet a teljes biztonsági mentést (vagy hibát eredményezhet). 
- **Ellenőrzőösszeg összehasonlító (CC) mód**: CC módja viszonylag lassabb, mint a optimalizált mód az azonnali helyreállítási pont Védettként használják. Ha már használja az azonnali helyreállítási pont Védettként, és az 1. szintű pillanatképek törölték, majd biztonsági mentés CC üzemmódra vált a biztonsági mentési művelet meghaladja a 24 óra (vagy sikertelen).

## <a name="restore-considerations"></a>Helyreállítási szempontjai

A visszaállítási művelet áll két fő feladat: adatok másolása a kiválasztott tárfiókban vissza a tárolóból, és létrehozta a virtuális gépet. Adatok másolása a tárolóhoz szükséges idő attól függ, a biztonsági mentések tárolására az Azure és a tárfiók helye. Az adatok másolásához szükséges idő attól függ:

- **Várólista várakozási idő**: Mivel a szolgáltatási folyamatoknak visszaállítási feladatok egyszerre több tárfiókokban, visszaállítási kérések sorba kerülnek.
- **Az adatmásolási idő**: A tárfiók adatokat másolja a tárolóból. Visszaállítás ideje függ a IOPS és átviteli sebessége, a kiválasztott tárfiók, amely az Azure Backup szolgáltatás használ. Nincs betöltve, más alkalmazások írási és olvasási tárfiók kiválasztása a visszaállítási folyamat során a másolási idő csökkentése érdekében.

## <a name="best-practices"></a>Ajánlott eljárások

Javasoljuk, hogy ezeket a gyakorlatokat a virtuális gép biztonsági mentéseinek konfigurálása során a következő:

- Érdemes lehet módosítani az alapértelmezett házirend idő megadott (az ex. Ha az alapértelmezett házirend idő 12:00 -kor, érdemes lehet növekszik, a percek) amikor a pillanatképet készíteni az adatokról biztosításához optimálisan használja az erőforrásokat.
- Prémium szintű virtuális gép biztonsági mentése nem azonnali RP-szolgáltatásról körülbelül 50 %-a teljes tárterület fiók foglal le. A biztonsági mentési szolgáltatás ezen a területen, a pillanatkép másolása ugyanazt a tárfiókot, és áthelyezte azt a tárolót igényel.
- A virtuális gépek visszaállítása egy helyen elérheti, erősen ajánlott különböző használandó [v2-tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) annak érdekében, hogy a célként megadott tárfiók nem leszabályozza. Minden virtuális gép például különböző storage-fiókot (ha 10-es virtuális gépek visszaállítását végzi, akkor fontolja meg 10 különböző storage-fiókok) kell rendelkeznie.
- 1. szintű tárolási réteg (Pillanatkép) a visszaállítások befejeződik a percek alatt (mivel ez ugyanazt a tárfiókot) szemben a 2. szintű tárolási réteget (tároló), amely órát is igénybe vehet. Azt javasoljuk, hogy használjon [azonnali visszaállítása](backup-instant-restore-capability.md) funkció a gyorsabb visszaállítás esetekhez, hol érhetők el az adatok 1. szintű (kell-e az adatokat a tároló lehet visszaállítani, akkor időt vesz igénybe).
- Lemezek száma a határérték képest hogyan nehéz a lemezek IaaS virtuális gépen futó alkalmazások által használt. Győződjön meg arról, ha több lemez egy tárfiókban lévő üzemeltetett. Általános megoldás, ha 5-10 lemezeket vagy több megtalálható az egy tárfiókban, a terhelés kiegyenlítése helyezi el a storage-fiókok egyes lemezek.

## <a name="backup-costs"></a>Biztonsági mentési költségek

Státuszban van az Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).

- A számlázás az első sikeres biztonsági mentés befejezéséig nem indul el. Ezen a ponton a Storage és a védett példányok díjszabása kezdődik.
- A számlázás továbbra is fennáll, mindaddig, amíg nincs a virtuális gép tárolóban tárolt biztonsági mentési adatokat. Ha a virtuális gép védelmének leállítása, de a virtuális gép biztonsági mentési adatok létezik a tárolóban, a számlázás továbbra is.
- Számlázási egy adott virtuális gép leáll, csak akkor, ha a védelem ki van kapcsolva, és az összes biztonsági mentési adatok törlődnek.
- Amikor a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, a legutóbbi sikeres virtuális gép biztonsági mentésének mérete a védett példány mérete, a havi számla használt válik.
- A védett példányok számítási alapul a *tényleges* méret a virtuális gép, amely a virtuális gépet, az átmeneti tárolás kivételével az összes adat összege.
- Díjszabás a tényleges, az adatok lemezen tárolt adatok alapján.
- Virtuális gépek biztonsági mentésének díjszabása az egyes virtuális géphez csatolt adatlemezek maximális támogatott mérete nem alapul.
- Hasonlóképpen a biztonsági mentési tár számlázási tárolt adatokat az Azure Backup, azaz a tényleges adatok az egyes helyreállítási pontokon összege mennyisége alapul.

Vegyünk példának egy Standard a2-es méretű virtuális gépet, amely két további adatlemezek 4 TB-os maximális mérettel. Az alábbi táblázat tartalmazza ezen lemezek tárolja a tényleges adatok:

| Lemez típusa | Maximális méret | Tényleges adatok |
| --------- | -------- | ----------- |
| Operációsrendszer-lemez |4095 GB |17 GB |
| Helyi lemez / ideiglenes lemez |135 GB |5 GB-os (biztonsági mentés nem tartalmazza) |
| Adatlemez 1 |4095 GB |30 GB |
| Adatlemez 2 |4095 GB |0 GB |

- A tényleges méret a virtuális gép virtuális gép ebben az esetben a 17 GB + 30 GB + 0 GB = 47 GB.
- A védett példány mérete (GB-os 47) lesz alapjául a havi számla.
- A virtuális gépet az adatmennyiség növekedésével a védett példány mérete használja a számlázási változásokat ennek megfelelően.


## <a name="next-steps"></a>További lépések

A biztonsági mentési folyamatból és a teljesítménnyel kapcsolatos szempontok áttekintése, után tegye a következőket:

- [Ismerje meg](../virtual-machines/windows/premium-storage-performance.md) hangolása az optimális teljesítmény érdekében az Azure storage-alkalmazásokat. A cikk a premium storage, de célja is a standard szintű tárolólemezek vonatkozik.
- [Első lépések](backup-azure-arm-vms-prepare.md) biztonsági másolat által a virtuális gépek támogatása és korlátozások áttekintése, egy tároló létrehozásának és felkészülés a virtuális gépek biztonsági mentése.
