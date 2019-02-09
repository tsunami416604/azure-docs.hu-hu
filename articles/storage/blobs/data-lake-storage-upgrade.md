---
title: A big data-elemzési megoldások az Azure Data Lake Storage Gen1 frissítése az Azure Data Lake Storage Gen2-re
description: A megoldás használatához az Azure Data Lake Storage Gen2 frissítése
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: feb656fcdbb98dcab0a3ccf372a05b3f3dca91c4
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961380"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>A big data-elemzési megoldások az Azure Data Lake Storage Gen1 frissítése az Azure Data Lake Storage Gen2-re

A big data-elemzési megoldások az Azure Data Lake Storage Gen1 használja, ha ez az útmutató segít ezen megoldások használata az Azure Data Lake Storage Gen2 frissítése. Ez a dokumentum segítségével felmérheti a függőségeket, amely a megoldás a Data Lake Storage Gen1 rendelkezik. Ez az útmutató emellett bemutatja, hogyan tervezheti meg és hajtsa végre a frissítést.

Mi pedig segítünk gondoskodni keresztül a következő feladatokat:

:heavy_check_mark: Mérje fel az upgrade Readiness-szel

:heavy_check_mark: Frissítés tervezése

:heavy_check_mark: A frissítés végrehajtása

## <a name="assess-your-upgrade-readiness"></a>Mérje fel az upgrade Readiness-szel

A célunk az, hogy minden képességet, amely megtalálható a Data Lake Storage Gen1 lesz elérhető a Data Lake Storage Gen2. Hogyan ezen képességek érhetők el például SDK-t, a parancssori felület stb., a Data Lake Storage Gen1 és a Data Lake Storage Gen2 közötti eltérőek lehetnek. Alkalmazások és szolgáltatások, amelyek együttműködnek a Data Lake Storage Gen1 kell tudni a Data Lake Storage Gen2 hasonlóan működik. Végül funkciói nem lesznek elérhetők a Data Lake Storage Gen2 azonnal. Amint elérhetővé válnak, tájékoztatást nyújtunk azokat ebben a dokumentumban.

Ezek a következő szakaszok segítségével eldöntheti, hogy hogyan érdemes frissíteni a Data Lake Storage Gen2-re, és ha ehhez a legtöbb ésszerű.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 megoldás-összetevőket

Nagy valószínűséggel az elemzési megoldások vagy folyamatokat a Data Lake Storage Gen1 használja, ha nincsenek számos olyan további technológiákat alkalmazó szoftverbiztonsági, ha a teljes, végpontok közötti funkciók eléréséhez. Ez [cikk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) különféle komponenseinek használatát az adatfolyam fürtjét, feldolgozása és felhasználni az adatokat tartalmazó ismerteti.

Emellett nincsenek szerveznie összetevők üzembe helyezése, kezelése és ezek az összetevők figyeléséhez. A egyes összetevőinek működtetése a Data Lake Storage Gen1, őket leginkább megfelelő egy felület használatával. Ha tervezi az Data Lake Storage Gen2-re a megoldás frissítése, kell figyelembe venni a felületek, amelyek használhatók. Szüksége lesz a felügyeleti felületek és is adatillesztők frissítése, mivel mindegyik adapterhez különböző követelményekkel rendelkezik.

![Data Lake Storage megoldás-összetevőket](./media/data-lake-storage-upgrade/solution-components.png)

**1. ábra** , hogy a legtöbb elemzési megoldásokat látszódik újabb bemutatja a funkció összetevők.

**2. ábra** szemlélteti, hogyan összetevőket hajtják végre az egyes technológiák használatával.

A tárolása funkciókat **Figure1** Data Lake Storage Gen1 által biztosított (**2. ábra**). Vegye figyelembe, hogy hogyan az adatfolyam különböző összetevői használják a Data Lake Storage Gen1 REST API-k vagy a Java SDK használatával. Azt is vegye figyelembe, hogyan kommunikáljanak az általános funkciókat összetevők Data Lake Storage Gen1. A kiépítési összetevő használja az Azure erőforráscsoport-sablonok, mivel a figyelés összetevő, amely használja a Log Analytics már használja az operatív adatok, Data Lake Storage Gen1 érkező.

Egy megoldás használatával a Data Lake Storage Gen1 Data Lake Storage Gen2-re a frissítéshez kell másolnia az adatokat és a metaadatok, a data-folyamatok ismét hook, és ezt követően minden összetevője kell lennie képesek együttműködni a Data Lake Storage Gen2.

Az alábbi szakaszok nyújtanak információkat jobb döntéseket:

:heavy_check_mark: Platform képességei

:heavy_check_mark: Programozási felületek

:heavy_check_mark: Azure-ökoszisztéma

:heavy_check_mark: Partneri ökoszisztéma

:heavy_check_mark: Működési információ

Az egyes, fogja meg tudja határozni a "kell-haves" a frissítéshez. Biztos, hogy a lehetőségek érhetők el, vagy biztos lehet, hogy nincsenek-e elfogadható megkerülő megoldások helyen, után folytassa a [verziófrissítésének tervezése](#planning-for-an-upgrade) című szakaszában talál.

### <a name="platform-capabilities"></a>Platform képességei

Ez a szakasz ismerteti, amelyek jelenleg a Data Lake Storage Gen2 Data Lake Storage Gen1 platform képességeit.

| | 1. generációs Data Lake Storage | Data Lake Storage Gen2 - cél | Data Lake Storage Gen2 - rendelkezésre állási állapotát  |
|-----------------------|-----------------|----------------------|----------------------------------|
| Az adatok elrendezése| Támogatja az adatokon a fájlok és mappák | Támogatja a tárolt objektumok/blobok, valamint a mappák és fájlok - adatok [hivatkozás](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Támogatja az adatokon a mappák és fájlok – *már elérhető* <br><br> Támogatja a tárolt objektumok/blobok – és adatokat *még nem érhető el* |
| Névtér| Hierarchikus | Hierarchikus |  *Már elérhető*  |
| API  | HTTPS-kapcsolaton keresztül a REST API | REST API HTTP/HTTPS-kapcsolaton keresztül| *Már elérhető* |
| Kiszolgálóoldali API| [WebHDFS-kompatibilis REST API-val](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Az Azure Blob Service REST API-val [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *elérhető most* <br><br> Az Azure Blob Service REST API – *még nem érhető el*       |
| Hadoop-fájl rendszer ügyfél | Igen ([az Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Igen ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Már elérhető*  |  | [Megosztott kulcs](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key), [az Azure Active Directory-identitásokkal](https://docs.microsoft.com/azure/active-directory/active-directory-authentication-scenarios), [közös hozzáférésű Jogosultságkód (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) | *Már elérhető*  |
| Adatműveletek – engedélyezés  | Fájl- és szintű POSIX hozzáférés-vezérlési listák (ACL-ek) az Azure Active Directory-identitások alapján  | Fájl- és szintű POSIX hozzáférés-vezérlési listák (ACL) alapján az Azure Active Directory-identitásokkal [megosztási kulcs](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) fiók szintű hitelesítéshez szerepköralapú hozzáférés-vezérlés ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) való hozzáférés tárolók | *Már elérhető* |
| Adatműveletek – naplók  | Igen | Támogatási jegy integrációs Azure Monitoring segítségével adott időtartamának naplók egyszeri kérelmek | Egyszeri kérelmek naplókhoz megadott idő a támogatási jegy – *már elérhető*<br><br> Az Azure Monitoring-integráció – *még nem érhető el* |
| Az inaktív adatok titkosítása | Szolgáltatás által kezelt kulcsokkal és az ügyfél által felügyelt kulcsok az Azure KeyVault transzparens, kiszolgálóoldali | Transzparens, kiszolgálóoldali szolgáltatás által kezelt kulcsokkal és ügyfélkulcsokkal által kezelt kulcsok az Azure-Kulcstartóban | Szolgáltatás által kezelt kulcsok – *már elérhető*<br><br> Felhasználó által kezelt kulcsok – *már elérhető*  |
| Virtual Network (VNet) támogatása  | [Virtuális hálózati integráció használatával](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Az Azure Storage Service-végpont használatával](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Már elérhető* | Felügyeleti műveletek (például fiók létrehozása) | [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) fiókok kezelése az Azure által biztosított (RBAC) | [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) fiókok kezelése az Azure által biztosított (RBAC) | *Már elérhető*| Fejlesztői SDK-k | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, a Ruby, PHP, Go, Android, iOS| *Még nem érhető el* | Optimalizált teljesítménygyűjtési párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebességű és iops-t. | Optimalizált teljesítménygyűjtési párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebességű és iops-t. | *Már elérhető* |
| Blobméretének korlátjai | A fiókok méretének, a fájlok méretét vagy a fájlok száma korlátlan | A fiókok méretének vagy fájlok száma korlátlan. A fájl legfeljebb 5 TB-os méret. | *Már elérhető*|
| Georedundancia| Helyileg redundáns (LRS) | Helyileg redundáns (LRS) zónaredundáns (ZRS) (GRS) írásvédett globálisan redundáns globálisan redundáns (RA-GRS) lásd [Itt](https://docs.microsoft.com/azure/storage/common/storage-redundancy) további információ| *Már elérhető* |
| Régiónkénti rendelkezésre állás | Lásd: [Itt](https://azure.microsoft.com/regions/) | Az összes [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Már elérhető*                                                                                                                           |
| Ár                                       | Lásd: [díjszabása](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Lásd: [díjszabása](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA szerinti rendelkezésre állás                            | [Tekintse meg a szolgáltatásiszint-szerződés](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Tekintse meg a szolgáltatásiszint-szerződés](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Már elérhető*                                                                                                                           |
| Adatkezelés                             | A fájlok lejáratának                                                                                                                                        | Életciklus-szabályzatok                                                                                                                                                                                                                                                                                                                                                                                                          | *Még nem érhető el*                                                                                                                       |

### <a name="programming-interfaces"></a>Programozási felületek

Ez a táblázat ismerteti, amely API-t állítja be, amely az egyéni alkalmazások érhetők el. Ahhoz, hogy egy kicsit világosabb dolog, hogy már elválasztott ezen API-készletek 2 típusokra: felügyeleti API-k és filesystem API-k.

Felügyeleti API-k segítségével fiókokat szeretne kezelni, miközben filesystem API-k segítségével, hogy a fájlokat és mappákat a művelethez.

|  API-készlet                           |  1. generációs Data Lake Storage                                                                                                                                                                                                                                                                                                   | Megosztott kulcsos hitelesítés a Data Lake Storage Gen2 - rendelkezésre állása | Az OAuth-hitelesítés a Data Lake Storage Gen2 - elérhetősége                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK - kezelés                  | [Hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Nem támogatott*                                                      | *Rendelkezésre álló most -* [hivatkozás](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK-t – fájlrendszer                  | [Hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| A Java SDK - kezelés                  | [Hivatkozás](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nem támogatott*                                                      | *Rendelkezésre álló most –* [hivatkozás](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| A Java SDK – fájlrendszer                  | [Hivatkozás](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| NODE.js - kezelés                   | [Hivatkozás](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Nem támogatott                                                      | *Rendelkezésre álló most -* [hivatkozás](http://azure.github.io/azure-storage-node/)                                                                                            |
| NODE.js - fájlrendszer                   | [Hivatkozás](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| Python - felügyelet                    | [Hivatkozás](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Nem támogatott*                                                      | *Rendelkezésre álló most -* [hivatkozás](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python - fájlrendszer                    | [Hivatkozás](http://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| REST API - kezelés                  | [Hivatkozás](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nem támogatott*                                                      | *-Már elérhető*                                                                                                                                               |
| REST API - fájlrendszer                  | [Hivatkozás](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Már elérhető*                                                    | *Rendelkezésre álló most -* [hivatkozás](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell – kezelés és fájlrendszer | [Hivatkozás](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Kezelés – nem támogatott a fájlrendszer - *még nem érhető el*        | Kezelés – *elérhető most -* [hivatkozás](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Fájlrendszer - *még nem érhető el* |
| Parancssori felület – felügyelet                       | [Hivatkozás](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nem támogatott*                                                      | *Rendelkezésre álló most -* [hivatkozás](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI - fájlrendszer                       | [Hivatkozás](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| Az Azure Resource Manager-sablonok - kezelés             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Nem támogatott*                                                      | *Rendelkezésre álló most -* [hivatkozás](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure-ökoszisztéma

Data Lake Storage Gen1 használata esetén a végpontok közötti folyamatok számos Microsoft-szolgáltatások és termékek is használhatja. Ezek a szolgáltatások és termékek együttműködve Data Lake Storage Gen1 közvetlenül vagy közvetve. Ez a táblázat a szolgáltatások listáját jeleníti meg, hogy azt dolgozhat a Data Lake Storage Gen1 úgy módosítottuk, és látható, melyeket jelenleg kompatibilis a Data Lake Storage Gen2.

| **Terület**             | **Data Lake Storage Gen1 rendelkezésre állása**                                                                                                                                    | **Megosztott kulcsos hitelesítés a Data Lake Storage Gen2 – rendelkezésre állás**                                                                                                           | **Rendelkezésre állás, a Data Lake Storage Gen2 – OAuth-val**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Elemzési keretrendszer  | [Az Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Már elérhető*                                                                                                                                                              | *Már elérhető*                                                                                                                                 |
|                      | [HDInsight ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *mostantól elérhető* HDInsight 4.0 - *még nem érhető el*      | A HDInsight 3.6-os ESP – *elérhető most* <br><br>  HDInsight 4.0 ESP - *még nem érhető el*                                                                 |
|                      | Databricks futtatókörnyezete 3.1-es vagy újabb verzió                                                                                                                                               | [Databricks futtatókörnyezete 5.1-es és újabb](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– most már elérhető* | [Databricks futtatókörnyezete 5.1-es és újabb](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *elérhető most*                                                                                              |
|                      | [SQL Data Warehouse ](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nem támogatott*                                                                                                                                                              | *Rendelkezésre álló most* [hivatkozás](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Adatintegráció     | [Data Factory ](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [2. verzió](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *mostantól elérhető* 1. verzió – *nem támogatott*                               | [2. verzió](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *elérhető most* <br><br> 1 –. verziójának *nem támogatott*  |
|                      | [Az AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nem támogatott*                                                                                                                                                              | *Nem támogatott*                                                                                                                                 |
|                      | [SQL Server Integration Services ](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Logic Apps ](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
| IoT                  | [Az Event Hubs – rögzítése ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Stream Analytics ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
| Használat          | [A Power bi Desktopban  ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Excel ](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Analysis Services ](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
| Termelékenység         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nem támogatott*                                                                                                                                                              | Fiókkezelés *– most már elérhető* <br><br>Adatműveletek *–**még nem érhető el*                                                                   |
|                      | [A Data Lake Tools for Visual Studio ](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Azure Storage Explorer ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Már elérhető*                                                                                                                                                              | *Már elérhető*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partneri ökoszisztéma

Ez a táblázat a harmadik féltől származó szolgáltatásokkal és a módosított dolgozhat a Data Lake Storage Gen1 termékek listáját jeleníti meg. Azt is ismerteti, melyek jelenleg kompatibilisek, a Data Lake Storage Gen2.

| Terület            | Partner  | Termék vagy szolgáltatás  | Data Lake Storage Gen1 rendelkezésre állása                                                                                                                                               | Megosztott kulcsos hitelesítés a Data Lake Storage Gen2 – rendelkezésre állás                                                   | Rendelkezésre állás, a Data Lake Storage Gen2 – Oauth-val                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Elemzési keretrendszer | Cloudera     | A CDH                  | [Hivatkozás](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Még nem érhető el*                                                                                                  | [Hivatkozás](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Hivatkozás](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nem támogatott*                                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Hivatkozás](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Qubole       |                      | [Hivatkozás](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
| ETL                 | StreamSets   |                      | [Hivatkozás](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Informatica  |                      | [Hivatkozás](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Az Attunity     |                      | [Hivatkozás](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Alteryx      |                      | [Hivatkozás](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | ImanisData   |                      | [Hivatkozás](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | A WANdisco     |                      | [Hivatkozás](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Hivatkozás](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Hivatkozás](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Működési információ

Data Lake Storage Gen1 leküldéses más szolgáltatásokkal, így a segítségével tesztelhetők a folyamatok adott információk és adatok. Ez a táblázat megfelelő támogatási rendelkezésre állását a Data Lake Storage Gen2.

| Adattípus                                                                                       | Data Lake Storage Gen1 rendelkezésre állása                                                                 | Data Lake Storage Gen2 rendelkezésre állása                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Számlázási adatok - mérőszámok kereskedelmi küldött csapat a számlázási és majd szeretné elérhetővé tenni az ügyfelek számára  | *Már elérhető*                                                                                             | *Már elérhető*                                                                                                                           |
| Tevékenységnaplók                                                                                          | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Egyszeri kérelmek naplókhoz megadott idő a támogatási jegy – *mostantól elérhető* Azure Monitoring-integráció - *még nem érhető el* |
| Diagnosztikai naplók                                                                                        | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Egyszeri kérelmek naplókhoz megadott idő a támogatási jegy – *mostantól elérhető* Azure Monitoring-integráció - *még nem érhető el* |
| Mérőszámok                                                                                                | *Nem támogatott*                                                                                               | *Rendelkezésre álló most -* [hivatkozás](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Frissítés tervezése

Ez a szakasz azt feltételezi, hogy áttekintette a [mérje fel az upgrade Readiness-szel](#assess-your-upgrade-readiness) című szakaszában talál, és a függőségek teljesül. Ha továbbra sem érhetők el a Data Lake Storage Gen2-képességeihez, folytassa csak akkor, ha ismeri a megfelelő megoldások. A következő szakaszok útmutatást nyújtanak a megtervezheti, hogyan a frissítésre, a folyamatok. A tényleges frissítés leírását a [a frissítés](#performing-the-upgrade) című szakaszában talál.

### <a name="upgrade-strategy"></a>A frissítési stratégia

A frissítés a legfontosabb részét el kell döntenie a stratégia. Ezt a döntést az elérhető lehetőségek határozza meg.

Ez a táblázat néhány jól ismert stratégiát, adatbázisok, Hadoop-fürtök és egyéb áttelepítése használt. Azt fogjuk hasonló stratégiákat elfogadják ügyfeleinket, és igazodjon a környezet.

| Stratégia                   | Szakemberek számára                                                                                  | Hátrányok                                                           | Mikor érdemes használni?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift-and-shift                 | A legegyszerűbb.                                                                                 | Állásidő szükséges adatokat, a mozgó feladatok, és a mozgó bejövő és kimenő keresztül másolja a                                             | Egyszerűbb megoldások Ha nem állnak több megoldás az azonos Gen1 fér hozzá a fiókját, és ezért együtt lehessen áthelyezni a gyors szabályozott módon.                                                  |
| Másolás egyszer-és-másolási növekményes | Csökkenthető a leállások közben a forrásrendszerben még mindig aktív példány elvégzésével a háttérben. | Állásidő szükséges bemenő és kimenő áthelyezése.                   | Át kell másolni az adatok mérete nagy, és az élettartam-and-shift társított állásidő nem fogadható el. Tesztelés lehet szükség a célrendszeren váltás előtt jelentős termelési adatokkal. |
| Párhuzamos elfogadása              | Alkalmazások áttelepítése a saját belátása szerint legalább állásidő lehetővé teszi az időt.           | 2 – kétutas szinkronizálás óta legtöbb összetettebb van szükség a két rendszer között. | A növekményes módon kérdezzék átkerül összetett forgatókönyvek, ahol Data Lake Storage Gen1 épülő alkalmazások egyszerre lehet átállás és kell lennie.                                                      |

Az alábbiakban további részleteket a lépéseket részt vevő az egyes stratégiák. A lépések listája, mire lenne az a következő összetevők kapnak szerepet a frissítés. Ez magában foglalja a forrás a rendszer általános, átfogó célrendszer, bejövő források a forrásrendszerben, a kimenő célok forrás rendszer, és a forrásrendszerben futó feladatok.

Ezeket a lépéseket nem jelentenek a előíró lehet. Ezek be, hogyan tudjuk tanulmánnyal egyes stratégiák kapcsolatban a keretrendszer célja. Biztosítunk esettanulmányok az egyes stratégiák, ahogy láthatjuk azokat végrehajtása alatt.

#### <a name="lift-and-shift"></a>Lift-and-shift

1. A forrásrendszerben – a bejövő forgalom forrásokból, a feladatok, a kimenő célok felfüggesztése.
2. Adatok másolása a forrásrendszerben a célrendszeren.
3. Minden bejövő forgalom forrás, mutasson a célrendszeren. A kimenő forgalom cél pont a célrendszeren.
4. Helyezze át, módosítsa, minden a feladatok futtatásához a célrendszerbe.
5. Kapcsolja ki a forrásrendszerben.

#### <a name="copy-once-and-copy-incremental"></a>Másolás – Miután és a növekményes másolási

1. Másolja át az adatokat a forrás rendszerből a célrendszeren.
2. Másolja át a növekményes adatok a forrásrendszerből a célrendszerbe rendszeres időközönként.
3. A kimenő forgalom cél pont a célrendszeren.
4. Helyezze át, módosítása, az összes feladat futtatása a célrendszeren.
5. Mutasson a bejövő forgalom források növekményes alapján a felhasználók kényelme érdekében a célrendszeren.
6. Miután az összes bejövő forrás mutat a célrendszeren.
    1. Kapcsolja ki a növekményes másolását.
    2. Kapcsolja ki a forrásrendszerben.

#### <a name="parallel-adoption"></a>Párhuzamos elfogadása

1. Állítsa be a célrendszerbe.
2. Állítsa be a kétirányú replikációt a forrás és cél között.
3. Bejövő forgalom adatforrások növekményes mutat a célrendszeren.
4. Helyezze át, módosítsa, növekményes feladatok futtatása a célrendszerbe.
5. Pont kimenő célhelyre Növekményesen a célrendszeren.
6. Az eredeti beérkezését követően forrás, a feladatok és a kimenő forgalom cél dolgozik a célrendszeren, kapcsolja ki a forrásrendszerben.

### <a name="data-upgrade"></a>Adatok frissítése

Az általános stratégia, amellyel a frissítés végrehajtása (ismertetett a [frissítési stratégia](#upgrade-strategy) című részében olvashat), határozza meg, az eszközöket, amelyek az adatok frissítése is használhat. Az alábbiakban az eszközök aktuális információk alapulnak, és javaslatok. 

#### <a name="tools-guidance"></a>Útmutató eszközök

| Stratégia                       | Eszközök                                                                                                             | Szakemberek számára                                                                                                                             | Megfontolandó szempontok                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift-and-shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Felügyelt felhőbeli szolgáltatás                                                                                                                | Csak másolja át adatokat. Hozzáférés-vezérlési listák keresztül jelenleg nem lehet másolni.                                                                                                                                                                                                                                                                      |
|                                    | [A Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Az ismert Hadoop által biztosított eszköz például az ACL-ek engedélyek lehet másolni az ezzel az eszközzel                                                   | Szükséges egy fürtöt, amely egyszerre Data Lake Storage Gen1 és Gen2 is csatlakozhatnak.                                                                                                                                                                                   |
| **Másolás egyszer-és-másolási növekményes** | Azure Data Factory                                                                                                    | Felügyelt felhőbeli szolgáltatás                                                                                                                | Támogatja a növekményes másolása az ADF-ben, adatokat kell egy idősorozat-módon rendszerezhetők. Növekményes másolatok közötti legrövidebb időköz [15 perc](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). Rövidebb időközönként, az ADF használatával nem fognak működni. Hozzáférés-vezérlési listák keresztül jelenleg nem lehet másolni. |
| **Párhuzamos elfogadása**              | [A WANdisco](http://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Támogatási konzisztens replikációs Ha egy tiszta Hadoop környezet használatával csatlakozik az Azure Data Lake Storage támogatja a kétirányú replikációt | Ha nem használ egy tiszta Hadoop-környezetben, lehet késleltetés a replikációs.                                                                                                                                                                                                                                                  |

Vegye figyelembe, hogy nincsenek-e a Data Lake Storage Gen1 Data Lake Storage Gen2 frissítésre képes kezelni a fenti adatok és metaadatok másolása eszközök bevonása nélkül harmadik felek (például: [Cloudera](http://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Adatok áttelepítése, valamint a számítási feladatok migrálását végző egy "egyablakos" élményt nyújtanak. Előfordulhat, hogy rendelkezik olyan eszközökkel, amelyek ökoszisztémát kívül a sávon kívüli frissítés végrehajtásához.

#### <a name="considerations"></a>Megfontolandó szempontok

* Manuálisan hozza létre a Data Lake Storage Gen2 fiókot először, mivel az aktuális útmutató nem tartalmaz minden Gen1 fiókadatai alapján automatikus Gen2 fiók folyamat bármely része a frissítés megkezdése előtt kell. Győződjön meg arról, hogy összehasonlítja a fiókok létrehozásának folyamatok [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) és [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 támogat fájlok akár 5 TB-os méret. A Data Lake Storage Gen2-re frissítéséhez szüksége lehet a fájlokat a Data Lake Storage Gen1 átméretezése, hogy azok 5 TB-nál kisebb.

* Ha egy eszköz, amely nem másolja át a hozzáférés-vezérlési listák használhatja, vagy másolja át a hozzáférés-vezérlési listák nem kívánja, majd szüksége a hozzáférés-vezérlési listák beállítása a cél a megfelelő legfelső szintű manuálisan. Megteheti a Storage Explorer használatával. Győződjön meg arról, hogy ezen ACL-ek az alapértelmezett ACL-EK, hogy a fájlokat és mappákat, másolja át őket öröklik.

* A Data Lake Storage Gen1 is megadhatja a hozzáférés-vezérlési listák a legmagasabb szintű jelenleg a fiók gyökérmappájában. A Data Lake Storage Gen2 azonban a legmagasabb szintű is megadhatja a hozzáférés-vezérlési listák, a fájlrendszer, nem a teljes fiók a gyökérmappában. Ezért ha azt szeretné, a fiók szintjén beállított alapértelmezett ACL-EK, a Data Lake Storage Gen2-fiókjában található összes fájl rendszer jeggyel ismétlődő kell.

* Fájl vonatkozó elnevezési korlátozás különböznek a két tárolási rendszerek között. Ezek a különbségek vannak különösen érintő bemásolja a Data Lake Storage Gen2 Data Lake Storage Gen1 mivel ez utóbbi van több korlátozott korlátozások.

### <a name="application-upgrade"></a>Alkalmazás frissítése

Alkalmazásokat hozhat létre a Data Lake Storage Gen1 vagy a Data Lake Storage Gen2 van szüksége, amikor kell választania a megfelelő alkalmazásprogramozási felületet. API-k az adott csatolón hívásakor kell a megfelelő URI-t és a megfelelő hitelesítő adatokat. Leképezése az felsorolt három elemet, az API URI-t, és hogyan a hitelesítő adatok vannak megadva, a másik Data Lake Storage Gen1 és a Data Lake Storage Gen2.So között az alkalmazásfrissítés részeként kell ezen három szerkezeteket megfelelően leképezni.

#### <a name="uri-changes"></a>URI-módosítások

A fő feladat lefordítani az adl: / / URI-t a meglévő számítási feladatokat egy abfss be volt használatban: / / URI-t.

Schéma URI Pro Data Lake Storage Gen1 említett [Itt](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) részletei, de Összefoglalva elmondható, a rendszer *adl://mydatalakestore.azuredatalakestore.net/\<fájl_elérési_útja\>.*

A Data Lake Storage Gen2 fájlelérés URI-séma kifejtett [Itt](https://docs.microsoft.com/azure/storage/data-lake-storage/use-hdi-cluster) részletei, de Összefoglalva elmondható, a rendszer *abfss: / /\<FILE_SYSTEM_NAME\> \@ \< ACCOUNT_NAME\>.dfs.core.widows.net/\<elérési út\>.*

Haladjon végig a meglévő alkalmazásokkal, és győződjön meg arról, hogy módosította az URI-k megfelelően, Data Lake Storage Gen2-re mutasson kell megjelennek. Emellett szüksége adja hozzá a megfelelő hitelesítő adatokat. Végül hogyan az eredeti telepítéséhez, és cserélje le az új alkalmazást kell szorosan össze kell hangolni a teljes frissítési stratégia.

#### <a name="custom-applications"></a>Egyéni alkalmazások

Az alkalmazás használja a Data Lake Storage Gen1 felületen, attól függően kell azt mindig a Data Lake Storage Gen2-re módosítja.

##### <a name="rest-apis"></a>REST API-k

Az alkalmazás a Data Lake Storage REST API-kat használja, ha módosítania kell az alkalmazás használhatja a Data Lake Storage Gen2 REST API-k. Hivatkozások szerepelnek *programozási felületek* szakaszban.

##### <a name="sdks"></a>SDK-k

A hívott ki a a *mérje fel az upgrade Readiness-szel* szakaszban SDK-k nem érhető el. Ha azt szeretné, porton keresztül az alkalmazások a Data Lake Storage Gen2-re, javasoljuk lesz, várja meg a támogatott SDK-k elérhető legyen.

##### <a name="powershell"></a>PowerShell

Mivel feltüntettük az a felmérési az upgrade Readiness-szel szakaszban, PowerShell-támogatása jelenleg nem érhető az adatsík az.

Felügyeleti sík parancsmagok sikerült cserélje a Data Lake Storage Gen2 megfelelő rétegében. Hivatkozások szerepelnek *programozási felületek* szakaszban.

##### <a name="cli"></a>parancssori felület

A hívott ki a *mérje fel az upgrade Readiness-szel* szakaszban parancssori felületi támogatása jelenleg nem áll rendelkezésre az adatsík az.

Felügyeleti sík parancsok sikerült cserélje a Data Lake Storage Gen2 megfelelő rétegében. Hivatkozások szerepelnek *programozási felületek* szakaszban.

### <a name="analytics-frameworks-upgrade"></a>Elemzési keretrendszerek frissítése

Ha az alkalmazás metaadatai információkat a tárolóban, például explicit fájl és mappa elérési útjának hoz létre, szüksége további műveletek elvégzésére, az áruházbeli adatok és metaadatok a frissítés után. Ez különösen igaz az elemzési keretrendszerek, például Azure HDInsight, általában létre eseménykatalógus-adatok a tároló adatainak Databricks stb.

Elemzési keretrendszerek adatok és metaadatok tárolódnak a távoli tárolókat, mint a Data Lake Storage Gen1 és Gen2 működik. Így elméletben a motorok lehet, hogy a rövid élettartamú, és csak akkor, ha a feladatok futtatásához a tárolt adatok kell leállását.

Azonban a teljesítmény optimalizálása az elemzési keretrendszerek előfordulhat, hogy a fájlok és mappák, a távoli tárolja mutató explicit hivatkozásokat létrehozása, és majd hozzon létre egy gyorsítótárat is tartja őket. Kell URI-ját a távoli adatok módosítása, például egy fürtöt, amely volt adattárolás a Data Lake Storage Gen1 korábban, és most már, aki a Data Lake Storage Gen2 tárolja az URI-t ugyanazon másolt tartalom eltérő lesz. Így az adatok és metaadatok a gyorsítótárak ezek motorok frissítése után is kell frissítése vagy újbóli inicializálása

A tervezési folyamat részeként a következőket kell tennie azonosítják az alkalmazást, és döntse el, hogyan lehet inicializálásakor a Data Lake Storage Gen2 most tárolt adatok metaadatot tartalmaz információkat. Az alábbi, az útmutató segítséget nyújt a frissítési lépéseket általánosan elfogadott elemzési keretrendszerek.

#### <a name="azure-databricks"></a>Azure Databricks

Attól függően, a frissítési stratégia választja a lépések eltérőek lehetnek. Az aktuális szakasz azt feltételezi, hogy kiválasztotta-e a "Élettartam-and-shift" stratégia. Emellett a meglévő Databricks-munkaterület, amely egy Data Lake Storage Gen1-fiókban lévő adatok eléréséhez használt várhatóan az adatok, Data Lake Storage Gen2 fiók át van másolva.

Először is győződjön meg arról, hogy létrehozta a Gen2-fiókot, és ezután átmásolja keresztül adatok és meta Gen1 Gen2-re megfelelő eszköz használatával. Ezeket az eszközöket hívjuk [adatfrissítési](#data-upgrade) című szakaszában talál.

Ezt követően [frissítése](https://docs.azuredatabricks.net/user-guide/clusters/index.html) a meglévő Databricks-fürt használatához a Databricks futtatókörnyezete 5.1-es vagy újabb, amelynek támogatnia kell a Data Lake Storage Gen2.

A lépések azt követően alapulnak hogyan a meglévő Databricks-munkaterülethez a Data Lake Storage Gen1 fiókban adatokhoz fér hozzá. Ezt megteheti vagy a hívó adl: / / URI-k [közvetlenül](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) notebookok vagy keresztül [akkor csatlakozási](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Ha a közvetlenül a notebookok azáltal, hogy a teljes adl hozzáférés: / / URI-k, kell haladjon végig az egyes jegyzetfüzet és elérni a megfelelő Data Lake Storage Gen2 URI úgy módosítaná a konfigurációt.

Jövőben kell újból konfigurálnia kell, hogy a Data Lake Storage Gen2 fiók mutasson. Nincsenek további módosítások szükségesek, és a notebookok dolgozhat, mint korábban képesnek kell lennie.

Ha a más frissítési stratégiák bármelyike használ, létrehozhat egy változata, a fenti lépéseket az igényeknek.

### <a name="azure-ecosystem-upgrade"></a>Azure-ökoszisztéma frissítése

Egyes eszközök és szolgáltatások feltüntettük az [Azure-ökoszisztéma](#azure-ecosystem) szakaszában kell konfigurálni, hogy a Data Lake Storage Gen2.

Először győződjön meg arról, hogy nincs integráció a Data Lake Storage Gen2.

Ezután fent feltüntettük az elemeket (például: URI-t és a hitelesítő adatok) kell módosítani. A meglévő példányt, amely együttműködik a Data Lake Storage Gen1, módosításával, vagy létrehozhat egy új példányt, amely a Data Lake Storage Gen2 működne.

### <a name="partner-ecosystem-upgrade"></a>Partneri ökoszisztéma frissítése

Együttműködve biztosít az összetevő és az eszközök való gondoskodás a partner a Data Lake Storage Gen2 is működhetnek. 

## <a name="performing-the-upgrade"></a>A frissítés

### <a name="pre-upgrade"></a>Frissítés előtti

Ennek részeként, akkor elvégezte a *mérje fel az upgrade Readiness-szel* szakaszt, és a [verziófrissítésének tervezése](#planning-for-an-upgrade) szakaszban ez az útmutató összes szükséges információ beérkezett, és hogy az igényeknek lenne csomagot létrehozni. Valószínűleg rendelkezni fog egy tesztelési feladat ebben a fázisban.

### <a name="in-upgrade"></a>A frissítés

A stratégia függően válassza ki, és kiküszöböli, hogy a megoldás, az ebben a fázisban lehet egy rövid vagy kiterjesztett egy ahol több számítási feladat Növekményesen átléptetjük a Data Lake Storage Gen2-re vár. Ez a frissítés a kritikus fontosságú része lesz.

### <a name="post-upgrade"></a>Frissítés után

Miután elkészült, az átmenet művelettel, az utolsó lépéseit alapos ellenőrzési magában foglalja. Ez lenne közé tartozik, de nem korlátozódnak adatok ellenőrzése a rendszer átmásolt keresztül megbízhatóan, ellenőrzi az ACL-ek megfelelően van beállítva, e2e ellenőrzése folyamatok megfelelő működésének ellenőrzéséhez stb. Miután az ellenőrzés befejeződött, ezután kapcsolja ki a régi folyamatok, a Data Lake Storage Gen1 forrás fiókok törlése és lépjen a Data Lake Storage Gen2-alapú megoldások teljes sebességgel.

## <a name="conclusion"></a>Összegzés

A jelen dokumentumban leírt útmutatást kell segítették megoldását szeretné használni a Data Lake Storage Gen2 frissítését. 

Ha további kérdése van, vagy visszajelzést szeretne küldeni, az alábbi megjegyzéseket, vagy küldjön visszajegyzést a [Azure visszajelzési fórumon](https://feedback.azure.com/forums/327234-data-lake).
