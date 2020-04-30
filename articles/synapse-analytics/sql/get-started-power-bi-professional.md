---
title: Kapcsolódás Power BI Professional rendszerhez
description: Ebben az oktatóanyagban áttekintjük, hogyan csatlakoztatható Power BI asztal az SQL on-demand (előzetes verzió) szolgáltatáshoz.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a9db42bcd69d9a24a454c02c9bb0e2d339cb4860
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82185778"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Kapcsolódás a szinapszis SQL-hez Power BI Professional használatával

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Ebben az oktatóanyagban áttekintjük a Power BI Desktop és az SQL on-demand (előzetes verzió) összekapcsolásának lépéseit.

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések kibocsátására szolgáló eszköz:

- Tetszőleges SQL-ügyfél:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI asztali telepítés

Paraméterek:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Igény szerinti SQL-szolgáltatás végpontjának címe    | Kiszolgáló neveként lesz használva                                   |
| Igény szerinti SQL-szolgáltatás végpontjának régiója     | A rendszer annak meghatározására szolgál, hogy milyen tárterületet fogunk használni a mintákban |
| A végpontok hozzáférésének felhasználóneve és jelszava | A végpont eléréséhez lesz használva                               |
| A nézetek létrehozásához használni kívánt adatbázis     | Ez az adatbázis a mintákban kezdőpontként lesz használva       |

## <a name="first-time-setup"></a>Első beállítás

A minták használata előtt két lépésből áll:

1. Adatbázis létrehozása a nézetekhez
2. A Storage-ban tárolt fájlok eléréséhez igény szerint használandó hitelesítő adatok létrehozása

### <a name="create-database"></a>Adatbázis létrehozása

Ebben az első lépéseket bemutató cikkben létre kell hoznia egy saját adatbázist, amelyet demóként kell használni. A nézetek létrehozásához szükség van egy adatbázisra. Ezt az adatbázist fogja használni a dokumentációban található példák némelyikében.

> [!NOTE]
> Az adatbázisokat csak a metaadatok megtekintésére használják, nem a tényleges adatokhoz.
>
> Jegyezze fel a használni kívánt adatbázisnevet, majd később szükség lesz rá.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lekérdezések futtatása előtt létre kell hoznia a hitelesítő adatokat. A hitelesítő adatokat az SQL igény szerinti szolgáltatása fogja használni a tárolóban lévő fájlok eléréséhez.

> [!NOTE]
> Létre kell hoznia egy hitelesítő adatot a Storage-fiókhoz való hozzáféréshez. Bár az SQL igény szerint a különböző régiókban is elérheti a tárolókat, a Storage és az Azure szinapszis munkaterület az adott régióban jobb teljesítményt nyújt.

Kódrészlet **a népszámlálási adattárolók hitelesítő adatainak létrehozásához**:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>Power BI asztali jelentés létrehozása

Nyissa meg Power BI asztali alkalmazást, és válassza **az adatolvasás** lehetőséget.
![Nyissa meg Power BI asztali alkalmazást, és válassza az adatlekérdezés lehetőséget.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>1. lépés – adatforrás kiválasztása

Válassza ki az **Azure** -t a menüben, majd **Azure SQL Database**.
![Válassza ki az adatforrást.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>2. lépés – adatbázis kiválasztása

Írja be az adatbázis URL-címét és annak az adatbázisnak a nevét, ahol a nézet található.
![Válassza ki az adatbázist a végponton.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>További lépések

A [Storage-fájlok lekérdezésével](get-started-azure-data-studio.md) megtudhatja, hogyan CSATLAKOZHAT az SQL on-demandhoz a Azure Data Studio használatával.
 