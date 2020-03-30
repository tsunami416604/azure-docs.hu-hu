---
title: Az Azure Analysis Services által támogatott adatforrások | Microsoft dokumentumok
description: Az Azure Analysis Services táblázatos 1200-as és magasabb adatmodelljeihez támogatott adatforrások és összekötők ismertetése.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461657"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Támogatott adatforrások az Azure Analysis Servicesben

A Visual Studio Adatok be- és táblázatimportálása varázslója az Analysis Services-projektekkel című programban látható adatforrások és összekötők az Azure Analysis Services és az SQL Server Analysis Services számára is megjelennek. Azonban nem minden jelenlévő adatforrás és összekötő támogatott az Azure Analysis Servicesben. Az adatforrások típusai, amelyekhez csatlakozhat, számos tényezőtől függ, például a modell kompatibilitási szintjétől, a rendelkezésre álló adatösszekötőktől, a hitelesítés típusától és a helyszíni adatátjáró támogatásától. Az alábbi táblázatok az Azure Analysis Services támogatott adatforrásait ismertetik.

## <a name="azure-data-sources"></a>Azure-beli adatforrások

|Adatforrás  |Memóriabeli  |DirectQuery  |Megjegyzések |
|---------|---------|---------|---------|
|Azure SQL Database      |   Igen      |    Igen      |<sup>[2,](#azprovider)</sup> <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL Adattár)      |   Igen      |   Igen       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Igen       |    Nem      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   Igen       |    Nem      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Igen        |  Nem        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Áruház Gen1      |   Igen       |    Nem      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Áruház Gen2       |   Igen       |    Nem      |<sup>[1,](#tab1400a)</sup> <sup> [5](#gen2)</sup>|
|Azure HDInsight HDF-ek    |     Igen     |   Nem       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Igen       |   Nem       |<sup>[1,](#tab1400a)</sup> <sup> [4](#databricks)</sup>|
||||

**Megjegyzések:**   
<a name="tab1400a">1</a> - Csak táblázatos 1400 és magasabb modellek.  
<a name="azprovider">2</a> - Ha *a táblázatos* 1200-as és magasabb modellekben szolgáltatói adatforrásként van megadva, mind a memóriában lévő, mind a DirectQuery-modellekhez Microsoft OLE DB illesztőprogram szükséges az SQL Server MSOLEDBSQL (ajánlott), az SQL Server Native Client 11.0 vagy a .NET Framework Data Provider for SQL Server rendszerhez.    
<a name="azsqlmanaged">3</a> - Az Azure SQL Database felügyelt példánya támogatott. Mivel a felügyelt példány az Azure Virtuális hálózaton belül fut egy privát IP-címmel, nyilvános végpontot engedélyezni kell a példányon. Ha nincs engedélyezve, a [helyszíni adatátjáró](analysis-services-gateway.md) szükséges.    
<a name="databricks">4</a> – A Spark-összekötő használatával az Azure Databricks jelenleg nem támogatott.   
<a name="gen2">5</a> – Az ADLS Gen2-összekötő jelenleg nem támogatott, azonban az Azure Blob Storage-összekötő használható ADLS Gen2 adatforrással.   

## <a name="other-data-sources"></a>Más adatforrások

|Adatforrás | Memóriabeli | DirectQuery |Megjegyzések   |
|  --- | --- | --- | --- |
|Access-adatbázis     |  Igen | Nem |  |
|Active Directory     |  Igen | Nem | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Igen | Nem |  |
|Analytics platformrendszer     |  Igen | Nem |  |
|CSV-fájl  |Igen | Nem |  |
|Dynamics 365     |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Excel-munkafüzet     |  Igen | Nem |  |
|Exchange      |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Mappa      |Igen | Nem | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Igen | Nem |  |
|JSON-dokumentum      |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Bináris sorok      | Igen | Nem | <sup>[6](#tab1400b)</sup> |
|MySQL-adatbázis     | Igen | Nem |  |
|OData-adatcsatorna      |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|ODBC lekérdezés     | Igen | Nem |  |
|OLE DB     |   Igen | Nem |  |
|Oracle  | Igen  |Igen  | <sup>[9](#oracle)</sup> |
|PostgreSQL-adatbázis   | Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Salesforce-objektumok|  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|Salesforce-jelentések |Igen | Nem | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Igen | Nem |  |
|SAP Business Warehouse    |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
|SharePoint-lista      |   Igen | Nem | <sup>[6,](#tab1400b)</sup> <sup> [11](#filesSP)</sup> |
|SQL Server |Igen   | Igen  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> | 
|SQL Server adattárház |Igen   | Igen  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> |
|Sybase-adatbázis     |  Igen | Nem |  |
|Teradata | Igen  | Igen  | <sup>[10](#teradata)</sup> |
|TXT fájl  |Igen | Nem |  |
|XML-táblázat    |  Igen | Nem | <sup>[6](#tab1400b)</sup> |
| | | |

**Megjegyzések:**   
<a name="tab1400b">6</a> - Táblázatos 1400 és magasabb modellek csak.  
<a name="sqlim">7</a> - Ha *a táblázatos* 1200-as és magasabb modellekben szolgáltatói adatforrásként van megadva, adja meg a Microsoft OLE DB drivert az SQL Server MSOLEDBSQL (ajánlott), az SQL Server Native Client 11.0 vagy a .NET Framework Data Provider for SQL Server számára.  
<a name="instgw">8</a> - Ha az MSOLEDBSQL adatszolgáltatóként való megadását adja meg, szükség lehet a Microsoft OLE DB Driver for SQL Server letöltésére és [telepítésére](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) ugyanazon a számítógépen, mint a helyszíni adatátjáró.  
<a name="oracle">9</a> - Táblázatos 1200 modellek esetén, vagy *szolgáltatói* adatforrásként táblázatos 1400+ modellekben adja meg az Oracle Data Provider értéket a .NET számára.  
<a name="teradata">10</a> - Táblázatos 1200 modellek esetén, vagy *szolgáltatóadatforrásként* táblázatos 1400+ modellekben adja meg a Teradata Data Provider értéket a .NET számára.   
<a name="filesSP">11</a> - A helyszíni SharePoint-fájlok nem támogatottak.

Az Azure Analysis Services-kiszolgálóról helyszíni adatforrásokhoz való csatlakozáshoz [helyszíni átjáróra](analysis-services-gateway.md)van szükség. Átjáró használata esetén 64 bites szolgáltatókra van szükség. 

## <a name="understanding-providers"></a>A szolgáltatók ismertetése

Táblázatos 1400-as és magasabb modellprojektek létrehozásakor a Visual Studio programban alapértelmezés szerint nem kell adatszolgáltatót megadni, amikor adatforráshoz csatlakozik az **Adatok betöltése**használatával. A táblázatos 1400-as és magasabb modellek [Power Query-összekötők](/power-query/power-query-what-is-power-query) használatával kezelik az adatforrás és az Analysis Services közötti kapcsolatokat, adatlekérdezéseket és adategyesítéseket. Ezeket strukturált *structured* adatforrás-kapcsolatoknak is nevezik, mivel a kapcsolattulajdonság-beállítások be vannak állítva. Az örökölt adatforrásokat azonban engedélyezheti. Ha engedélyezve van, a **Táblaimportálás varázslóval** csatlakozhat bizonyos, a táblázatos 1200- as és az alacsonyabb modellekáltal örökölt ként vagy *szolgáltatói* *adatforrásként*támogatott adatforrásokhoz. Ha szolgáltatói adatforrásként van megadva, megadhat egy adott adatszolgáltatót és más speciális kapcsolattulajdonságokat. Például csatlakozhat egy helyszíni SQL Server Data Warehouse, vagy akár egy Azure SQL-adatbázis, mint egy örökölt adatforrás. Ezután kiválaszthatja az OLE DB drivert az SQL Server MSOLEDBSQL adatszolgáltatóhoz. Ebben az esetben az OLE DB adatszolgáltató kiválasztása jobb teljesítményt nyújthat a Power Query-összekötőn keresztül. 

Ha a Visual Studio Táblaimportálás varázslóját használja, a bármely adatforráshoz való csatlakozáshoz adatszolgáltatóra van szükség. Egy alapértelmezett adatszolgáltató van kiválasztva. Szükség esetén módosíthatja az adatszolgáltatót. A választott szolgáltató típusa függhet a teljesítménytől, függetlenül attól, hogy a modell a memóriában lévő tárhelyet vagy a DirectQueryt használja-e, és hogy melyik Analysis Services platformra telepíti a modellt.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Szolgáltatói adatforrások megadása táblázatos 1400-as és magasabb modellprojektekben

A szolgáltató adatforrásának engedélyezéséhez a Visual Studio ban kattintson **az Eszközök** > **beállításai** > **elemzési szolgáltatások táblázatos** > **adatimportálás parancsára,** és válassza az Örökölt **adatforrások engedélyezése**lehetőséget.

![Örökölt adatforrások engedélyezése](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Ha az örökölt adatforrások engedélyezve vannak, a **Táblázatos modellkezelőben**kattintson a jobb gombbal **az adatforrások** > **importálása adatforrásból (örökölt)** elemre.

![Örökölt adatforrások a táblázatos modellkezelőben](media/analysis-services-datasource/aas-import-legacy-datasources.png)

A táblázatos 1200-as modellprojektekhez hasonlóan a **Táblaimportálás varázslóval** is csatlakozhat egy adatforráshoz. A csatlakozás lapon kattintson a **Speciális gombra.** Adja meg az adatszolgáltatót és az egyéb kapcsolatbeállításokat a **Speciális tulajdonságok beállítása párbeszédpanelen.**

![Örökölt adatforrások – Speciális tulajdonságok](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Megszemélyesítés
Bizonyos esetekben szükség lehet egy másik megszemélyesítési fiók megadására. A megszemélyesítési fiók megadható a Visual Studio vagy az SSMS alkalmazásban.

Helyszíni adatforrások esetén:

* Ha SQL-hitelesítést használ, a megszemélyesítés nek szolgáltatásfióknak kell lennie.
* Ha Windows-hitelesítést használ, állítsa be a Windows felhasználói/jelszavát. Az SQL Server esetében a windowsos hitelesítés egy adott megszemélyesítési fiókkal csak a memórián belüli adatmodellek esetében támogatott.

Felhőalapú adatforrások esetén:

* Ha SQL-hitelesítést használ, a megszemélyesítés nek szolgáltatásfióknak kell lennie.

## <a name="oauth-credentials"></a>OAuth hitelesítő adatok

Az 1400-as és magasabb kompatibilitási szinten lévő táblázatos modellek esetében az Azure SQL Database, az Azure Synapse Analytics (SQL Data Warehouse), a Dynamics 365 és a SharePoint List támogatja az OAuth hitelesítő adatokat. Az Azure Analysis Services kezeli az OAuth adatforrások tokenfrissítését, hogy elkerülje a hosszú ideig futó frissítési műveletek időmegtorásait. Érvényes jogkivonatok létrehozásához állítsa be a hitelesítő adatokat az SSMS használatával.

A közvetlen lekérdezési módot az OAuth hitelesítő adatai nem támogatják.

## <a name="next-steps"></a>További lépések
[Helyszíni átjáró](analysis-services-gateway.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)   

