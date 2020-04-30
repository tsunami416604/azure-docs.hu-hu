---
title: Munkaterületekhez, adatbázisokhoz és folyamatokhoz való hozzáférés kezelése
description: Megtudhatja, hogyan kezelheti az Azure szinapszis Analytics-munkaterületen (előzetes verzió) lévő munkaterületekhez, adatszolgáltatásokhoz és folyamatokhoz való hozzáférés-vezérlést.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424768"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Munkaterületekhez, adatbázisokhoz és folyamatokhoz való hozzáférés kezelése

Megtudhatja, hogyan kezelheti az Azure szinapszis Analytics-munkaterületen (előzetes verzió) lévő munkaterületekhez, adatszolgáltatásokhoz és folyamatokhoz való hozzáférés-vezérlést.

> [!NOTE]
> A GA esetében a RBAC-t a szinapszis-specifikus Azure RBAC-szerepkörök bevezetésével fejlesztjük

## <a name="access-control-for-workspace"></a>Munkaterület Access Control

Az éles környezetben történő üzembe helyezéshez az Azure szinapszis munkaterületen javasoljuk, hogy szervezze a környezetét, hogy megkönnyítse a felhasználók és a rendszergazdák kiépítését.

> [!NOTE]
> Az itt ismertetett megközelítés több biztonsági csoport létrehozása, majd a munkaterület konzisztens használatára való konfigurálása. A csoportok beállítása után a rendszergazdának csak a biztonsági csoportokon belüli tagságot kell kezelnie.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>1. lépés: biztonsági csoportok beállítása az ezt a mintát követő névvel

1. Nevű biztonsági csoport létrehozása`Synapse_WORKSPACENAME_Users`
2. Nevű biztonsági csoport létrehozása`Synapse_WORKSPACENAME_Admins`
3. A `Synapse_WORKSPACENAME_Admins` hozzáadása ehhez: `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>2. lépés: az alapértelmezett ADLS Gen2 fiók előkészítése

Ha kiépíti a munkaterületet, ki kellett választania egy ADLSGEN2-fiókot és egy tárolót a munkaterülethez tartozó fájlrendszerhez.

1. A [Azure Portal](https://portal.azure.com) megnyitása
2. Navigáljon a ADLSGEN2-fiókhoz
3. Navigáljon az Azure szinapszis munkaterülethez kiválasztott tárolóhoz (fájlrendszerhez)
4. Kattintson **Access Control (iam)**
5. Rendelje hozzá a következő szerepköröket:
   1. **Olvasói** szerepkör:`Synapse_WORKSPACENAME_Users`
   2. **Storage blob-adat tulajdonosi** szerepköre:`Synapse_WORKSPACENAME_Admins`
   3. **Storage-blob adatközreműködői** szerepköre:`Synapse_WORKSPACENAME_Users`
   4. **Storage blob-adat tulajdonosi** szerepköre:`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>3. lépés: a munkaterület-felügyeleti lista konfigurálása

1. Ugrás az [ **Azure szinapszis webes felhasználói felületére**](https://web.azuresynapse.net)
2. Ugrás a **Manage**  > **biztonsági** > **hozzáférés-vezérlés** kezelése
3. Kattintson a **rendszergazda hozzáadása**lehetőségre, majd válassza a`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>4. lépés: az SQL-rendszergazdai hozzáférés konfigurálása a munkaterülethez

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
2. Navigáljon a munkaterületre
3. Lépjen a **Beállítások** > **Active Directory adminisztrátor**
4. Kattintson a **rendszergazda beállítása** elemre.
5. A következők szerint válasszon: `Synapse_WORKSPACENAME_Admins`
6. kattintson a **kiválasztás** elemre.
7. kattintson a **Mentés** gombra

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>5. lépés: felhasználók és rendszergazdák hozzáadása és eltávolítása biztonsági csoportokhoz

1. Rendszergazdai hozzáférésre szoruló felhasználók hozzáadása`Synapse_WORKSPACENAME_Admins`
2. Az összes többi felhasználó hozzáadása`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>AdatAccess Control

Az alapul szolgáló adat hozzáférés-vezérlése három részre oszlik:

- Adatsík-hozzáférés a Storage-fiókhoz (a 2. lépésben már fentebb van konfigurálva)
- Adatsíkokhoz való hozzáférés az SQL-adatbázisokhoz (SQL-készletek és SQL-igény esetén egyaránt)
- Hitelesítő adatok létrehozása az igény szerinti SQL-adatbázisokhoz a Storage-fiókon keresztül

## <a name="access-control-to-sql-databases"></a>Hozzáférés-vezérlés SQL-adatbázisokhoz

> [!TIP]
> Az alábbi lépéseket **minden** SQL-adatbázishoz le kell futtatni, hogy minden SQL-adatbázishoz felhasználói hozzáférést biztosítson.

### <a name="sql-on-demand"></a>Igény szerinti SQL

Ha hozzáférést szeretne adni egy felhasználónak **egyetlen** SQL igény szerinti adatbázishoz, kövesse az alábbi példában szereplő lépéseket:

1. Bejelentkezés létrehozása

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. FELHASZNÁLÓ létrehozása

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. FELHASZNÁLÓ hozzáadása a megadott szerepkör tagjaihoz

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
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

A felhasználók létrehozása után ellenőrizze, hogy az SQL on-demand képes-e lekérdezni a Storage-fiókot:

- Futtassa az alábbi parancsot az SQL igény szerinti **Master** adatbázisához:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Hozzáférés-vezérlés a munkaterület-folyamat futtatásához

### <a name="workspace-managed-identity"></a>Munkaterület által felügyelt identitás

> [!IMPORTANT]
> Az SQL-készletre hivatkozó adatkészleteket vagy tevékenységeket tartalmazó folyamatok sikeres futtatásához a munkaterület identitásának közvetlenül az SQL-készlethez kell hozzáférést biztosítania.

Futtassa az alábbi parancsokat az egyes SQL-készleteken, hogy a munkaterület felügyelt identitása folyamatokat futtasson az SQL-készlet adatbázisában:

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

A szinapszis SQL hozzáférésének és vezérlésének áttekintését lásd: [SZINAPSZIS SQL-hozzáférés-vezérlés](../sql/access-control.md). További információ az adatbázis-rendszerbiztonsági tagekről: [rendszerbiztonsági tag](https://msdn.microsoft.com/library/ms181127.aspx). Az adatbázis-szerepkörökkel kapcsolatos további információkért tekintse meg az [adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx) című cikket.
