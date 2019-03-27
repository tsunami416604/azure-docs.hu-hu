---
title: Támogatott adatforrások az Azure Analysis Services |} A Microsoft Docs
description: Az Azure Analysis Services adatmodellek támogatott adatforrások ismerteti.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 374ffc3a9d181d0596b5a4af1cd8082023e227fe
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498519"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Támogatott adatforrások az Azure Analysis Services

Az adatforrások és összekötők jelennek meg adatok lekérése vagy a Visual Studióban importálása varázsló az Azure Analysis Services és az SQL Server Analysis Services jelennek meg. Azonban nem minden adatforrások és az összekötők látható az Azure Analysis Servicesben támogatottak. Adatforrásokhoz csatlakozhat, mint például számos tényezőtől függ a típusú modell kompatibilitási szintje, elérhető összekötők, hitelesítés típusa, szolgáltatók és On-premises data gateway-támogatás. 

## <a name="azure-data-sources"></a>Azure-beli adatforrások

|Adatforrás  |Memóriabeli  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Igen      |    Igen      |
|Azure SQL Data Warehouse     |   Igen      |   Igen       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Igen       |    Nem      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   Igen       |    Nem      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Igen        |  Nem        |
|Az Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup> [4](#gen2)</sup>      |   Igen       |    Nem      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Igen     |   Nem       |
|Az Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup> [3](#databricks)</sup>     |   Igen       |   Nem       |
||||

<a name="tab1400a">1</a> – táblázatos 1400-as és újabb modellek csak.   
<a name="azsqlmanaged">2</a> – azure SQL Database felügyelt példányába használata támogatott. Mivel a felügyelt példány az Azure virtuális hálózaton belüli magánhálózati IP-címmel, egy helyszíni Adatátjáróra szükség.   
<a name="databricks">3</a> – azure Databricks használatával a Spark-összekötő jelenleg nem támogatott.   
<a name="gen2">4</a> – ADLS Gen2 jelenleg nem támogatott.


**Szolgáltató**   
A memóriában, és csatlakozik az Azure-adatforrás DirectQuery-modellekben használata a .NET-keretrendszer adatszolgáltatója az SQL Server.

## <a name="on-premises-data-sources"></a>Helyszíni adatforrások

Csatlakozás helyszíni erőforrásait és Azure-AS-kiszolgálóhoz szükséges egy helyszíni átjárót. Az átjáró használatakor a 64 bites szolgáltatók szükség.

### <a name="in-memory-and-directquery"></a>A memóriában, és a DirectQuery

|Adatforrás | A memória szolgáltató | A DirectQuery-szolgáltató |
|  --- | --- | --- |
| SQL Server |Az SQL Server natív ügyfél 11.0, Microsoft OLE DB-szolgáltató az SQL Server, a .NET-keretrendszer adatszolgáltatója az SQL Server | .NET-keretrendszer adatszolgáltatója az SQL Server |
| Az SQL Server Data Warehouse |Az SQL Server natív ügyfél 11.0, Microsoft OLE DB-szolgáltató az SQL Server, a .NET-keretrendszer adatszolgáltatója az SQL Server | .NET-keretrendszer adatszolgáltatója az SQL Server |
| Oracle |Microsoft OLE DB Provider Pro Oracle, Oracle-adatszolgáltató a .NET-hez |Oracle-adatszolgáltató a .NET-hez |
| Teradata |OLE DB-szolgáltató a teradata rendszerhez, Teradata-adatszolgáltató a .NET-hez |Teradata-adatszolgáltató a .NET-hez |
| | | |

### <a name="in-memory-only"></a>A memóriában csak

|Adatforrás  |  
|---------|
|Access-adatbázis     |  
|Az Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analytics Platform System     |  
|CSV-fájl  |
|A Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel-munkafüzet     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Folder<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b) </sup> (bétaverzió) |
|JSON-dokumentumok<sup>[1](#tab1400b)</sup>     |  
|A bináris sorok<sup>[1](#tab1400b)</sup>     | 
|MySQL-adatbázis     | 
|OData-csatorna<sup>[1](#tab1400b)</sup>     |  
|ODBC-lekérdezés     | 
|OLE DB     |   
|Postgre SQL-adatbázis<sup>[1](#tab1400b)</sup>    | 
|Salesforce-objektumok<sup>[1](#tab1400b)</sup> |  
|Salesforce-jelentések<sup>[1](#tab1400b)</sup> |
|AZ SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint<sup>[1](#tab1400b)</sup>     |   
|Sybase-adatbázis     |  
|TXT file  |
|XML-táblázatok<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> táblázatos 1400-as és újabb modellek csak.

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

