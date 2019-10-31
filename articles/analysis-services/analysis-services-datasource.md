---
title: A Azure Analysis Services által támogatott adatforrások | Microsoft Docs
description: A Azure Analysis Services adatmodelljei által támogatott adatforrásokat ismerteti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cc47ff50aa7bb6dce58ed83f75f69fb600ad040b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146941"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>A Azure Analysis Services által támogatott adatforrások

A Visual Studióban az adatgyűjtés vagy importálás varázslóban látható adatforrások és összekötők mind Azure Analysis Services, mind SQL Server Analysis Services esetében megjelennek. A Azure Analysis Servicesban azonban nem minden megjelenített adatforrás és összekötő támogatott. A csatlakozáshoz használható adatforrások típusai számos tényezőtől függenek, például a modell kompatibilitási szintjétől, a rendelkezésre álló adatösszekötők, a hitelesítés típusa, a szolgáltatók és a helyszíni adatátjárók támogatása. 

## <a name="azure-data-sources"></a>Azure-beli adatforrások

|Adatforrás  |Memóriabeli  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Igen      |    Igen      |
|Azure SQL Data Warehouse     |   Igen      |   Igen       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Igen       |    Nem      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   Igen       |    Nem      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Igen        |  Nem        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Igen       |    Nem      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Igen     |   Nem       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Igen       |   Nem       |
||||

<a name="tab1400a">1</a> – táblázatos 1400 és újabb modellek.   
<a name="azsqlmanaged">2</a> – Azure SQL Database felügyelt példány támogatott. Mivel a felügyelt példány egy magánhálózati IP-címmel rendelkező Azure VNet fut, a példányon engedélyezni kell a nyilvános végpontot. Ha nincs engedélyezve, helyszíni adatátjáró szükséges.    
<a name="databricks">3</a> – a Spark-összekötőt használó Azure Databricks jelenleg nem támogatott.   
<a name="gen2">4</a> – ADLS Gen2 jelenleg nem támogatott.


**Szolgáltató**    
Az Azure-adatforrásokhoz csatlakozó memóriában tárolt és DirectQuery modellek a .NET-keretrendszer adatszolgáltatóját használják SQL Serverhoz.

## <a name="other-data-sources"></a>Egyéb adatforrások

A helyszíni adatforrásokhoz és az Azure AS-kiszolgálóhoz való csatlakozáshoz helyszíni átjáró szükséges. Átjáró használata esetén 64 bites szolgáltatók szükségesek.

### <a name="in-memory-and-directquery"></a>Memóriában tárolt és DirectQuery

|Adatforrás | Memóriában lévő szolgáltató | DirectQuery-szolgáltató |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11,0, Microsoft OLE DB szolgáltató a SQL Serverhoz, .NET-keretrendszer adatszolgáltatója SQL Server | .NET-keretrendszer adatszolgáltatója SQL Server |
| Adattárház SQL Server |SQL Server Native Client 11,0, Microsoft OLE DB szolgáltató a SQL Serverhoz, .NET-keretrendszer adatszolgáltatója SQL Server | .NET-keretrendszer adatszolgáltatója SQL Server |
| Oracle | OLE DB-szolgáltató Oracle-hez, Oracle-adatszolgáltató a .NET-hez |Oracle-adatszolgáltató a .NET-hez |
| Teradata |OLE DB szolgáltató a Teradata-hez, Teradata-adatszolgáltató a .NET-hez |Teradata-adatszolgáltató a .NET-hez |
| | | |

### <a name="in-memory-only"></a>Csak a memóriában

|Adatforrás  |  
|---------|
|Access-adatbázis     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Elemzési platform rendszer     |  
|CSV-fájl  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel-munkafüzet     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|<sup>[1](#tab1400b) . mappa</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (bétaverzió) |
|1\. JSON-dokumentum<sup>[](#tab1400b)</sup>     |  
|Bináris<sup>[1-től](#tab1400b) származó sorok</sup>     | 
|MySQL-adatbázis     | 
|1\. OData-hírcsatorna<sup>[](#tab1400b)</sup>     |  
|ODBC-lekérdezés     | 
|OLE DB     |   
|PostgreSQL-adatbázis<sup>[1](#tab1400b)</sup>    | 
|Salesforce objektumok<sup>[1](#tab1400b)</sup> |  
|Salesforce-jelentések<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint-lista<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Sybase-adatbázis     |  
|TXT-fájl  |
|1\. XML-táblázat<sup>[](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> – táblázatos 1400 és újabb modellek.   
<a name="filesSP">2</a> – a helyszíni SharePointban található fájlok nem támogatottak.

## <a name="specifying-a-different-provider"></a>Másik szolgáltató meghatározása

Egyes adatforrásokhoz való kapcsolódáskor a Azure Analysis Services adatmodelljei eltérő adatszolgáltatót igényelhetnek. Bizonyos esetekben a natív szolgáltatók, például a SQL Server Native Client (SQLNCLI11) használatával az adatforrásokhoz csatlakozó táblázatos modellek hibát adhatnak vissza. Ha a SQLOLEDB-től eltérő natív szolgáltatókat használ, hibaüzenet jelenik meg: **a (z) "sqlncli 11.1" szolgáltató nincs regisztrálva**. Ha a helyszíni adatforrásokhoz kapcsolódó DirectQuery-modellel rendelkezik, és natív szolgáltatókat használ, a következő hibaüzenet jelenhet meg: **hiba OLE DB sor létrehozásakor. Helytelen szintaxis a "LIMIT" közelében**.

Ha helyszíni SQL Server Analysis Services táblázatos modellt telepít át Azure Analysis Servicesre, akkor szükség lehet a szolgáltató módosítására.

**Szolgáltató megadásához**

1. A Visual Studióban > **táblázatos modell Explorer** > **adatforrások**területen kattintson a jobb gombbal az adatforrás-kapcsolatok elemre, majd kattintson az **Adatforrás szerkesztése**elemre.
2. A **Kapcsolódás szerkesztése**ablakban kattintson a **speciális** elemre az előzetes Tulajdonságok ablak megnyitásához.
3. A **Speciális tulajdonságok beállítása**  > **szolgáltatók**elemnél válassza ki a megfelelő szolgáltatót.

## <a name="impersonation"></a>Megszemélyesítési
Bizonyos esetekben szükség lehet egy másik megszemélyesítési fiók megadására. A megszemélyesítési fiók megadható a Visual Studióban vagy a SSMS.

Helyszíni adatforrások esetén:

* SQL-hitelesítés használata esetén a Megszemélyesítésnek szolgáltatásfiók-nek kell lennie.
* Windows-hitelesítés használata esetén állítsa be a Windows-felhasználó/jelszó beállítását. SQL Server esetében az adott megszemélyesítési fiókkal rendelkező Windows-hitelesítés csak memóriában tárolt adatmodellek esetén támogatott.

Felhőbeli adatforrások esetén:

* SQL-hitelesítés használata esetén a Megszemélyesítésnek szolgáltatásfiók-nek kell lennie.

## <a name="oauth-credentials"></a>OAuth hitelesítő adatai

A 1400-es és újabb kompatibilitási szinten található táblázatos modellek esetében a Azure SQL Database, a Azure SQL Data Warehouse, a Dynamics 365 és a SharePoint-lista támogatja a OAuth hitelesítő adatait. Azure Analysis Services kezeli a OAuth-adatforrások jogkivonat-frissítését, hogy elkerülje a hosszan futó frissítési műveletek időtúllépését. Érvényes tokenek létrehozásához állítsa be a hitelesítő adatokat a SSMS használatával.

## <a name="next-steps"></a>Következő lépések
Helyszíni [átjáró](analysis-services-gateway.md)    
[A kiszolgáló kezelése](analysis-services-manage.md)   

