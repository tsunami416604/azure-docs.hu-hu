---
title: 'Azure Data Studio (előzetes verzió): A Synapse SQL csatlakoztatása és lekérdezése'
description: Az Azure Data Stu-val (előzetes verzió) csatlakozhat a Synapse SQL-hez, és lekérdezheti az Azure Synapse Analytics szolgáltatásban.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: edf5a6a6a0f17c21abb818a0d41d0d0b1c39949c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423802"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Csatlakozás a Synapse SQL-hez az Azure Data Studio segítségével (előzetes verzió)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Az [Azure Data Studio (előzetes verzió)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) segítségével csatlakozhat és lekérdezheti a Synapse SQL-t az Azure Synapse Analytics szolgáltatásban. 

## <a name="connect"></a>Kapcsolódás

A Synapse SQL-hez való csatlakozáshoz nyissa meg az Azure Data Studio alkalmazást, és válassza az **Új kapcsolat**lehetőséget.

![Az Azure Data Studio megnyitása](./media/get-started-azure-data-studio/1-start.png)

Válassza a **Microsoft SQL Server lehetőséget** **kapcsolattípusként**.

A kapcsolathoz a következő paraméterek szükségesek:

* **Kiszolgáló:** Kiszolgáló a `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net
* **Adatbázis:** Adatbázis neve

> [!NOTE]
> Ha igény szerinti **SQL-t** szeretne használni (előzetes verzió), az URL-címnek így kell kinéznie:
>
> - `<Azure Synapse workspace name>`- ondemand.sql.azuresynapse.net.
>
> Ha **SQL-készletet** szeretne használni, az URL-címnek így kell kinéznie:
>
> - `<Azure Synapse workspace name>`sql.azuresynapse.net

A Hitelesítés típusa a **Windows-hitelesítés**, **az Azure Active Directory**vagy az SQL **Login (SQL Login)** **lehetőséget választja.**

Ha **az SQL Login-t** szeretné hitelesítési típusként használni, adja hozzá a felhasználónév/jelszó paramétereit:

* **Felhasználó:** Kiszolgáló felhasználója az űrlapon`<User>`
* **Jelszó:** A felhasználóhoz társított jelszó

Az Azure Active Directory használatához ki kell választania a szükséges hitelesítési típust.

![AAD-hitelesítés](./media/get-started-azure-data-studio/3-aad-auth.png)

Ez a képernyőkép a **Windows-hitelesítés** **kapcsolatrészleteiről** látható:

![Windows-hitelesítés](./media/get-started-azure-data-studio/3-windows-auth.png)

Ez a képernyőkép a **Kapcsolat részleteiről** az **SQL Login**használatával látható:

![SQL-bejelentkezés](./media/get-started-azure-data-studio/2-database-details.png)

A sikeres bejelentkezés után egy ilyen irányítópultot kell látnia: ![Irányítópult](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Lekérdezés

A csatlakozás után lekérdezheti a Synapse SQL-t a támogatott [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) utasítások használatával a példányellen. A kezdéshez válassza az **Új lekérdezés** lehetőséget az irányítópult nézetből.

![Új lekérdezés](./media/get-started-azure-data-studio/5-new-query.png)

A következő Transact-SQL utasítással például igény szerint [lekérdezheti a Parquet fájlokat](query-parquet-files.md) az SQL használatával:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>További lépések 
További lehetőségek a Synapse SQL-hez való csatlakozásra: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)
 
