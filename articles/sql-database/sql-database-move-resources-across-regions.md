---
title: Erőforrások áthelyezése másik régióba
description: Megtudhatja, hogyan helyezheti át az Azure SQL-adatbázist, az Azure SQL rugalmas készletet vagy az Azure SQL által felügyelt példányt egy másik régióba.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821433"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Az Azure SQL-erőforrások áthelyezése egy másik régióba

Ez a cikk egy általános munkafolyamatot ad meg az Azure SQL Database egyetlen adatbázis, rugalmas készlet és felügyelt példány áthelyezése egy új régióba. 

## <a name="overview"></a>Áttekintés

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni a meglévő Azure SQL-erőforrások egyik régióból a másikba. Például kiterjesztheti vállalkozását egy új régióra, és optimalizálni szeretné azt az új ügyfélkörhöz. Vagy megfelelőségi okokból át kell helyeznie a műveleteket egy másik régióba. Vagy az Azure kiadott egy vadonatúj régiót, amely jobb közelséget biztosít, és javítja az ügyfélélményt.  

Ez a cikk általános munkafolyamatot biztosít az erőforrások másik régióba való áthelyezéséhez. A munkafolyamat a következő lépésekből áll: 

- Az áthelyezés előfeltételeinek ellenőrzése 
- Felkészülés az erőforrások hatókörbe való áthelyezésére
- Az előkészítési folyamat figyelemmel kísérése
- Az áthelyezési folyamat tesztelése
- A tényleges áthelyezés kezdeményezése 
- Az erőforrások eltávolítása a forrásrégióból 


> [!NOTE]
> Ez a cikk az Azure nyilvános felhőben vagy ugyanazon a szuverén felhőben történő áttelepítések vonatkozik. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Egyetlen adatbázis áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése 

1. Hozzon létre egy céllogikai kiszolgálót minden forráskiszolgálóhoz. 
1. Konfigurálja a tűzfalat a megfelelő kivételekkel a [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)használatával.  
1. Konfigurálja a logikai kiszolgálókat a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy az SQL-kiszolgáló rendszergazdája, a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. További információ: [Az Azure SQL-adatbázis biztonságának kezelése a vészhelyreállítás után.](sql-database-geo-replication-security-config.md) 
1. Ha az adatbázisok tde titkosítással vannak titkosítva, és saját titkosítási kulcsot használ az Azure key vaultban, győződjön meg arról, hogy a megfelelő titkosítási anyag van kiépítve a célrégiókban. További információ: [Azure SQL Transparent Data Encryption with customer-managed keys in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Ha az adatbázisszintű naplózás engedélyezve van, tiltsa le, és engedélyezze a kiszolgálószintű naplózást. A feladatátvétel után az adatbázisszintű naplózáshoz régióközi forgalomra van szükség, amely nem lesz kívánatos vagy lehetséges az áthelyezés után. 
1. A kiszolgálószintű auditok esetén győződjön meg arról, hogy:
   - A tároló, a Log Analytics vagy az eseményközpont a meglévő naplónaplók kal a célrégióba kerül. 
   - A naplózás a célkiszolgálón van konfigurálva. További információ: [Az SQL-adatbázisok naplózásának első lépései](sql-database-auditing.md)című témakörben talál. 
1. Ha a példány hosszú távú adatmegőrzési házirenddel (LTR) rendelkezik, a meglévő LTR-biztonsági mentések az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forráskiszolgáló segítségével hozzáférhet a forrásrégió régebbi LTR biztonsági másolataihoz, még akkor is, ha a kiszolgáló törlődik. 

  > [!NOTE]
  > Ez nem lesz elegendő a szuverén felhő és egy nyilvános régió közötti mozgáshoz. Az ilyen áttelepítéshez az LTR biztonsági másolatait át kell állítani a célkiszolgálóra, amely jelenleg nem támogatott. 

### <a name="prepare-resources"></a>Erőforrások előkészítése

1. Hozzon létre [feladatátvételi csoportot](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) a forrás logikai kiszolgálója és a cél logikai kiszolgálója között.  
1. Adja hozzá azátmozgatot a feladatátvételi csoportba áthelyezni kívánt adatbázisokat. 
    - Az összes hozzáadott adatbázis replikációja automatikusan megkezdődik. További információt a [feladatátvevő csoportok egyetlen adatbázissal való használatának gyakorlati tanácsai című témakörben talál.](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools) 
 
### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelemmel kísérése

Rendszeresen [meghívhatGet-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) az adatbázisok replikációjának figyeléséhez a forrásról a célra. A kimeneti `Get-AzSqlDatabaseFailoverGroup` objektum tartalmaz egy tulajdonságot a **ReplicationState**: 
   - **A ReplicationState = 2** (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és biztonságosan átadható. 
   - **ReplicationState = 0** (SEEDING) azt jelzi, hogy az adatbázis még nincs elvetve, és a feladatátvételi kísérlet sikertelen lesz. 

### <a name="test-synchronization"></a>Teszt szinkronizálása

Miután **a ReplicationState** állapot , `2`csatlakozzon az egyes adatbázisokhoz `<fog-name>.secondary.database.windows.net` vagy adatbázisok részhalmazához a másodlagos végpont használatával, és végezzen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatreplikáció biztosítása érdekében. 

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

1. Csatlakozzon a célkiszolgálóhoz a `<fog-name>.secondary.database.windows.net`másodlagos végpont használatával.
1. A [Switch-AzSqlDatabaseFailoverGroup segítségével](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) váltson a másodlagos felügyelt példányt elsődlegesvé teljes szinkronizálással. Ez a művelet vagy sikeres lesz, vagy visszaáll. 
1. Ellenőrizze, hogy a parancs `nslook up <fog-name>.secondary.database.windows.net` sikeresen befejeződött-e, és győződjön meg arról, hogy a DNS CNAME bejegyzés a célrégió IP-címéhez mutat. Ha a kapcsolóparancs sikertelen, a CNAME nem frissül. 

### <a name="remove-the-source-databases"></a>A forrásadatbázisok eltávolítása

Miután az áthelyezés befejeződött, távolítsa el az erőforrásokat a forrásrégióban a szükségtelen költségek elkerülése érdekében. 

1. Törölje a feladatátvételi csoportot az [Remove-AzSqlDatabaseFailoverGroup csoport használatával.](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) 
1. Törölje az egyes forrásadatbázisokat az [Eltávolítás-AzSqlDatabase segítségével](/powershell/module/az.sql/remove-azsqldatabase) a forráskiszolgáló egyes adatbázisaihoz. Ez automatikusan megszakítja a georeplikációs kapcsolatokat. 
1. Törölje a forráskiszolgálót az [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)segítségével. 
1. Távolítsa el a key vault, a naplózási tárolók, az eseményközpont, az AAD-példány és más függő erőforrások at, hogy ne kelljen fizetni ük. 

## <a name="move-elastic-pools"></a>Rugalmas készletek mozgatása

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése 

1. Hozzon létre egy céllogikai kiszolgálót minden forráskiszolgálóhoz. 
1. Konfigurálja a tűzfalat a megfelelő kivételekkel a [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)használatával. 
1. Konfigurálja a logikai kiszolgálókat a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy az SQL-kiszolgáló rendszergazdája, a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. További információ: [Az Azure SQL-adatbázis biztonságának kezelése a vészhelyreállítás után.](sql-database-geo-replication-security-config.md) 
1. Ha az adatbázisok tde-vel vannak titkosítva, és saját titkosítási kulcsot használ az Azure key vaultban, győződjön meg arról, hogy a megfelelő titkosítási anyag van kiépítve a célrégióban.
1. Hozzon létre egy cél rugalmas készlet minden forrás rugalmas készlet, ügyelve arra, hogy a készlet ugyanabban a szolgáltatási rétegben jön létre, az azonos nevű és azonos méretű. 
1. Ha egy adatbázisszintű naplózás engedélyezve van, tiltsa le, és engedélyezze a kiszolgálószintű naplózást. A feladatátvétel után az adatbázis-szintű naplózás hoz szükség régióközi forgalom, amely nem kívánatos, vagy lehetséges az áthelyezés után. 
1. A kiszolgálószintű auditok esetén győződjön meg arról, hogy:
    - A tároló, a Log Analytics vagy az eseményközpont a meglévő naplónaplók kal a célrégióba kerül.
    - A naplózási konfiguráció a célkiszolgálón van konfigurálva. További információt az [SQL-adatbázis naplózása című témakörben talál.](sql-database-auditing.md)
1. Ha a példány hosszú távú adatmegőrzési házirenddel (LTR) rendelkezik, a meglévő LTR-biztonsági mentések az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forráskiszolgáló segítségével hozzáférhet a forrásrégió régebbi LTR biztonsági másolataihoz, még akkor is, ha a kiszolgáló törlődik. 

  > [!NOTE]
  > Ez nem lesz elegendő a szuverén felhő és egy nyilvános régió közötti mozgáshoz. Az ilyen áttelepítéshez az LTR biztonsági másolatait át kell állítani a célkiszolgálóra, amely jelenleg nem támogatott. 

### <a name="prepare-to-move"></a>Felkészülés az áthelyezésre
 
1.  Hozzon létre egy külön [feladatátvételi csoportot](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) a forráslogikai kiszolgálón lévő rugalmas készletek és a célkiszolgálón lévő megfelelő rugalmas készlet között. 
1.  Adja hozzá a készlet összes adatbázisát a feladatátvételi csoporthoz. 
    - A hozzáadott adatbázisok replikációja automatikusan megkezdődik. További információt a [rugalmas készletekkel rendelkező feladatátvételi csoportok ajánlott eljárásokban](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)talál. 

  > [!NOTE]
  > Bár lehetőség van egy több rugalmas készletet tartalmazó feladatátvételi csoport létrehozására, ezért javasoljuk, hogy hozzon létre egy külön feladatátvételi csoportot minden készlethez. Ha több rugalmas készleten keresztül számos adatbázist kell áthelyeznie, párhuzamosan futtathatja az előkészítési lépéseket, majd párhuzamosan kezdeményezheti az áthelyezési lépést. Ez a folyamat jobban skálázódik, és kevesebb időt vesz igénybe, mint ha több rugalmas készlet van ugyanabban a feladatátvételi csoportban. 

### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelemmel kísérése

Rendszeresen [meghívhatGet-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) az adatbázisok replikációjának figyeléséhez a forrásról a célra. A kimeneti `Get-AzSqlDatabaseFailoverGroup` objektum tartalmaz egy tulajdonságot a **ReplicationState**: 
   - **A ReplicationState = 2** (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és biztonságosan átadható. 
   - **ReplicationState = 0** (SEEDING) azt jelzi, hogy az adatbázis még nincs elvetve, és a feladatátvételi kísérlet sikertelen lesz. 

### <a name="test-synchronization"></a>Teszt szinkronizálása
 
Miután **a ReplicationState** állapot , `2`csatlakozzon az egyes adatbázisokhoz `<fog-name>.secondary.database.windows.net` vagy adatbázisok részhalmazához a másodlagos végpont használatával, és végezzen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatreplikáció biztosítása érdekében. 

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése
 
1. Csatlakozzon a célkiszolgálóhoz a `<fog-name>.secondary.database.windows.net`másodlagos végpont használatával.
1. A [Switch-AzSqlDatabaseFailoverGroup segítségével](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) váltson a másodlagos felügyelt példányt elsődlegesvé teljes szinkronizálással. Ez a művelet vagy sikeres lesz, vagy visszaáll. 
1. Ellenőrizze, hogy a parancs `nslook up <fog-name>.secondary.database.windows.net` sikeresen befejeződött-e, és győződjön meg arról, hogy a DNS CNAME bejegyzés a célrégió IP-címéhez mutat. Ha a kapcsolóparancs sikertelen, a CNAME nem frissül. 

### <a name="remove-the-source-elastic-pools"></a>A forrásrugalmas készletek eltávolítása
 
Miután az áthelyezés befejeződött, távolítsa el az erőforrásokat a forrásrégióban a szükségtelen költségek elkerülése érdekében. 

1. Törölje a feladatátvételi csoportot az [Remove-AzSqlDatabaseFailoverGroup csoport használatával.](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)
1. Törölje az egyes forráselasztikus készleteket a forráskiszolgálón az [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)használatával. 
1. Törölje a forráskiszolgálót az [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)segítségével. 
1. Távolítsa el a key vault, a naplózási tárolók, az eseményközpont, az AAD-példány és más függő erőforrások at, hogy ne kelljen fizetni ük. 

## <a name="move-managed-instance"></a>Felügyelt példány áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése
 
1. Minden egyes forrás felügyelt példányhozzon létre egy azonos méretű cél felügyelt példányt a célrégióban.  
1. Konfigurálja a hálózatot felügyelt példányhoz. További információt a [hálózati konfiguráció](sql-database-howto-managed-instance.md#network-configuration)című témakörben talál.
1. Konfigurálja a célfőadatbázist a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy az SQL-kiszolgáló rendszergazdája, a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. 
1. Ha az adatbázisok tde titkosítással vannak titkosítva, és saját titkosítási kulcsot használ az Azure key vaultban, győződjön meg arról, hogy az Azonos titkosítási kulcsokkal rendelkező AKV létezik a forrás- és a célrégiókban. További információ: [TDE az ügyfél által felügyelt kulcsokkal az Azure Key Vaultban.](transparent-data-encryption-byok-azure-sql.md)
1. Ha a példány naplózása engedélyezve van, győződjön meg arról, hogy:
    - A tárolótároló vagy a meglévő naplókat tartalmazó eseményközpont a célterületre kerül. 
    - A naplózás a célpéldányon van konfigurálva. További információt a [felügyelt példánynaplózás című témakörben talál.](sql-database-managed-instance-auditing.md)
1. Ha a példány hosszú távú adatmegőrzési házirenddel (LTR) rendelkezik, a meglévő LTR-biztonsági mentések az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forráskiszolgáló segítségével hozzáférhet a forrásrégió régebbi LTR biztonsági másolataihoz, még akkor is, ha a kiszolgáló törlődik. 

  > [!NOTE]
  > Ez nem lesz elegendő a szuverén felhő és egy nyilvános régió közötti mozgáshoz. Az ilyen áttelepítéshez az LTR biztonsági másolatait át kell állítani a célkiszolgálóra, amely jelenleg nem támogatott. 

### <a name="prepare-resources"></a>Erőforrások előkészítése

Hozzon létre egy feladatátvételi csoportot az egyes forráspéldányok és a megfelelő célpéldány között.
    - Az egyes példányok összes adatbázisának replikációja automatikusan megkezdődik. További információt az [Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md) című témakörben talál.

 
### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelemmel kísérése

Rendszeresen [meghívhatGet-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) az adatbázisok replikációjának figyeléséhez a forrásról a célra. A kimeneti `Get-AzSqlDatabaseFailoverGroup` objektum tartalmaz egy tulajdonságot a **ReplicationState**: 
   - **A ReplicationState = 2** (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és biztonságosan átadható. 
   - **ReplicationState = 0** (SEEDING) azt jelzi, hogy az adatbázis még nincs elvetve, és a feladatátvételi kísérlet sikertelen lesz. 

### <a name="test-synchronization"></a>Teszt szinkronizálása

Miután **a ReplicationState** állapot , `2`csatlakozzon az egyes adatbázisokhoz `<fog-name>.secondary.database.windows.net` vagy adatbázisok részhalmazához a másodlagos végpont használatával, és végezzen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatreplikáció biztosítása érdekében. 

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése 

1. Csatlakozzon a célkiszolgálóhoz a `<fog-name>.secondary.database.windows.net`másodlagos végpont használatával.
1. A [Switch-AzSqlDatabaseFailoverGroup segítségével](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) váltson a másodlagos felügyelt példányt elsődlegesvé teljes szinkronizálással. Ez a művelet vagy sikeres lesz, vagy visszaáll. 
1. Ellenőrizze, hogy a parancs `nslook up <fog-name>.secondary.database.windows.net` sikeresen befejeződött-e, és győződjön meg arról, hogy a DNS CNAME bejegyzés a célrégió IP-címéhez mutat. Ha a kapcsolóparancs sikertelen, a CNAME nem frissül. 

### <a name="remove-the-source-managed-instances"></a>A forrás felügyelt példányainak eltávolítása
Miután az áthelyezés befejeződött, távolítsa el az erőforrásokat a forrásrégióban a szükségtelen költségek elkerülése érdekében. 

1. Törölje a feladatátvételi csoportot az [Remove-AzSqlDatabaseFailoverGroup csoport használatával.](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) Ezzel megszakítja a feladatátvételi csoport konfigurációját, és megszakítja a georeplikációs kapcsolatokat a két példány között. 
1. Törölje a felügyelt forráspéldányt az [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)segítségével. 
1. Távolítsa el az erőforráscsoport további erőforrásait, például a virtuális fürtöt, a virtuális hálózatot és a biztonsági csoportot. 

## <a name="next-steps"></a>További lépések 

Az Azure SQL-adatbázis áttelepítése után [kezelheti.](sql-database-manage-after-migration.md) 

