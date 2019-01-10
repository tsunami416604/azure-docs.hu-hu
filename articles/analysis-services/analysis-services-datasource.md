---
title: Támogatott adatforrások az Azure Analysis Services |} A Microsoft Docs
description: Az Azure Analysis Services adatmodellek támogatott adatforrások ismerteti.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e1a001a60151136be6bde9de38f971807cf0c288
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188402"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Támogatott adatforrások az Azure Analysis Services

Az adatforrások és összekötők jelennek meg adatok lekérése vagy a Visual Studióban importálása varázsló az Azure Analysis Services és az SQL Server Analysis Services jelennek meg. Azonban nem minden adatforrások és az összekötők látható az Azure Analysis Servicesben támogatottak. Adatforrásokhoz csatlakozhat, mint például számos tényezőtől függ a típusú modell kompatibilitási szintje, elérhető összekötők, hitelesítés típusa, szolgáltatók és On-premises data gateway-támogatás. 

## <a name="azure-data-sources"></a>Azure-beli adatforrások

|Adatforrás  |Memóriabeli  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   Igen      |    Igen      |
|Azure SQL Data Warehouse     |   Igen      |   Igen       |
|Azure Blob Storage*     |   Igen       |    Nem      |
|Azure Table Storage*    |   Igen       |    Nem      |
|Azure Cosmos DB*     |  Igen        |  Nem        |
|Azure Data Lake Store*     |   Igen       |    Nem      |
|Azure HDInsight HDFS*     |     Igen     |   Nem       |
|Az Azure HDInsight Spark *     |   Igen       |   Nem       |
||||

\* Tabular 1400-modellekkel csak.

**Szolgáltató**   
A memóriában, és csatlakozik az Azure-adatforrás DirectQuery-modellekben használata a .NET-keretrendszer adatszolgáltatója az SQL Server.

## <a name="on-premises-data-sources"></a>Helyszíni adatforrások

Csatlakozás helyszíni erőforrásait és Azure-AS-kiszolgálóhoz szükséges egy helyszíni átjárót. Az átjáró használatakor a 64 bites szolgáltatók szükség.

### <a name="in-memory-and-directquery"></a>A memóriában, és a DirectQuery

|Adatforrás | A memória szolgáltató | A DirectQuery-szolgáltató |
|  --- | --- | --- |
| SQL Server |Az SQL Server natív ügyfél 11.0, Microsoft OLE DB-szolgáltató az SQL Server, a .NET-keretrendszer adatszolgáltatója az SQL Server | .NET-keretrendszer adatszolgáltatója az SQL Server |
| Az SQL Server Data Warehouse |Az SQL Server natív ügyfél 11.0, Microsoft OLE DB-szolgáltató az SQL Server, a .NET-keretrendszer adatszolgáltatója az SQL Server | .NET-keretrendszer adatszolgáltatója az SQL Server |
| Oracle |Microsoft OLE DB Provider Pro Oracle, Oracle-adatszolgáltató a .NET-hez |Oracle-adatszolgáltató a .NET-hez | |
| Teradata |OLE DB-szolgáltató a teradata rendszerhez, Teradata-adatszolgáltató a .NET-hez |Teradata-adatszolgáltató a .NET-hez | |
| | | |

### <a name="in-memory-only"></a>A memóriában csak

|Adatforrás  |  
|---------|---------|
|Access-adatbázis     |  
|Active Directory*     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Dynamics CRM *     |  
|Excel-munkafüzet     |  
|Exchange*     |  
|Mappa *     |
|IBM Informix * (bétaverzió) |
|JSON-dokumentum *     |  
|A bináris * sorok     | 
|MySQL-adatbázis     | 
|OData-adatcsatorna *     |  
|ODBC-lekérdezés     | 
|OLE DB     |   
|Postgre SQL Database *    | 
|Salesforce-objektumok * |  
|Salesforce-jelentések * |
|AZ SAP HANA *    |  
|SAP Business Warehouse *    |  
|SharePoint*     |   
|Sybase-adatbázis     |  
|XML-tábla *    |  
|||
 
\* Tabular 1400-modellekkel csak.

## <a name="specifying-a-different-provider"></a>Adjon meg egy másik szolgáltató

Az Azure Analysis Services adatmodellek szükség lehet különböző adatszolgáltatók bizonyos adatforrásokhoz való kapcsolódáskor. Bizonyos esetekben a táblázatos modellek használatával natív szolgáltatók, például az SQL Server Native Client (SQLNCLI11) adatforráshoz csatlakozik hibát adhat vissza. Ha natív szolgáltatók eltérő SQLOLEDB használja, hibaüzenet jelenhet meg: **A szolgáltató nincs regisztrálva a "SQLNCLI11.1"**. Vagy, ha egy DirectQuery-modell helyszíni adatforráshoz csatlakozik, és natív szolgáltató használata, hibaüzenet jelenik: **Hiba történt a létrehozásakor OLE DB sorban állítsa be. Helytelen szintaxis közelében "LIMIT"**.

Egy helyszíni SQL Server Analysis Services táblázatos modell az Azure Analysis Services áttelepítésekor lehet módosítania kell a szolgáltatót.

**A szolgáltató megadása**

1. Az SSDT > **Táblázatosmodell-tallózóban** > **adatforrások**, kattintson a jobb gombbal egy adatforrás-kapcsolatot, és kattintson **adatforrás szerkesztése**.
2. A **kapcsolat szerkesztése**, kattintson a **speciális** a Speciális tulajdonságok ablak megnyitásához.
3. A **speciális tulajdonságok** > **szolgáltatók**, majd válassza ki a megfelelő szolgáltató.

## <a name="impersonation"></a>A megszemélyesítés
Bizonyos esetekben, előfordulhat, hogy kell a különböző megszemélyesítési fiókot szeretne megadni. Megszemélyesítési fiókhoz Visual Studio (SSDT) vagy az SSMS segítségével is megadható.

A helyszíni adatforrások:

* Ha az SQL-hitelesítés használata esetén megszemélyesítési szolgáltatásfiókot kell lennie.
* Ha Windows-hitelesítést használ, állítsa be a Windows-felhasználó/jelszó. Az SQL Server Windows-hitelesítés egy adott megszemélyesítési fiókhoz csak támogatott memóriában adatmodelleket.

Felhőbeli adatforrások:

* Ha az SQL-hitelesítés használata esetén megszemélyesítési szolgáltatásfiókot kell lennie.

## <a name="next-steps"></a>További lépések
[Helyszíni átjáró](analysis-services-gateway.md)   
[Kiszolgáló kezelése](analysis-services-manage.md)   

