---
title: Gyors üzembe helyezési Fivetran az Azure SQL Data Warehouse |} A Microsoft Docs
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
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355476"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Gyorsan használatba Fivetran és az SQL Data warehouse-bA

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy meglévő példányát az SQL Data warehouse-bA.

## <a name="setup-connection"></a>Kapcsolat beállítása

1. Keresse meg a teljes kiszolgálónevet és csatlakozás az Azure SQL Data Warehouse-adatbázis neve.

   [Hogyan találhatja meg a kiszolgáló nevét és az adatbázis nevét a portál?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. A telepítővarázsló döntse el, ha szeretné-e csatlakozni az adatbázis közvetlenül vagy az SSH-alagút használatával.

   Ha úgy dönt, hogy közvetlenül kapcsolódni az adatbázishoz, szüksége lesz a hozzon létre egy tűzfalszabályt, hogy engedélyezze a hozzáférést. Ez a módszer a legegyszerűbb és legbiztonságosabb módszer.

   Ha úgy dönt, hogy az SSH-alagút-n keresztül csatlakozik, Fivetran kapcsolódik egy másik kiszolgálóra, amely az adatbázis egy SSH-alagutat biztosít a hálózaton. Ez a módszer szükség, ha az adatbázis egy virtuális hálózaton nem érhető el alhálózat szerepel.

3. Adja hozzá a "52.0.2.4" a kiszolgáló szintű tűzfalat, hogy a bejövő kapcsolatok engedélyezése, hogy az Azure SQL Data Warehouse Fivetran az IP-címét.

   [Hogyan kiszolgáló adatbázisszintű tűzfalszabály hozzáadása?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>A telepítő felhasználó hitelesítő adatait

Az Azure SQL Data Warehouse az SQL Server Management Studio vagy a választott eszköz használatával kiszolgálói rendszergazda felhasználóként csatlakozik, és a következő SQL parancsok futtatásával hozzon létre egy felhasználót a Fivetran:

A master adatbázisban: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

Az SQL Data Warehouse-adatbázis:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

Felhasználói fivetran létrehozása után adja meg, az adattárház a következő engedélyekkel:

```
GRANT CONTROL to fivetran;
```

Adja hozzá a megfelelő erőforrásosztály a létrehozott felhasználóval, attól függően, hogy az oszlopcentrikus index létrehozását a memóriakövetelményét. Például, például a Salesforce és a Marketo Integrációk kell-e nagyobb erőforrásosztály miatt a nagy oszlopok száma / nagyobb mennyiségű adat az oszlopcentrikus indexek létrehozása több memóriát igényel.

Statikus erőforrásosztályok használata ajánlott. Erőforrásosztály kezdhet `staticrc20`, amely 200 MB-ot foglal le attól függetlenül, a teljesítményszint, használja a felhasználó számára. Az aktuális erőforrásosztály oszloptár-indexelés nem sikerül, ha van erőforrásosztály növelése érdekében.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

További információkért tekintse meg a dokumentumokat a [memória- és egyidejűségi korlátok](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) és [erőforrásosztályok](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)

Fájlok betöltése a PolyBase Blob Storage-ból során használandó adatbázishoz kötődő hitelesítő adatok létrehozása vezérlő engedélyre van szükség.

Adja meg az Azure SQL Data Warehouse eléréséhez szükséges hitelesítő adatok

1. Állomás (a kiszolgáló neve)
2. Port
3. Adatbázis
4. Felhasználó (felhasználói név legyen `fivetran@<server_name>` ahol `<server_name>` része az Azure-beli gazdagéppel uri: `<server_name>.database.windows.net`)
5. Jelszó