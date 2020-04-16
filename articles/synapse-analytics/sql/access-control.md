---
title: A munkaterületekhez, adatokhoz és folyamatokhoz való hozzáférés kezelése
description: Megtudhatja, hogyan kezelheti a munkaterületek, az adatok és a folyamatok hozzáférés-vezérlését az Azure Synapse Analytics-munkaterületen (előzetes verzió).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424768"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>A munkaterületekhez, adatokhoz és folyamatokhoz való hozzáférés kezelése

Megtudhatja, hogyan kezelheti a munkaterületek, az adatok és a folyamatok hozzáférés-vezérlését az Azure Synapse Analytics-munkaterületen (előzetes verzió).

> [!NOTE]
> GA esetén az RBAC a Szinapsze-specifikus Azure RBAC-szerepkörök bevezetésével lesz fejleszthetőbb

## <a name="access-control-for-workspace"></a>Hozzáférés-vezérlés munkaterülethez

Az Azure Synapse-munkaterület éles környezetben történő üzembe helyezés esetén javasoljuk, hogy rendszerezze a környezetet, hogy megkönnyítse a felhasználók és a rendszergazdák kiépítését.

> [!NOTE]
> Az itt alkalmazott módszer több biztonsági csoport létrehozása, majd a munkaterület konfigurálása azok következetes használatára. A csoportok beállítása után a rendszergazdának csak a biztonsági csoportokon belüli tagságot kell kezelnie.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>1. lépés: Biztonsági csoportok beállítása nevekkel az alábbi minta szerint

1. Létrehozás nevű biztonsági csoport`Synapse_WORKSPACENAME_Users`
2. Létrehozás nevű biztonsági csoport`Synapse_WORKSPACENAME_Admins`
3. A `Synapse_WORKSPACENAME_Admins` hozzáadása ehhez: `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>2. lépés: Az alapértelmezett ADLS Gen2-fiók előkészítése

A munkaterület kiépítésekor ki kellett választania egy ADLSGEN2-fiókot és egy tárolót a fájlrendszerhez a munkaterület használatához.

1. Az [Azure-portál megnyitása](https://portal.azure.com)
2. Keresse meg az ADLSGEN2 fiókot
3. Keresse meg az Azure Synapse-munkaterülethez kiválasztott tárolót (fájlrendszert)
4. Kattintson **a hozzáférés-vezérlés (IAM)** elemre
5. Rendelje hozzá a következő szerepköröket:
   1. **Olvasói** szerepkör:`Synapse_WORKSPACENAME_Users`
   2. **Storage Blob-adattulajdonosi** szerepkör:`Synapse_WORKSPACENAME_Admins`
   3. **Storage Blob-adatközreműködői** szerepkör:`Synapse_WORKSPACENAME_Users`
   4. **Storage Blob-adattulajdonosi** szerepkör:`WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>3. lépés: A munkaterület felügyeleti listájának konfigurálása

1. Nyissa meg az [ **Azure Synapse webes felhasználói felületét**](https://web.azuresynapse.net)
2. Nyissa meg a Biztonsági hozzáférés **vezérlő kezelése (Kezelés a**  > **biztonsági** > **hozzáférés vezérlő)**
3. Kattintson **a Rendszergazda hozzáadása**gombra, és válassza a`Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>4. lépés: Az SQL Felügyeleti hozzáférés konfigurálása a munkaterülethez

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
2. Navigálás a munkaterületre
3. Nyissa meg az Active Directory **beállításai** > **nak rendszergazdáját**
4. Kattintson **a Rendszergazda beállítása gombra.**
5. A következők szerint válasszon: `Synapse_WORKSPACENAME_Admins`
6. kattintson **a Kijelölés gombra**
7. kattintson a **Mentés gombra**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>5. lépés: Felhasználók és rendszergazdák hozzáadása és eltávolítása a biztonsági csoportokhoz

1. Rendszergazdai hozzáféréssel rendelkező felhasználók hozzáadása`Synapse_WORKSPACENAME_Admins`
2. Az összes többi felhasználó hozzáadása`Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Hozzáférés-vezérlés az adatokhoz

Az alapul szolgáló adatokhoz való hozzáférés-vezérlés három részre oszlik:

- Adatsík-hozzáférés a tárfiókhoz (a 2. lépésben már be van állítva)
- Adatsík-hozzáférés az SQL-adatbázisokhoz (sql-készletekhez és sql-on-demand-hez)
- Hitelesítő adatok létrehozása az SQL igény szerinti adatbázisaihoz a tárfiókon keresztül

## <a name="access-control-to-sql-databases"></a>Hozzáférés-vezérlés az SQL-adatbázisokhoz

> [!TIP]
> Az alábbi lépéseket **minden** SQL-adatbázishoz futtatni kell, hogy felhasználói hozzáférés legyen az összes SQL-adatbázishoz.

### <a name="sql-on-demand"></a>SQL igény szerint

Ha hozzáférést szeretne adni egy felhasználónak **egyetlen** igény szerinti SQL-adatbázishoz, kövesse az alábbi példában leírt lépéseket:

1. BEJELENTKEZÉS létrehozása

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Felhasználó létrehozása

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Felhasználó hozzáadása a megadott szerepkör tagjaihoz

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>SQL-készletek

Ha **egyetlen** SQL-adatbázishoz szeretne hozzáférést biztosítani egy felhasználónak, kövesse az alábbi lépéseket:

1. Hozza létre a felhasználót az adatbázisban a következő parancs futtatásával, amely a kívánt adatbázist célozza meg a környezetválasztóban (legördülő menü az adatbázisok kiválasztásához):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. A felhasználó nak szerepkört kell adni az adatbázis eléréséhez:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* és *db_datawriter* olvasási/írási engedélyekhez is dolgozhatnak, ha *db_owner* engedély megadása nem kívánatos.
> A Spark-felhasználó közvetlenül a Spark-ból olvasni és írni egy SQL-készletből, *db_owner* engedély szükséges.

A felhasználók létrehozása után ellenőrizze, hogy az SQL igény szerinti lekérdezheti-e a tárfiókot:

- Futtassa a következő **master** parancsot, amely az SQL fő adatbázisát célozza igény szerint:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>A munkaterületi folyamat hoz való hozzáférés-vezérlés futtatása

### <a name="workspace-managed-identity"></a>Munkaterület által felügyelt identitás

> [!IMPORTANT]
> Az SQL-készletre hivatkozó adatkészleteket vagy tevékenységeket tartalmazó folyamatok sikeres futtatásához a munkaterületidentitásnak közvetlenül hozzáférést kell biztosítani az SQL-készlethez.

Futtassa a következő parancsokat az egyes SQL-készleteken, hogy a munkaterület által felügyelt identitás futtassa a folyamatokat az SQL készlet adatbázisában:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Ez az engedély eltávolítható a következő parancsfájl futtatásával ugyanazon az SQL-készleten:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>További lépések

A Synapse SQL hozzáférésének és vezérlésének áttekintését a [Synapse SQL hozzáférés-vezérlés című](../sql/access-control.md)témakörben találja. Ha többet szeretne megtudni az adatbázis-biztonsági tagokról, olvassa el [a Rendszerbiztonsági tagok](https://msdn.microsoft.com/library/ms181127.aspx). Az adatbázis-szerepkörökkel kapcsolatos további információk az [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx) cikkben találhatók.
