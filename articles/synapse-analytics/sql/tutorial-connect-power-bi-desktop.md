---
title: 'Oktatóanyag: az SQL on-demand (előzetes verzió) összekapcsolása Power BI Desktop & jelentés létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakoztatható az SQL on-demand (előzetes verzió) az Azure szinapszis Analytics szolgáltatásban Power BI Desktophoz, és hogyan hozhat létre bemutató jelentést egy nézet alapján.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1bdf2d0e3613af7eec339194d6d8a446be83f365
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692411"
---
# <a name="tutorial-use-sql-on-demand-preview-with-power-bi-desktop--create-a-report"></a>Oktatóanyag: SQL on-demand (előzetes verzió) használata Power BI Desktop & jelentés létrehozása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Bemutató adatbázis létrehozása
> - Jelentéshez használt nézet létrehozása
> - Power BI Desktop összekapcsolása az SQL igény szerinti használatával
> - Jelentés létrehozása nézet alapján

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő szoftverek szükségesek:

- Egy SQL-lekérdezési eszköz, például [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)vagy [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power bi Desktop](https://powerbi.microsoft.com/downloads/).

A következő paraméterek értékei:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Igény szerinti SQL-szolgáltatás végpontjának címe    | Kiszolgáló neveként használatos                                   |
| Igény szerinti SQL-szolgáltatás végpontjának régiója     | A mintákban használt tárterület meghatározására szolgál |
| A végpontok hozzáférésének felhasználóneve és jelszava | Végponthoz való hozzáféréshez használatos                               |
| A nézetek létrehozásához használni kívánt adatbázis     | A mintákban kiindulási pontként használt adatbázis       |

## <a name="1---create-database"></a>1 – adatbázis létrehozása

A bemutató környezethez hozzon létre egy saját bemutató-adatbázist. Ezzel az adatbázissal megtekintheti a metaadatokat, és nem tárolhatja a tényleges adatokat.

A következő Transact-SQL (T-SQL) parancsfájl futtatásával hozza létre a bemutató adatbázist (és szükség esetén dobja el a meglévő adatbázist):

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

## <a name="2---create-credential"></a>2 – hitelesítő adat létrehozása

Hitelesítő adatok szükségesek ahhoz, hogy az SQL igény szerinti szolgáltatás hozzáférjen a fájlokhoz a tárolóban. Hozzon létre egy olyan Storage-fiók hitelesítő adatait, amely ugyanabban a régióban található, mint a végpont. Bár az SQL igény szerint hozzáférhet a különböző régiókban található Storage-fiókokhoz, és az azonos régióban található tároló és végpont jobb teljesítményt biztosít.

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

## <a name="3---prepare-view"></a>3 – nézet előkészítése

A következő Transact-SQL-(T-SQL-) parancsfájl futtatásával hozza létre a nézetet a Power BI külső bemutatójának alapján:

Hozza létre a `usPopulationView` nézetet az adatbázison `Demo` belül a következő lekérdezéssel:

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

A bemutatóban szereplő adat a következő adatkészleteket tartalmazza:

Az USA népessége nemek és faji hovatartozás szerint, a 2000-es és a 2010-es Decennial-népszámlálás a parketta formátumában.

| Mappa elérési útja                                                  | Leírás                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /Release parancsot                                                    | A demo Storage-fiókban lévő adatszülő mappa               |
| /Release/us_population_county/                               | Az USA-beli populációs adatfájlok parkettás formátumban, évről évre particionálva a kaptár/Hadoop particionálási sémával. |

## <a name="4---create-power-bi-report"></a>4 – Power BI jelentés létrehozása

Hozza létre a Power BI Desktop jelentést a következő lépések végrehajtásával:

1. Nyissa meg a Power BI Desktop alkalmazást, és válassza az **adatlekérdezés**lehetőséget.

   ![Nyissa meg Power BI asztali alkalmazást, és válassza az adatlekérdezés lehetőséget.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Válassza az **Azure** > **Azure SQL Database**lehetőséget. 

   ![Válassza ki az adatforrást.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Írja be annak a kiszolgálónak a nevét, ahol az adatbázis található a **kiszolgáló** mezőben, majd írja be `Demo` az adatbázis nevét. Válassza az **Importálás** lehetőséget, majd kattintson **az OK gombra**. 

   ![Válassza ki az adatbázist a végponton.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Válassza ki az előnyben részesített hitelesítési módszert:

    - Példa HRE 
  
        ![Kattintson a Bejelentkezés elemre.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Példa az SQL-bejelentkezéshez – írja be a felhasználónevét és a jelszavát.

        ![SQL-bejelentkezés használata.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Válassza ki a `usPopulationView`nézetet, majd válassza a **Betöltés**lehetőséget. 

   ![Válasszon ki egy nézetet a kiválasztott adatbázison.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Várjon, amíg a művelet befejeződik, majd megjelenik egy előugró üzenet `There are pending changes in your queries that haven't been applied`. Válassza a **módosítások alkalmazása**lehetőséget. 

   ![Kattintson a módosítások alkalmazása lehetőségre.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Várjon, amíg a **lekérdezési módosítások alkalmazása** párbeszédpanel eltűnik, ami eltarthat néhány percig. 

   ![Várjon, amíg a lekérdezés befejeződik.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. A betöltés befejeződése után válassza ki a következő oszlopokat ebben a sorrendben a jelentés létrehozásához:
   - countyName
   - feltöltése
   - stateName

   ![Válassza ki a kívánt oszlopokat a térképes jelentés létrehozásához.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a jelentéssel, törölje az erőforrásokat a következő lépésekkel:

1. A Storage-fiók hitelesítő adatainak törlése

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

Folytassa a [lekérdezési tároló fájljaival](develop-storage-files-overview.md) , hogy megtudja, hogyan kérdezheti le a tárolási fájlokat a szinapszis SQL használatával.
