---
title: 'Gyors útmutató: Fivetran és adattárház'
description: Ismerkedés a Fivetran és az Azure szinapszis Analytics adattárházával.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e679c0b284cc649dbde3fba1b640f4e09df05e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201650"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Gyors útmutató: Fivetran az adattárházban 

Ez a rövid útmutató azt ismerteti, hogyan állíthat be új Fivetran-felhasználót egy SQL-készlettel kiépített Azure szinapszis Analytics-adattárházhoz való együttműködéshez. A cikk feltételezi, hogy van egy meglévő adattárháza.

## <a name="set-up-a-connection"></a>Kapcsolatok beállítása

1. Keresse meg az adattárházhoz való kapcsolódáshoz használt teljes kiszolgálónév és adatbázisnév nevét.
    
    Ha segítségre van szüksége az információk megtalálásához, tekintse meg a [Kapcsolódás az adattárházhoz](../sql/connect-overview.md)című témakört.

2. A telepítővarázsló segítségével válassza ki, hogy közvetlenül vagy SSH-alagúton keresztül kívánja-e csatlakozni az adatbázist.

   Ha úgy dönt, hogy közvetlenül csatlakozik az adatbázishoz, létre kell hoznia egy tűzfalszabály használatát a hozzáférés engedélyezéséhez. Ez a legegyszerűbb és legbiztonságosabb módszer.

   Ha úgy dönt, hogy SSH-alagúton keresztül csatlakozik, a Fivetran egy külön kiszolgálóhoz csatlakozik a hálózaton. A kiszolgáló egy SSH-alagutat biztosít az adatbázishoz. Ezt a módszert kell használnia, ha az adatbázis nem elérhető alhálózatban van egy virtuális hálózaton.

3. Adja hozzá az IP- **52.0.2.4** a kiszolgálói szintű tűzfallal, hogy engedélyezze a bejövő kapcsolatokat az adattárház-példányhoz a Fivetran.

   További információ: [kiszolgálói szintű tűzfalszabály létrehozása](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Felhasználói hitelesítő adatok beállítása

1. Kapcsolódjon az adattárházhoz SQL Server Management Studio (SSMS) vagy az Ön által előnyben részesített eszköz használatával. Jelentkezzen be kiszolgálói rendszergazda felhasználóként. Ezután futtassa a következő SQL-parancsokat a Fivetran felhasználó létrehozásához:

    - A Master adatbázisban: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Az adatraktár-adatbázisban:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Adja meg a Fivetran felhasználónak a következő engedélyeket az adattárházhoz:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    A VEZÉRLÉSi engedély szükséges ahhoz, hogy adatbázis-hatókörű hitelesítő adatokat hozzon létre, amelyeket akkor használ a rendszer, ha a felhasználó a fájlok Azure Blob Storage-ból való betöltését végzi.

3. Adjon hozzá egy megfelelő erőforrás-osztályt a Fivetran-felhasználóhoz. A használt erőforrás-osztály a oszlopcentrikus index létrehozásához szükséges memóriától függ. Például a Marketo és a Salesforce termékekkel való integrációhoz nagyobb erőforrás-osztályra van szükség, mert a nagy számú oszlop és a termékek által használt nagyobb mennyiségű érték van. A magasabb szintű erőforrásokhoz több memória szükséges a oszlopcentrikus indexek létrehozásához.

    Javasoljuk, hogy használjon statikus erőforrás-osztályokat. A kezdéshez használhatja az `staticrc20` erőforrás osztályt. Az `staticrc20` erőforrás osztály 200 MB-ot foglal le minden felhasználó számára, a használt teljesítményszinttól függetlenül. Ha a oszlopcentrikus indexelése sikertelen a kezdeti erőforrás-osztály szintjén, növelje az erőforrás osztályt.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    További információért olvassa el a [memória-és egyidejűségi korlátokkal](memory-concurrency-limits.md) és az [erőforrás-osztályokkal](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)kapcsolatos tudnivalókat.


## <a name="connect-from-fivetran"></a>Kapcsolat a Fivetran

Ha a Fivetran-fiókból szeretne csatlakozni az adattárházhoz, adja meg az adatraktár eléréséhez használt hitelesítő adatokat: 

* Gazdagép (a kiszolgáló neve).
* Port.
* Adatbázis.
* Felhasználó (a felhasználónévnek ** \@ _server_name_ fivetran** kell lennie, ahol a *server_name* az Azure gazdagép URI-ja: ** _kiszolgálónév. \_ _Database.Windows.net**).
* Jelszó.
