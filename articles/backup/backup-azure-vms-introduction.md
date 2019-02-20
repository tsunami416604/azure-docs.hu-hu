---
title: Azure virtuális gépek biztonsági mentéséről
description: További tudnivalók az Azure virtuális gépek biztonsági mentését, és jegyezze fel néhány ajánlott eljárást.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: c331c3617f421c913abbc3554aa5ba17e86cb978
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429179"
---
# <a name="about-azure-vm-backup"></a>Azure virtuális gépek biztonsági mentéséről

Ez a cikk azt ismerteti, hogyan a [Azure Backup szolgáltatás](backup-introduction-to-azure-backup.md) Azure virtuális gépek biztonsági mentését.

## <a name="backup-process"></a>Biztonsági mentési folyamat

Itt látható, hogyan Azure Backup befejeződött a biztonsági mentés az Azure virtuális gépek.

1. Az Azure virtuális gépek biztonsági mentésre kijelölt az Azure Backup szolgáltatás biztonsági mentési feladat a megadott biztonsági mentési ütemterv szerint indítja el.
2. Az első biztonsági mentés során egy biztonsági mentési bővítményt telepítve van a virtuális Gépen fut, ha.
    - Windows virtuális gépek a _VMSnapshot_ bővítmény telepítve van.
    - Linux rendszerű virtuális gépekhez a _VMSnapshotLinux_ bővítmény telepítve van.
3. Windows virtuális gépeket futtató, a biztonsági mentés a virtuális gép alkalmazáskonzisztens pillanatképet a VSS koordinálja.
    - Alapértelmezés szerint a biztonsági mentés teljes kötetárnyékmásolat vesz igénybe.
    - Ha biztonsági mentés nem sikerült egy alkalmazáskonzisztens pillanatképet, majd vesz igénybe az alapul szolgáló tárolóról fájlkonzisztens pillanatképet (mivel nincs alkalmazásírás, miközben a virtuális gép le van állítva).
4. Linux rendszerű virtuális gépek biztonsági mentési vesz igénybe egy fájlkonzisztens biztonsági mentésre. Az alkalmazáskonzisztens pillanatképek kell manuálisan testre szabja az előkészítő/utólagos parancsfájl.
5. A pillanatkép elkészítésének után az adatok átkerülnek a tárolóba. 
    - Biztonsági mentés minden egyes Virtuálisgép-lemez párhuzamos biztonsági mentésével optimalizáltuk.
    - Biztonsági mentés alatt álló lemezek Azure Backup beolvassa azokat az adatblokkokat a lemezen, és azonosítja és továbbítja a csak az adatblokkok, amely az előző biztonsági mentés (különbözeti) óta megváltozott.
    - Miután a pillanatképet készít, az adatok átkerülnek a tárolóba.
    - A pillanatkép adatainak előfordulhat, hogy nem kell azonnal-tárolóba másol. Ez órát is igénybe vehet bizonyos időszakokban. Egy virtuális gép teljes biztonsági mentés ideje lesz, kisebb a napi biztonsági mentési házirendeket, hogy 24 óra.

6. Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

![Az Azure virtuális gép biztonsági mentési architektúra](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encrypting-azure-vm-backups"></a>Azure virtuális gép biztonsági másolatok titkosítása

Ha Ön Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással, virtuális gépek inaktív Storage Service Encryption (SSE) vannak titkosítva. Emellett az Azure Backup is az Azure virtuális gépek biztonsági mentése az Azure Disk Encryption (ADE) használatával titkosított.


**Titkosítás** | **Részletek** | **Támogatás**
--- | --- | ---
**ADE** | ADE titkosítja az Azure virtuális gépek egyaránt operációsrendszer- és adatlemezek titkosítja.<br/><br/> ADE hogyan gondoskodik a védelmükről a kulcstartóban található titkos adatait, mint a BitLocker-titkosítási kulcsok (blokktitkosítási kulcsot), vagy az Azure Key Vault kulcstitkosítási kulcs-(KEK) integrálható. | Az Azure Backup támogatja a felügyelt és nem felügyelt Azure-beli virtuális csak a blokktitkosítási kulcsot, vagy rendelkeznek BEk-KEL és KEK titkosított biztonsági mentés.<br/><br/> Mindkét rendelkeznek BEk-KEL és és a biztonsági mentés és a titkosított.<br/><br/> Mivel KEK és rendelkeznek BEk-KEL készül biztonsági másolat, ha a szükséges engedélyekkel rendelkező felhasználók is visszaállítani kulcsok és titkos kulcsok a key vaultban, és a titkosított virtuális gép helyreállításához.<br/><br/> A titkosított kulcsokat és titkos kulcsokat jogosulatlan felhasználók vagy az Azure-ban nem olvasható.
**SSE** | Az SSE az Azure storage biztosítja, hogy a titkosítás inaktív adatok titkosítása automatikusan az tárolás előtt, és visszafejti őket a lekérés előtt. | Az Azure Backup SSE az inaktív adatok titkosítása az Azure-beli virtuális használ.

- A BitLocker titkosítási kulcs (rendelkeznek BEk-KEL) csak és a Blokktitkosítási kulcs titkosítási Key(KEK) együtt titkosított virtuális gépek biztonsági mentése támogatott, felügyelt és nem felügyelt Azure-beli virtuális gépek.
- A biztonsági mentése (titkos adatok) rendelkeznek BEk-KEL és KEK (kulcsok) titkosított. Ezek olvashatók és csak akkor, amikor a jogosult felhasználók vissza a key vault visszaállítva. Jogosulatlan felhasználók és az Azure nem tudja olvasni, vagy használja biztonsági mentése a kulcsok vagy titkos adatokat.
- A blokktitkosítási kulcsot is biztonsági másolatot, ha megszakad a blokktitkosítási kulcsot, mert jogosult felhasználók visszaállíthatja a blokktitkosítási kulcsot az a KeyVault, és a titkosított virtuális gép helyreállítása. 
- Csak a megfelelő szintű engedélyeket is biztonsági mentése és visszaállítása titkosított virtuális gépek, valamint a kulcsok és titkok.



## <a name="taking-snapshots"></a>Pillanatképek készítése

Az Azure a biztonsági mentési pillanatképek a biztonsági mentési ütemterv szerint. 

- **Windows virtuális gépek**: Windows virtuális gépek a Backup szolgáltatás koordinálja a a kötet árnyékmásolata másolási szolgáltatás (VSS) egy alkalmazáskonzisztens pillanatképet készíteni a Virtuálisgép-lemezek.
    - Alapértelmezés szerint az Azure Backup elkészítette a teljes VSS biztonsági mentést. [További információk](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Ha szeretné módosítani a beállítást, hogy az Azure biztonsági mentések VSS másolásos biztonsági mentéshez szükséges, állítsa be a következő beállításkulcsot a parancssorból: **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **Linux rendszerű virtuális gépek**: Szeretne Linux rendszerű virtuális gépek alkalmazáskonzisztens pillanatképek készítése, ha a Linux előtti parancsfájlt, és utólagos parancsfájl keretrendszer konzisztencia biztosításához a saját egyéni parancsfájlok írása.
    -  Az Azure Backup csak az Ön által írt előtti/utáni parancsfájlok hív meg.
    - A szkript előtti és utáni szkriptek végrehajtása sikertelen, ha az Azure Backup, alkalmazáskonzisztens helyreállítási pontot jelöli meg. Azonban Ön végső soron felelős az alkalmazáskonzisztencia egyéni parancsfájlok használata esetén.
    - [További](backup-azure-linux-app-consistent.md) parancsfájlok konfigurálásáról.


### <a name="snapshot-consistency"></a>Pillanatkép konzisztencia

A következő táblázat ismerteti a különböző típusú konzisztencia.

**Snapshot** | **Részletek** | **Helyreállítás** | **Consideration**
--- | --- | --- | ---
**Application-consistent** | Alkalmazáskonzisztens biztonsági mentések memória a tartalom és a függőben lévő i/o-műveleteket rögzíti. Alkalmazáskonzisztens pillanatképek használja a VSS-író (vagy Linux-előkészítő/utólagos parancsfájl), amely az alkalmazás adatok konzisztenciájának biztosítása előtt a biztonsági másolat. | Az alkalmazáskonzisztens pillanatkép helyreállításakor a virtuális gép elindul. Nincs, adatvesztés vagy adatsérülés. Az alkalmazások konzisztens állapotban indítsa el. | Windows: Az összes VSS-írók sikeres volt<br/><br/> Linux: Előkészítő/utólagos parancsfájl konfigurált és sikeres volt
**Fájlrendszerkonzisztens** | Alkalmazáskonzisztens biztonsági másolatainak tartalmazó fájlokat alkalmazáskonzisztens biztonsági mentései szolgáltatása pillanatképének az összes fájl egyszerre.<br/><br/> | A fájlkonzisztens pillanatképek helyreállításakor a virtuális gép elindul. Nincs, adatvesztés vagy adatsérülés. Alkalmazások kell megvalósítani a saját "javítás felfelé" mechanizmus, amellyel győződjön meg arról, hogy a visszaállított adatok összhangban-e. | Windows: Néhány VSS-írók nem sikerült <br/><br/> Linux: Alapértelmezett (ha előkészítő/utólagos parancsfájl nincs konfigurálva, vagy sikertelen)
**Crash-consistent** | Az összeomlási konzisztencia gyakran akkor történik, ha egy Azure virtuális gép leáll a biztonsági mentés idején.  Csak azokat az adatokat, amely már létezik a lemezen a biztonsági mentés időpontjában rögzített, és biztonsági mentés.<br/><br/> Összeomlás-konzisztens helyreállítási pont létrehozása nem garantálja az operációs rendszer vagy az alkalmazás az adatok konzisztenciáját. | Nincsenek nem garantálja, de általában a virtuális gép indítása, és a egy lemezt a következőképpen ellenőrizze, hogy a sérülés hibák javítása. Memóriában lévő adatokat vagy írási, nem továbbított lemez elvesznek. Alkalmazás valósít meg saját adatainak ellenőrzése. Például egy adatbázis-alkalmazások esetén a tranzakciónapló olyan bejegyzéseket, amelyek nem szerepelnek az adatbázisban, ha az adatbázis-szoftver állapotfigyelőket amíg adatok nem konzisztens. | Virtuális gép leállítási állapotban van


## <a name="restore-considerations"></a>Helyreállítási szempontjai 



**Consideration** | **Részletek**
--- | ---
**Lemez** | Virtuálisgép-lemezek biztonsági mentése párhuzamos. Például ha egy virtuális gép lemezei négy, a szolgáltatás megkísérli párhuzamosan összes négy lemezek biztonsági mentése. Biztonsági mentés: Növekményes (csak a módosított adatokat).
**Ütemezés** |  A biztonsági mentési forgalom, a nap, nincsenek átfedésben van a különböző időpontokban különböző virtuális gépek biztonsági mentése csökkentése érdekében. Virtuális gépek biztonsági mentésének egyszerre hatására a forgalmi torlódásokat.
**Biztonsági mentés előkészítése** | Fontolja meg a biztonsági mentés előkészítő időpontot, amely tartalmazza a telepítése vagy frissítése a biztonsági mentési bővítményt, és a egy pillanatképet a biztonsági mentési ütemterv szerint elindítása.
**Adatátvitel** | Biztonsági mentési szolgáltatásban a növekményes változásokat az előző biztonsági mentés a kiszámításához szükséges idő.<br/><br/> A növekményes biztonsági mentés a blokk ellenőrzőösszege a módosításokat a szolgáltatás számítógépek meghatározása. Ha egy blokk-tárolóba küldéséhez megváltozott az azonosított. A szolgáltatás működése során használt azonosított blokkokra kísérli meg az adatok vihetők át további minimalizálása érdekében. Letiltott kiértékelése minden módosítása után a módosítások átkerülnek a tárolóba.<br/><br/> Előfordulhat, hogy a pillanatképének és tárba való másolás közötti késés.<br/><br/> Időszakokban biztonsági mentésekhez legfeljebb folyamat akár 8 órát is igénybe vehet. Virtuális gép biztonsági mentésének ideje 24 óránál rövidebb a napi biztonsági mentés lesz.
**Kezdeti biztonsági mentés** | Bár a teljes biztonsági mentési idő kevesebb, mint 24 órányi növekményes biztonsági mentések esetében érvényes, nem lehet az első biztonsági mentés. Szükséges idő mérete az adatok és a biztonsági mentés készül függ.
**Állítsa vissza üzenetsorba** | Azure Backup által feldolgozott feladatok visszaállítása több tárfiók egy időben, és a egy üzenetsorban lévő visszaállítási kérések kerüljenek.
**Másolat visszaállításához** | A visszaállítási folyamat során adatokat másolja a tárolóból a storage-fiókot.<br/><br/> Visszaállítás ideje függ a IOPS és átviteli sebesség, a storage-fiók.<br/><br/> A másolási idő csökkentése érdekében válassza ki a storage-fiók, amely nincs betöltve, más alkalmazások írási és olvasási.


### <a name="backup-performance"></a>Biztonsági mentés teljesítményét

Ezek a gyakori forgatókönyvek hatással lehetnek a biztonsági mentés időpontja:

- Adjon hozzá egy új lemezt a védett Azure virtuális gépek: Ha egy virtuális Gépet a növekményes biztonsági mentés alatt áll, és a egy új lemezt meg van adva, biztonsági mentés több mint 24 órával az új lemez együtt a meglévő lemezt a változások replikálása a kezdeti replikálás miatt előfordulhat, hogy utolsó.
- Feldarabolt lemezek: Biztonsági mentési műveletek akkor gyorsabb, ha a lemezen változások vannak közös elhelyezésű. Ha a módosítások terjednek, és több lemez töredezett, biztonsági mentés lassabb lesz. 
- Lemez lemorzsolódási: Ha a növekményes biztonsági mentés alatt álló védett lemezek egy több mint 200 GB-os napi adatváltozás, a biztonsági mentés is igénybe vehet egy hosszú ideig (több mint 8 óra). 
- Biztonságimásolat-verziók: Biztonsági mentés (más néven az azonnali visszaállítása verzió) legújabb verzióját használja, összehasonlítása a változások ellenőrzőösszeg összehasonlítása, mint egy több optimalizált folyamatot használ. Ha a legújabb verziót használja, és a törölt biztonsági mentési pillanatképet, a biztonsági mentési kapcsolók ellenőrzőösszeg összehasonlítása, illetve a biztonsági mentési művelet lesz haladhatja meg a 24 óra (vagy sikertelen).

## <a name="best-practices"></a>Ajánlott eljárások 
Javasoljuk, hogy ezeket a gyakorlatokat a virtuális gép biztonsági mentéseinek konfigurálása során a következő:

- Vegye figyelembe, hogy a házirend az alapértelmezett ütemezés ideje módosítása. Például ha az alapértelmezett érték a szabályzat a 12:00 -kor, érdemes növekszik percek, így optimális használja az erőforrásokat.
- Prémium szintű virtuális gép biztonsági mentése nem azonnali RP-szolgáltatásról körülbelül 50 %-a teljes tárterület fiók foglal le. A biztonsági mentési szolgáltatás ezen a területen, a pillanatkép másolása ugyanazt a tárfiókot, és áthelyezte azt a tárolót igényel.
- A virtuális gépek visszaállítása egy helyen elérheti, erősen ajánlott különböző használandó [v2-tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) annak érdekében, hogy a célként megadott tárfiók nem leszabályozza. Minden virtuális gép például különböző storage-fiókot (ha 10-es virtuális gépek visszaállítását végzi, akkor fontolja meg 10 különböző storage-fiókok) kell rendelkeznie.
- 1. szintű tárolási réteg (Pillanatkép) a visszaállítások befejeződik a percek alatt (mivel ez ugyanazt a tárfiókot) szemben a 2. szintű tárolási réteget (tároló), amely órát is igénybe vehet. Azt javasoljuk, hogy használjon [azonnali visszaállítása](backup-instant-restore-capability.md) funkció a gyorsabb visszaállítás esetekhez, hol érhetők el az adatok 1. szintű (kell-e az adatokat a tároló lehet visszaállítani, akkor időt vesz igénybe).
- Lemezek száma a határérték képest hogyan nehéz a lemezek IaaS virtuális gépen futó alkalmazások által használt. Győződjön meg arról, ha több lemez egy tárfiókban lévő üzemeltetett. Általános megoldás, ha 5-10 lemezeket vagy több megtalálható az egy tárfiókban, a terhelés kiegyenlítése helyezi el a storage-fiókok egyes lemezek.

## <a name="backup-costs"></a>Biztonsági mentési költségek

Státuszban van az Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).

- A számlázás nem indul el, amíg be nem fejeződik az első sikeres biztonsági mentés. Ezen a ponton a storage és a védett virtuális gépek használati díjának felszámolása kezdődik.
- A számlázás továbbra is fennáll, mindaddig, amíg nincs a virtuális gép tárolóban tárolt biztonsági mentési adatokat. Ha leállítja a virtuális gép védelmét, de a virtuális gép biztonsági mentési adatok létezik a tárolóban, a számlázás továbbra is.
- Számlázási egy adott virtuális gép leáll, csak akkor, ha a védelem ki van kapcsolva, és az összes biztonsági mentési adatok törlődnek.
- Amikor a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, a legutóbbi sikeres virtuális gép biztonsági mentésének mérete a védett példány mérete, a havi számla használt válik.
- A védett példányok számítási alapul a *tényleges* méret a virtuális gép, amely a virtuális Gépet, az átmeneti tárolás kivételével az összes adat összege.
- Díjszabás a tényleges, az adatok lemezen tárolt adatok alapján.
- Virtuális gépek biztonsági mentésének díjszabása az egyes virtuális Géphez csatlakoztatott adatlemezek maximális támogatott mérete nem alapul.
- Hasonlóképpen a biztonsági mentési tár számlázási tárolt adatokat az Azure Backup, azaz a tényleges adatok az egyes helyreállítási pontokon összege mennyisége alapul.

Vegyünk példának egy Standard a2-es méretű virtuális Gépet, amely két további adatlemezek 4 TB-os maximális mérettel rendelkezik. Az alábbi táblázat tartalmazza ezen lemezek tárolja a tényleges adatok:

**Lemez** | **Max. mérete** | **Tényleges adatok**
--- | --- | ---
Operációsrendszer-lemez | 4095 GB | 17 GB 
Helyi/ideiglenes lemez | 135 GB | 5 GB-os (biztonsági mentés nem tartalmazza) 
Adatlemez 1 | 4095 GB | 30 GB 
Adatlemez 2 | 4095 GB | 0 GB 

- A tényleges méret a virtuális gép ebben az esetben a 17 GB + 30 GB + 0 GB = 47 GB.
- A védett példány mérete (GB-os 47) lesz alapjául a havi számla.
- A virtuális Gépet az adatmennyiség növekedésével a védett példány mérete használja a számlázási változásokat ennek megfelelően.


## <a name="next-steps"></a>További lépések

Most [előkészítése](backup-azure-arm-vms-prepare.md) Azure VM backup esetében.
