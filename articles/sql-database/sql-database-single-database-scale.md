---
title: A méretezési csoport egyetlen adatbázis-erőforrás – Azure SQL Database |} A Microsoft Docs
description: Ez a cikk ismerteti a számítási és tárolási erőforrások, elérhető egy önálló adatbázis méretezése az Azure SQL Database-ben.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: carlrab
ms.openlocfilehash: 5bc03b8fe8ea64d85154198b8009592f45805a7e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031849"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Önálló adatbázis-erőforrások skálázása az Azure SQL Database-ben

Ez a cikk ismerteti a számítási és tárolási erőforrások, elérhető egy önálló adatbázis méretezése az Azure SQL Database-ben. 

## <a name="vcore-based-purchasing-model-change-storage-size"></a>Virtuálismag-alapú vásárlási modell: tároló méretének módosítása

- Tárolók kiépítésével legfeljebb 1 GB-onként növelhető tárhelyet használ a maximális méretkorlátot. A minimális konfigurálható adattárolási az 5GB 
- Önálló adatbázis tárolási növelésével vagy csökkentésével, a maximális méret használatával helyezhetők a [az Azure portal](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql?r#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Az azure CLI](/cli/azure/sql/db#az-sql-db-update), vagy a [REST API-val](/rest/api/sql/databases/update).
- Az SQL Database automatikusan foglalja le a 30 %-a további tárhely a naplófájlok és 32GB / virtuális mag a TempDB, de nem haladhatja meg a 384GB. A TempDB található minden szolgáltatásszinten csatlakoztatott SSD-t.
- A szolgáltatás összege adatok tárolási és a naplófájlok tárolási megszorozza a szolgáltatási rétegben tárolási egységára egy önálló adatbázis tárolási díja. A TempDB adatbázis költségét a virtuális mag díja tartalmazza. Az extra tárterület ára a részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>Virtuálismag-alapú vásárlási modell: módosítsa a számítási erőforrásokat

Miután kiválasztotta a virtuális magok száma, méretezhetők egy önálló adatbázis felfelé vagy lefelé dinamikusan használatával a tényleges tapasztalatok alapján a [az Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-server), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [az Azure CLI](/cli/azure/sql/db#az-sql-db-update), vagy a [REST API-val](/rest/api/sql/databases/update). 

A szolgáltatás rétegbeli és/vagy számítási egy adatbázist hoz létre az eredeti adatbázis replikája új számítási méret és majd átáll a replikára. A folyamat során nem történik adatvesztés, de a replikára való átálláskor egy pillanatra az adatbázis felé irányuló kapcsolatok le lesznek tiltva, így lehet, hogy néhány folyamatban lévő tranzakció vissza lesz állítva. A hosszát a kapcsoló-több mint változik, de általában 4 másodpercnél kevesebb mint 30 másodperc 99 %-ában. Ha nagy számú tranzakció van folyamatban a kapcsolatok pillanatában le vannak tiltva, akkor a hosszát a kapcsoló-több mint hosszabb lehet. 

A teljes felskálázási folyamat időtartama az adatbázis a módosítás előtti és utáni méretétől és szolgáltatásszintjétől függ. Ha például egy 250 GB-os adatbázis, amely a, a vagy általános célú szolgáltatási szinten belül módosul hat órán belül be kell fejeződnie. A vertikális felskálázás három órán belül, amely a számítási méret az üzletileg kritikus szolgáltatási szinten belül módosul az azonos méretű adatbázis esetén be kell fejeződnie.

> [!TIP]
> A lévő üzenetekre vonatkozó műveletek figyeléséről lásd:: [SQL REST API használatával műveleteinek kezelésére](/rest/api/sql/Operations/List), [kezelése CLI használatával az operations](/cli/azure/sql/db/op), [T-SQL használatával végzett műveletek monitorozására](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) , és ez a két PowerShell-parancsok: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) és [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Ha egy magasabb szolgáltatási szintre frissít, vagy méretű számítási, az adatbázis maximális méretét nem növekszik, hacsak Ön kifejezetten megad egy nagyobb méretű (maxsize).
* Alacsonyabb szolgáltatásszintre váltásához egy adatbázist, a használt adatbázis-terület kisebb, mint a megengedett maximális a cél szolgáltatásszint és számítási mérete kell lennie. 
* Ha az adatbázis frissítése [georeplikációs](sql-database-geo-replication-portal.md) engedélyezve van, frissítse a másodlagos adatbázisok a kívánt szolgáltatásszintet és a számítási méret az elsődleges adatbázis (általános útmutatást a legjobb teljesítmény érdekében) a frissítés előtt. Frissíti egy másik, a másodlagos adatbázis frissítése először szükség.
* Ha egy adatbázis alacsonyabb szolgáltatásszintre [georeplikációs](sql-database-geo-replication-portal.md) engedélyezve van, az elsődleges adatbázisok a kívánt szolgáltatási szint alacsonyabbra és a számítási méret előtt alacsonyabb verziójúra változtatása a másodlagos adatbázis (általános útmutatást a legjobb teljesítmény érdekében). Ha alacsonyabb szolgáltatásszintre egy másik kiadásra, az elsődleges adatbázis alacsonyabb szolgáltatásszintre először megadása kötelező.
* Az adatbázis új tulajdonságai csak akkor lesznek alkalmazva, ha a módosítások befejeződtek.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>DTU-alapú vásárlási modell: tároló méretének módosítása

- Önálló adatbázis dtu-k díjszabása tartalmaz egy bizonyos mennyiségű tárolási további költségek nélkül. A csomagban foglalt adatmennyiségen felüli extra tárterület legfeljebb 250 GB-os fel 1 TB-os egységekben, majd, 256 GB 1 TB-os léptékben maximális méretkorlátot díjfizetés mellett bővítheti. Belefoglalt tárterület összegek és a maximális méret korlátok [egyetlen adatbázishoz: tárterületet és számítási méretek](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Extra tárterülettel önálló adatbázis kiépítése az Azure Portalon, a maximális méretének növelésével [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure CLI-vel](/cli/azure/sql/db#az-sql-db-update), vagy a [ REST API-val](/rest/api/sql/databases/update).
- Az extra tárterülettel önálló adatbázis szolgáltatás díja az extra tárterület egységára a szolgáltatási rétegben megszorozza extra tárterület keletkezett. Az extra tárterület ára a részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>DTU-alapú vásárlási modell: módosítsa a számítási erőforrásokat (Dtu)

Miután kiválasztotta a egy szolgáltatási rétegben, számítási mérete és mennyisége, akkor is egy önálló adatbázis kisebbre vagy nagyobbra méretezhetők az Azure portal használatával, a tényleges tapasztalatok alapján dinamikusan [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [az Azure CLI](/cli/azure/sql/db#az-sql-db-update), vagy a [REST API-val](/rest/api/sql/databases/update). 

Dinamikusan módosítása a szolgáltatás a következő videó bemutatja a csomagot, és számítási méret növelése elérhető dtu-k egy önálló adatbázis.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

A szolgáltatás rétegbeli és/vagy számítási egy adatbázist hoz létre az eredeti adatbázis replikája új számítási méret és majd átáll a replikára. A folyamat során nem történik adatvesztés, de a replikára való átálláskor egy pillanatra az adatbázis felé irányuló kapcsolatok le lesznek tiltva, így lehet, hogy néhány folyamatban lévő tranzakció vissza lesz állítva. Mennyi ideig a kapcsoló-több mint az érték, de kevesebb mint 30 másodperc 99 %-ában. Ha nagy számú tranzakció van folyamatban a kapcsolatok pillanatában le vannak tiltva, akkor a hosszát a kapcsoló-több mint hosszabb lehet. 

A teljes felskálázási folyamat időtartama az adatbázis a módosítás előtti és utáni méretétől és szolgáltatásszintjétől függ. Ha például egy 250 GB-os adatbázis, amely megváltoztatja a, a vagy a Standard szolgáltatásszinten belül hat órán belüli be kell fejeződnie. A vertikális felskálázás három órán belül, amely számítási méretű prémium szolgáltatási szinten belül módosul az azonos méretű adatbázis esetén be kell fejeződnie.

> [!TIP]
> A lévő üzenetekre vonatkozó műveletek figyeléséről lásd:: [SQL REST API használatával műveleteinek kezelésére](/rest/api/sql/Operations/List), [kezelése CLI használatával az operations](/cli/azure/sql/db/op), [T-SQL használatával végzett műveletek monitorozására](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) , és ez a két PowerShell-parancsok: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) és [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Ha egy magasabb szolgáltatási szintre frissít, vagy méretű számítási, az adatbázis maximális méretét nem növekszik, hacsak Ön kifejezetten megad egy nagyobb méretű (maxsize).
* Alacsonyabb szolgáltatásszintre váltásához egy adatbázist, a használt adatbázis-terület kisebb, mint a megengedett maximális a cél szolgáltatásszint és számítási mérete kell lennie. 
* Ha alacsonyabb szolgáltatásszintre vált **prémium** , a **Standard** szinten az egy extra tárterület költséget érvényes, ha mind az adatbázis maximális mérete (1) a cél számítási mérete a támogatott, és (2) a maximális mérete meghaladja a csomagban foglalt a cél tárolási mennyiségű számítási mérete. Például 500 GB maximális mérettel P1 adatbázis van downsized S3 szintű adatbázist, ha ezután egy extra tárterület költség érvényes óta S3 támogatja az 500 GB-os maximális méretét, és a belefoglalt tárterület tárterülete 250 GB csak. Tehát az extra tárterület keletkezett az 500 GB-250 GB = 250 GB. Extra tárterület díjszabással kapcsolatos információk: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges felhasznált lemezterület mérete kisebb, mint a csomagban foglalt tárhely, majd ezzel többletköltség elkerülhető az adatbázis maximális méretének csökkentésével a csomagban foglalt adatmennyiségen. 
* Ha az adatbázis frissítése [georeplikációs](sql-database-geo-replication-portal.md) engedélyezve van, frissítse a másodlagos adatbázisok a kívánt szolgáltatásszintet és a számítási méret az elsődleges adatbázis (általános útmutatást a legjobb teljesítmény érdekében) a frissítés előtt. Frissíti egy másik, a másodlagos adatbázis frissítése először szükség.
* Ha egy adatbázis alacsonyabb szolgáltatásszintre [georeplikációs](sql-database-geo-replication-portal.md) engedélyezve van, az elsődleges adatbázisok a kívánt szolgáltatási szint alacsonyabbra és a számítási méret előtt alacsonyabb verziójúra változtatása a másodlagos adatbázis (általános útmutatást a legjobb teljesítmény érdekében). Ha alacsonyabb szolgáltatásszintre egy másik kiadásra, az elsődleges adatbázis alacsonyabb szolgáltatásszintre először megadása kötelező.
* A visszaállítási szolgáltatásajánlatok eltérőek a különböző szolgáltatásszintek esetében. Ha alacsonyabb szolgáltatásszintre a **alapszintű** szint, van egy alacsonyabb biztonsági másolat megőrzési idejének – lásd: [Azure SQL Database biztonsági másolatainak](sql-database-automated-backups.md).
* Az adatbázis új tulajdonságai csak akkor lesznek alkalmazva, ha a módosítások befejeződtek.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>DTU-alapú vásárlási modell: korlátozások a P11 és p15 csomagokhoz, amikor a maximális méret 1 TB-nál nagyobb

A P11 és P15-adatbázis a következő régiókban támogatott 1 TB-nál nagyobb a maximális méretet: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, közép-Kanada, kelet-Kanada, USA középső RÉGIÓJA, közép-Franciaország, közép-Németország, kelet-japán, Nyugat-japán, Korea középső régiója USA északi középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA East2, USA nyugati RÉGIÓJA, USA-beli államigazgatás – Virginia és Nyugat-Európa. Nagyobb, mint 1 TB-os maximális mérettel P11 és P15 adatbázisokat az alábbi szempontok és korlátozások vonatkoznak:

- Ha 1 TB-nál nagyobb a maximális méretet (4 TB-os vagy 4096 GB-os érték használata) adatbázis létrehozásakor, a create parancs egy hibaüzenettel meghiúsul, ha az adatbázis egy nem támogatott régióban van kiépítve.
- A már létező P11 és P15 adatbázisok a támogatott régiók egyikében található, a maximális tárhelyet 1 TB, 256 GB-os léptékben növelhető akár 4 TB-ig. Ha egy nagyobb méretű támogatott-e az Ön régiójában megtekintéséhez használja a [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) működik, vagy vizsgálja meg az adatbázis méretét az Azure Portalon. Frissíti egy meglévő P11 és p15-ös adatbázis csak hajtható végre egy kiszolgálószintű fő bejelentkező vagy a dbmanager adatbázis-szerepkör tagjai által. 
- Ha egy támogatott régióban egy frissítési művelet a konfigurációs azonnal frissül. Az adatbázis online állapotban marad, a frissítési folyamat során. Azonban nem használhatja a tárhelyet 1 TB tárterület teljes mennyisége mindaddig, amíg az aktuális adatbázisfájlok frissítettek az új maximális mérete. Hogy mennyi idő szükséges a frissítés alatt áll az adatbázis méretétől függ. 
- Ha a létrehozása vagy frissítése egy P11 és p15-ös adatbázis 1 TB-os és a 4 TB-os maximális méret 256 GB-os lépésekben csak választhat. A P11 és P15 létrehozásakor az alapértelmezett tárolási beállítás 1 TB-os előre kiválasztott. Adatbázisok esetén a támogatott régiók egyikében található a storage maximális, akár 4 TB-ig maximum egy új vagy meglévő önálló adatbázis lehet növelni. Az összes többi régió esetében a maximális mérete 1 TB-nál nagyobb nem növelhető. Az ár nem változik, ha 4 TB-os belefoglalt tárterület választja.
- Ha egy adatbázis maximális mérete értéke nagyobb, mint 1 TB-ot, majd azt nem lehet módosítani 1 TB-os akkor is, ha a használt tényleges tárterület 1 TB-os alatt. Ebből kifolyólag nem elő-vagy Visszaléptetés a P11-es vagy P15 nagyobb, mint 1 TB-os P11-es vagy 1 TB-os P15 1 TB-os maximális mérettel és csökkentse a méretét, P1 – P6 például számítási). Ez a korlátozás is vonatkozik a visszaállítás és másolási forgatókönyvek esetén az időpontban, beleértve a geo-visszaállítás, hosszú-távú – backup-megőrzés és -adatbázis másolatát. Ha egy adatbázis nagyobb, mint 1 TB-os maximális mérettel van konfigurálva, az adatbázis minden visszaállítási művelet nagyobb, mint 1 TB-os maximális mérettel be a P11 és p15-ös kell futnia.
- Aktív georeplikáció forgatókönyvek esetén:
   - Georeplikáció kapcsolat beállítása: Ha az elsődleges adatbázis P11 és p15-ös, az secondary(ies) is kell lennie a P11 és P15; alacsonyabb számítási méret másodlagos példány hozható létre, a rendszer elutasítja, mivel azok nem képes a több mint 1 TB.
   - Frissítése az elsődleges adatbázis georeplikációs kapcsolatban: az elsődleges adatbázison több mint 1 TB-os maximális méretének módosítása elindítja ezt a változtatást a másodlagos adatbázison. Mindkét frissítése sikeres a módosítás érvénybe léptetéséhez az elsődleges kell lennie. A több mint 1 TB-os lehetőség régió korlátozások érvényesek. Ha a másodlagos régióban, amely nem támogatja a több mint 1 TB-ot, az elsődleges nem frissül.
- A P11 és P15 adatbázisok esetén több mint 1 TB betöltése az Import/Export szolgáltatás használata nem támogatott. Az SqlPackage.exe használata [importálása](sql-database-import.md) és [exportálása](sql-database-export.md) adatokat.


## <a name="next-steps"></a>További lépések

Általános erőforrás-korlátozásaival, lásd: [SQL Database Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [SQL Database DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-single-databases.md).
