---
title: "Kiszolgálók Azure veremben üzemeltető SQL |} Microsoft Docs"
description: "SQL-példány az SQL Adapter erőforrás-szolgáltató használatával történő üzembe helyezéséhez hozzáadása"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 0a29ef133a045b2828777050f2d7a204c0add4a8
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>Az SQL-adapter általi használatra üzemeltetési kiszolgáló hozzáadása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A virtuális gépeken belül az SQL Server-példányok is használhatja a [Azure verem](azure-stack-poc.md), vagy kívül az Azure verem környezet, az erőforrás-szolgáltató példánya tud hozzá csatlakozni. Az általános követelmények a következők:

* A függő Entitás- és felhasználói feladatait által az SQL-példány dedikált való használatra. Bármely más fogyasztó, beleértve az App Service szolgáltatások által használt SQL-példány nem használható.
* A függő Entitás adapter nincs tartományhoz csatlakoztatva, és csak kapcsolódhatnak az SQL-hitelesítéssel.
* A függő Entitás számára megfelelő jogosultságokkal rendelkező fiókkal kell konfigurálnia.
* A függő Entitás és a felhasználók például a Web Apps használ a felhasználói hálózati, így az SQL-példány a hálózati kapcsolatra szükség. Ez a követelmény általában azt jelenti, hogy az IP-Címek az SQL Server-példányok nyilvános hálózaton kell lennie.
* Az SQL Server-példányok és gazdagépeik felügyeleti rendszer Öntől; a függő Entitás nem javítási, biztonsági mentés végrehajtásához, hitelesítőadat-Elforgatás stb.
* SKU használható osztályokat hozhatnak létre különböző SQL funkcióit, például a teljesítmény érdekében mindig a stb.


SQL IaaS virtuális számítógépképet számos a piactér-kezelési funkción keresztül érhetők el. Ellenőrizze, hogy mindig a virtuális gép egy Piactéri elemet központi telepítése előtt töltse le az SQL IaaS bővítmény legújabb verziója. Az SQL-rendszerképek ugyanazok, mint az SQL virtuális gépen elérhető az Azure-ban. Ezeket a lemezképeket, az IaaS-bővítmény a létrehozott SQL virtuális gépen, és a megfelelő portál fejlesztései biztosítják az automatikus javítás és a biztonsági mentési funkciókat szolgáltatásokat.

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

  ![Hosting Servers](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

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

    Példa:

![Termékváltozatok](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Felhasználók nem hozható létre a Termékváltozat teljesen létrehozásáig.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>SQL Always On rendelkezésre állási csoportok használatával kapacitás
SQL Always On példányok konfigurálásához további lépéseket igényel, és legalább három virtuális gépek (vagy fizikai gépek) vonatkozik.

> [!NOTE]
> Az SQL-adapter RP _csak_ támogatja az SQL 2016 SP1 Enterprise vagy újabb példány a mindig bekapcsolva, mivel az új SQL-szolgáltatások például az automatikus összehangolása. A fenti gyakori jegyzékét követelmények:

* Meg kell adnia egy fájlkiszolgálón, az SQL mindig a számítógépek mellett. Van egy [Azure verem gyorsindítási sablonon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) meg ebben a környezetben, amely hozhat létre. Azt is működhetnek való összeállítása a saját példányát.

* Be kell állítania az SQL Server-kiszolgálók. Pontosabban engedélyeznie kell az [automatikus összehangolása](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) minden egyes példányaihoz tartozó SQL Server rendelkezésre állási csoporton.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

Másodlagos példányokra
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



SQL Always On üzemeltetési kiszolgáló hozzáadásához kövesse az alábbi lépéseket:

1. Szolgáltatás-rendszergazdaként jelentkezzen be a verem Azure felügyeleti portálon

2. Kattintson a **Tallózás** &gt; **felügyeleti erőforrások** &gt; **üzemeltető kiszolgálók SQL** &gt; **+ Hozzáadás**.

    A **SQL üzemeltető kiszolgálók** panel, amelyen keresztül csatlakozhat az SQL Server erőforrás-szolgáltató, amely az erőforrás-szolgáltató háttér módon ellenőrizhető, hogy az SQL Server tényleges példányait.


3. Az űrlap kitöltése a kapcsolódási adatait. az SQL Server-példány, figyeljen arra, hogy mindig figyelő (és nem kötelező portszám) teljes Tartománynevét vagy IPv4-cím használata. Adja meg a fiók rendszergazdai jogosultságokkal konfigurálta a fiók adatait.

4. SQL Always On rendelkezésre állási csoportnak példányok támogatásának engedélyezése jelölőnégyzet.

    ![Hosting Servers](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adja hozzá az SQL Always On példány egy másikra. Önálló kiszolgálók nem keverhetők az Always On osztályt a azonos Termékváltozat. Amely az első üzemeltető kiszolgálót hozzáadásakor határozza meg. Ezt követően kombinálhat típusokhoz próbál hibát eredményez.


## <a name="making-sql-databases-available-to-users"></a>SQL-adatbázisok elérhetővé tétele a felhasználók számára

Tervek és SQL-adatbázisok a felhasználók számára elérhetővé ajánlatok létrehozása. A Microsoft.SqlAdapter szolgáltatás hozzáadása a tervet, és adja hozzá vagy egy meglévő kvóta, vagy hozzon létre egy újat. Kvóta létrehozása, ha a kapacitás, a felhasználó adja meg.

![Tervek és adatbázisokat tartalmazza ajánlatok létrehozása](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>Az SQL-Adapter RP karbantartása

SQL-példánya karbantartási nem érvényes itt, elforgatás jelszóadatokat kivételével. Ön felelősséggel tartozik javítását és az SQL-adapterrel használt adatbázis-kiszolgálók biztonsági mentési vagy helyreállítási.

### <a name="patching-and-updating"></a>Javítás és frissítése
 Az SQL-Adapter nem kiszolgált Azure verem részeként, mert az bővítménye. Microsoft fogják biztosítani a frissítések az SQL-adapter szükség szerint. Az SQL-Adapter példányosítani egy _felhasználói_ virtuális gép az alapértelmezett szolgáltató előfizetésben. Ezért fontos adja meg a Windows javítások, vírusvédelmi aláírások, stb. A Windows frissítése a csomagok által biztosított javítási és frissítési ciklusában részét is lehet használni a frissítések alkalmazása a Windows virtuális gép. Egy frissített adapter megjelenésekor, a parancsfájl a frissítés alkalmazásához valósul meg. Ezt a parancsfájlt hoz létre egy új RP virtuális Gépet, és telepítse át a már állapotokat.

 ### <a name="backuprestoredisaster-recovery"></a>Backup/Restore/katasztrófa utáni helyreállítás
 Az SQL-Adapter nincs biztonsági másolata Azure verem BC-vész-Helyreállítási folyamat részeként, mert az bővítménye. Parancsfájlok megkönnyítésére biztosítja:
- A szükséges állapotadatokat (egy verem Azure storage-fiókban tárolt) biztonsági mentése
- A függő Entitás visszaállítása, abban az esetben, ha a teljes verem helyreállítás akkor van szükség.
Adatbázis-kiszolgálók helyre kell állítani az első (ha szükséges), a függő Entitás visszaállítására.

### <a name="updating-sql-credentials"></a>SQL-hitelesítő adatainak frissítése

Ön felelősséggel tartozik létrehozására és karbantartására rendszer rendszergazdai fiókokhoz az SQL-kiszolgálón. A függő Entitás ezek a felhasználók nevében adatbázisok kezelése engedéllyel rendelkező fiók szükséges, mert nem kell ezeket az adatbázisokat az adatokhoz való hozzáférés. Ha az SQL-kiszolgálón a rendszergazdai (SA) jelszó frissíteni kell, a frissítési funkció, a függő Entitás rendszergazdai felület segítségével a függő Entitás által használt tárolt jelszó megváltoztatásához. Ezek a jelszavak a kulcstároló, az Azure-verem példányon tárolják.

A beállítások módosításához kattintson **Tallózás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltető kiszolgálók** &gt; **SQL-Bejelentkezésekben** válassza ki a bejelentkezési nevet. A módosítani kell az SQL-példányon először (és bármilyen replikákat, ha szükséges). Az a **beállítások** panelen, kattintson a **jelszó**.

![Frissítés a rendszergazdai jelszó](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>További lépések

[Adatbázisok hozzáadása](azure-stack-sql-resource-provider-databases.md)
