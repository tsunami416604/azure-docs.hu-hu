---
title: Az Azure SQL Data Warehouse Fivetran rövid |} A Microsoft Docs
description: Gyorsan használatba Fivetran és az Azure SQL Data warehouse-bA.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 50f5f813444ddf38d15863d028b1f61bb9b0d55c
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52580531"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Gyorsan használatba Fivetran és az SQL Data warehouse-bA

Ez a rövid útmutató ismerteti, hogyan állítható be új Fivetran felhasználó használata az Azure SQL Data Warehouse. A cikk feltételezi, hogy az SQL Data Warehouse egy meglévő példányát.

## <a name="set-up-a-connection"></a>Kapcsolat beállítása

1. Keresse meg a teljes kiszolgálónevet és az adatbázis neve, amellyel csatlakozhat az SQL Data warehouse-bA.
    
    Ha ez az információ megkereséséről segítségre van szüksége, tekintse meg [csatlakozhat az Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. A telepítővarázsló döntse el, az adatbázishoz csatlakozni, vagy közvetlenül az SSH-alagút használatával.

   Ha közvetlenül kapcsolódni az adatbázishoz, létre kell hoznia egy tűzfalszabályt, hogy engedélyezze a hozzáférést. Ez a módszer a legegyszerűbb és legbiztonságosabb módszer.

   Ha az SSH-alagút használatával csatlakoznak, Fivetran csatlakozik egy különálló kiszolgálón a hálózaton. A kiszolgáló biztosít egy SSH-alagutat az adatbázishoz. Ezt a módszert kell használnia, ha az adatbázis-nem érhető el alhálózat virtuális hálózaton.

3. Az IP-cím hozzáadása **52.0.2.4** a kiszolgálószintű tűzfal a bejövő kapcsolatok engedélyezése az SQL Data Warehouse-példányhoz Fivetran.

   További információkért lásd: [hozzon létre egy kiszolgálószintű tűzfalszabályt](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Felhasználói hitelesítő adatok beállítása

1. Csatlakozhat az Azure SQL Data Warehouse az SQL Server Management Studio vagy az eszköz, amely igény szerint. Jelentkezzen be egy kiszolgálói rendszergazda felhasználó. Ezután futtassa a következő SQL-parancsok hozzon létre egy felhasználót a Fivetran:
    - A master adatbázisban: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Az SQL Data Warehouse-adatbázis:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Adja meg a Fivetran felhasználónak az adattárház a következő engedélyekkel:

    ```
    GRANT CONTROL to fivetran;
    ```

    VEZÉRLŐI jogosultságot kell létrehoznia az adatbázishoz kötődő hitelesítő adatokat, amikor egy felhasználó fájlok betöltése az Azure Blob storage-ból a PolyBase használatával.

3. Adja hozzá a megfelelő erőforrásosztály Fivetran felhasználó. Erőforrásosztály használata az oszlopcentrikus index létrehozásához szükséges memória függ. Például integrációt a termékek, mint a Marketo és a Salesforce-hoz szükséges egy nagyobb erőforrásosztály nagyobb adatmennyiség és oszlopok nagy száma miatt a termékek használja. Egy nagyobb erőforrásosztály hozhat létre az oszlopcentrikus indexek több memóriát igényel.

    Azt javasoljuk, hogy a statikus erőforrásosztályok használata. Kezdhet az `staticrc20` erőforrásosztályhoz. A `staticrc20` erőforrásosztály 200 MB lefoglalja minden egyes felhasználó számára, függetlenül a teljesítményszintet használhatja. Ha a kezdeti erőforrás feladatvezérlőben oszloptár-indexelés nem sikerül, növelje erőforrásosztály.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    További információkért olvassa el [memória- és egyidejűségi korlátok](memory-and-concurrency-limits.md) és [erőforrásosztályok](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Jelentkezzen be a Fivetran

A Fivetran bejelentkezni, az SQL Data Warehouse eléréséhez használt hitelesítő adatok megadása: 

* Állomás (a kiszolgáló neve).
* Port.
* Adatbázis.
* Felhasználói (a felhasználónév legyen **fivetran @_kiszolgáló_neve_**  ahol *kiszolgáló_neve* része az Azure-beli gazdagéppel URI: ***server_name*.database.windows .net** ).
* A jelszó.
