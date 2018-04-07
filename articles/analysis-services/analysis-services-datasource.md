---
title: Az Azure Analysis Services támogatott adatforrások |} Microsoft Docs
description: Az Azure Analysis Services adatmodellekben támogatott adatforrások ismerteti.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/05/2018
ms.author: owend
ms.openlocfilehash: 16bf333fe18c912db7cb81e74fce29960b0728d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Az Azure Analysis Services támogatott adatforrások

Az adatforrások és összekötők jelennek meg adatok beolvasása vagy a Visual Studio importálása varázsló az Azure Analysis Services és az SQL Server Analysis Services jelennek meg. Azonban nem minden adatforrások és összekötők látható az Azure Analysis Services használata támogatott. A típusú adatforrások csatlakozhat számos tényezőtől függ, mint a modell kompatibilitási szint, a rendelkezésre álló adatok összekötők, a hitelesítési típus, a szolgáltatók és a helyszíni gateway-támogatás. 

## <a name="azure-data-sources"></a>Azure-adatforrással

|Adatforrás  |Memóriabeli  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   Igen      |    Igen      |
|Azure SQL Data Warehouse     |   Igen      |   Igen       |
|Azure Blob Storage*     |   Igen       |    Nem      |
|Azure Table Storage*    |   Igen       |    Nem      |
|Azure Cosmos DB*     |  Igen        |  Nem        |
|Azure Data Lake Store*     |   Igen       |    Nem      |
|Azure HDInsight HDFS*     |     Igen     |   Nem       |
|Azure HDInsight Spark*     |   Igen       |   Nem       |
||||

\* A táblázatos 1 400 modellek csak.

**Provider**   
A memóriában és csatlakozás az Azure adatforrásokhoz DirectQuery-modellekre használata a .NET Framework Data Provider az SQL Server.

## <a name="on-premises-data-sources"></a>Helyszíni adatforrások

Csatlakozás helyszíni erőforrásait és Azure AS-kiszolgáló szükséges egy helyszíni átjárót. Átjáró használata esetén a 64 bites szolgáltatók szükség.

### <a name="in-memory-and-directquery"></a>A memória és a DirectQuery

|Adatforrás | A memória szolgáltató | DirectQuery-szolgáltató |
|  --- | --- | --- |
| SQL Server |SQL Server natív ügyfél 11.0, Microsoft OLE DB-szolgáltató az SQL Server, a .NET Framework Data Provider az SQL Server | .NET framework Data Provider az SQL Server |
| SQL Server Data Warehouse |SQL Server natív ügyfél 11.0, Microsoft OLE DB-szolgáltató az SQL Server, a .NET Framework Data Provider az SQL Server | .NET framework Data Provider az SQL Server |
| Oracle |Microsoft OLE DB-szolgáltató az Oracle, Oracle-adatszolgáltatóban a .NET-hez |Oracle-adatszolgáltatóban a .NET-hez | |
| Teradata |OLE DB-szolgáltató a teradata rendszerhez, Teradata-adatszolgáltatója a .NET-hez |Teradata-adatszolgáltatója a .NET-hez | |
| | | |

### <a name="in-memory-only"></a>A memóriában csak

|Adatforrás  |  
|---------|---------|
|Access-adatbázishoz     |  
|Active Directory*     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Dynamics CRM*     |  
|Az Excel-munkafüzet     |  
|Exchange*     |  
|Mappa *     | 
|JSON-dokumentum *     |  
|A bináris * sorok     | 
|MySQL-adatbázis     | 
|Az OData-adatcsatorna *     |  
|Az ODBC-lekérdezés     | 
|OLE DB RENDSZERHEZ     |   
|Postgre SQL adatbázis *    | 
|SAP HANA *    |  
|SAP Business Warehouse *    |  
|SharePoint*     |   
|Sybase-adatbázis     |  
|XML tábla *    |  
|||
 
\* A táblázatos 1 400 modellek csak.

## <a name="specifying-a-different-provider"></a>Egy másik szolgáltató megadása

Az Azure Analysis Services adatmodellekben szükség lehet különböző adatszolgáltatók, egyes adatforrásokhoz való kapcsolódáskor. Bizonyos esetekben csatlakozás az adatforrásokhoz, például az SQL Server Native Client (SQLNCLI11) natív szolgáltatók használata táblázatos modellek hibát adhat vissza. Ha eltérő SQLOLEDB natív szolgáltatókat használ, a hibaüzenet jelenhet: **a szolgáltató nincs regisztrálva "SQLNCLI11.1"**. Vagy, ha a Kapcsolódás a helyi adatforrások DirectQuery modell és natív szolgáltatókat használ, a hibaüzenet jelenhet: **hiba történt a sorkészlet OLE DB létrehozásakor. Helytelen szintaxis a(z) "KORLÁTJA" közelében**.

Amikor telepít egy helyi SQL Server Analysis Services táblázatos modell Azure Analysis Services, akkor lehet szükség módosítani a szolgáltatót.

**A szolgáltató megadása**

1. Az SSDT > **táblázatos modell Explorer** > **adatforrások**, kattintson a jobb gombbal az adatforrás-kapcsolat, és kattintson a **adatforrás szerkesztése**.
2. A **kapcsolat szerkesztése**, kattintson a **speciális** a Speciális tulajdonságok ablak megnyitásához.
3. A **speciális tulajdonságok** > **szolgáltatók**, majd válassza ki a megfelelő szolgáltatót.

## <a name="impersonation"></a>A megszemélyesítés
Bizonyos esetekben elképzelhető különböző megszemélyesítési fiókot szeretne megadni. A Visual Studio (SSDT) vagy az SSMS megszemélyesítési fiókhoz adható meg.

A helyszíni adatforrások:

* Ha az SQL-hitelesítést használ, a megszemélyesítési szolgáltatás fióknak kell lennie.
* Ha Windows-hitelesítést használ, állítsa be a Windows felhasználói/jelszó. Az SQL Server Windows-hitelesítés egy adott megszemélyesítési fiókhoz csak támogatott a memóriában levő modellek.

Felhő adatforrások:

* Ha az SQL-hitelesítést használ, a megszemélyesítési szolgáltatás fióknak kell lennie.

## <a name="next-steps"></a>További lépések
[A helyszíni átjáró](analysis-services-gateway.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)   

