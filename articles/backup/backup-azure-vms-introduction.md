---
title: Az Azure-beli virtuális gép biztonsági mentési infrastruktúrájának tervezése
description: Az Azure-beli virtuális gépek biztonsági mentése megtervezésekor fontos szempontok
services: backup
author: markgalioto
manager: carmonm
keywords: virtuális gépek biztonsági mentése, virtuális gépek biztonsági mentését
ms.service: backup
ms.topic: conceptual
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 9e2ef16cffb044409b6f7f8e7785010097bcda87
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286652"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Virtuális gép biztonsági infrastruktúrájának megtervezése az Azure-ban
A cikk ismerteti a teljesítmény- és erőforrás-javaslatok segítséget nyújt a virtuális gép biztonsági infrastruktúrájának megtervezése. Azt is meghatározza a biztonsági mentési szolgáltatás; fő szempontok Ezeket a szempontokat architektúra, amely meghatározza, hogy kritikus fontosságú lehet kapacitástervezés és ütemezését. Ha [a környezet előkészítése](backup-azure-arm-vms-prepare.md), tervezési a következő lépés megkezdése előtt [a virtuális gépek biztonsági mentése](backup-azure-arm-vms.md). Ha további információt az Azure virtual machines van szüksége, tekintse meg a [Virtual Machines dokumentációja](https://azure.microsoft.com/documentation/services/virtual-machines/). 

> [!NOTE]
> Ez a cikk a felügyelt és nem felügyelt lemezek való használatra. Ha nem felügyelt lemezeket használ, nincsenek tárfiókokra vonatkozó javaslatok. Ha [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), nem kell aggódnia a teljesítmény vagy a resource kihasználtság problémákat. Azure optimalizálja a tárterület kihasználtságát az Ön számára.
>

## <a name="how-does-azure-back-up-virtual-machines"></a>Hogyan történik az Azure virtuális gépek biztonsági mentése?
Ha az Azure Backup szolgáltatás indítja el egy biztonsági mentési feladat a megadott időpontban, a szolgáltatás eseményindítók a biztonsági mentési bővítmény időponthoz pillanatképének elkészítéséhez. Az Azure Backup szolgáltatás által használt a _VMSnapshot_ kiterjesztése a Windows, és a _VMSnapshotLinux_ Linux-bővítményt. A bővítmény telepítve van a virtuális gép első biztonsági mentés során. A bővítmény telepítéséhez a virtuális Gépen kell futnia. Ha a virtuális gép nem fut, a Backup szolgáltatás az alapul szolgáló tárolóról készít pillanatképet (mivel nem történik alkalmazásírás, amikor a virtuális gép le van állítva).

Amikor Windows rendszerű virtuális gépekről készít pillanatképet, a Backup szolgáltatás együttműködik a Kötet árnyékmásolata szolgáltatással (VSS), hogy egységes pillanatképet készítsen a virtuális gép lemezeiről. Ha biztonsági mentését végzi a Linux rendszerű virtuális gépek, a saját egyéni parancsfájlok konzisztencia biztosításához, ha a virtuális gép pillanatképének elkészítése írhat. Ezek a szkriptek meghívása a részleteket a cikk későbbi részében találhatók.

Amikor az Azure Backup szolgáltatás elkészítette a pillanatképet, az adatok átkerülnek a tárolóba. A maximális hatékonyság érdekében a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

![Az Azure virtuális gép biztonsági mentési architektúra](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

> [!NOTE]
> 1. A biztonsági mentési folyamat során az Azure Backup nem tartalmazza az ideiglenes lemez a virtuális géphez csatlakozik. További információkért lásd a következő blogbejegyzésben [ideiglenes tároló](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Az Azure Backup storage-szintű pillanatképet készít, és a pillanatkép tárba való átvitele. Ne módosítsa a tárfiókkulcsokat, a biztonsági mentési feladat befejezéséig.
>

### <a name="data-consistency"></a>Adatkonzisztencia
Biztonsági mentése és visszaállítása a kritikus fontosságú adatokat az a tény, hogy a kritikus fontosságú üzleti adatokat kell készíteni az alkalmazásokat, amelyek az adatok előállításához közben van bonyolult üzleti futnak. Ennek az Azure Backup támogatja az alkalmazáskonzisztens biztonsági mentést Windows és Linux rendszerű virtuális gépek
#### <a name="windows-vm"></a>Windows rendszerű virtuális gép
Az Azure Backup fogja elvégezni a VSS teljes biztonsági mentés, a Windows virtuális gépek (További információ [VSS teljes biztonsági mentés](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Ahhoz, hogy a VSS másolásos biztonsági mentéshez, a következő beállításkulcs kell állítani a virtuális gépen.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Linux rendszerű virtuális gépek

Az Azure Backup egy parancsfájl-kezelési keretet biztosít szabályozása a biztonsági mentési munkafolyamat és a környezetben. Ahhoz, hogy az alkalmazáskonzisztens Linux rendszerű virtuális gép biztonsági mentést, a parancsfájl-kezelési keretrendszer használatával hozzon létre egyéni előtti és utáni parancsfájlok. Az előkészítő parancsprogram meghívása előtt a virtuális gép pillanatképének, és ezután meghívja az utólagos parancsprogram, a virtuális gép pillanatkép-feladat befejezése után. További információkért tekintse meg a cikket [az alkalmazások konzisztens Linux rendszerű virtuális gép biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

> [!NOTE]
> Az Azure Backup csak meghívja az ügyfél által írt előtti és utáni parancsfájlokat. A szkript előtti és utáni szkriptek végrehajtása sikertelen, ha az Azure Backup jelöli meg a helyreállítási pont alkalmazás konzisztens. Azonban az ügyfél a végső soron felelős az alkalmazáskonzisztencia, egyéni parancsfájlok használata esetén.
>

A következő táblázat ismerteti a konzisztencia és a feltételeknek, azok bekövetkezésekor.

| Konzisztencia | VSS alapú | MAGYARÁZAT és részletei |
| --- | --- | --- |
| Alkalmazáskonzisztencia |Igen, a Windows|Alkalmazáskonzisztencia ideális, biztosítja, hogy:<ol><li> A virtuális gép *generációazonosítójának*. <li>Nincs *sérülés nem*. <li>Nincs *adatvesztés*.<li> Az adatok a konzisztens az alkalmazáshoz, amely az adatokat, használja az alkalmazás biztonsági mentését – a VSS vagy előkészítő/utólagos parancsfájl használatával bevonásával.</ol> <li>*Windows virtuális gépek*– leginkább a Microsoft-munkaterhelések rendelkeznek VSS-írót használ, amely a kapcsolódó adatok konzisztenciájának munkaterhelés-specifikus műveletek végrehajtása. Például az SQL Server VSS-író biztosítható az írások, a tranzakciós naplófájlt, és az adatbázis, megfelelően történik. IaaS Windows virtuális gép biztonsági mentésekhez egy alkalmazáskonzisztens helyreállítási pont létrehozása a biztonsági mentési bővítményt kell meghívni a VSS-munkafolyamat, és ennek befejeződését, mielőtt a virtuális gép pillanatképének. A pontos Azure VM-pillanatkép az összes Azure-beli Virtuálisgép-alkalmazás VSS-írók kell kitölteni is. (További a [VSS alapjait](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) és részletesen részleteit [működését](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Linux rendszerű virtuális gépek*-ügyfelek hajthat végre [alkalmazás konzisztencia biztosításához egyéni szkript előtti és utáni](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Fájlrendszer konzisztencia |Igen – Windows-alapú számítógépekhez |Két olyan esetben, ahol a helyreállítási pont lehet *fájlrendszerkonzisztens*:<ul><li>Linux rendszerű virtuális gépek az Azure-ban, nélkül előtti-script/követő-script vagy ha előtti-script/követő-script sikertelen biztonsági mentések. <li>VSS-hiba Windows-beli virtuális gépek biztonsági mentése során.</li></ul> Mindkét esetben a lehető legjobb teheti meg, hogy ellenőrizze, hogy: <ol><li> A virtuális gép *generációazonosítójának*. <li>Nincs *sérülés nem*.<li>Nincs *adatvesztés*.</ol> Alkalmazások kell megvalósítani a saját "javítás felfelé" mechanizmus a visszaállított adatok. |
| Az összeomlási konzisztencia |Nem |Ebben a helyzetben egyenértékű egy "összeomlási" (a gyenge vagy rögzített reset) tapasztaló virtuális géphez. Az összeomlási konzisztencia általában akkor fordul elő, amikor az Azure virtuális gép leállítása a biztonsági mentés idején. Összeomlás-konzisztens helyreállítási pont létrehozása nem garantálja az adatok konzisztenciájának körül nyújt az adattároló – vagy az operációs rendszer vagy az alkalmazás szempontjából. Csak azokat az adatokat, amely már létezik a lemezen a biztonsági mentés időpontjában rögzített, és biztonsági mentés. <br/> <br/> Nincsenek általában nem garantálja, míg az operációs rendszer indítása követ lemez-ellenőrzés eljárás, például a chkdsk eszközhöz, a sérülés hibák javításához. Memóriában lévő adatokat vagy nem átadott a lemezre írás elvesznek. Az alkalmazás általában a saját hitelesítési mechanizmusa követi, abban az esetben adatok visszaállítási kell végezni. <br><br>Például ha a tranzakciós napló bejegyzések nem szerepel az adatbázisban, a az adatbázisszoftvert összesíti az vissza addig, amíg az adatok összhangban. Adatok (például átnyúló kötetek) több virtuális lemez van elosztva, amikor összeomlás-konzisztens helyreállítási pont nincs garancia biztosít az adatok helyességét. |

## <a name="performance-and-resource-utilization"></a>Teljesítmény- és Erőforrás-kihasználtsága
Például a biztonsági mentési szoftver, amely üzembe helyezhető a helyszínen meg kell terveznie a kapacitás- és erőforrás-használat igényeinek megfelelően, ha az Azure-beli virtuális gépek biztonsági mentésének. A [korlátozza az Azure Storage](../azure-subscription-service-limits.md#storage-limits) meghatározhatja, hogyan kell a struktúra legnagyobb teljesítmény minimális hatással van, a számítási feladatokat futtató virtuális gépek üzembe helyezése.

### <a name="number-of-disks"></a>Lemezek száma
A biztonsági mentési folyamat megpróbálja a lehető leggyorsabban egy biztonsági mentési feladat végrehajtásához. Ennek során azt eltárolni a legtöbb erőforrást használ fel. Azonban az összes i/o-műveletek révén korlátozottak a *cél átviteli egyetlen blobhoz*, amely rendelkezik egy legfeljebb 60 MB / másodperc. A sebesség maximalizálása érdekében próbál, a biztonsági mentési folyamat próbál biztonsági másolatot készíteni a Virtuálisgép-lemezek mindegyike *párhuzamosan*. Ha a virtuális gép lemezei négy, a szolgáltatás megkísérli párhuzamosan összes négy lemezek biztonsági mentése. A **lemezek száma** mentésre, az a legfontosabb tényező meghatározásakor a storage-fiók biztonsági mentések forgalmától.

### <a name="backup-schedule"></a>Biztonsági mentés ütemezése
Egy további tényező, amely hatással van a teljesítmény a **biztonsági mentési ütemezés**. Így minden virtuális gép készül biztonsági másolat egy időben konfigurálhatja a házirendeket, ha a forgalom csávából ütemezett. A biztonsági mentési folyamat megpróbálja párhuzamosan összes lemezek biztonsági mentése. A biztonsági mentési forgalom csökkentése érdekében különböző virtuális gépek biztonsági mentése nélkül, a nap másik időpontban.

## <a name="capacity-planning"></a>Kapacitástervezés
Töltse le a [virtuális gép biztonsági mentési kapacitástervezési Excel-számolótáblában](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) -azonosítókra gyakorolt hatást a lemez és a biztonsági mentési ütemezés választható.

### <a name="backup-throughput"></a>Biztonsági mentési átviteli sebesség
Az egyes lemezek biztonsági mentés alatt álló, az Azure Backup azokat az adatblokkokat a lemez beolvassa és csak a módosított adatokat (növekményes biztonsági mentés) tárolja. Az alábbi táblázat a biztonsági mentési szolgáltatás átlagos átviteli sebesség értékét. A következő adatokat használja, meg tudja becsülni a biztonsági másolatot készíteni egy adott méretű lemez szükséges idő.

| Biztonsági mentési művelet | Hányad átviteli sebesség |
| --- | --- |
| Kezdeti biztonsági mentés |160 MB/s |
| A növekményes biztonsági mentés (DR) |640 Mbps <br><br> Átviteli sebesség jelentősen csökken, ha a módosított adatokat (igénylő biztonsági mentése) a rendszer a lemez összes adatát.|

## <a name="total-vm-backup-time"></a>Virtuális gép teljes biztonsági mentés időpontja
A biztonsági mentési legtöbbször töltött beolvasása, illetve az adatok másolását, míg más műveletek egy virtuális gép biztonsági másolatának szükséges teljes időt hozzájárulhat:

* Szükséges idő [telepíteni vagy frissíteni a biztonsági mentési bővítményt](backup-azure-arm-vms.md).
* Pillanatkép időpontot, amely az idő a pillanatkép-trigger. A pillanatképek aktivált közelében az ütemezett biztonsági mentés időpontja.
* Várólista várakozási idő. A biztonsági mentési szolgáltatás folyamatok feladatok több ügyfeleink egyszerre, mert a pillanatkép adatainak előfordulhat, hogy nem azonnal másolja a Recovery Services-tároló. Betöltés időszakokban előtt a biztonsági mentések feldolgozása legfeljebb nyolc órát is igénybe vehet. Viszont a teljes virtuális gép biztonsági mentésének ideje, a napi biztonsági mentési szabályzatok legfeljebb 24 óra.
Teljes biztonsági mentés időpontja legfeljebb 24 órás növekményes biztonsági mentések esetében érvényes, de nem lehet az első biztonsági mentés. Első biztonsági mentés ideje arányos, és 24 óra, attól függően, hogy az adatok és a biztonsági mentés készül a mérete nagyobb is lehet.
* Adatátviteli idő, a biztonsági mentési szolgáltatásban a növekményes változásokat előző biztonsági mentés a számítási és a változások átvitele a storage-tároló szükséges idő.

### <a name="why-are-backup-times-longer-than-12-hours"></a>Miért vannak a biztonsági mentés időpontjai hosszabb, mint 12 órán át?

Biztonsági mentés két fázisból áll: pillanatképeket, és a pillanatképek átvitele a tárba. A Backup szolgáltatás optimalizálja a tároláshoz. A pillanatkép adatainak továbbítson egy tárolót, a szolgáltatás csak továbbítja a növekményes változásokat az előző pillanatképet.  Annak megállapításához, a növekményes változásokat, a szolgáltatás kiszámítja a blokkok ellenőrzőösszeget. Ha módosul egy, a blokk kell küldeni a tároló egy blokk azonosítja. Ezután a szolgáltatás működése során további minden egyes azonosított blokkok, minimalizálása érdekében az adatok átvitele lehetőségeket keres. Után minden megváltozott blokkokat kiértékelését, a szolgáltatás számmá egyesíti a módosításokat, és elküldi azokat a tárolóban. Egyes örökölt alkalmazások, a kicsi, töredezett írási műveletek nem állnak optimális tároló. Ha a pillanatkép sok kisebb, töredezett írások tartalmaz, a szolgáltatás további időt, az alkalmazások által írt adatok feldolgozására futtatásával töltött. A virtuális Gépen futó alkalmazások esetében a minimálisan ajánlott alkalmazásírás blokk 8 KB-os. Ha az alkalmazás egy 8 KB-blokk, biztonsági mentés teljesítményét történik. Hangolása biztonsági mentési teljesítményének javítása érdekében az alkalmazás-témakörben talál segítséget [hangolása az optimális teljesítmény és az Azure storage alkalmazások](../virtual-machines/windows/premium-storage-performance.md). Bár a cikkben a biztonsági mentés teljesítményét a prémium szintű storage-példák használata, az útmutató érvényes a standard szintű tárolólemezek vonatkozik.

## <a name="total-restore-time"></a>Teljes visszaállítási idő

A visszaállítási művelet áll két fő feladat: adatok másolása a kiválasztott ügyfél tárfiókja vissza a tárolóból, és létrehozta a virtuális gépet. Adatok másolása a tárolóhoz szükséges idő attól függ, a biztonsági mentések tárolására az Azure és az ügyfél tárfiókja helyét. Az adatok másolásához szükséges idő attól függ:
* Várólista várakozási idő – mivel a szolgáltatási folyamatoknak feladatok visszaállítása több ügyfél egyszerre, visszaállítási jár sorba helyezni.
* Adatok másolása az idő - adatokat a tárolóból az ügyfél tárfiókja másolja. Visszaállítás idő függ az IOPS és átviteli sebesség az Azure Backup szolgáltatás lekérdezi a kijelölt ügyfél tárfiókban. Nincs betöltve, más alkalmazások írási és olvasási tárfiók kiválasztása a visszaállítási folyamat során a másolási idő csökkentése érdekében.

## <a name="best-practices"></a>Ajánlott eljárások

Javasoljuk, hogy ezeket a gyakorlatokat minden virtuális gép biztonsági mentések konfigurálása során a következő:

* 10-nél több klasszikus virtuális gépek ugyanazon a felhőszolgáltatáson, az nem ütemezhető biztonsági mentések egyszerre. Ha azt szeretné, biztonsági mentése több virtuális gép ugyanazon a felhőszolgáltatáson, szinkronizálások a biztonsági mentés kezdési idejének eltolása szerint egy óra.
* Több mint 100 virtuális gép egy tárolóból, biztonsági mentések egyszerre nem ütemezhető.
* Virtuális gép biztonsági mentéseinek ütemezéséhez nem csúcsidőre során. Ezzel a módszerrel a Backup szolgáltatás adatokat visz át az ügyfél tárfiókja a tároló iops-t használ.
* Győződjön meg arról, hogy Linux rendszerű virtuális gépek biztonsági mentésének engedélyezve van, a Python 2.7-es vagy újabb verziót.

### <a name="best-practices-for-vms-with-unmanaged-disks"></a>Nem felügyelt lemezekkel rendelkező virtuális gépek védelmének bevált gyakorlata

Az alábbi javaslatokat csak a nem felügyelt lemezeket használó virtuális gépekre vonatkozik. Ha a virtuális gépek felügyelt lemezeket használ, a Backup szolgáltatás kezeli az összes tárolási felügyeleti tevékenység.

* Ellenőrizze, hogy egy biztonsági mentési szabályzat alkalmazása a virtuális gépekre több tárfiókon keresztül terjednek. Az azonos biztonsági mentési ütemezés egyetlen tárfiókban legfeljebb csak 20 teljes lemezeket kell ellátni. Ha nagyobb, mint 20 lemezeket a storage-fiókban, helyezkednek el ezeket a virtuális gépek több szabályzat beolvasni a szükséges iops-t a biztonsági mentési folyamat átviteli fázisában.
* Ne állítson vissza egy virtuális Gépet futtató a Premium storage ugyanazt a tárfiókot. A visszaállítási művelet folyamat egybeessen a biztonsági mentési műveletet, ha csökkenti a biztonsági mentés a rendelkezésre álló iops-érték.
* A prémium szintű virtuális gép biztonsági mentése a virtuális gép biztonsági másolat verem V1 érdemes lefoglalni a csak 50 %-a a fiók teljes tárterület, így a Backup szolgáltatás a pillanatkép másolása a storage-fiókba, és adatok átvitele a storage-fiókot a tárolóba.


## <a name="data-encryption"></a>Adattitkosítás
Az Azure Backup nem titkosítja az adatokat a biztonsági mentési folyamatának részeként. Azonban a virtuális Gépen lévő adatok titkosításához, és zökkenőmentesen biztonsági mentése a védett adatok (További információ [titkosított adatok biztonsági mentését](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>A védett példányok esetében a költség kiszámítása
Státuszban van az Azure virtuális gépek, amelyekről az Azure Backup segítségével [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/). A védett példányok számítási alapul a *tényleges* méret a virtuális gép, amely a virtuális gép – az átmeneti tárolás kivételével az összes adat összege.

Virtuális gépek biztonsági mentésének díjszabása az egyes virtuális géphez csatolt adatlemezek maximális támogatott mérete nem alapul. Díjszabás a tényleges, az adatok lemezen tárolt adatok alapján. Hasonlóképpen a biztonsági mentési tár számlázási tárolt adatokat az Azure Backup, azaz a tényleges adatok az egyes helyreállítási pontokon összege mennyisége alapul.

Vegyünk példának egy Standard a2-es méretű virtuális gépet, amely két további adatlemezek 4 TB-os maximális mérettel. Az alábbi táblázat tartalmazza ezen lemezek tárolja a tényleges adatok:

| Lemez típusa | Maximális méret | Tényleges adatok |
| --------- | -------- | ----------- |
| Operációsrendszer-lemez |4095 GB |17 GB |
| Helyi lemez / ideiglenes lemez |135 GB |5 GB-os (biztonsági mentés nem tartalmazza) |
| Adatlemez 1 |4095 GB |30 GB |
| Adatlemez 2 |4095 GB |0 GB |

A tényleges méret a virtuális gép ebben az esetben a 17 GB + 30 GB + 0 GB = 47 GB. A védett példány mérete (GB-os 47) lesz alapjául a havi számla. A virtuális gépet az adatmennyiség növekedésével a védett példány mérete használja a számlázási változásokat ennek megfelelően.

A számlázás az első sikeres biztonsági mentés befejezéséig nem indul el. Ezen a ponton a Storage és a védett példányok díjszabása kezdődik. A számlázás továbbra is fennáll, mindaddig, amíg nincs a virtuális gép tárolóban tárolt biztonsági mentési adatokat. Ha a virtuális gép védelmének leállítása, de a virtuális gép biztonsági mentési adatok létezik a tárolóban, a számlázás továbbra is.

Számlázási megadott virtuális gép leáll, csak akkor, ha a védelem ki van kapcsolva, és az összes biztonsági mentési adatok törlődnek. Amikor a védelem leáll, és nincsenek aktív biztonsági mentési feladatok, a legutóbbi sikeres virtuális gép biztonsági mentésének mérete a védett példány mérete, a havi számla használt válik.

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>További lépések
* [Virtuális gépek biztonsági mentése](backup-azure-arm-vms.md)
* [Virtuális gépek biztonsági mentésének kezelése](backup-azure-manage-vms.md)
* [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
* [Virtuális gép biztonsági másolatával kapcsolatos problémák elhárítása](backup-azure-vms-troubleshoot.md)
