---
title: 'Oktatóanyag: Az SQL igény szerinti csatlakoztatása (előzetes verzió) csatlakoztatása a Power BI Desktop & jelentés létrehozásához'
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztathat az SQL-t igény szerinti (előzetes verzió) az Azure Synapse Analytics szolgáltatásban a Power BI asztali verziójához, és hogyan hozhat létre bemutatójelentést egy nézet alapján.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e0ac6ccde2443a7b374d9eb85f6f960af79c69dc
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769484"
---
# <a name="tutorial-connect-sql-on-demand-preview-to-power-bi-desktop--create-report"></a>Oktatóanyag: Az SQL igény szerinti csatlakoztatása (előzetes verzió) csatlakoztatása a Power BI Desktop & jelentés létrehozásához

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Demó-adatbázis létrehozása
> - Jelentéshez használt nézet létrehozása
> - Csatlakozás a Power BI Desktophoz
> - Jelentés létrehozása nézet alapján

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő szoftverekre van szükség:

- SQL-lekérdezési eszköz, például [az Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)vagy az SQL Server Management Studio [(SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/).

A következő paraméterek értékei:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL igény szerinti szolgáltatás végpontcíme    | Kiszolgálónévként használva                                   |
| SQL igény szerinti szolgáltatásvégpont-régió     | A mintákban használt tároló meghatározásához |
| Felhasználónév és jelszó a végpontokhoz való hozzáféréshez | Végpont eléréséhez használva                               |
| Nézetek létrehozásához használt adatbázis     | A minták kiindulási pontjaként használt adatbázis       |

## <a name="1---create-database"></a>1 - Adatbázis létrehozása

A demo környezetben, hozzon létre saját demo adatbázis. Az adatbázis segítségével metaadatokat tekinthet meg, nem pedig tényleges adatokat.

Hozza létre a demó adatbázist (és szükség esetén eldobjon egy meglévő adatbázist) a következő Transact-SQL (T-SQL) parancsfájl futtatásával:

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - Hitelesítő adatok létrehozása

A hitelesítő adatok szükségesek az SQL igény szerinti szolgáltatás a tárolóban lévő fájlok eléréséhez. Hozza létre a hitelesítő adatokat egy tárfiók, amely ugyanabban a régióban található, mint a végpont. Bár az SQL igény szerinti hozzáférés különböző régiókból származó tárfiókok, a tárolás és a végpont ugyanabban a régióban jobb teljesítményt nyújt.

Hozza létre a hitelesítő adatokat a következő Transact-SQL (T-SQL) parancsfájl futtatásával:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3 - Előkészítés nézet

Hozza létre a nézetet a Power BI által felhasználható külső bemutatóadatok alapján a következő Transact-SQL (T-SQL) parancsfájl futtatásával:

Hozza létre `usPopulationView` az `Demo` adatbázison belüli nézetet a következő lekérdezéssel:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

A bemutató adatok a következő adatkészleteket tartalmazzák:

Amerikai lakosság nemek és a faj minden amerikai megyében származó 2000-ben és 2010-ben tízévenkénti népszámlálás parketta formátumban.

| Mappa elérési útja                                                  | Leírás                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Szülőmappa a bemutatótárfiókban lévő adatokhoz               |
| /release/us_population_county/                               | Us population data files in Parquet format, partitioned by year using Hive/Hadoop partitioning scheme. |

## <a name="4---create-power-bi-report"></a>4 – Power BI-jelentés létrehozása

Hozza létre a jelentést a Power BI Desktophoz az alábbi lépésekkel:

1. Nyissa meg a Power BI Desktop alkalmazást, és válassza **az Adatok beszedése**lehetőséget.

   ![Nyissa meg az asztali Power BI alkalmazást, és válassza az Adatok bekéselése lehetőséget.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Válassza az **Azure** > **Azure SQL Database lehetőséget.** 

   ![Jelölje ki az adatforrást.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Írja be annak a kiszolgálónak a **Server** nevét, amelynek `Demo` helye az adatbázis a Kiszolgáló mezőben, majd írja be az adatbázis nevét. Jelölje be az **Importálás** jelölőnégyzetet, majd az **OK gombot.** 

   ![Válassza ki az adatbázist a végponton.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Válassza ki az előnyben részesített hitelesítési módszert:

    - Példa az AAD-re 
  
    ![Kattintson a Bejelentkezés elemre.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Példa az SQL Login - Írja be a felhasználónevét és jelszavát.

    ![Sql login használata.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Jelölje ki `usPopulationView`a nézetet, majd válassza **a Betöltés**lehetőséget. 

   ![Jelöljön ki egy nézetet a kijelölt adatbázisban.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Várja meg, amíg a művelet befejeződik, `There are pending changes in your queries that haven't been applied`majd egy előugró ablak jelenik meg, amely tartalmazza a . Válassza **a Módosítások alkalmazása**lehetőséget. 

   ![Kattintson a Módosítások alkalmazása gombra.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Várja meg, amíg a **Lekérdezésmódosítások alkalmazása** párbeszédpanel eltűnik, ami eltarthat néhány percig. 

   ![Várja meg, amíg a lekérdezés befejeződik.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. A terhelés befejezése után válassza ki a következő oszlopokat a jelentés létrehozásához:
   - megyeNév
   - Lakosság
   - stateName (államnév)

   ![Térképjelentés létrehozásához válassza ki az érdeklődésre számot tartó oszlopokat.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte a jelentés használatát, törölje az erőforrásokat a következő lépésekkel:

1. A tárfiók hitelesítő adatainak törlése

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. A nézet törlése

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Az adatbázis eldobása

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>További lépések

A [Query tárolófájljaival](develop-storage-files-overview.md) megtudhatja, hogyan kérdezheti le a tárolófájlokat a Synapse SQL használatával.
