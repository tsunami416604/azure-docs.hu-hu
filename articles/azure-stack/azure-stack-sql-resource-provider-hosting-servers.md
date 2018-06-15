---
title: Kiszolgálók Azure veremben üzemeltető SQL |} Microsoft Docs
description: SQL-példány az SQL Adapter erőforrás-szolgáltató használatával történő üzembe helyezéséhez hozzáadása
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
ms.date: 05/18/2018
ms.author: jeffgilb
ms.openlocfilehash: e08c0bfd3cbed64f5042e469801e20c913c2f70e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359424"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató az üzemeltetési kiszolgáló hozzáadása
A virtuális gépeken belül az SQL Server-példányok is használhatja a [Azure verem](azure-stack-poc.md), vagy kívül az Azure verem környezet, az erőforrás-szolgáltató példánya tud hozzá csatlakozni. Az általános követelmények a következők:

* A függő Entitás- és felhasználói feladatait által az SQL-példány dedikált való használatra. Bármely más fogyasztó, beleértve az App Service szolgáltatások által használt SQL-példány nem használható.
* Az SQL-erőforrás-szolgáltató VM nincs tartományhoz csatlakoztatva, és csak kapcsolódhatnak az SQL-hitelesítéssel.
* Konfigurálnia kell egy fiók megfelelő jogosultságokkal az erőforrás-szolgáltató általi használatra.
* Az erőforrás-szolgáltató és a felhasználók számára, például webalkalmazások, használ a felhasználói hálózati, így az SQL-példány a hálózati kapcsolatra szükség. Ez a követelmény általában azt jelenti, hogy az IP-Címek az SQL Server-példányok nyilvános hálózaton kell lennie.
* Az SQL Server-példányok és gazdagépeik felügyeleti rendszer Öntől; az erőforrás-szolgáltató nem javítási, biztonsági mentés végrehajtásához, hitelesítőadat-Elforgatás stb.
* SKU használható osztályokat hozhatnak létre különböző SQL funkcióit, például a teljesítmény érdekében mindig a stb.

SQL IaaS virtuális számítógépképet számos a piactér-kezelési funkción keresztül érhetők el. Győződjön meg arról, hogy mindig a legújabb verziójának letöltése a **SQL IaaS bővítmény** egy virtuális gép egy Piactéri elemet központi telepítése előtt. Az SQL-rendszerképek ugyanazok, mint az SQL virtuális gépen elérhető az Azure-ban. Ezeket a lemezképeket, az IaaS-bővítmény a létrehozott SQL virtuális gépen, és a megfelelő portál fejlesztései biztosítják az automatikus javítás és a biztonsági mentési funkciókat szolgáltatásokat.

Egyéb módon telepítéséhez SQL virtuális gépeken, beleértve a sablonokat a [Azure verem gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Egy több csomópontos Azure veremben telepített bármely üzemeltetési kiszolgáló felhasználói előfizetés kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. Azok a felhasználói portál vagy egy megfelelő bejelentkezési azonosító egy PowerShell-munkamenetet kell létrehozni. Minden üzemeltetési kiszolgáló terhelhető virtuális gép, és a megfelelő SQL-licenccel kell rendelkeznie. A szolgáltatás-rendszergazda _is_ lehet, hogy az előfizetés tulajdonosa.


### <a name="required-privileges"></a>Szükséges jogosultságok

Új rendszergazda felhasználó kisebb, mint a teljes körű rendszergazdai jogosultságokkal is létrehozhat. Engedélyezni kell a konkrét műveletek a következők:

- Adatbázis: Létrehozás, módosítás, amelyben a Containment (mindig a csak), dobja el, a biztonsági mentés
- Rendelkezésre állási csoporthoz: Alter, a csatlakoztatáshoz hozzáadása adatbázis
- Bejelentkezési identitás: Hozzon létre, válassza ki, Alter, dobja el, visszavonása
- SELECT műveletek: \[fő\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), a sys.databases, \[fő\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[fő\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Adja meg az SQL Server kiszolgálót futtató önálló csatlakozás kapacitás
Használhatja az önálló (nem-magas rendelkezésre ÁLLÁSÚ) bármely SQL Server 2014 vagy SQL Server 2016 kiadása SQL-kiszolgálók. Győződjön meg arról, hogy egy rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adatait.

Már kiépített kiszolgálót futtató önálló hozzáadásához kövesse az alábbi lépéseket:

1. Szolgáltatás-rendszergazdaként jelentkezzen be a verem Azure felügyeleti portálon

2. Kattintson a **Tallózás** &gt; **felügyeleti erőforrások** &gt; **üzemeltető kiszolgálók SQL**.

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  A **SQL üzemeltető kiszolgálók** panel, amelyen keresztül csatlakozhat az SQL Server erőforrás-szolgáltató, amely az erőforrás-szolgáltató háttér módon ellenőrizhető, hogy az SQL Server tényleges példányait.

  ![Üzemeltetési kiszolgáló](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Az űrlap kitöltése a kapcsolódási adatait. az SQL Server-példány.

  ![New Hosting Server](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Nem kötelezően megadhatja a példány nevét, és egy portszámot megadható, ha a példány nem hozzá van rendelve az alapértelmezett port a 1433-as értéket.

  > [!NOTE]
  > Mindaddig, amíg az SQL-példány elérhetők, a felhasználó és rendszergazda Azure Resource Manager, az erőforrás-szolgáltató ellenőrzése alatt lehet tenni. Az SQL-példány __kell__ lehet kizárólag a függő Entitás számára.

4. Kiszolgálók hozzáadása során, akkor hozzá kell rendelnie azokat egy új vagy meglévő SKU szolgáltatásajánlatok megkülönböztetéséhez. Például lehet egy SQL vállalati példányt biztosít:
  - adatbázis-kapacitás
  - az automatikus biztonsági mentés
  - az egyes részlegek nagy teljesítményű kiszolgálók
  - és így tovább.

  A termékváltozat tükröznie kell tulajdonságait, hogy a felhasználók megfelelően elhelyezheti az adatbázisokat. A termékváltozatban minden üzemeltetési kiszolgáló ugyanazokat a képességeket kell rendelkeznie.

> [!IMPORTANT]
> Nem támogatottak a különleges karaktereket, szóközöket és időszakok, beleértve a **termékcsalád** vagy **réteg** neve, amikor az SQL és a MySQL-szolgáltatók hoz létre a Termékváltozat.

Példa:

![Termékváltozatok](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Felhasználók nem hozható létre a Termékváltozat teljesen létrehozásáig.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>SQL Always On rendelkezésre állási csoportok használatával kapacitás
SQL Always On példányok konfigurálásához további lépéseket igényel, és legalább három virtuális gépek (vagy fizikai gépek) vonatkozik.

> [!NOTE]
> Az SQL-adapter RP _csak_ támogatja az SQL 2016 SP1 Enterprise vagy újabb példány a mindig bekapcsolva, mivel az új SQL-szolgáltatások például az automatikus összehangolása. A fenti gyakori jegyzékét követelmények:

Pontosabban engedélyeznie kell az [automatikus összehangolása](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) minden egyes rendelkezésre állási csoporton minden SQL Server-példány:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Másodlagos példányokra ezen az SQL-parancsok használatával:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

SQL Always On üzemeltetési kiszolgáló hozzáadásához kövesse az alábbi lépéseket:

1. Szolgáltatás-rendszergazdaként jelentkezzen be a verem Azure felügyeleti portálon

2. Kattintson a **Tallózás** &gt; **felügyeleti erőforrások** &gt; **üzemeltető kiszolgálók SQL** &gt; **+ Hozzáadás**.

    A **SQL üzemeltető kiszolgálók** panel, amelyen keresztül csatlakozhat az SQL Server erőforrás-szolgáltató, amely az erőforrás-szolgáltató háttér módon ellenőrizhető, hogy az SQL Server tényleges példányait.

3. Az űrlap kitöltése a kapcsolódási adatait. az SQL Server-példány, figyeljen arra, hogy a teljes Tartománynevét a címe mindig figyelő (választható portszám) használja. Adja meg a fiók rendszergazdai jogosultságokkal konfigurálta a fiók adatait.

4. SQL Always On rendelkezésre állási csoportnak példányok támogatásának engedélyezése jelölőnégyzet.

    ![Üzemeltetési kiszolgáló](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adja hozzá az SQL Always On példány egy másikra. 

> [!IMPORTANT]
> Önálló kiszolgálók nem keverhetők az Always On osztályt a azonos Termékváltozat. Kísérlet történt kombinálhatók típusok hozzáadása az első üzemeltetési kiszolgáló eredmények a hiba után.


## <a name="making-sql-databases-available-to-users"></a>SQL-adatbázisok elérhetővé tétele a felhasználók számára

Tervek és SQL-adatbázisok a felhasználók számára elérhetővé ajánlatok létrehozása. A Microsoft.SqlAdapter szolgáltatás hozzáadása a tervet, és adja hozzá vagy egy meglévő kvóta, vagy hozzon létre egy újat. Kvóta létrehozása, ha a kapacitás, a felhasználó adja meg.

![Tervek és adatbázisokat tartalmazza ajánlatok létrehozása](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)


## <a name="next-steps"></a>További lépések

[Adatbázisok hozzáadása](azure-stack-sql-resource-provider-databases.md)
