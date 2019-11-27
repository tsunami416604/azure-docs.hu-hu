---
title: Fivetran gyors útmutató
description: Gyorsan elsajátíthatja a Fivetran és a Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 715f891484458f3bf3febc6807c3490b88062d50
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229092"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>A Fivetran és a SQL Data Warehouse gyors kezdése

Ez a rövid útmutató azt ismerteti, hogyan állíthat be új Fivetran-felhasználót a Azure SQL Data Warehouse való együttműködéshez. A cikk feltételezi, hogy rendelkezik a SQL Data Warehouse meglévő példányával.

## <a name="set-up-a-connection"></a>Kapcsolatok beállítása

1. Keresse meg a SQL Data Warehousehoz való kapcsolódáshoz használt teljes kiszolgálónév és adatbázisnév nevét.
    
    Ha segítségre van szüksége az információk megkereséséhez, tekintse [meg a kapcsolódás Azure SQL Data Warehousehoz](sql-data-warehouse-connect-overview.md)című témakört.

2. A telepítővarázsló segítségével válassza ki, hogy közvetlenül vagy SSH-alagúton keresztül kívánja-e csatlakozni az adatbázist.

   Ha úgy dönt, hogy közvetlenül csatlakozik az adatbázishoz, létre kell hoznia egy tűzfalszabály használatát a hozzáférés engedélyezéséhez. Ez a legegyszerűbb és legbiztonságosabb módszer.

   Ha úgy dönt, hogy SSH-alagút használatával csatlakozik, a Fivetran egy különálló kiszolgálóhoz csatlakozik a hálózaton. A kiszolgáló egy SSH-alagutat biztosít az adatbázishoz. Ezt a módszert kell használnia, ha az adatbázis nem elérhető alhálózatban van egy virtuális hálózaton.

3. Adja hozzá az IP- **52.0.2.4** a kiszolgálói szintű tűzfalhoz, hogy engedélyezze a bejövő kapcsolatokat a SQL Data Warehouse-példányhoz a Fivetran.

   További információ: [kiszolgálói szintű tűzfalszabály létrehozása](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Felhasználói hitelesítő adatok beállítása

1. Kapcsolódjon a Azure SQL Data Warehousehoz SQL Server Management Studio vagy az Ön által előnyben részesített eszköz használatával. Jelentkezzen be kiszolgálói rendszergazda felhasználóként. Ezután futtassa a következő SQL-parancsokat a Fivetran felhasználó létrehozásához:
    - A Master adatbázisban: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - SQL Data Warehouse adatbázisban:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Adja meg a Fivetran felhasználónak a következő engedélyeket a tárháznak:

    ```
    GRANT CONTROL to fivetran;
    ```

    A VEZÉRLÉSi engedély szükséges ahhoz, hogy adatbázis-hatókörű hitelesítő adatokat hozzon létre, amelyeket akkor használ a rendszer, ha a felhasználó a fájlok Azure Blob Storage-ból való betöltését végzi.

3. Adjon hozzá egy megfelelő erőforrás-osztályt a Fivetran-felhasználóhoz. A használt erőforrás-osztály a oszlopcentrikus index létrehozásához szükséges memóriától függ. Például a Marketo és a Salesforce termékekkel való integrációhoz nagyobb erőforrás-osztályra van szükség, mert a nagy számú oszlop és a termékek által használt nagyobb mennyiségű érték van. A magasabb szintű erőforrásokhoz több memória szükséges a oszlopcentrikus indexek létrehozásához.

    Javasoljuk, hogy használjon statikus erőforrás-osztályokat. Először a `staticrc20` Resource osztályt használhatja. A `staticrc20` Resource osztály 200 MB-ot foglal le minden felhasználó számára, a használt teljesítményszinttól függetlenül. Ha a oszlopcentrikus indexelése sikertelen a kezdeti erőforrás-osztály szintjén, növelje az erőforrás osztályt.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    További információért olvassa el a [memória-és egyidejűségi korlátokkal](memory-concurrency-limits.md) és az [erőforrás-osztályokkal](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)kapcsolatos tudnivalókat.


## <a name="sign-in-to-fivetran"></a>Bejelentkezés a Fivetran

A Fivetran való bejelentkezéshez adja meg a SQL Data Warehouse eléréséhez használt hitelesítő adatokat: 

* Gazdagép (a kiszolgáló neve).
* Port.
* Adatbázis.
* Felhasználó (a felhasználónévnek fivetran kell lennie **\@_server_name_**  , ahol a *server_name* az Azure gazdagép URI-ja: ***server_name *. database. Windows. net**).
* Jelszó.
