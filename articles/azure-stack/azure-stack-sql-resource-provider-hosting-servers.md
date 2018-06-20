---
title: Kiszolgálók Azure veremben üzemeltető SQL |} Microsoft Docs
description: Hogyan adhat az SQL-példány az SQL-Adapter erőforrás-szolgáltató használatával történő üzembe helyezéséhez.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265228"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató az üzemeltetési kiszolgáló hozzáadása

Tárolhatja a virtuális gépen (VM) SQL-példány [Azure verem](azure-stack-poc.md), vagy a virtuális gép Azure verem környezetét, amennyiben az erőforrás-szolgáltató SQL csatlakozhat a példány kívül.

## <a name="overview"></a>Áttekintés

Üzemeltetési SQL-kiszolgáló általános követelményei a következők:

* Az erőforrás-szolgáltató és a felhasználó munkaterhelés az SQL-példány dedikált való használatra. Bármely más felhasználók által használt SQL-példány nem használható. Ez a korlátozás vonatkozik alkalmazásszolgáltatások is.
* Az SQL-erőforrás-szolgáltató VM nem tartományhoz csatlakoztatott, és csak kapcsolódhatnak az SQL-hitelesítéssel.
* Konfigurálnia kell egy fiók megfelelő jogosultságokkal az erőforrás-szolgáltató általi használatra.
* Az erőforrás-szolgáltató és a felhasználók számára, például webalkalmazások, használ a felhasználói hálózati, így az SQL-példány a hálózati kapcsolatra szükség. Ez a követelmény általában azt jelenti, hogy az IP-Címek az SQL Server-példányok nyilvános hálózaton kell lennie.
* Az SQL Server-példányok és gazdagépeik-kezelés a megosztásról Ön dönt. Például az erőforrás-szolgáltató nem alkalmazza a frissítéseket, kezeli a biztonsági mentések vagy kezeli a hitelesítő adatok elforgatási.
* Is használhatja, amelyek támogatják a különböző osztályú SQL funkcióit, például a teljesítmény és a magas rendelkezésre állás, az AlwaysOn funkciót használó SKU.

### <a name="sql-server-virtual-machine-images"></a>SQL Server virtuálisgép-rendszerképek

SQL IaaS virtuálisgép-rendszerképek a piactér-kezelési funkción keresztül érhetők el. Ezeket a lemezképeket ugyanazok, mint az SQL virtuális gépen elérhető az Azure-ban.

Győződjön meg arról, hogy mindig a legújabb verziójának letöltése a **SQL IaaS bővítmény** egy virtuális gép egy Piactéri elemet központi telepítése előtt.  A IaaS bővítményt, és megfelelő portál fejlesztést tartalmaz további funkciókat, például az automatikus javítás és biztonsági mentését.

Egyéb módon telepítéséhez SQL virtuális gépeken, beleértve a sablonokat a [Azure verem gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Egy több csomópontos Azure veremben telepített bármely üzemeltetési kiszolgáló felhasználói előfizetés kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. Azok a felhasználói portál vagy egy megfelelő bejelentkezési azonosító egy PowerShell-munkamenetet kell létrehozni. Minden üzemeltetési kiszolgáló számlázható virtuális gép, és a megfelelő SQL-licenccel kell rendelkeznie. A szolgáltatás-rendszergazda _is_ lehet, hogy az előfizetés tulajdonosa.

### <a name="required-privileges"></a>Szükséges jogosultságok

Létrehozhat egy rendszergazda felhasználó alacsonyabb jogosultságokkal, amely egy SQL SysAdmin (rendszergazda). A felhasználói engedélyek csak kell a következő műveletek:

- Adatbázis: Hozzon létre, Alter, amelyben a Containment (a mindig bekapcsolva csak), dobja el, a biztonsági mentés
- Rendelkezésre állási csoporthoz: Alter, a csatlakoztatáshoz hozzáadása adatbázis
- Bejelentkezési identitás: Hozzon létre, válassza ki, Alter, dobja el, visszavonása
- SELECT műveletek: \[fő\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), a sys.databases, \[fő\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[fő\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Adja meg az SQL Server kiszolgálót futtató önálló csatlakozás kapacitás

Használhatja az önálló (nem-magas rendelkezésre ÁLLÁSÚ) bármely SQL Server 2014 vagy SQL Server 2016 kiadása SQL-kiszolgálók. Győződjön meg arról, hogy egy rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adatait.

Egy önálló üzemeltető kiszolgálót, amely már be van állítva hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure verem operátor portálra szolgáltatás-rendszergazdaként.

2. Válassza ki **Tallózás** &gt; **felügyeleti erőforrások** &gt; **üzemeltető kiszolgálók SQL**.

   ![Az SQL futtató kiszolgálók](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   A **SQL üzemeltető kiszolgálók**, erőforrás-szolgáltató SQL csatlakozhat az SQL Server példányai, amelyek az erőforrás-szolgáltató háttér szolgál.

   ![SQL-csatoló irányítópult](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Az űrlap kitöltése a kapcsolódási adatait. az SQL Server-példány.

   ![Egy SQL-kiszolgálót futtató hozzáadása](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Lehetősége van a példány nevét tartalmazza, és adjon meg egy portszámot, ha a példány nincs hozzárendelve az alapértelmezett port a 1433-as.

   > [!NOTE]
   > Mindaddig, amíg az SQL-példány elérhetők, a felhasználó és rendszergazda Azure Resource Manager, az erőforrás-szolgáltató ellenőrzése alatt lehet tenni. Az SQL-példány __kell__ kizárólag az erőforrás-szolgáltató kiosztani.

4. Kiszolgálók hozzáadása során, akkor hozzá kell rendelnie azokat egy új vagy meglévő SKU szolgáltatásajánlatok megkülönböztetéséhez. Lehet például egy SQL vállalati példányt, amely biztosítja:
  
   - adatbázis-kapacitás
   - az automatikus biztonsági mentés
   - az egyes részlegek nagy teljesítményű kiszolgálók

   Az üzemeltetési kiszolgáló, a termékváltozatban kell ugyanazokat a képességeket. A **neve** a Termékváltozat tulajdonságainak tükröznie kell, így a felhasználók a megfelelő SKU tudjanak telepíteni az adatbázisok.

   > [!IMPORTANT]
   > Nem támogatottak a különleges karaktereket, szóközöket és időszakok, beleértve a **termékcsalád** vagy **réteg** neve, amikor az SQL és a MySQL-szolgáltatók hoz létre a Termékváltozat.

   Példa:

   ![Hozzon létre Termékváltozat](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > SKU órát is igénybe vehet egy megjeleníteni a portálon. Felhasználók nem hozható létre a Termékváltozat teljesen létrehozásáig.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>SQL Always On rendelkezésre állási csoportok használatával magas rendelkezésre állás biztosításához

SQL Always On példányok konfigurálásához további lépéseket igényel, és szükséges legalább három virtuális gépek (vagy fizikai gépek.) Ez a cikk feltételezi, hogy már rendelkezik egy Always On rendelkezésre állási csoportok alapos ismerete. További információkért lásd:

* [SQL Server Always On rendelkezésre állási csoportok az Azure virtuális gépeken bemutatása](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On rendelkezésre állási csoportok (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Az SQL adapter erőforrás-szolgáltató _csak_ támogatja az SQL 2016 SP1 Enterprise, vagy később példányának mindig bekapcsolva. Az adapter konfigurációs új SQL-szolgáltatások például az automatikus összehangolása szükséges.

Előző listájának követelmények mellett engedélyeznie kell az [automatikus összehangolása](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) minden egyes példányaihoz tartozó SQL Server rendelkezésre állási csoporton.

Automatikus összehangolása összes példánya engedélyezéséhez módosítsa, majd futtassa a következő SQL-parancsot az összes olyan példány:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

A másodlagos példányokra szerkesztheti, és futtassa a következő SQL-parancsot az összes olyan példány:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>SQL Always On üzemeltető kiszolgálók hozzáadása

1. A szolgáltatás-rendszergazdaként jelentkezzen be a verem felügyeleti portálján

2. Válassza ki **Tallózás** &gt; **felügyeleti erőforrások** &gt; **üzemeltető kiszolgálók SQL** &gt; **+ Hozzáadás**.

   A **SQL üzemeltető kiszolgálók** kapcsolódás az SQL Server erőforrás-szolgáltató az SQL Server tényleges példányait, átadott háttér az erőforrás-szolgáltató neve.

3. Töltse ki az űrlapot a kapcsolódási adatait. az SQL Server-példányhoz. Győződjön meg arról, hogy használja-e a teljes Tartománynevét a címe mindig figyelő (opcionális port számát.) Adja meg a konfigurált rendszergazdai jogosultságokkal rendelkező fiók adatait.

4. Always On rendelkezésre állási csoportnak jelölőnégyzet SQL Always On rendelkezésre állási csoportnak példányok támogatásának engedélyezéséhez.

   ![Mindig engedélyezése](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adja hozzá az SQL Always On példány egy másikra.

   > [!IMPORTANT]
   > Önálló kiszolgálók nem keverhetők az Always On osztályt a azonos Termékváltozat. Kísérlet történt kombinálhatók típusok hozzáadása az első üzemeltetési kiszolgáló eredmények a hiba után.

## <a name="make-the-sql-databases-available-to-users"></a>Az SQL-adatbázisok elérhetővé tétele a felhasználók számára

Tervek és SQL-adatbázisok a felhasználók számára elérhetővé ajánlatok létrehozása. Adja hozzá a **Microsoft.SqlAdapter** szolgáltatás pedig a tervet, és adja hozzá az alapértelmezett kvótát, vagy létrehozhat egy új kvótát.

![Tervek és adatbázisokat tartalmazza ajánlatok létrehozása](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>További lépések

[Adatbázisok hozzáadása](azure-stack-sql-resource-provider-databases.md)
