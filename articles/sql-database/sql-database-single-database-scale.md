---
title: Önálló adatbázis erőforrásainak skálázása
description: Ez a cikk ismerteti, hogyan skálázható a számítási és tárolási erőforrások egyetlen adatbázis az Azure SQL Database-ben elérhető.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061597"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Egyetlen adatbázis-erőforrások méretezése az Azure SQL Database-ben

Ez a cikk ismerteti, hogyan skálázható az Azure SQL-adatbázis a kiépített számítási rétegben elérhető számítási és tárolási erőforrások. Másik lehetőségként a [kiszolgáló nélküli számítási szint](sql-database-serverless.md) számítási automatikus skálázást és számlázott másodpercenként idáig biztosít számítási használható.

Miután először kiválasztotta a virtuális magok vagy dto-k számát, dinamikusan skálázhat egy adatbázist az [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), a [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)a [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)tényleges tapasztalatai alapján.

Az alábbi videó dinamikusan módosítja a szolgáltatási szintet és a számítási méretet, hogy növelje a rendelkezésre álló DIT-eket egyetlen adatbázishoz.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

## <a name="impact"></a>Hatás

A szolgáltatási szint vagy a számítási méret módosítása főként a következő lépéseket hajtja végre:

1. Új számítási példány létrehozása az adatbázishoz  

    Egy új számítási példány jön létre a kért szolgáltatási szint és a számítási méret. A szolgáltatási szint és a számítási méret változásainak egyes kombinációi hozatása érdekében létre kell hozni az adatbázis replikáját az új számítási példányban, amely adatok másolásával jár, és erősen befolyásolhatja az általános késést. Ettől függetlenül az adatbázis online marad ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példányban az adatbázishoz lesznek irányítva.

2. Kapcsolatok átirányítása új számítási példányra

    Az eredeti számítási példányban az adatbázissal létesített kapcsolatok megszakadnak. Minden új kapcsolat jön létre az adatbázishoz az új számítási példányban. A szolgáltatási szint és a számítási méret változásainak egyes kombinációi esetén az adatbázisfájlok leválnak és újracsatolódnak a váltás során.  Ettől függetlenül a kapcsoló rövid szolgáltatásmegszakítást eredményezhet, ha az adatbázis általában 30 másodpercnél rövidebb ideig, de gyakran csak néhány másodpercig nem érhető el. Ha a kapcsolatok elvetésekén hosszú ideig futó tranzakciók futnak, a lépés időtartama hosszabb időt vehet igénybe a megszakított tranzakciók helyreállítása érdekében. [A gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md) csökkentheti a hosszú ideig futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat bármely lépése során nem vesznek el adatok. Győződjön meg arról, hogy megvalósított néhány [újrapróbálkozási logikát](sql-database-connectivity-issues.md) az Azure SQL Database-t használó alkalmazásokban és összetevőkben, amíg a szolgáltatási szint megváltozott.

## <a name="latency"></a>Késés 

A szolgáltatási szint módosításának vagy egyetlen adatbázis vagy rugalmas készlet számítási méretének átméretezéséhez szükséges becsült késés a következőképpen van paraméterezve:

|Szolgáltatásszint|Alapvető egységes adatbázis,</br>Standard (S0-S1)|Alapvető rugalmas medence,</br>Szabvány (S2-S12), </br>Nagy léptékű, </br>Általános célú egyetlen adatbázis vagy rugalmas készlet|Prémium vagy üzleti legkritikusabb egyetlen adatbázis vagy rugalmas készlet|
|:---|:---|:---|:---|
|**Alapszintű adatbázis,</br> Standard (S0-S1)**|&bull;&nbsp;Állandó időkésleltetés a felhasznált területtől függetlenül</br>&bull;&nbsp;Jellemzően kevesebb, mint 5 perc|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|
|**Alapszintű rugalmas </br>készlet, Standard (S2-S12), </br>Nagykapacitású, </br>Általános célú egy-adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Állandó időkésleltetés a felhasznált területtől függetlenül</br>&bull;&nbsp;Jellemzően kevesebb, mint 5 perc|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|
|**Prémium vagy üzleti legkritikusabb egyetlen adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|&bull;&nbsp;Az adatmásolás miatt használt adatbázisterülettel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB terület|

> [!TIP]
> A folyamatban lévő műveletek figyeléséhez lásd: [Műveletek kezelése az SQL REST API használatával](https://docs.microsoft.com/rest/api/sql/operations/list), Műveletek kezelése [CLI használatával](/cli/azure/sql/db/op), Műveletek [figyelése T-SQL használatával,](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) és a két PowerShell-parancs: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) és [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Módosítások megszakítása

A szolgáltatási szint módosítása vagy számítási átskálázási művelet megszakítható.

#### <a name="azure-portal"></a>Azure portál

Az adatbázis-áttekintés panelen keresse meg az **Értesítések** lapot, és kattintson a csempére, jelezve, hogy folyamatban van egy művelet:

![Folyamatban lévő működés](media/sql-database-single-database-scale/ongoing-operations.png)

Ezután kattintson a **Művelet megszakítása**feliratú gombra.

![Folyamatban lévő művelet megszakítása](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

A PowerShell parancssorból `$resourceGroupName`állítsa `$serverName`be `$databaseName`a , és a , majd futtassa a következő parancsot:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

- Ha magasabb szolgáltatási szintre vagy számítási méretre frissít, az adatbázis maximális mérete nem növekszik, hacsak kifejezetten meg nem ad egy nagyobb méretet (maxsize).
- Az adatbázis visszaminősítéséhez a használt adatbázisterületnek kisebbnek kell lennie, mint a célszolgáltatási szint és a számítási méret megengedett maximális méretének.
- A prémium **szintről** a **standard** szintre való visszaminősítéskor egy extra tárolási költség akkor jelentkezik, ha (1) az adatbázis maximális mérete támogatott a cél számítási méretben, és (2) a maximális méret meghaladja a cél számítási méret tartalmazza a benne foglalt tárolási mennyiséget. Ha például egy 500 GB-os maximális méretű P1-adatbázis takarásba van csökkentve, akkor egy extra tárolási költség vonatkozik, mivel az S3 legfeljebb 1 TB-os méretet támogatja, és a benne foglalt tárterület csak 250 GB. Tehát az extra tárhely 500 GB – 250 GB = 250 GB. Az extra tárhely díjszabásáról az [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)című témakörben található. Ha a ténylegesen felhasznált terület kisebb, mint a benne foglalt tárterület, akkor ez a többletköltség elkerülhető az adatbázis maximális méretének a benne foglalt összegre történő csökkentésével.
- Ha egy adatbázist olyan módon frissít, amelyen engedélyezve van a [georeplikáció,](sql-database-geo-replication-portal.md) frissítse másodlagos adatbázisait a kívánt szolgáltatási szintre és számítási méretre az elsődleges adatbázis frissítése előtt (általános útmutatás a legjobb teljesítmény érdekében). Ha egy másik kiadásra frissít, követelmény, hogy először a másodlagos adatbázist frissítse.
- Ha egy adatbázis visszaminősítése [a georeplikáció](sql-database-geo-replication-portal.md) engedélyezve van, visszaminősíti az elsődleges adatbázisok a kívánt szolgáltatási szint re és a számítási méret visszaminősítése előtt a másodlagos adatbázis (általános útmutatást a legjobb teljesítmény). Ha egy másik kiadásra való visszaminősítés, követelmény, hogy az elsődleges adatbázis leminősítése először.
- A visszaállítási szolgáltatásajánlatok eltérőek a különböző szolgáltatási szintek esetében. Ha az **alapszintű** szintre való visszaminősítés, alacsonyabb biztonsági mentési megőrzési időszak. Lásd: [Azure SQL Database biztonsági mentések](sql-database-automated-backups.md).
- Az adatbázis új tulajdonságai csak akkor lesznek alkalmazva, ha a módosítások befejeződtek.

## <a name="billing"></a>Számlázás 

A számlázás minden órában az adatbázis létezik a legmagasabb szolgáltatási szint + számítási méret, amely az adott órában alkalmazott, függetlenül a ttól, hogy a használat, vagy hogy az adatbázis aktív volt kevesebb, mint egy óra. Ha például egyetlen adatbázist hoz létre, és öt perccel később törli, a számla egy adatbázis-óra díját tükrözi.

## <a name="change-storage-size"></a>A tárhely méretének módosítása

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület az adattárolási maximális méretkorlátig 1 GB-os lépésekben kiépíthető. A minimálisan konfigurálható adattár 1 GB. Tekintse meg az egyes adatbázisok és rugalmas készletek erőforrás-korlátozási [dokumentációs](sql-database-vcore-resource-limits-single-databases.md) [lapjait](sql-database-vcore-resource-limits-elastic-pools.md) az egyes szolgáltatáscélok maximális méretkorlátaihoz.
- Egyetlen adatbázis adattárolása az [Azure Portal](https://portal.azure.com), [transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy REST [API](https://docs.microsoft.com/rest/api/sql/databases/update)használatával történő maximális méretének növelésével vagy csökkentésével építhető ki. Ha a maximális méret érték bájtban van megadva, annak 1 GB (1073741824 bájt) többszörösének kell lennie.
- Az adatbázis adatfájljaiban tárolható adatok mennyiségét a beállított adattárház maximális mérete korlátozza. A tárolón kívül az SQL Database automatikusan 30%-kal több tárhelyet foglal le a tranzakciós naplóhoz.
- Az SQL Database automatikusan virtuális magonként 32 GB-ot foglal le az `tempdb` adatbázis számára. `tempdb`a helyi SSD-tárolón található az összes szolgáltatási szinten.
- Egyetlen adatbázis vagy egy rugalmas készlet tárolási ára az adattárolási és tranzakciós napló tárolási összegének szorzata a szolgáltatási szint tárolási egységárával. Az ár `tempdb` benne van az árban. A tárolási árról az [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)című témakörben talál részleteket.

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- Egyetlen adatbázis DTU-ára tartalmaz egy bizonyos mennyiségű tárhelyet további költségek nélkül. A benne foglalt összeget meghaladó további tárterület a maximális méretkorlátig 250 GB-ig, 1 TB-ig, majd 1 TB-on túl256 GB-os lépésekben is kiépíthető. A belefoglalt tárolási mennyiségeket és a maximális méretkorlátokat lásd: [Egyetlen adatbázis: Tárolási méretek és számítási méretek.](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Egyetlen adatbázis további tárhelye az Azure Portal, a [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)a [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)használatával történő maximális méretének növelésével építhető ki.
- Az egyetlen adatbázis extra tárterületének ára az extra tárterület és a szolgáltatási szint extra tárolási egységára szorzata. Az extra tárhely áráról az [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)című témakörben talál részleteket.

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

### <a name="geo-replicated-database"></a>Georeplikált adatbázis

A replikált másodlagos adatbázis adatbázisméretének módosításához módosítsa az elsődleges adatbázis méretét. Ezt a módosítást ezután replikálja és megvalósítja a másodlagos adatbázisban is. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 és P15 kényszerek, ha a maximális méret nagyobb, mint 1 TB

A prémium szint több mint 1 TB tárhelye jelenleg minden régióban elérhető, kivéve: Kína keleti, Észak-Kína, Németország központi, északkeleti régiója, USA nyugati középső régiói, az USA DoD régiói és az Egyesült Államok központi kormánya. Ezekben a régiókban a prémium szint maximális tárháza 1 TB-ra korlátozódik. Az alábbi szempontok és korlátozások az 1 TB-nál nagyobb maximális méretű P11 és P15 adatbázisokra vonatkoznak:

- Ha egy P11 vagy P15 adatbázis maximális mérete valaha is 1 TB-nál nagyobb értékre volt állítva, akkor csak P11 vagy P15 adatbázisba állítható vissza vagy másolható.  Ezt követően az adatbázis átméretezhető egy másik számítási méret, feltéve, hogy az újraskálázási művelet idején lefoglalt terület nem haladja meg az új számítási méret maximális méretkorlátait.
- Aktív georeplikációs forgatókönyvek esetén:
  - Georeplikációs kapcsolat beállítása: Ha az elsődleges adatbázis P11 vagy P15, a másodlagos(ok) is P11 vagy P15; az alacsonyabb számítási méretet másodlagosként utasítják el, mivel nem képesek 1 TB-nál több támogatására.
  - Az elsődleges adatbázis frissítése georeplikációs kapcsolatban: A maximális méret módosítása 1 TB-nál többre egy elsődleges adatbázisban ugyanazt a változást indítja el a másodlagos adatbázisban. Mindkét frissítésnek sikeresnek kell lennie ahhoz, hogy az elsődleges módosítás érvénybe lépjen. Az 1 TB-nál több opcióra vonatkozó régiókorlátozások érvényesek. Ha a másodlagos olyan régióban van, amely nem támogatja az 1 TB-nál többet, az elsődleges nem frissül.
- Az 1 TB-nál nagyobb P11/P15 adatbázisok betöltésére az Importálás/exportálás szolgáltatás használata nem támogatott. Az SqlPackage.exe segítségével [importálhatja](sql-database-import.md) és [exportálhatja](sql-database-export.md) az adatokat.

## <a name="next-steps"></a>További lépések

Az általános erőforráskorlátokat az [SQL Database virtuálismag-alapú erőforráskorlátok – egyetlen adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és SQL Database [DTU-alapú erőforráskorlátok – rugalmas készletek című témakörben tetszetős gyűjtők.](sql-database-dtu-resource-limits-single-databases.md)
