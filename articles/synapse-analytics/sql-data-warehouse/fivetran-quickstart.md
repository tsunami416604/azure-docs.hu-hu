---
title: 'Rövid útmutató: Fivetran és adattárház'
description: Ismerkedés a Fivetran és az Azure Synapse Analytics adattárházával.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8f164232a3b1782511758f93a9e9b8d17d3714d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414271"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Rövid útmutató: Fivetran adattárház 

Ez a rövid útmutató ismerteti, hogyan állíthat be egy új Fivetran-felhasználót egy SQL Poolnal kiosztott Azure Synapse Analytics adattárral való együttműködésre. A cikk feltételezi, hogy van egy meglévő adattárház.

## <a name="set-up-a-connection"></a>Kapcsolat beállítása

1. Keresse meg az adattárházhoz való csatlakozáshoz használt teljesen minősített kiszolgáló- és adatbázisnevet.
    
    Ha segítségre van szüksége ezen információk megkereséséhez, olvassa el [a Csatlakozás az adattárházhoz című témakört.](../sql/connect-overview.md)

2. A beállítási varázslóban válassza ki, hogy közvetlenül vagy SSH-alagúthasználatával szeretné-e csatlakoztatni az adatbázist.

   Ha úgy dönt, hogy közvetlenül csatlakozik az adatbázishoz, létre kell hoznia egy tűzfalszabályt a hozzáférés engedélyezéséhez. Ez a módszer a legegyszerűbb és legbiztonságosabb módszer.

   Ha úgy dönt, hogy SSH-alagúton keresztül csatlakozik, a Fivetran egy külön kiszolgálóhoz csatlakozik a hálózaton. A kiszolgáló SSH-alagutat biztosít az adatbázishoz. Ezt a módszert kell használnia, ha az adatbázis egy virtuális hálózaton nem elérhető alhálózatban van.

3. Adja hozzá az **52.0.2.4** IP-címet a kiszolgálószintű tűzfalhoz, hogy a Fivetran bejövő kapcsolatot létesítsen az adattárház-példánysal.

   További információt a [Kiszolgálószintű tűzfalszabály létrehozása](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)című témakörben talál.

## <a name="set-up-user-credentials"></a>Felhasználói hitelesítő adatok beállítása

1. Csatlakozzon az adattárházhoz az SQL Server Management Studio (SSMS) vagy a kívánt eszköz használatával. Jelentkezzen be kiszolgálófelügyeleti felhasználóként. Ezután futtassa a következő SQL-parancsokat a Fivetran felhasználójának létrehozásához:

    - A fő adatbázisban: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Az adattárház-adatbázisban:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Adja meg a Fivetran felhasználónak a következő engedélyeket az adattárházhoz:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    CONTROL engedély szükséges adatbázis-hatókörrel kapcsolatos hitelesítő adatok létrehozásához, amelyek akkor használatosak, amikor egy felhasználó fájlokat tölt be az Azure Blob storage-ból a PolyBase használatával.

3. Adjon hozzá egy megfelelő erőforrásosztályt a Fivetran felhasználóhoz. A használt erőforrásosztály az oszlopcentrikus index létrehozásához szükséges memóriától függ. Például a Marketo és a Salesforce termékekkel való integráció magasabb erőforrásosztályt igényel, mivel a termékek nagy számú oszlopot és nagyobb mennyiségű adatot használnak. Egy magasabb erőforrásosztály több memóriát igényel az oszlopcentrikus indexek létrehozásához.

    Azt javasoljuk, hogy statikus erőforrásosztályokat használjon. Kezdheti az `staticrc20` erőforrásosztállyal. Az `staticrc20` erőforrásosztály 200 MB-ot foglal le minden felhasználó számára, függetlenül a használt teljesítményszinttől. Ha az oszlopcentrikus indexelés sikertelen a kezdeti erőforrásosztály szintjén, növelje az erőforrásosztályt.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    További információt a [memória- és egyidejűségi korlátokról](memory-concurrency-limits.md) és [az erőforrásosztályokról](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)talál.


## <a name="connect-from-fivetran"></a>Csatlakozás a Fivetran-ról

Ha fivetran-fiókjából szeretne csatlakozni az adattárházhoz, adja meg az adattárház eléréséhez használt hitelesítő adatokat: 

* Állomás (a kiszolgáló neve).
* Port.
* Adatbázis.
* Felhasználó (a felhasználónév nek **\@öttran** server_name kell *lennie,* ahol server_name az Azure-állomás URI-jának része: ** _kiszolgálónév\__.database.windows.net).**
* Jelszó.
