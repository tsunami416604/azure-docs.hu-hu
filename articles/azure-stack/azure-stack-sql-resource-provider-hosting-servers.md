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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.openlocfilehash: af820f90c5d8822dbdaa768b16360d534fd47828
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060042"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató az üzemeltetési kiszolgáló hozzáadása

Tárolhatja a virtuális gépen (VM) SQL-példány [Azure verem](azure-stack-poc.md), vagy a virtuális gép Azure verem környezetét, amennyiben az erőforrás-szolgáltató SQL csatlakozhat a példány kívül.

## <a name="overview"></a>Áttekintés

Egy SQL-kiszolgálót futtató hozzáadása előtt tekintse át a következő kötelező és általános.

**Kötelező követelmények**

* Engedélyezze az SQL Server-példány SQL-hitelesítést. Mivel az SQL-erőforrás-szolgáltató VM nem tartományhoz csatlakoztatott, hogy csak kapcsolódni tud egy SQL-hitelesítéssel üzemeltető kiszolgálót.
* Konfigurálja az IP-címet az SQL Server-példányok nyilvános. Az erőforrás-szolgáltató és a felhasználók számára, például webalkalmazások, felhasználói hálózati kommunikációra, ezért az SQL-példány a hálózati kapcsolatra szükség.

**Általános követelmények**

* Az erőforrás-szolgáltató és a felhasználó munkaterhelés jelölt ki az SQL-példány számára. Bármely más felhasználók által használt SQL-példány nem használható. Ez a korlátozás vonatkozik alkalmazásszolgáltatások is.
* Egy fiókot a megfelelő jogosultsággal rendelkező konfigurálása az erőforrás-szolgáltató.
* Ön az SQL Server-példányok és gazdagépeik kezeléséért.  Például az erőforrás-szolgáltató nem alkalmazza a frissítéseket, kezeli a biztonsági mentések vagy kezeli a hitelesítő adatok elforgatási.

### <a name="sql-server-virtual-machine-images"></a>SQL Server virtuálisgép-rendszerképek

SQL IaaS virtuálisgép-rendszerképek a piactér-kezelési funkción keresztül érhetők el. Ezeket a lemezképeket ugyanazok, mint az SQL virtuális gépen elérhető az Azure-ban.

Győződjön meg arról, hogy mindig a legújabb verziójának letöltése a **SQL IaaS bővítmény** egy SQL virtuális gép egy Piactéri elemet központi telepítése előtt. A IaaS bővítményt, és megfelelő portál fejlesztést tartalmaz további funkciókat, például az automatikus javítás és biztonsági mentését. A bővítmény kapcsolatos további információkért lásd: [Azure virtuális gépeken a SQL Server Agent kiterjesztésű felügyeleti feladatok automatizálására](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Egyéb módon telepítéséhez SQL virtuális gépeken, beleértve a sablonokat a [Azure verem gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Egy több csomópontos Azure veremben telepített bármely üzemeltetési kiszolgáló felhasználói előfizetés kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. Azok a felhasználói portál vagy egy megfelelő bejelentkezési azonosító egy PowerShell-munkamenetet kell létrehozni. Minden üzemeltetési kiszolgáló számlázható virtuális gép, és a megfelelő SQL-licenccel kell rendelkeznie. A szolgáltatás-rendszergazda _is_ lehet, hogy az előfizetés tulajdonosa.

### <a name="required-privileges"></a>Szükséges jogosultságok

Létrehozhat egy rendszergazda felhasználó alacsonyabb jogosultsággal, mint egy SQL SysAdmin (rendszergazda). A felhasználói engedélyek csak kell a következő műveletek:

* Adatbázis: Hozzon létre, Alter, amelyben a Containment (a mindig bekapcsolva csak), dobja el, a biztonsági mentés
* Rendelkezésre állási csoporthoz: Alter, a csatlakoztatáshoz hozzáadása adatbázis
* Bejelentkezési identitás: Hozzon létre, válassza ki, Alter, dobja el, visszavonása
* SELECT műveletek: \[fő\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), a sys.databases, \[fő\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[fő\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Adja meg az SQL Server kiszolgálót futtató önálló csatlakozás kapacitás

Használhatja az önálló (nem-magas rendelkezésre ÁLLÁSÚ) bármely SQL Server 2014 vagy SQL Server 2016 kiadása SQL-kiszolgálók. Győződjön meg arról, hogy egy rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adatait.

Egy önálló üzemeltető kiszolgálót, amely már be van állítva hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure verem operátor portálra szolgáltatás-rendszergazdaként.

2. Válassza ki **Tallózás** &gt; **felügyeleti erőforrások** &gt; **üzemeltető kiszolgálók SQL**.

   ![Az SQL futtató kiszolgálók](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   A **SQL üzemeltető kiszolgálók**, erőforrás-szolgáltató SQL csatlakozhat az SQL Server példányai, amelyek az erőforrás-szolgáltató háttér szolgál.

   ![SQL-csatoló irányítópult](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. A **üzemeltető SQL-kiszolgáló hozzáadása**, adja meg a kapcsolat adatait az SQL Server-példányhoz.

   ![Egy SQL-kiszolgálót futtató hozzáadása](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Szükség esetén adja meg a példány nevét, és adjon meg egy portszámot, ha a példány nincs hozzárendelve az alapértelmezett port a 1433-as.

   > [!NOTE]
   > Mindaddig, amíg az SQL-példány elérhetők, a felhasználó és rendszergazda Azure Resource Manager, az erőforrás-szolgáltató ellenőrzése alatt lehet tenni. Az SQL-példány __kell__ kizárólag az erőforrás-szolgáltató kiosztani.

4. Kiszolgálók hozzáadása, rendeljen hozzájuk egy meglévő SKU, vagy hozzon létre egy új Termékváltozat. A **üzemeltető kiszolgáló hozzáadása**, jelölje be **termékváltozatok**.

   * Egy meglévő SKU használni, válasszon egy elérhető SKU, és válassza **létrehozása**.
   * A Termékváltozat létrehozásához válassza **+ hozzon létre új SKU**. A **létrehozása SKU**, adja meg a szükséges adatokat, és válassza **OK**.

     > [!IMPORTANT]
     > Különleges karaktereket, szóközöket és időszakok, beleértve a nem támogatott **neve** mező. Az alábbi képernyőfelvételen a példák segítségével adja meg az értékeket a **termékcsalád**, **réteg**, és **Edition** mezőket.

     ![Hozzon létre egy Termékváltozat](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

      SKU órát is igénybe vehet egy megjeleníteni a portálon. Felhasználók nem hozható létre a Termékváltozat teljesen létrehozásáig.

### <a name="sku-notes"></a>Termékváltozat megjegyzések

SKU segítségével szolgáltatásajánlatok megkülönböztetéséhez. Lehet például egy SQL vállalati példányt, amely a következő jellemzőkkel rendelkezik:
  
* nagy kapacitású
* nagy teljesítményű
* Magas rendelkezésre állás

Azokat a csoportokat, egy nagy teljesítményű adatbázis szükséges való hozzáférés korlátozása a fenti példában egy SKU hozhat létre.

>[!TIP]
>Használjon, amely tükrözi SKU nevet, például a kapacitást és teljesítményt az SKU-kiszolgálóinak képességeit ismerteti. A név segítenek a hozzájuk tartozó adatbázisok telepítse a megfelelő SKU elősegítésére szolgál.

Ajánlott eljárásként az üzemeltetési kiszolgáló, a termékváltozatban kell rendelkeznie a azonos erőforrás és teljesítménybeli jellemzőit.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>SQL Always On rendelkezésre állási csoportok használatával magas rendelkezésre állás biztosításához

SQL Always On példányok konfigurálásához további lépéseket igényel, és megköveteli a három virtuális gépek (vagy fizikai gépek.) Ez a cikk feltételezi, hogy már rendelkezik egy Always On rendelkezésre állási csoportok alapos ismerete. További információkért tekintse át a következő cikkeket:

* [SQL Server Always On rendelkezésre állási csoportok az Azure virtuális gépeken bemutatása](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On rendelkezésre állási csoportok (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Az SQL adapter erőforrás-szolgáltató _csak_ támogatja az SQL 2016 SP1 Enterprise, vagy később példányának mindig bekapcsolva. Az adapter konfigurációs új SQL-szolgáltatások például az automatikus összehangolása szükséges.

Ezenkívül engedélyeznie kell [automatikus összehangolása](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) minden egyes példányaihoz tartozó SQL Server rendelkezésre állási csoporton.

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

   A **SQL üzemeltető kiszolgálók**, az SQL Server erőforrás-szolgáltató csatlakozhat az SQL Server tényleges példányait, átadott háttér az erőforrás-szolgáltató neve.

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
