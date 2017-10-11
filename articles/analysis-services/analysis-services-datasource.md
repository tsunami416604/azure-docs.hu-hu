---
title: "Az Azure Analysis Services támogatott adatforrások |} Microsoft Docs"
description: "Az Azure Analysis Services adatmodellekben támogatott adatforrások ismerteti."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 8bd6c3b5a923ce2f3cd0f60af82e59c5cc27cbb4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Az Azure Analysis Services támogatott adatforrások
Azure Analysis Services-kiszolgálók esetén támogatja az adatforrások a felhőben és a szervezet helyszíni csatlakozik. További támogatott adatforrások folyamatosan felvétel alatt. Gyakran ellátogatnia webhelyünkre. 

A következő adatforrások jelenleg támogatottak:

| Felhő  |
|---|
| Az Azure Blob Storage *  |
| Azure SQL Database  |
| Az Azure Data warehouse-bA |


| Helyszíni követelmények  |   |   |   |
|---|---|---|---|
| Access-adatbázishoz  | Mappa * | Oracle Database  | Teradata-adatbázis |
| Active Directory *  | JSON-dokumentum *  | Postgre SQL adatbázis *  |XML tábla * |
| Analysis Services  | A bináris * sorok  | SAP HANA *  |
| Analytics Platform System  | MySQL-adatbázis  | SAP Business Warehouse *  | |
| Dynamics CRM *  | Az OData-adatcsatorna *  | SharePoint *  |
| Az Excel-munkafüzet  | Az ODBC-lekérdezés  | SQL Database  |
| Exchange *  | OLE DB RENDSZERHEZ  | Sybase-adatbázishoz  |

\*A táblázatos 1 400 modellek csak. 

> [!IMPORTANT]
> A helyszíni adatforrásokhoz való csatlakozás igényel egy [helyszíni adatátjáró](analysis-services-gateway.md) környezetét a számítógépen.

## <a name="data-providers"></a>Adatszolgáltatók

Az Azure Analysis Services adatmodellekben szükség lehet különböző adatszolgáltatók, egyes adatforrásokhoz való kapcsolódáskor. Bizonyos esetekben csatlakozás az adatforrásokhoz, például az SQL Server Native Client (SQLNCLI11) natív szolgáltatók használata táblázatos modellek hibát adhat vissza.

Egy felhőbeli adatát csatlakozó adatmodellekben forrás például az Azure SQL Database, ha eltérő SQLOLEDB natív szolgáltatók használatához hibaüzenet jelenhet: **"a provider" "SQLNCLI11.1" nincs regisztrálva.** Vagy, ha egy DirectQuery modell csatlakozik a helyi adatforrások, ha natív szolgáltatók hibaüzenet jelenhet: **"hiba történt a sorkészlet OLE DB létrehozásakor. Helytelen szintaxis a(z) "KORLÁTJA" közelében "**.

A következő adatforrás-szolgáltatók támogatottak a memóriában vagy a DirectQuery-modellekre adatok a felhőben, vagy a helyszíni adatforrások való csatlakozáskor:

### <a name="cloud"></a>Felhő
| **Adatforrás** | **Memóriabeli** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |.NET framework Data Provider az SQL Server |.NET framework Data Provider az SQL Server |
| Azure SQL Database |.NET framework Data Provider az SQL Server |.NET framework Data Provider az SQL Server | |

### <a name="on-premises-via-gateway"></a>A helyszíni (átjáró) keresztül
|**Adatforrás** | **Memóriabeli** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server natív ügyfél 11.0 |.NET framework Data Provider az SQL Server |
| SQL Server |Microsoft OLE DB-szolgáltató az SQL Server |.NET framework Data Provider az SQL Server | |
| SQL Server |.NET framework Data Provider az SQL Server |.NET framework Data Provider az SQL Server | |
| Oracle |Microsoft OLE DB-szolgáltató az Oracle rendszerhez |Oracle-adatszolgáltatóban a .NET-hez | |
| Oracle |Oracle-adatszolgáltatóban a .NET-hez |Oracle-adatszolgáltatóban a .NET-hez | |
| Teradata |OLE DB-szolgáltató a teradata rendszerhez |Teradata-adatszolgáltatója a .NET-hez | |
| Teradata |Teradata-adatszolgáltatója a .NET-hez |Teradata-adatszolgáltatója a .NET-hez | |
| Analytics Platform System |.NET framework Data Provider az SQL Server |.NET framework Data Provider az SQL Server | |

> [!NOTE]
> Győződjön meg arról, 64 bites szolgáltató van telepítve, a helyszíni átjáró használatakor.
> 
> 

Amikor telepít egy helyi SQL Server Analysis Services táblázatos modell Azure Analysis Services, akkor lehet szükség módosítani a szolgáltatót.

**Egy adatforrás-szolgáltatója megadása**

1. Az SSDT > **táblázatos modell Explorer** > **adatforrások**, kattintson a jobb gombbal az adatforrás-kapcsolat, és kattintson a **adatforrás szerkesztése**.
2. A **kapcsolat szerkesztése**, kattintson a **speciális** a Speciális tulajdonságok ablak megnyitásához.
3. A **speciális tulajdonságok** > **szolgáltatók**, majd válassza ki a megfelelő szolgáltatót.

## <a name="impersonation"></a>A megszemélyesítés
Bizonyos esetekben elképzelhető különböző megszemélyesítési fiókot szeretne megadni. Megszemélyesítési fiókhoz SSDT vagy SSMS adható meg.

A helyszíni adatforrások:

* Ha az SQL-hitelesítést használ, a megszemélyesítési szolgáltatás fióknak kell lennie.
* Ha Windows-hitelesítést használ, állítsa be a Windows felhasználói/jelszó. Az SQL Server Windows-hitelesítés egy adott megszemélyesítési fiókhoz csak támogatott a memóriában levő modellek.

Felhő adatforrások:

* Ha az SQL-hitelesítést használ, a megszemélyesítési szolgáltatás fióknak kell lennie.

## <a name="next-steps"></a>Következő lépések
Ha a helyszíni adatforrások, ügyeljen arra, hogy telepítse a [helyszíni átjáró](analysis-services-gateway.md).   
A kiszolgáló SSDT vagy SSMS kezelésével kapcsolatos további tudnivalókért lásd: [kezelheti a kiszolgálót](analysis-services-manage.md).

