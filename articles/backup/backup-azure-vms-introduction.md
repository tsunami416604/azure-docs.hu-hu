---
title: Azure-beli virtuális gépek biztonsági mentése
description: További tudnivalók az Azure virtuális gépek biztonsági mentését, és jegyezze fel néhány ajánlott eljárást.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 1e80b2083a2fce90259ac0634d9e7f796f459fcd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880955"
---
# <a name="about-azure-vm-backup"></a>Azure-beli virtuális gépek biztonsági mentése

Ez a cikk azt ismerteti, hogyan a [Azure Backup szolgáltatás](backup-introduction-to-azure-backup.md) Azure-beli virtuális gépek (VM) biztonsági mentését.

## <a name="backup-process"></a>Biztonsági mentési folyamat

Itt látható, hogyan Azure Backup befejeződött a biztonsági mentés az Azure virtuális gépek:

1. Az Azure virtuális gépek biztonsági mentésre kiválasztott Azure Backup elindítja a biztonsági mentési feladatot, adja meg a biztonsági mentési ütemezés szerint.
1. Az első biztonsági mentés során egy biztonsági mentési bővítménynek telepítve van a virtuális gép, ha a virtuális gép fut-e.
    - Windows virtuális gépek esetén a _VMSnapshot_ bővítmény telepítve van.
    - Linux rendszerű virtuális gépekhez a _VMSnapshotLinux_ bővítmény telepítve van.
1. Windows virtuális gépeket futtató, a biztonsági mentés koordinálja a Windows kötet árnyékmásolata szolgáltatás (VSS) egy alkalmazáskonzisztens pillanatképet készíteni a virtuális gép.
    - Alapértelmezés szerint a biztonsági mentés teljes kötetárnyékmásolat vesz igénybe.
    - Ha a biztonsági mentés nem tudja átvenni a egy alkalmazáskonzisztens pillanatképet, majd azt pillanatképet fájlkonzisztens az alapul szolgáló tárolóról (mivel nincs alkalmazásírás, miközben a virtuális gép le van állítva).
1. Linux rendszerű virtuális gépekhez a biztonsági mentés egy fájlkonzisztens biztonsági mentés vesz igénybe. Az alkalmazáskonzisztens pillanatképek kell manuálisan testre szabja az előkészítő/utólagos parancsfájl.
1. Backup elkészítette a pillanatképet, miután az adatok átvitele a tárba. 
    - A biztonsági mentés minden egyes Virtuálisgép-lemez párhuzamos biztonsági mentésével optimalizáltuk.
    - Az egyes lemezek, amelyek másolat készül Azure Backup beolvassa azokat az adatblokkokat a lemezen, és azonosítja, és csak a (különbözeti) az előző biztonsági mentés óta módosított adatok blokkokat továbbítja.
    - A pillanatkép adatainak előfordulhat, hogy nem kell azonnal-tárolóba másol. Ez órát is igénybe vehet bizonyos időszakokban. Virtuális gép teljes biztonsági mentés időpontja a napi biztonsági mentési szabályzatok legfeljebb 24 óra lesz.

1. Az adatátvitel befejeződése után a rendszer eltávolítja a pillanatképet, és a egy helyreállítási pont létrehozása.

![Az Azure virtuális gép biztonsági mentési architektúra](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Azure virtuális gép biztonsági mentéseinek titkosítását

Ha Ön Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással, virtuális gépek inaktív Storage Service Encryption (SSE) vannak titkosítva. Az Azure Backup is készíthet biztonsági másolatot az Azure Disk Encryption használatával titkosított Azure virtuális gépeken.

**Titkosítás** | **Részletek** | **Támogatás**
--- | --- | ---
**Az Azure Disk Encryption** | Az Azure Disk Encryption az Azure virtuális gépek egyaránt operációsrendszer- és adatlemezek titkosítja.<br/><br/> Az Azure Disk Encryption integrálható a BitLocker titkosítási kulcsok (BEKs), amely számára biztosít védelmet a kulcstartóban található titkos kódként. Az Azure Disk Encryption együttműködik az Azure Key Vault-kulcs titkosítási kulcsok (KEk). | Az Azure Backup támogatja a felügyelt és nem felügyelt Azure-beli virtuális csak BEKs, vagy együtt kek BEKs titkosított biztonsági mentés.<br/><br/> Biztonsági mentés és titkosított BEKs és kek is.<br/><br/> Kek és BEKs készül biztonsági másolat, mert a szükséges engedélyekkel rendelkező felhasználók visszaállíthatják a kulcsok és titkos kulcsok a key vault szükség esetén vissza. Ezek a felhasználók is helyreállíthatja a titkosított virtuális Gépet.<br/><br/> A titkosított kulcsokat és titkos kulcsokat jogosulatlan felhasználók vagy az Azure-ban nem olvasható.
**SSE** | SSE technológiával Azure Storage által automatikusan tárolás előtt az adatok titkosítása inaktív adatok titkosítását biztosít. Az Azure Storage is mindig visszafejti az adatokat, beolvasása előtt. | Az Azure Backup SSE inaktív titkosítása az Azure virtuális gépeket használ.

Felügyelt és nem felügyelt Azure-beli virtuális gépek a Backup BEKs csak titkosított virtuális gépeket vagy BEKs kek együtt a titkosított virtuális gépeket is támogatja.

A biztonsági másolat BEKs (titkos adatok) és kek (kulcsok) titkosított. Ezek olvashatók és csak akkor, amikor azok már visszaállított térjen vissza a key vault a jogosult felhasználók. Jogosulatlan felhasználók számára, sem az Azure olvassa el, vagy használja a biztonsági másolatban szereplő kulcsok vagy titkos adatokat.

BEKs is készül biztonsági másolat. Így a BEKs elvesznek, ha hitelesített felhasználók a BEKs visszaállítása a key vaulthoz, és a titkosított virtuális gépek helyreállítása. Csak a szükséges szintű engedélyekkel rendelkező felhasználók is biztonsági mentése és visszaállítása titkosított virtuális gépek vagy a kulcsok és titkos kulcsok.

## <a name="snapshot-creation"></a>Pillanatkép létrehozása

Az Azure Backup biztonsági mentési ütemezés szerint pillanatképeket készít. 

- **Windows virtuális gépek:** Windows virtuális gépek a Backup szolgáltatás koordinálja a VSS egy alkalmazáskonzisztens pillanatképet készíteni a Virtuálisgép-lemezek.

  - Alapértelmezés szerint az Azure Backup elkészítette a teljes VSS biztonsági mentést. [További információk](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Módosítsa a beállítást, hogy az Azure Backup elkészítette a biztonsági másolatok VSS, állítsa be a parancsot a parancssorba a következő beállításkulcsot:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **Linux rendszerű virtuális gépek:** Linux rendszerű virtuális gépek alkalmazáskonzisztens pillanatképek készítése, a Linux előtti parancsfájlt, és utólagos parancsfájl keretrendszer konzisztencia biztosításához a saját egyéni parancsfájlok írása.

  - Az Azure Backup csak az Ön által írt előtti és utáni parancsfájlokat hív meg.
  - Az üzem előtti és utáni parancsfájlok végrehajtása sikertelen, ha az Azure Backup, alkalmazáskonzisztens helyreállítási pontot jelöli meg. Azonban egyéni parancsfájlok használja, amikor Ön az alkalmazáskonzisztencia végső soron felelős.
  - [További](backup-azure-linux-app-consistent.md) parancsfájlok konfigurálásáról.

### <a name="snapshot-consistency"></a>Pillanatkép konzisztencia

A következő táblázat ismerteti a különböző típusú pillanatkép konzisztencia:

**Snapshot** | **Részletek** | **Helyreállítás** | **Consideration**
--- | --- | --- | ---
**Application-consistent** | Alkalmazáskonzisztens biztonsági mentések memória a tartalom és a függőben lévő i/o-műveleteket rögzíti. Alkalmazáskonzisztens pillanatképek használja a VSS-író (vagy Linux előkészítő/utólagos parancsfájl-) biztonsági bekövetkezte előtt az alkalmazás adatok konzisztenciájának biztosítása érdekében. | Az alkalmazáskonzisztens pillanatkép a virtuális gép végzi a helyreállítást, ha a virtuális gép elindul. Nincs, adatvesztés vagy adatsérülés. Az alkalmazások konzisztens állapotban indítsa el. | Windows: Az összes VSS-írók sikeres volt<br/><br/> Linux: Előkészítő/utólagos parancsfájl konfigurált és sikeres volt
**Fájlrendszerkonzisztens** | A fájlrendszer konzisztens biztonsági mentések konzisztencia szolgáltatása pillanatképének az összes fájl egyszerre.<br/><br/> | Ha egy virtuális Gépet a fájlrendszer konzisztens pillanatkép végzi a helyreállítást, a virtuális gép elindul. Nincs, adatvesztés vagy adatsérülés. Alkalmazások kell megvalósítani a saját "javítás felfelé" mechanizmus, amellyel győződjön meg arról, hogy a visszaállított adatok összhangban-e. | Windows: Néhány VSS-írók nem sikerült <br/><br/> Linux: Alapértelmezett (ha előkészítő/utólagos parancsfájl nincs konfigurálva, vagy sikertelen)
**Crash-consistent** | Összeomlás-konzisztens pillanatképekkel általában akkor fordul elő, ha egy Azure virtuális gép leáll a biztonsági mentés idején. Csak azokat az adatokat, amely már létezik a lemezen a biztonsági mentés időpontjában rögzített, és biztonsági mentés.<br/><br/> Összeomlás-konzisztens helyreállítási pont létrehozása nem garantálja az operációs rendszer vagy az alkalmazás az adatok konzisztenciáját. | Garanciát nem jelentenek, bár a virtuális gép általában indul el, és ezután elindítja a lemez ellenőrzi, hogy a sérülés hibák javítása. Memóriában lévő adatokat vagy írási műveleteket, amelyek nem továbbítja a lemezre elvesznek az összeomlás előtt. Alkalmazás valósít meg saját adatainak ellenőrzése. Például egy adatbázis-alkalmazást, a tranzakciós napló az ellenőrzéshez használhatja. Ha a tranzakciós napló olyan bejegyzéseket, amelyek nem szerepelnek az adatbázisban, az adatbázisszoftver tranzakciók összesíti az vissza addig, amíg az adatok összhangban. | Virtuális gép leállítási állapotban van

## <a name="backup-and-restore-considerations"></a>Biztonsági mentési és helyreállítási szempontjai 

**Consideration** | **Részletek**
--- | ---
**Lemez** | Virtuálisgép-lemezek biztonsági mentése párhuzamos. Például ha egy virtuális gép lemezei négy, a Backup szolgáltatás megkísérli párhuzamosan összes négy lemezek biztonsági mentése. Biztonsági mentés: Növekményes (csak a módosított adatokat).
**Ütemezés** |  Biztonsági mentési adatforgalom csökkentése érdekében különböző virtuális gépek biztonsági mentése különböző időpontokban, a nap, és ellenőrizze, hogy az időpontokat nincsenek átfedésben. Virtuális gépek biztonsági mentésének egyszerre hatására a forgalmi torlódásokat.
**Biztonsági mentés előkészítése** | Vegye figyelembe a biztonsági mentés előkészítéséhez szükséges időt. Az előkészítési időt tartalmaz telepítése vagy frissítése a biztonsági mentési bővítményt, és elindítása egy pillanatképet a biztonsági mentési ütemezés szerint.
**Adatátvitel** | Fontolja meg az Azure Backup a növekményes változásokat az előző biztonsági mentés a azonosításához szükséges időt.<br/><br/> A növekményes biztonsági mentés Azure Backup meghatározza a módosításokat a blokk az ellenőrzőösszeg kiszámítása. Egy blokkblob megváltozásakor átvitele a tárba van megjelölve. A szolgáltatás további csökkentése érdekében az adatok átvitele megpróbálja azonosított blokkok elemzi. Kiértékelése a módosult blokkokat, után az Azure Backup-tárolóba továbbítja a módosításokat.<br/><br/> Előfordulhat, hogy a pillanatképének és tárba való másolás közötti késés.<br/><br/> Időszakokban biztonsági mentések a dolgozhatók akár 8 órát is igénybe vehet. Virtuális gép biztonsági mentésének ideje 24 óránál rövidebb a napi biztonsági mentés lesz.
**Kezdeti biztonsági mentés** | Bár a növekményes biztonsági mentések teljes biztonsági mentés időpontja legfeljebb 24 óra, amely nem feltétlenül az esetben az első biztonsági mentés. A kezdeti biztonsági mentés szükséges idő a mérete, az adatok és a biztonsági mentés feldolgozásakor a rendszer függ.
**Állítsa vissza üzenetsorba** | Azure Backup által feldolgozott feladatok visszaállítása több tárfiók egy időben, és a egy üzenetsorban lévő visszaállítási kérések helyezi el.
**Másolat visszaállításához** | A visszaállítási folyamat során adatokat másolja a tárolóból a storage-fiókot.<br/><br/> A teljes visszaállítási idő attól függ, hogy a bemeneti és kimeneti műveletek másodpercenként (IOPS) és az átviteli sebességet, a storage-fiók.<br/><br/> A másolási idő csökkentése érdekében válassza ki a storage-fiók, amely nincs betöltve, más alkalmazások írási és olvasási.

### <a name="backup-performance"></a>Biztonsági mentés teljesítményét

Ezek a gyakori forgatókönyvek hatással lehet a teljes biztonsági mentés időpontja:

- **Új lemez hozzáadása egy védett Azure virtuális géphez:** Ha egy virtuális Gépet a növekményes biztonsági mentés alatt áll, és a egy új lemezt meg van adva, a biztonsági mentés időpontja növekedni fog. A teljes biztonsági mentés ideje az új lemez együtt a meglévő lemezt a változások replikálása a kezdeti replikálás miatt előfordulhat, hogy utolsó 24 óránál hosszabb.
- **Feldarabolt lemezek:** Biztonsági mentési műveletek gyorsabbak, ha lemezt egybefüggően áll rendelkezésre. Ha a módosítások terjednek, és több lemez töredezett, biztonsági mentés lassabb lesz. 
- **Lemez lemorzsolódási:** Lemezek növekményes biztonsági mentés folyamatban van a védett rendelkezik egy több mint 200 GB-os napi adatváltozás, a biztonsági mentés (több mint 8 óra) hosszú időt vehet igénybe végrehajtásához. 
- **Biztonságimásolat-verziók:** Biztonsági mentés (más néven az azonnali visszaállítása verzió) legújabb verzióját használ az több optimalizált ellenőrzőösszeg összehasonlítása, mint a változásokat. De azonnali visszaállításához használja, és a törölt biztonsági mentési pillanatképet, ha a biztonsági mentés vált, amennyiben az ellenőrzőösszeg összehasonlítása. Ebben az esetben a biztonsági mentési művelet lesz haladhatja meg a 24 óra (vagy sikertelen).

## <a name="best-practices"></a>Ajánlott eljárások

Ha a virtuális gép biztonsági mentéseinek konfigurálása esetén, javasoljuk, hogy ezeket az eljárásokat követve:

- A házirendben beállított alapértelmezett ütemezés módosítása Például ha a házirend az alapértelmezett időtartamot 12:00-kor, növelni időzítése néhány percig, hogy optimális használja az erőforrásokat.
- Virtuális gépek biztonsági mentését, hogy prémium szintű tárhelyet használ, javasoljuk a legújabb verziója, az Azure Backup ([azonnali visszaállítása](backup-instant-restore-capability.md)). Ha nem a legújabb verziót futtatja, biztonsági mentés körülbelül 50 százalékát a teljes tárterület lefoglal. A Backup szolgáltatás ezt a helyet, a pillanatkép másolása ugyanazt a tárfiókot, és áthelyezte azt a tárolót igényel.
- Ha a virtuális gépek egy közös tároló az Ön visszaállítása, erősen ajánlott használt különböző [általános célú v2-tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) annak érdekében, hogy leszabályozza a célként megadott tárfiók nem. Minden virtuális gép például rendelkeznie kell egy másik tárfiókot. Például ha 10 virtuális gépek visszaállítását végzi, használjon 10 különböző tárfiókokat.
- A visszaállítás egy általános célú v1 tárolási réteg (Pillanatkép) fog hajtható percek alatt, mert a pillanatkép ugyanabban a tárfiókban található. Az általános célú v2 tárolási réteg (tároló) a visszaállítás órát is igénybe vehet. Olyan esetekben, ahol az adatok érhető el az általános célú v1, azt javasoljuk, hogy használja a [azonnali visszaállítása](backup-instant-restore-capability.md) a gyorsabb visszaállítás szolgáltatást. (Ha az adatokat vissza kell állítani egy tárolót, majd azt fog több időt vesz igénybe.)
- Lemezek száma határértéke, a lemezek szolgáltatásként (IaaS) virtuális gép egy infrastruktúrán futó alkalmazások által használt hogyan erősen viszonyítva. Általános megoldás, ha 5-10 lemezeket vagy több megtalálható az egy tárfiókban, a terhelés kiegyenlítése helyezi el a storage-fiókok egyes lemezek.

## <a name="backup-costs"></a>Biztonsági mentési költségek

Státuszban van az Azure virtuális gépek biztonsági mentése az Azure Backup szolgáltatással [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).

A számlázás nem indul el, az első sikeres biztonsági mentés befejezéséig. Ezen a ponton a storage és a védett virtuális gépek használati díjának felszámolása kezdődik. A számlázás továbbra is fennáll, mindaddig, amíg a virtuális gép biztonsági mentési adatok tárolása egy tárolóban történik. Ha leállítja a virtuális gép védelmét, de a virtuális gép biztonsági mentési adatok létezik a tárolóban, a számlázás továbbra is.

Számlázási egy adott virtuális gép leáll, csak akkor, ha a védelem ki van kapcsolva, és az összes biztonsági mentési adatok törlődnek. Amikor a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, a legutóbbi sikeres virtuális gép biztonsági mentésének mérete a védett példány mérete, a havi számla használt válik.

A védett példány mérete kiszámítása alapján történik a *tényleges* a virtuális gép méretét. A virtuális gép mérete egyezik meg a virtuális Gépet, az átmeneti tárolás kivételével az összes adatot. Díjszabás, amely rendelkezik az adatok lemezen tárolja a nem az a maximális támogatott mérete az egyes adatlemezek, amely a virtuális Géphez van csatlakoztatva, a tényleges adatok alapján.

Hasonlóképpen a biztonsági mentési tár számlázási tárolt adatokat az Azure Backup, azaz a tényleges adatok az egyes helyreállítási pontokon összege mennyisége alapul.

Vegyünk példának egy A2-Standard méretű virtuális Gépet, amely két további adatlemezek 4 TB-os maximális mérettel rendelkezik. Az alábbi táblázat a tényleges adatok ezen lemezek tárolja:

**Lemez** | **Max. mérete** | **Tényleges adatok**
--- | --- | ---
Operációsrendszer-lemez | 4095 GB | 17 GB
Helyi/ideiglenes lemez | 135 GB | 5 GB-os (biztonsági mentés nem tartalmazza)
Adatlemez 1 | 4095 GB | 30 GB
Adatlemez 2 | 4095 GB | 0 GB

A tényleges méret a virtuális gép ebben az esetben a 17 GB + 30 GB + 0 GB = 47 GB. A védett példány mérete (GB-os 47) lesz alapjául a havi számla. A virtuális Gépet az adatmennyiség növekedésével a védett példány mérete a számlázási változásokat egyeztetéséhez használt.

## <a name="next-steps"></a>További lépések

Most [Azure virtuális gépek biztonsági mentésének előkészítése](backup-azure-arm-vms-prepare.md).
