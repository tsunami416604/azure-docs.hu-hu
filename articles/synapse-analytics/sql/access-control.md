---
title: Munkaterületekhez, adatbázisokhoz és folyamatokhoz való hozzáférés kezelése
description: Megtudhatja, hogyan kezelheti az Azure szinapszis Analytics-munkaterületen (előzetes verzió) lévő munkaterületekhez, adatszolgáltatásokhoz és folyamatokhoz való hozzáférés-vezérlést.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: a2e6111f2df5a8d7334a85ec5b6a9e514368ad6c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289480"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Munkaterületekhez, adatbázisokhoz és folyamatokhoz való hozzáférés kezelése

Megtudhatja, hogyan kezelheti az Azure szinapszis Analytics-munkaterületen (előzetes verzió) lévő munkaterületekhez, adatszolgáltatásokhoz és folyamatokhoz való hozzáférés-vezérlést.

> [!NOTE]
> A GA esetében a RBAC a szinapszis-specifikus Azure-szerepkörök bevezetésével fejleszthető

## <a name="access-control-for-workspace"></a>Munkaterület Access Control

Az éles környezetben történő üzembe helyezéshez az Azure szinapszis munkaterületen javasoljuk, hogy szervezze a környezetét, hogy megkönnyítse a felhasználók és a rendszergazdák kiépítését.

> [!NOTE]
> Az itt ismertetett megközelítés több biztonsági csoport létrehozása, majd a munkaterület konzisztens használatára való konfigurálása. A csoportok beállítása után a rendszergazdának csak a biztonsági csoportokon belüli tagságot kell kezelnie.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>1. lépés: biztonsági csoportok beállítása az ezt a mintát követő névvel

1. Nevű biztonsági csoport létrehozása `Synapse_WORKSPACENAME_Users`
2. Nevű biztonsági csoport létrehozása `Synapse_WORKSPACENAME_Admins`
3. A `Synapse_WORKSPACENAME_Admins` hozzáadása ehhez: `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> [Ebből a cikkből](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)megtudhatja, hogyan hozhat létre biztonsági csoportot ebben a cikkben.
>
> [Ebből a cikkből](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal)megtudhatja, hogyan adhat hozzá biztonsági csoportot egy másik biztonsági csoportból ebben a cikkben.
>
> WORKSPACENAME – ezt a részt a munkaterület tényleges nevével kell helyettesíteni.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>2. lépés: az alapértelmezett ADLS Gen2 fiók előkészítése

A munkaterület kiépítés után ki kell választania egy [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) fiókot és egy tárolót a munkaterülethez tartozó fájlrendszerhez.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
2. Navigáljon a Azure Data Lake Storage Gen2 fiókhoz
3. Navigáljon az Azure szinapszis munkaterülethez kiválasztott tárolóhoz (fájlrendszerhez)
4. **Access Control kiválasztása (iam)**
5. Rendelje hozzá a következő szerepköröket:
   1. **Olvasói** szerepkör:  `Synapse_WORKSPACENAME_Users`
   2. **Storage blob-adattulajdonosi** szerepkör:  `Synapse_WORKSPACENAME_Admins`
   3. **Storage blob-adatközreműködői** szerepkör: `Synapse_WORKSPACENAME_Users`
   4. **Storage blob-adattulajdonosi** szerepkör:  `WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME – ezt a részt a munkaterület tényleges nevével kell helyettesíteni.

### <a name="step-3-configure-the-workspace-admin-list"></a>3. lépés: a munkaterület-felügyeleti lista konfigurálása

1. Ugrás az [ **Azure szinapszis webes felhasználói felületére**](https://web.azuresynapse.net)
2. Ugrás a **Manage**   >  **biztonsági**  >  **hozzáférés-vezérlés** kezelése
3. Válassza a **rendszergazda hozzáadása**lehetőséget, majd válassza a `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>4. lépés: az SQL-rendszergazdai hozzáférés konfigurálása a munkaterülethez

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
2. Navigáljon a munkaterületre
3. Lépjen a **Beállítások**  >  **Active Directory adminisztrátor**
4. Válassza a **rendszergazda beállítása** lehetőséget.
5. A következők szerint válasszon: `Synapse_WORKSPACENAME_Admins`
6. Válassza a **kiválasztás** lehetőséget.
7. Válassza a **Mentés** lehetőséget

> [!NOTE]
> WORKSPACENAME – ezt a részt a munkaterület tényleges nevével kell helyettesíteni.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>5. lépés: felhasználók és rendszergazdák hozzáadása és eltávolítása biztonsági csoportokhoz

1. Rendszergazdai hozzáférésre szoruló felhasználók hozzáadása `Synapse_WORKSPACENAME_Admins`
2. Az összes többi felhasználó hozzáadása `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> [Ebből a cikkből](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal) megtudhatja, hogyan vehet fel felhasználót tagként egy biztonsági csoportba ebben a cikkben
> 
> WORKSPACENAME – ezt a részt a munkaterület tényleges nevével kell helyettesíteni.

## <a name="access-control-to-data"></a>AdatAccess Control

Az alapul szolgáló adat hozzáférés-vezérlése három részre oszlik:

- Adatsík-hozzáférés a Storage-fiókhoz (a 2. lépésben már fentebb van konfigurálva)
- Adatsíkokhoz való hozzáférés az SQL-adatbázisokhoz (SQL-készletek és SQL-igény esetén egyaránt)
- Hitelesítő adatok létrehozása az igény szerinti SQL-adatbázisokhoz a Storage-fiókon keresztül

## <a name="access-control-to-sql-databases"></a>Hozzáférés-vezérlés SQL-adatbázisokhoz

> [!TIP]
> Az alábbi lépéseket **minden** SQL-adatbázishoz le kell futtatni ahhoz, hogy felhasználói hozzáférést biztosítson az összes SQL-adatbázishoz, kivéve a szakasz [kiszolgálói szintű engedélyét](#server-level-permission) , ahol rendszergazdai szerepkört rendelhet a felhasználóhoz.

### <a name="sql-on-demand"></a>Igény szerinti SQL

Ebben a szakaszban példákat talál arra, hogy hogyan adhat engedélyt a felhasználónak egy adott adatbázisra vagy teljes kiszolgálói engedélyekre.

#### <a name="database-level-permission"></a>Adatbázis szintű engedély

Ha hozzáférést szeretne adni egy felhasználónak **egyetlen** SQL igény szerinti adatbázishoz, kövesse az alábbi példában szereplő lépéseket:

1. Bejelentkezés létrehozása

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. FELHASZNÁLÓ létrehozása

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. FELHASZNÁLÓ hozzáadása a megadott szerepkör tagjaihoz

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Cserélje le az aliast arra a felhasználóra, aki az Ön által használt vállalati tartománnyal hozzáférést és tartományt szeretne megadni.

#### <a name="server-level-permission"></a>Kiszolgálói szintű engedély

Ha teljes hozzáférést szeretne biztosítani egy felhasználónak az **összes** SQL-igény szerinti adatbázishoz, kövesse a jelen példában szereplő lépést:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="sql-pools"></a>SQL-készletek

Ha hozzáférést szeretne adni egy felhasználónak **egyetlen** SQL Databasehoz, kövesse az alábbi lépéseket:

1. Hozza létre a felhasználót az adatbázisban a következő parancs futtatásával, amely a helyi választóban célozza meg a kívánt adatbázist (a legördülő listából válassza az adatbázisok lehetőséget):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Adja meg a felhasználónak az adatbázis eléréséhez szükséges szerepkört:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> a *db_datareader* és a *db_datawriter* írási/olvasási engedélyekkel is működhet, ha a *db_owner* engedély megadása nem kívánatos.
> Ahhoz, hogy egy Spark-felhasználó közvetlenül a Sparkból vagy egy SQL-készletből olvassa el és írja az írást, *db_owner* engedélyre van szükség.

A felhasználók létrehozása után ellenőrizze, hogy az SQL on-demand képes-e lekérdezni a Storage-fiókot.

## <a name="access-control-to-workspace-pipeline-runs"></a>Hozzáférés-vezérlés a munkaterület-folyamat futtatásához

### <a name="workspace-managed-identity"></a>Munkaterület által felügyelt identitás

> [!IMPORTANT]
> Az SQL-készletre hivatkozó adatkészleteket vagy tevékenységeket tartalmazó folyamatok sikeres futtatásához a munkaterület identitásának közvetlenül az SQL-készlethez kell hozzáférést biztosítania.

Futtassa az alábbi parancsokat az egyes SQL-készleteken, hogy a munkaterület által felügyelt identitás az SQL-készlet adatbázisában futtasson folyamatokat:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Ez az engedély a következő parancsfájl ugyanazon SQL-készleten való futtatásával távolítható el:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>További lépések

A szinapszis munkaterület által felügyelt identitás áttekintését lásd: az [Azure szinapszis munkaterület felügyelt identitása](../security/synapse-workspace-managed-identity.md). További információ az adatbázis-rendszerbiztonsági tagekről: [rendszerbiztonsági tag](https://msdn.microsoft.com/library/ms181127.aspx). Az adatbázis-szerepkörökkel kapcsolatos további információkért tekintse meg az [adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx) című cikket.
