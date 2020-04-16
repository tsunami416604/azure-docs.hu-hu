---
title: Csatlakozás a Power BI Professional programhoz
description: Ebben az oktatóanyagban végighaladunk a Power BI-asztal igény szerinti SQL-hez való csatlakoztatásának lépésein (előzetes verzió).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422563"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Csatlakozás a Synapse SQL-hez a Power BI Professional segítségével

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Ebben az oktatóanyagban végighaladunk a Power BI-asztal igény szerinti SQL-hez való csatlakoztatásának lépésein (előzetes verzió).

## <a name="prerequisites"></a>Előfeltételek

Lekérdezések kiadására eszköz:

- SQL kliens az Ön által választott:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI desktop telepítve

Paraméterek:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL igény szerinti szolgáltatás végpontcíme    | A rendszer kiszolgálónévként fogja használni                                   |
| SQL igény szerinti szolgáltatásvégpont-régió     | A mintákban használt tároló meghatározására szolgálnak |
| Felhasználónév és jelszó a végpontokhoz való hozzáféréshez | A végpont elérésére szolgál                               |
| Nézetek létrehozásához használt adatbázis     | Ez az adatbázis lesz kiindulási pont ként a mintákban       |

## <a name="first-time-setup"></a>Első beállítás

A minták használata előtt két lépéssel lehet elkezdeni:

1. Adatbázis létrehozása a nézetekhez
2. Az SQL igény szerinti hozzáférési adatainak létrehozása a tárolóban lévő fájlok eléréséhez

### <a name="create-database"></a>Adatbázis létrehozása

Mivel a demo környezetet fogja használni, saját adatbázist kell létrehoznia demo célokra. A nézetek létrehozásához adatbázisra van szükség. Ezt az adatbázist fogja használni a dokumentáció néhány mintalekérdezésében.

> [!NOTE]
> Ne feledje, hogy az adatbázisok csak a metaadatok megtekintésére szolgálnak, a tényleges adatokra nem.
>
> Írja le a használt adatbázisnevet, később szüksége lesz rá.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Létre kell hoznunk a hitelesítő adatokat, mielőtt futtathat lekérdezéseket. Ezt a hitelesítő adatokat az SQL igény szerinti szolgáltatás fogja használni a tárolóban lévő fájlok eléréséhez.

> [!NOTE]
> Vegye figyelembe, hogy létre kell hoznia a tárfiókhoz való hozzáféréshez szükséges hitelesítő adatokat. Bár az SQL igény szerinti hozzáférés különböző régiókból származó tárolókhoz férhet hozzá, a storage és az Azure Synapse munkaterület ugyanabban a régióban jobb teljesítményélményt nyújt.

**Kódrészlet a népszámlálási adattárolók hitelesítő adatainak létrehozásához,** futtassa a következőt:

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

## <a name="creating-power-bi-desktop-report"></a>A Power BI asztali jelentéslétrehozása

Nyissa meg az asztali Power BI alkalmazást, és válassza az "Adatok beszerezni" lehetőséget.
![Nyissa meg az asztali Power BI alkalmazást, és válassza az Adatok bekéselése lehetőséget.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>1. lépés – Adatforrás kiválasztása

Válassza az "Azure" menüpontot, majd az "Azure SQL Database".Select "Azure" in the menu and then "Azure SQL Database".
![Jelölje ki az adatforrást.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>2. lépés - Adatbázis kiválasztása

Írja az adatbázis URL-címét és annak az adatbázisnak a nevét, amelyben a nézet található.
![Válassza ki az adatbázist a végponton.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>További lépések

Lépjen a [Query storage-fájlokhoz,](get-started-azure-data-studio.md) és ismerje meg, hogyan csatlakozhat az SQL-hez igény szerint az Azure Data Studio használatával.
 