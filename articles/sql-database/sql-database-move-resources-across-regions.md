---
title: Azure SQL Database erőforrások áthelyezése másik régióba | Microsoft Docs
description: Megtudhatja, hogyan helyezheti át a Azure SQL Databaset, az Azure SQL rugalmas készletét vagy az Azure SQL felügyelt példányát egy másik régióba.
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
ms.openlocfilehash: 2158d4120445de4c62461fb89555a1b73bc1e2b4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567166"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Azure SQL-erőforrások áthelyezése másik régióba

Ez a cikk általános munkafolyamatot mutat be, amellyel áthelyezheti Azure SQL Database önálló adatbázisát, rugalmas készletét és felügyelt példányát egy új régióba. 

## <a name="overview"></a>Áttekintés

Különböző helyzetekben érdemes áthelyeznie meglévő Azure SQL-erőforrásait az egyik régióból a másikba. Kiterjesztheti például vállalatát egy új régióra, és optimalizálni szeretné az új ügyfélkörre. Vagy a műveleteket egy másik régióba kell áthelyeznie megfelelőségi okokból. Vagy az Azure egy vadonatúj régiót bocsátott ki, amely jobb közelséget biztosít, és javítja a felhasználói élményt.  

Ez a cikk általános munkafolyamatot biztosít az erőforrások másik régióba való áthelyezéséhez. A munkafolyamat a következő lépésekből áll: 

- Az áthelyezés előfeltételeinek ellenőrzése 
- Felkészülés az erőforrások hatókörbe való áthelyezésére
- Az előkészítési folyamat figyelése
- Az áthelyezési folyamat tesztelése
- A tényleges áthelyezés kezdeményezése 
- Erőforrások eltávolítása a forrás régiójából 


> [!NOTE]
> Ez a cikk az Azure nyilvános felhőben vagy ugyanazon a szuverén felhőben lévő áttelepítésekre vonatkozik. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Önálló adatbázis áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése 

1. Hozzon létre egy cél logikai kiszolgálót minden forráskiszolgálón. 
1. Konfigurálja a tűzfalat a megfelelő kivételekkel a [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)használatával.  
1. Konfigurálja a logikai kiszolgálókat a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy az SQL Server rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. További információkért lásd: [Az Azure SQL Database biztonságának kezelése](sql-database-geo-replication-security-config.md)a vész-helyreállítás után. 
1. Ha az adatbázisok titkosítása TDE történik, és saját titkosítási kulcsot használ az Azure Key vaultban, győződjön meg arról, hogy a megfelelő titkosítási anyagok vannak kiépítve a célcsoportokban. További információ: [Azure SQL transzparens adattitkosítás ügyfél által felügyelt kulcsokkal Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Ha az adatbázis-szintű naplózás engedélyezve van, tiltsa le, és engedélyezze a kiszolgálói szintű naplózást. A feladatátvételt követően az adatbázis szintjének naplózásához szükség van a régiók közötti forgalomra, amely az áthelyezés után nem kívánatos vagy lehetséges. 
1. A kiszolgálói szintű auditok esetében ügyeljen a következőre:
   - A rendszer áthelyezi a tárolót, Log Analytics vagy az Event hub-t a meglévő naplókba. 
   - A naplózás konfigurálva van a célkiszolgálón. További információ: Ismerkedés [az SQL Database naplózási](sql-database-auditing.md)szolgáltatásával. 
1. Ha a példány hosszú távú adatmegőrzési szabályzattal (LTR) rendelkezik, a meglévő LTR biztonsági mentések továbbra is az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forrás-régióban lévő régebbi LTR biztonsági mentéseket a forráskiszolgáló használatával érheti el, még akkor is, ha a kiszolgálót törölték. 

  > [!NOTE]
  > Ez nem lesz elegendő a szuverén felhő és a nyilvános régió közötti váltáshoz. Ilyen áttelepítés esetén a LTR biztonsági mentéseket a célkiszolgálón kell áthelyezni, amely jelenleg nem támogatott. 

### <a name="prepare-resources"></a>Erőforrások előkészítése

1. Hozzon létre egy [feladatátvételi csoportot](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) a forrás logikai kiszolgálója között a cél logikai kiszolgálója között.  
1. Adja hozzá azokat az adatbázisokat, amelyeket át szeretne helyezni a feladatátvételi csoportba. 
    - A rendszer automatikusan kezdeményezi az összes hozzáadott adatbázis replikálását. További információ: [ajánlott eljárások az önálló adatbázisokkal rendelkező feladatátvételi csoportok használatához](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelése

Rendszeres időközönként meghívhatja a [Get-AzSqlDatabaseFailoverGroup-](/powershell/module/az.sql/get-azsqldatabasefailovergroup) t az adatbázisok replikációjának figyelésére a forrásról a célra. A kimeneti objektum `Get-AzSqlDatabaseFailoverGroup` tartalmazza a **ReplicationState**tulajdonságát: 
   - **ReplicationState = 2** A (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és a rendszer biztonságosan feladatátvételt hajt végre. 
   - **ReplicationState = 0** (Kivetés) azt jelzi, hogy az adatbázis még nincs bevezetve, és a feladatátvételi kísérlet sikertelen lesz. 

### <a name="test-synchronization"></a>Szinkronizálás tesztelése

Ha **ReplicationState** `2`, kapcsolódjon az egyes adatbázisokhoz vagy az adatbázisok egy részhalmazához a másodlagos `<fog-name>.secondary.database.windows.net` végpont használatával, és hajtson végre bármilyen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatok biztosításához. replikációs. 

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

1. Kapcsolódjon a célkiszolgálóra a másodlagos végpont `<fog-name>.secondary.database.windows.net`használatával.
1. A [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) használatával váltson át a másodlagos felügyelt példányra, hogy az elsődleges legyen teljes szinkronizálással. A művelet vagy sikeres lesz, vagy visszaállítja. 
1. Győződjön meg arról, hogy a parancs sikeresen befejeződött `nslook up <fog-name>.secondary.database.windows.net` a használatával annak megállapításához, hogy a DNS-CNAME bejegyzés a célként megadott régió IP-címére mutat. Ha a Switch parancs sikertelen, a CNAME nem frissül. 

### <a name="remove-the-source-databases"></a>A forrás-adatbázisok eltávolítása

Az áthelyezés befejeződése után távolítsa el a forrás régió erőforrásait a szükségtelen költségek elkerülése érdekében. 

1. Törölje a feladatátvételi csoportot a [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)használatával. 
1. Törölje az összes forrásoldali adatbázist a [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) használatával a forráskiszolgálón található összes adatbázishoz. Ekkor a rendszer automatikusan leállítja a Geo-replikációs hivatkozásokat. 
1. A [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)használatával törölje a forráskiszolgáló-kiszolgálót. 
1. Távolítsa el a Key vaultot, a naplózási tárolókat, az Event hub-t, az HRE-példányt és a többi függő erőforrást, hogy ne kelljen fizetniük. 

## <a name="move-elastic-pools"></a>Rugalmas készletek áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése 

1. Hozzon létre egy cél logikai kiszolgálót minden forráskiszolgálón. 
1. Konfigurálja a tűzfalat a megfelelő kivételekkel a [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)használatával. 
1. Konfigurálja a logikai kiszolgálókat a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy az SQL Server rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. További információkért lásd: [Az Azure SQL Database biztonságának kezelése](sql-database-geo-replication-security-config.md)a vész-helyreállítás után. 
1. Ha az adatbázisok titkosítása TDE történik, és saját titkosítási kulcsot használ az Azure Key vaultban, győződjön meg arról, hogy a megfelelő titkosítási anyag van kiépítve a céltartományban.
1. Hozzon létre egy cél rugalmas készletet minden forrás rugalmas készlethez, és győződjön meg arról, hogy a készlet ugyanabban a szolgáltatási szinten jön létre, ugyanazzal a névvel és ugyanazzal a mérettel. 
1. Ha engedélyezve van az adatbázis-szintű naplózás, tiltsa le, és engedélyezze a kiszolgálói szintű naplózást. A feladatátvételt követően az adatbázis-szintű naplózáshoz a régión kívüli forgalom szükséges, amely nem kívánatos, vagy lehetséges az áthelyezés után is. 
1. A kiszolgálói szintű auditok esetében ügyeljen a következőre:
    - A rendszer áthelyezi a tárolót, Log Analytics vagy az Event hub-t a meglévő naplókba.
    - A naplózási konfiguráció a célkiszolgálón van konfigurálva. További információ: [SQL Database Auditing](sql-database-auditing.md).
1. Ha a példány hosszú távú adatmegőrzési szabályzattal (LTR) rendelkezik, a meglévő LTR biztonsági mentések továbbra is az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forrás-régióban lévő régebbi LTR biztonsági mentéseket a forráskiszolgáló használatával érheti el, még akkor is, ha a kiszolgálót törölték. 

  > [!NOTE]
  > Ez nem lesz elegendő a szuverén felhő és a nyilvános régió közötti váltáshoz. Ilyen áttelepítés esetén a LTR biztonsági mentéseket a célkiszolgálón kell áthelyezni, amely jelenleg nem támogatott. 

### <a name="prepare-to-move"></a>Felkészülés az áthelyezésre
 
1.  Hozzon létre egy külön [feladatátvételi csoportot](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) a forrás logikai kiszolgáló és a hozzá tartozó rugalmas készlet között a célkiszolgálón. 
1.  Adja hozzá a készletben lévő összes adatbázist a feladatátvételi csoporthoz. 
    - A rendszer automatikusan kezdeményezi a hozzáadott adatbázisok replikálását. További információ: [ajánlott eljárások a rugalmas készletekkel rendelkező feladatátvételi csoportokhoz](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Habár több rugalmas készletet tartalmazó feladatátvételi csoportot is létrehozhat, javasoljuk, hogy mindegyik készlethez hozzon létre egy külön feladatátvételi csoportot. Ha nagy számú adatbázist használ több rugalmas készletben, amelyeket át kell helyeznie, akkor párhuzamosan futtathatja az előkészítési lépéseket, majd elindíthatja az áthelyezés lépést párhuzamosan. Ez a folyamat jobb skálázást tesz lehetővé, és kevesebb időt vesz igénybe, mint a több rugalmas készlet ugyanabban a feladatátvételi csoportban. 

### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelése

Rendszeres időközönként meghívhatja a [Get-AzSqlDatabaseFailoverGroup-](/powershell/module/az.sql/get-azsqldatabasefailovergroup) t az adatbázisok replikációjának figyelésére a forrásról a célra. A kimeneti objektum `Get-AzSqlDatabaseFailoverGroup` tartalmazza a **ReplicationState**tulajdonságát: 
   - **ReplicationState = 2** A (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és a rendszer biztonságosan feladatátvételt hajt végre. 
   - **ReplicationState = 0** (Kivetés) azt jelzi, hogy az adatbázis még nincs bevezetve, és a feladatátvételi kísérlet sikertelen lesz. 

### <a name="test-synchronization"></a>Szinkronizálás tesztelése
 
Ha **ReplicationState** `2`, kapcsolódjon az egyes adatbázisokhoz vagy az adatbázisok egy részhalmazához a másodlagos `<fog-name>.secondary.database.windows.net` végpont használatával, és hajtson végre bármilyen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatok biztosításához. replikációs. 

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése
 
1. Kapcsolódjon a célkiszolgálóra a másodlagos végpont `<fog-name>.secondary.database.windows.net`használatával.
1. A [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) használatával váltson át a másodlagos felügyelt példányra, hogy az elsődleges legyen teljes szinkronizálással. A művelet vagy sikeres lesz, vagy visszaállítja. 
1. Győződjön meg arról, hogy a parancs sikeresen befejeződött `nslook up <fog-name>.secondary.database.windows.net` a használatával annak megállapításához, hogy a DNS-CNAME bejegyzés a célként megadott régió IP-címére mutat. Ha a Switch parancs sikertelen, a CNAME nem frissül. 

### <a name="remove-the-source-elastic-pools"></a>A forrás rugalmas készletek eltávolítása
 
Az áthelyezés befejeződése után távolítsa el a forrás régió erőforrásait a szükségtelen költségek elkerülése érdekében. 

1. Törölje a feladatátvételi csoportot a [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)használatával.
1. Törölje az összes forrás rugalmas készletet a forráskiszolgálón a [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)használatával. 
1. A [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)használatával törölje a forráskiszolgáló-kiszolgálót. 
1. Távolítsa el a Key vaultot, a naplózási tárolókat, az Event hub-t, az HRE-példányt és a többi függő erőforrást, hogy ne kelljen fizetniük. 

## <a name="move-managed-instance"></a>Felügyelt példány áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése
 
1. Mindegyik forrás felügyelt példányhoz hozzon létre egy célként felügyelt példányt, amely azonos méretű a célként megadott régióban.  
1. A hálózat konfigurálása felügyelt példányhoz. További információ: [hálózati konfiguráció](sql-database-howto-managed-instance.md#network-configuration).
1. Konfigurálja a célként szolgáló főkiszolgáló-adatbázist a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy az SQL Server rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. 
1. Ha az adatbázisok titkosítása TDE történik, és saját titkosítási kulcsot használ az Azure Key vaultban, győződjön meg arról, hogy a AKV azonos titkosítási kulccsal rendelkeznek a forrás-és a célként megadott régiókban is. További információ: [TDE az ügyfél által felügyelt kulcsokkal Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Ha a példány naplózása engedélyezve van, ügyeljen arra, hogy:
    - A rendszer áthelyezi a tárolási tárolót vagy az Event hub-t a meglévő naplókba. 
    - A naplózás konfigurálva van a cél példányon. További információ: [a felügyelt példányok naplózása](sql-database-managed-instance-auditing.md).
1. Ha a példány hosszú távú adatmegőrzési szabályzattal (LTR) rendelkezik, a meglévő LTR biztonsági mentések továbbra is az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forrás-régióban lévő régebbi LTR biztonsági mentéseket a forráskiszolgáló használatával érheti el, még akkor is, ha a kiszolgálót törölték. 

  > [!NOTE]
  > Ez nem lesz elegendő a szuverén felhő és a nyilvános régió közötti váltáshoz. Ilyen áttelepítés esetén a LTR biztonsági mentéseket a célkiszolgálón kell áthelyezni, amely jelenleg nem támogatott. 

### <a name="prepare-resources"></a>Erőforrások előkészítése

Hozzon létre egy feladatátvételi csoportot az egyes forrásoldali példányok és a megfelelő cél példány között.
    - Az egyes példányokon lévő összes adatbázis replikációja automatikusan megkezdődik. További információért lásd: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md) .

 
### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelése

Rendszeres időközönként meghívhatja a [Get-AzSqlDatabaseFailoverGroup-](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) t az adatbázisok replikációjának figyelésére a forrásról a célra. A kimeneti objektum `Get-AzSqlDatabaseFailoverGroup` tartalmazza a **ReplicationState**tulajdonságát: 
   - **ReplicationState = 2** A (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és a rendszer biztonságosan feladatátvételt hajt végre. 
   - **ReplicationState = 0** (Kivetés) azt jelzi, hogy az adatbázis még nincs bevezetve, és a feladatátvételi kísérlet sikertelen lesz. 

### <a name="test-synchronization"></a>Szinkronizálás tesztelése

Ha **ReplicationState** `2`, kapcsolódjon az egyes adatbázisokhoz vagy az adatbázisok egy részhalmazához a másodlagos `<fog-name>.secondary.database.windows.net` végpont használatával, és hajtson végre bármilyen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatok biztosításához. replikációs. 

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése 

1. Kapcsolódjon a célkiszolgálóra a másodlagos végpont `<fog-name>.secondary.database.windows.net`használatával.
1. A [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) használatával váltson át a másodlagos felügyelt példányra, hogy az elsődleges legyen teljes szinkronizálással. A művelet vagy sikeres lesz, vagy visszaállítja. 
1. Győződjön meg arról, hogy a parancs sikeresen befejeződött `nslook up <fog-name>.secondary.database.windows.net` a használatával annak megállapításához, hogy a DNS-CNAME bejegyzés a célként megadott régió IP-címére mutat. Ha a Switch parancs sikertelen, a CNAME nem frissül. 

### <a name="remove-the-source-managed-instances"></a>A forrás felügyelt példányainak eltávolítása
Az áthelyezés befejeződése után távolítsa el a forrás régió erőforrásait a szükségtelen költségek elkerülése érdekében. 

1. Törölje a feladatátvételi csoportot a [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)használatával. Ezzel elkerüli a feladatátvételi csoport konfigurációját, és leállítja a Geo-replikációs kapcsolatokat a két példány között. 
1. Törölje a forrás felügyelt példányt a [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)használatával. 
1. Távolítsa el az erőforráscsoport további erőforrásait, például a virtuális fürtöt, a virtuális hálózatot és a biztonsági csoportot. 

## <a name="next-steps"></a>További lépések 

Azure SQL Database [kezelése](sql-database-manage-after-migration.md) a Migrálás után. 

