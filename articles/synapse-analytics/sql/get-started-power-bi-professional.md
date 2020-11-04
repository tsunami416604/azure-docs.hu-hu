---
title: Kapcsolódás a szinapszis SQL-hez Power BI Professional használatával
description: Ebben az oktatóanyagban áttekintjük, hogyan csatlakoztatható Power BI asztali kiszolgáló nélküli SQL-készlethez (előzetes verzió).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 97b611c449302c95d4b24c305ce50ee7683e85ea
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316472"
---
# <a name="connect-to-serverless-sql-pool-with-power-bi-professional"></a>Kapcsolódás kiszolgáló nélküli SQL-készlethez Power BI Professional használatával

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Ebben az oktatóanyagban áttekintjük a Power BI Desktop kiszolgáló nélküli SQL-készlethez (előzetes verzió) való csatlakoztatásának lépéseit.

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések kiküldéséhez a következő eszközök szükségesek:

- Tetszőleges SQL-ügyfél:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI asztali telepítés

Paraméterek:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Kiszolgáló nélküli SQL Pool szolgáltatás végpontjának címe    | Kiszolgáló neveként lesz használva                                   |
| Kiszolgáló nélküli SQL Pool szolgáltatás végponti régiója     | A rendszer annak meghatározására szolgál, hogy milyen tárterületet fogunk használni a mintákban |
| A végpontok hozzáférésének felhasználóneve és jelszava | A végpont eléréséhez lesz használva                               |
| A nézetek létrehozásához használni kívánt adatbázis       | Ez az adatbázis a mintákban kezdőpontként lesz használva       |

## <a name="first-time-setup"></a>Első beállítás

A minták használata előtt két lépésből áll:

1. Adatbázis létrehozása a nézetekhez
2. A kiszolgáló nélküli SQL-készlet által a tárolóban lévő fájlok eléréséhez használandó hitelesítő adatok létrehozása

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

A lekérdezések futtatása előtt létre kell hoznia a hitelesítő adatokat. Ezt a hitelesítő adatot a kiszolgáló nélküli SQL Pool szolgáltatás fogja használni a tárolóban lévő fájlok eléréséhez.

> [!NOTE]
> A Storage-fiókhoz való hozzáféréshez hitelesítő adatokat kell létrehoznia. Bár a kiszolgáló nélküli SQL-készlet képes a különböző régiókból származó tárterület elérésére, a Storage és az Azure szinapszis munkaterület az adott régióban jobb teljesítményt nyújt.

Kódrészlet **a népszámlálási adattárolók hitelesítő adatainak létrehozásához** :

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

## <a name="create-a-power-bi-desktop-report"></a>Power BI asztali jelentés létrehozása

Nyissa meg a Power BI asztali alkalmazást, és válassza az **adatlekérdezés** lehetőséget.

![Nyissa meg Power BI asztali alkalmazást, és válassza az adatlekérdezés lehetőséget.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>1. lépés – adatforrás kiválasztása

Válassza ki az **Azure** -t a menüben, majd **Azure SQL Database**.
![Válassza ki az adatforrást.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>2. lépés – adatbázis kiválasztása

Írja be az adatbázis URL-címét és annak az adatbázisnak a nevét, ahol a nézet található.
![Válassza ki az adatbázist a végponton.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Következő lépések

A [tárolási fájlok lekérdezésével](get-started-azure-data-studio.md) megtudhatja, hogyan csatlakozhat a kiszolgáló nélküli SQL-készlethez Azure Data Studio használatával.