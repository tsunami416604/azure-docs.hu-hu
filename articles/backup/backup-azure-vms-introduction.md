---
title: Azure-beli virtuális gépek biztonsági mentése
description: Ebben a cikkben megtudhatja, hogy az Azure Backup szolgáltatás hogyan készít biztonsági másolatot az Azure virtuális gépekről, és hogyan követheti az ajánlott eljárásokat.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415967"
---
# <a name="an-overview-of-azure-vm-backup"></a>Az Azure virtuális gépek biztonsági mentésének áttekintése

Ez a cikk ismerteti, hogy az [Azure Backup szolgáltatás](backup-introduction-to-azure-backup.md) hogyan biztonsági másolatot készít az Azure virtuális gépekről(VM).

Az Azure Backup független és elkülönített biztonsági mentéseket biztosít a virtuális gépeken lévő adatok nem kívánt megsemmisítése ellen. A biztonsági mentések a helyreállítási pontok beépített kezelésével rendelkező Recovery Services-tárolóban tárolódnak. A konfiguráció és a méretezés egyszerű, a biztonsági mentések optimalizáltak, és szükség szerint könnyen visszaállítható.

A biztonsági mentési folyamat részeként egy [pillanatkép készül,](#snapshot-creation)és az adatok átvitele a Recovery Services-tárolóba, az éles számítási feladatokra gyakorolt hatás nélkül. A pillanatkép az [itt](#snapshot-consistency)leírtak szerint különböző konzisztenciaszinteket biztosít.

Az Azure Backup is rendelkezik speciális ajánlatok at adatbázis-számítási feladatok, például [az SQL Server](backup-azure-sql-database.md) és az SAP [HANA,](sap-hana-db-about.md) amelyek a munkaterhelés-tudatos, 15 perces RPO (helyreállítási pont cél), és lehetővé teszi az egyes adatbázisok biztonsági mentését és visszaállítását.

## <a name="backup-process"></a>A biztonsági mentés folyamata

Az Azure Biztonsági másolat az alábbiakszerint készítbiztonsági mentést az Azure virtuális gépekről:

1. A biztonsági mentéshez kiválasztott Azure-beli virtuális gépek esetében az Azure Backup a megadott biztonsági mentési ütemezésnek megfelelően indít el egy biztonsági mentési feladatot.
1. Az első biztonsági mentés során egy biztonsági mentési bővítmény van telepítve a virtuális gépre, ha a virtuális gép fut.
    - Windows virtuális gépek esetén a [VMSnapshot bővítmény](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows) telepítve van.
    - Linuxos virtuális gépek esetén a [VMSnapshotLinux-bővítmény](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux) telepítve van.
1. Futó Windows-virtuális gépek esetén a Biztonsági másolat a Windows kötet árnyékmásolata szolgáltatásával (VSS) való koordinátákat készíthet a virtuális gép alkalmazáskonzisztens pillanatképének készítéséhez.
    - Alapértelmezés szerint a Biztonsági másolat teljes VSS biztonsági mentést készít.
    - Ha a biztonsági mentés nem tud egy alkalmazás-konzisztens pillanatképet készíteni, majd fájlkonzisztens pillanatképet készít az alapul szolgáló tárolóról (mivel a virtuális gép leállítása közben egyetlen alkalmazás írási művelet sem fordul elő).
1. Linuxos virtuális gépek esetén a biztonsági mentés fájlkonzisztens biztonsági mentést készít. Az alkalmazáskonzisztens pillanatképekhez manuálisan kell testreszabnia az elő-/postai parancsfájlokat.
1. Miután a biztonsági mentés elvezeti a pillanatképet, továbbítja az adatokat a tárolóba.
    - A biztonsági mentés úgy van optimalizálva, hogy az egyes virtuális géplemezek párhuzamosan biztonsági mentést készít.
    - Minden egyes lemez, amely a biztonsági mentés t, az Azure Backup beolvassa a blokkokat a lemezen, és azonosítja és továbbítja csak az adatblokkok, amelyek megváltoztak (a delta) az előző biztonsági mentés óta.
    - Előfordulhat, hogy a pillanatkép adatok nem azonnal másolja a tárolóba. Csúcsidőben órákba telhet. A virtuális gép teljes biztonsági mentési ideje kevesebb lesz, mint 24 óra a napi biztonsági mentési szabályzatok esetében.
1. A Windows virtuális gépeken az Azure Backup engedélyezése után végrehajtott módosítások a következők:
    - Microsoft Visual C++ 2013 Terjeszthető(x64) - 12.0.40660 telepítve van a virtuális gép
    - A Kötet árnyékmásolata szolgáltatás (VSS) indítási típusa manuálisról automatikusra változott
    - Az IaaSVmProvider Windows szolgáltatás hozzáadva

1. Amikor az adatátvitel befejeződött, a pillanatkép törlődik, és létrejön egy helyreállítási pont.

![Az Azure virtuális gép biztonsági mentési architektúrája](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Az Azure virtuális gép biztonsági másolatainak titkosítása

Amikor az Azure-beli virtuális gépekről biztonsági másolatot készít az Azure Backup szolgáltatással, a virtuális gépek titkosítva vannak a storage-szolgáltatás titkosításával (SSE). Az Azure Backup is biztonsági másolatot készíthet az Azure lemeztitkosítással titkosított Azure-beli virtuális gépekről.

**Titkosítás** | **Részletek** | **Támogatás**
--- | --- | ---
**Azure Disk Encryption** | Az Azure Disk Encryption titkosítja az operációs rendszer és az azure-beli virtuális gépek adatlemezeit is.<br/><br/> Az Azure Disk Encryption integrálható a BitLocker titkosítási kulcsokkal (BEKs), amelyek titkos kulcsként védettek a kulcstartóban. Az Azure Disk Encryption az Azure Key Vault kulcstitkosítási kulcsokkal (KEK) is integrálható. | Az Azure Backup támogatja a felügyelt és nem felügyelt Azure-beli virtuális gépek biztonsági mentését, amelyeket csak BEK-k vagy BEK-k és KEK-k együtt titkosítanak.<br/><br/> Mind a BEKs és KEKs biztonsági másolatot és titkosított.<br/><br/> Mivel a KEK-kről és a BEK-król biztonsági mentés készül, a szükséges engedélyekkel rendelkező felhasználók szükség esetén visszaállíthatják a kulcsokat és a titkos kulcsokat a key vaultba. Ezek a felhasználók is visszaállíthatja a titkosított virtuális gép.<br/><br/> A titkosított kulcsokat és titkos kulcsokat nem olvashatják a jogosulatlan felhasználók vagy az Azure.
**Sse** | Az SSE-vel az Azure Storage inaktív titkosítást biztosít az adatok automatikus titkosításával, mielőtt tárolnák azokat. Az Azure Storage is visszafejti az adatokat, mielőtt beolvasná azokat. | Az Azure Backup sse-t használ az Azure virtuális gépek nyugalmi titkosításához.

Felügyelt és nem felügyelt Azure-beli virtuális gépek esetén a biztonsági mentés támogatja a csak BEK-val titkosított virtuális gépeket, vagy a BEK-kkel titkosított virtuális gépeket a KEK-ekkel együtt.

A biztonsági másolatot alátámasztott BEK-ek (titkos kulcsok) és a kEK-k (kulcsok) titkosítottak. Csak akkor olvashatók és használhatók, ha a jogosult felhasználók visszaállítják őket a key vaultba. Sem a jogosulatlan felhasználók, sem az Azure nem olvashatnak és nem használhatnak biztonsági másolatot vagy titkos kulcsokat.

A BEK-król is biztonsági másolatot kell tenni. Így ha a BEK-k elvesznek, a jogosult felhasználók visszaállíthatják a BEK-ket a key vaultba, és visszaállíthatják a titkosított virtuális gépeket. Csak a szükséges szintű engedélyekkel rendelkező felhasználók tudnak biztonsági másolatot és visszaállítást a titkosított virtuális gépekről vagy kulcsokról és titkos kulcsokról.

## <a name="snapshot-creation"></a>Pillanatkép létrehozása

Az Azure Backup pillanatképeket készít a biztonsági mentésütemezés nek megfelelően.

- **Windows virtuális gépek:** Windows virtuális gépek esetén a biztonsági mentési szolgáltatás koordinálja a VSS-t, hogy a virtuális gép lemezei alkalmazáskonzisztens pillanatképet készítsenek.  Alapértelmezés szerint az Azure Backup teljes VSS biztonsági mentést készít (csonkolja az alkalmazás naplóit, például az SQL Servert a biztonsági mentés időpontjában, hogy alkalmazásszintű konzisztens biztonsági mentést kapjon).  Ha egy SQL Server-adatbázist használ az Azure VM biztonsági mentésén, majd módosíthatja a beállítást, hogy a VSS-másolás biztonsági mentés (a naplók megőrzése). További információt [ebben a cikkben](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues)talál.

- **Linux os virtuális gépek:** A Linux-virtuális gépek alkalmazáskonzisztens pillanatképeinek készítéséhez használja a Linux elő- és parancsfájl utáni keretrendszert a saját egyéni parancsfájlok írásához a konzisztencia érdekében.

  - Az Azure Backup csak az Ön által írt előre//közzé parancsfájlokat hívja meg.
  - Ha az előzetes parancsfájlok és a parancsfájlok utáni sikeresen végrehajtás, az Azure Backup megjelöli a helyreállítási pontot, mint alkalmazás-konzisztens. Azonban egyéni parancsfájlok használata esetén végső soron ön felelős az alkalmazás konzisztenciájáért.
  - [További információ](backup-azure-linux-app-consistent.md) a parancsfájlok konfigurálásáról.

## <a name="snapshot-consistency"></a>Pillanatkép konzisztenciája

Az alábbi táblázat a pillanatképek konzisztenciájának különböző típusait ismerteti:

**Pillanatkép** | **Részletek** | **Helyreállítás** | **Szempont**
--- | --- | --- | ---
**Alkalmazás-konzisztens** | Az alkalmazáskonzisztens biztonsági mentések rögzítik a memória tartalmát és a függőben lévő I/O-műveleteket. Az alkalmazáskonzisztens pillanatképek vss-írót (vagy linuxos parancsfájlok előtti/közzétételét) használják az alkalmazásadatok konzisztenciájának biztosításához, mielőtt biztonsági mentés történik. | Amikor egy virtuális gép helyreállítása egy alkalmazás-konzisztens pillanatkép, a virtuális gép elindul. Nincs adatsérülés vagy veszteség. Az alkalmazások konzisztens állapotban kezdődnek. | Windows: Minden VSS-író sikeresvolt<br/><br/> Linux: Az előre/poszt parancsfájlok konfigurálva vannak és sikeresek
**Konzisztens fájlrendszer** | A fájlrendszerkonzisztens biztonsági mentések konzisztenciát biztosítanak azáltal, hogy egyszerre készítik el az összes fájl pillanatképét.<br/><br/> | Amikor egy fájlrendszerkonzisztens pillanatképpel helyreállítása egy virtuális gép, a virtuális gép elindul. Nincs adatsérülés vagy veszteség. Az alkalmazásoknak saját "javítási" mechanizmust kell megvalósítaniuk, hogy a visszaállított adatok konzisztensek legyenek. | Windows: Néhány VSS-író nem sikerült <br/><br/> Linux: Alapértelmezett (ha az előzetes/poszt parancsfájlok nincsenek konfigurálva vagy sikertelenek)
**Összeomlás-konzisztens** | Összeomlás-konzisztens pillanatképek általában akkor fordul nak elő, ha egy Azure-beli virtuális gép leáll a biztonsági mentés idején. A rendszer csak azokat az adatokat rögzíti és készíti biztonsági másolatba, amelyek a biztonsági mentés időpontjában már léteznek a lemezen. | A virtuális gép rendszerindítási folyamatával kezdődik, majd egy lemezellenőrzéssel a sérülési hibák kijavítása érdekében. A memóriában lévő adatok vagy írási műveletek, amelyek nem kerültek át a lemezre, mielőtt az összeomlás elveszett. Az alkalmazások saját adatellenőrzést hajtanak végre. Egy adatbázis-alkalmazás például használhatja a tranzakciós naplóját ellenőrzésre. Ha a tranzakciós napló olyan bejegyzéseket tartalmaz, amelyek nem szerepelnek az adatbázisban, az adatbázis-szoftver visszaállítja a tranzakciókat, amíg az adatok konzisztensek nem lesznek. | A virtuális gép levan állítva (leállítva/felszabadított) állapotban van.

## <a name="backup-and-restore-considerations"></a>Biztonsági mentési és visszaállítási szempontok

**Szempont** | **Részletek**
--- | ---
**Disk** | A virtuális gép lemezek biztonsági mentése párhuzamos. Ha például egy virtuális gép négy lemezzel rendelkezik, a biztonsági mentési szolgáltatás megkísérli a négy lemez párhuzamos biztonsági mentését. A biztonsági mentés növekményes (csak módosított adatok).
**Ütemezés** |  A biztonsági mentési forgalom csökkentése érdekében készítsen biztonsági másolatot a különböző virtuális gépekről a nap különböző időpontjaiban, és győződjön meg arról, hogy az idők nem fedik át egymást. A virtuális gépek biztonsági mentése egyidejűleg forgalmi dugókat okoz.
**Biztonsági mentések előkészítése** | Ne feledje, hogy a biztonsági mentés előkészítéséhez szükséges idő szükséges. Az előkészítési idő magában foglalja a biztonsági mentési bővítmény telepítését vagy frissítését, és a biztonsági mentésütemezés nek megfelelő pillanatkép indítását.
**Adatátvitel** | Fontolja meg az Azure Backup számára szükséges időt az előző biztonsági mentés növekményes változásainak azonosításához.<br/><br/> Egy növekményes biztonsági mentésben az Azure Backup határozza meg a módosításokat a blokk ellenőrzőösszegének kiszámításával. Ha egy blokk ot módosítanak, az átkerül a tárolóba. A szolgáltatás elemzi az azonosított blokkokat, hogy megpróbálja tovább minimalizálni az átvinni kívánt adatok mennyiségét. A módosított blokkok kiértékelése után az Azure Backup átviszi a módosításokat a tárolóba.<br/><br/> Lehet, hogy van egy késés a pillanatkép készítése és a tárolóba másolása között.<br/><br/> Csúcsidőben akár nyolc órát is igénybe vehet a biztonsági mentések feldolgozása. A virtuális gép biztonsági mentési ideje kevesebb lesz, mint 24 óra a napi biztonsági mentés.
**Kezdeti biztonsági mentés** | Bár a növekményes biztonsági mentések teljes biztonsági mentési ideje kevesebb, mint 24 óra, előfordulhat, hogy az első biztonsági mentés nem. A kezdeti biztonsági mentéshez szükséges idő az adatok méretétől és a biztonsági mentés feldolgozásának időpontjától függ.
**Várólista visszaállítása** | Az Azure Backup folyamatok visszaállítása feladatok at több tárfiókok egyszerre, és a visszaállítási kérelmek et egy várólistába helyezi.
**Másolat visszaállítása** | A visszaállítási folyamat során az adatok a tárolóból a tárfiókba másolódnak.<br/><br/> A teljes visszaállítási idő az I/O-műveletek másodpercenkénti (IOPS) és a tárfiók átviteli.<br/><br/> A másolási idő csökkentéséhez válasszon ki egy olyan tárfiókot, amely nincs betöltve más alkalmazásírásokkal és olvasásokkal.

### <a name="backup-performance"></a>Biztonsági mentés teljesítménye

Ezek a gyakori forgatókönyvek hatással lehetnek a teljes biztonsági mentési időre:

- **Új lemez hozzáadása védett Azure-géphez:** Ha egy virtuális gép növekményes biztonsági mentésen megy keresztül, és új lemezt ad hozzá, a biztonsági mentési idő növekedni fog. A teljes biztonsági mentési idő az új lemez kezdeti replikációja, valamint a meglévő lemezek különbözeti replikációja miatt 24 óránál tovább tarthat.
- **Töredezett lemezek:** A biztonsági mentési műveletek gyorsabbak, ha a lemez módosításai összefüggőek. Ha a módosítások szétvannak osztva és széttöredezettek a lemezen, a biztonsági mentés lassabb lesz.
- **Lemezforgalom:** Ha a növekményes biztonsági mentésen átesett védett lemezek napi forgalomban több mint 200 GB-ot tartalmaznak, a biztonsági mentés végrehajtása hosszú időt (több mint nyolc órát) is igénybe vehet.
- **Biztonsági másolat verziói:** A Biztonsági másolat legújabb verziója (más néven az Azonnali visszaállítás verzió) optimalizáltabb folyamatot használ, mint az ellenőrzőösszeg-összehasonlítás a változások azonosítására. De ha azonnali visszaállítást használ, és törölte a biztonsági mentés pillanatképét, a biztonsági mentés átvált az ellenőrzőösszeg összehasonlításához. Ebben az esetben a biztonsági mentési művelet meghaladja a 24 órát (vagy sikertelen).

## <a name="best-practices"></a>Ajánlott eljárások

Virtuálisgép-biztonsági mentések konfigurálásakor azt javasoljuk, hogy kövesse az alábbi gyakorlatokat:

- Módosítsa a házirendben beállított alapértelmezett ütemezési időket. Ha például a házirendben az alapértelmezett idő 12:00 óra, az időzítés tágítsa néhány perccel, hogy az erőforrások optimálisan használhatók.
- Ha egy tárolóból állítja vissza a virtuális gépeket, javasoljuk, hogy használjon különböző [általános célú v2-es tárfiókokat](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) annak érdekében, hogy a céltárfiók ne legyen szabályozva. Például minden virtuális gépnek egy másik tárfiókkal kell rendelkeznie. Például ha 10 virtuális gép visszaállítása, 10 különböző tárfiókok használata.
- A prémium szintű tárhelyet használó virtuális gépek biztonsági mentése esetén az Azonnali visszaállítás szolgáltatással azt javasoljuk, hogy a teljes lefoglalt tárhely *50%-os* szabad területét különítse el, ami **csak** az első biztonsági mentéshez szükséges. Az 50%-os szabad terület nem követelmény a biztonsági mentéshez az első biztonsági mentés befejezése után
- A tárfiókonkénti lemezek számának korlátozása attól függ, hogy milyen erősen érhetők el a lemezek olyan alkalmazások, amelyek szolgáltatásként (IaaS) virtuális gépként egy infrastruktúrán futnak. Általános gyakorlat, ha 5–10 lemez vagy több van egy tárfiókban, kiegyenlítse a terhelést úgy, hogy egyes lemezeket külön tárfiókokba helyez át.

## <a name="backup-costs"></a>Biztonsági mentésköltségei

Az Azure Biztonsági mentésben biztonsági mentésben az Azure Backup szolgáltatással biztonsági mentésben való támogatást az [Azure Backup díjszabása vonatkozik.](https://azure.microsoft.com/pricing/details/backup/)

A számlázás csak akkor kezdődik el, ha az első sikeres biztonsági mentés befejeződik. Ezen a ponton a tárterület és a védett virtuális gépek számlázása is megkezdődik. A számlázás addig folytatódik, amíg a virtuális gép biztonsági mentési adatait egy tárolóban tárolják. Ha leállítja a virtuális gép védelmét, de a virtuális gép biztonsági mentési adatai egy tárolóban vannak, a számlázás folytatódik.

Egy adott virtuális gép számlázása csak akkor áll le, ha a védelem leáll, és az összes biztonsági mentési adatok törlődnek. Ha a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, az utolsó sikeres virtuális gép biztonsági mentésének mérete a havi számlához használt védett példányméret lesz.

A védett példány méretszámítás a *virtuális* gép tényleges mérete alapján. A virtuális gép mérete a virtuális gép összes adatának összege, az ideiglenes tárolás nélkül. A díjszabás az adatlemezeken tárolt tényleges adatokon alapul, nem pedig a virtuális géphez csatlakoztatott egyes adatlemezek maximális támogatott méretén.

Hasonlóképpen a biztonsági mentési tárolási számla az Azure Backupban tárolt adatok mennyiségén alapul, amely az egyes helyreállítási ponttényleges adatok összege.

Például vegyen egy A2-standard méretű virtuális gép, amely két további adatlemezek maximális mérete 32 TB mindegyik. Az alábbi táblázat az egyes lemezeken tárolt tényleges adatokat mutatja be:

**Disk** | **Maximális méret** | **A jelen lévő tényleges adatok**
--- | --- | ---
Operációsrendszer-lemez | 32 TB | 17 GB
Helyi/ideiglenes lemez | 135 GB | 5 GB (nem tartalmazza a biztonsági mentés)
1. adatlemez | 32 TB| 30 GB
2. adatlemez | 32 TB | 0 GB

A virtuális gép tényleges mérete ebben az esetben 17 GB + 30 GB + 0 GB = 47 GB. Ez a védett példányméret (47 GB) lesz a havi számla alapja. A virtuális gépben lévő adatok mennyiségének növekedésével a számlázási módosításokhoz használt védett példányméretet.

## <a name="next-steps"></a>További lépések

Most, [felkészülés az Azure VM biztonsági mentés.](backup-azure-arm-vms-prepare.md)
