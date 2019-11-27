---
title: Azure Data Lake Storage frissítése a Gen1-ből a Gen2-be
description: Frissítse Azure Data Lake Storaget a Gen1-ből a Gen2-be.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/19/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 41074561b4805fef1889bd889b625e1a59d57d91
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327869"
---
# <a name="upgrade-azure-data-lake-storage-from-gen1-to-gen2"></a>Azure Data Lake Storage frissítése a Gen1-ből a Gen2-be

Ha Azure Data Lake Storage Gen1t használ a big data Analytics-megoldásokban, ez az útmutató segítséget nyújt a megoldásoknak a Azure Data Lake Storage Gen2 használatára való frissítéséhez. Ez a dokumentum segítségével felmérheti a függőségeket, amely a megoldás a Data Lake Storage Gen1 rendelkezik. Ez az útmutató emellett bemutatja, hogyan tervezheti meg és hajtsa végre a frissítést.

Mi pedig segítünk gondoskodni keresztül a következő feladatokat:

: heavy_check_mark: mérje fel az upgrade Readiness-szel

: heavy_check_mark: verziófrissítésének tervezése

: heavy_check_mark: a frissítés végrehajtása

## <a name="assess-your-upgrade-readiness"></a>Mérje fel az upgrade Readiness-szel

A célunk az, hogy minden képességet, amely megtalálható a Data Lake Storage Gen1 lesz elérhető a Data Lake Storage Gen2. Hogyan ezen képességek érhetők el például SDK-t, a parancssori felület stb., a Data Lake Storage Gen1 és a Data Lake Storage Gen2 közötti eltérőek lehetnek. Alkalmazások és szolgáltatások, amelyek együttműködnek a Data Lake Storage Gen1 kell tudni a Data Lake Storage Gen2 hasonlóan működik. Végül funkciói nem lesznek elérhetők a Data Lake Storage Gen2 azonnal. Amint elérhetővé válnak, tájékoztatást nyújtunk azokat ebben a dokumentumban.

Ezek a következő szakaszok segítségével eldöntheti, hogy hogyan érdemes frissíteni a Data Lake Storage Gen2-re, és ha ehhez a legtöbb ésszerű.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 megoldás-összetevőket

Nagy valószínűséggel az elemzési megoldások vagy folyamatokat a Data Lake Storage Gen1 használja, ha nincsenek számos olyan további technológiákat alkalmazó szoftverbiztonsági, ha a teljes, végpontok közötti funkciók eléréséhez. Ez a [cikk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) az adatforgalom különböző összetevőit ismerteti, amelyek tartalmazzák az adatfeldolgozást,-feldolgozást és-felhasználást.

Emellett nincsenek szerveznie összetevők üzembe helyezése, kezelése és ezek az összetevők figyeléséhez. A egyes összetevőinek működtetése a Data Lake Storage Gen1, őket leginkább megfelelő egy felület használatával. Ha tervezi az Data Lake Storage Gen2-re a megoldás frissítése, kell figyelembe venni a felületek, amelyek használhatók. Szüksége lesz a felügyeleti felületek és is adatillesztők frissítése, mivel mindegyik adapterhez különböző követelményekkel rendelkezik.

![Data Lake Storage megoldás-összetevőket](./media/data-lake-storage-upgrade/solution-components.png)

Az **1. ábrán** a legtöbb analitikai megoldásban megjelenő funkció-összetevők láthatók.

A **2. ábrán** egy példa látható arra, hogy ezek az összetevők hogyan lesznek implementálva bizonyos technológiák használatával.

A **ábra** tárolási funkcióját a Data Lake Storage Gen1 biztosítja (**2. ábra**). Vegye figyelembe, hogy hogyan az adatfolyam különböző összetevői használják a Data Lake Storage Gen1 REST API-k vagy a Java SDK használatával. Azt is vegye figyelembe, hogyan kommunikáljanak az általános funkciókat összetevők Data Lake Storage Gen1. A kiépítési összetevő az Azure Resource templates szolgáltatást használja, míg a Azure Monitor naplókat használó figyelési összetevő a Data Lake Storage Gen1ból származó operatív adatok használatát használja.

Egy megoldás használatával a Data Lake Storage Gen1 Data Lake Storage Gen2-re a frissítéshez kell másolnia az adatokat és a metaadatok, a data-folyamatok ismét hook, és ezt követően minden összetevője kell lennie képesek együttműködni a Data Lake Storage Gen2.

Az alábbi szakaszok nyújtanak információkat jobb döntéseket:

: heavy_check_mark: Platform képességei

: heavy_check_mark: programozási felületek

: heavy_check_mark: Azure-ökoszisztéma

: heavy_check_mark: partneri ökoszisztéma

: heavy_check_mark: működési információ

Az egyes, fogja meg tudja határozni a "kell-haves" a frissítéshez. Miután biztos abban, hogy elérhetők a képességek, vagy ha biztos abban, hogy ésszerű megkerülő megoldásokat tartalmaz, folytassa az útmutató [verziófrissítésének megtervezésével](#planning-for-an-upgrade) foglalkozó szakaszát.

### <a name="platform-capabilities"></a>Platform képességei

Ez a szakasz ismerteti, amelyek jelenleg a Data Lake Storage Gen2 Data Lake Storage Gen1 platform képességeit.

| | 1\. generációs Data Lake Storage | Data Lake Storage Gen2 - cél | Data Lake Storage Gen2 - rendelkezésre állási állapotát  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Az adatok elrendezése| Támogatja az adatokon a fájlok és mappák | Az objektumok/Blobok, valamint a mappák és fájlok [hivatkozással](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) tárolt adattárolók támogatása | Támogatja a mappákban és fájlban tárolt, *jelenleg elérhetővé* tett fájlokat <br><br> Az objektumként/blobként tárolt, *még nem elérhető* adatkészleteket támogatja. |
| Névtér| Hierarchikus | Hierarchikus |  *Most már elérhető*  |
| API  | HTTPS-kapcsolaton keresztül a REST API | REST API HTTP/HTTPS-kapcsolaton keresztül| *Most már elérhető* |
| Kiszolgálóoldali API| [WebHDFS – kompatibilis REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob Service REST API [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *jelenleg elérhető* <br><br> Azure Blob Service REST API – *most már elérhető*       |
| Hadoop-fájl rendszer ügyfél | Igen ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Igen ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Most már elérhető*  |  
| Adatműveletek – engedélyezés  | Fájl- és szintű POSIX hozzáférés-vezérlési listák (ACL-ek) az Azure Active Directory-identitások alapján  | A fájlok és mappák szintjének POSIX Access Control listái (ACL-ek) alapján Azure Active Directory identitások [megosztási kulcsa](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) a fiók szintű engedélyezési szerepkör-alapú Access Control ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) számára a tárolók eléréséhez | *Most már elérhető* |
| Adatműveletek – naplók  | Igen | Igen | *Elérhető most* (előzetes verzió). Tekintse meg az [ismert problémákat](data-lake-storage-known-issues.md).<br><br> Azure monitoring-integráció – *még nem érhető el* |
| Az inaktív adatok titkosítása | Szolgáltatás által kezelt kulcsokkal és az ügyfél által felügyelt kulcsok az Azure KeyVault transzparens, kiszolgálóoldali | Transzparens, kiszolgálóoldali szolgáltatás által kezelt kulcsokkal és ügyfélkulcsokkal által kezelt kulcsok az Azure-Kulcstartóban | Szolgáltatás által felügyelt kulcsok – *jelenleg elérhető*<br><br> Ügyfél által felügyelt kulcsok – *mostantól elérhető*  |
| Felügyeleti műveletek (például fiók létrehozása) | Az Azure által biztosított [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) a fiókok felügyeletéhez | Az Azure által biztosított [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) a fiókok felügyeletéhez | *Most már elérhető*|
| Fejlesztői SDK-k | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, a Ruby, PHP, Go, Android, iOS| BLOB SDK – *jelenleg elérhető*. Azure Data Lake Storage Gen2 SDK – *még nem érhető el*  |
| |Optimalizált teljesítménygyűjtési párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebességű és iops-t. | Optimalizált teljesítménygyűjtési párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebességű és iops-t. | *Most már elérhető* |
| Virtual Network (VNet) támogatása  | [Virtual Network integráció használata](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Szolgáltatási végpont használata az Azure Storage-hoz](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Most már elérhető* |
| Blobméretének korlátjai | A fiókok méretének, a fájlok méretét vagy a fájlok száma korlátlan | A fiókok méretének vagy fájlok száma korlátlan. A fájl legfeljebb 5 TB-os méret. | *Most már elérhető*|
| Georedundancia| Helyileg redundáns (LRS) | Helyileg redundáns (LRS) zóna redundáns (ZRS), Geo-redundáns (GRS) olvasási hozzáférés geo-redundáns (RA-GRS) további információért lásd [itt](https://docs.microsoft.com/azure/storage/common/storage-redundancy) .| *Most már elérhető* |
| Régiónkénti rendelkezésre állás | Lásd [itt](https://azure.microsoft.com/regions/) | Minden [Azure-régió](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Most már elérhető*                                                                                                                           |
| Ár                                       | [Díjszabás](https://azure.microsoft.com/pricing/details/data-lake-store/) megtekintése                                                                            | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/data-lake/) megtekintése                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA szerinti rendelkezésre állás                            | [Lásd: SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Lásd: SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Most már elérhető*                                                                                                                           |
| Adatkezelés                             | A fájlok lejáratának                                                                                                                                        | Életciklus-szabályzatok                                                                                                                                                                                                                                                                                                                                                                                                          | Az életciklus-szabályzatok *most már elérhetők* (előzetes verzió). Tekintse meg az [ismert problémákat](data-lake-storage-known-issues.md).                                                                                                                     |

### <a name="programming-interfaces"></a>Programozási felületek

Ez a táblázat ismerteti, amely API-t állítja be, amely az egyéni alkalmazások érhetők el. Az ACL és a címtár-szintű műveletek SDK-támogatása még nem támogatott.

|  API-készlet                           |  1\. generációs Data Lake Storage                                                                                                                                                                                                                                                                                                   | Megosztott kulcsos hitelesítés a Data Lake Storage Gen2 - rendelkezésre állása | Az OAuth-hitelesítés a Data Lake Storage Gen2 - elérhetősége                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK                  | [Hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Most már elérhető* | *Most már elérhető* |
| Java SDK                | [Hivatkozás](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Most már elérhető*                                                      | *Most már elérhető*                                     |
| Node.js                | [Hivatkozás](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Most már elérhető*                                                     | *Most már elérhető*                                                                                           |
| Python SDK                   | [Hivatkozás](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Most már elérhető*                                                      | *Most már elérhető*                                        |
| REST API                 | [Hivatkozás](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Most már elérhető*                                                      | *Most már elérhető*                                                                                                                                               |
| PowerShell | [Hivatkozás](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Most már elérhető* |
| parancssori felület                 | [Hivatkozás](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Most már elérhető*                                                      | *Most már elérhető*                                                               |
| Az Azure Resource Manager-sablonok - kezelés             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Most már elérhető*                                                      | *Most már elérhető*                                          |

### <a name="azure-ecosystem"></a>Azure-ökoszisztéma

Data Lake Storage Gen1 használata esetén a végpontok közötti folyamatok számos Microsoft-szolgáltatások és termékek is használhatja. Ezek a szolgáltatások és termékek együttműködve Data Lake Storage Gen1 közvetlenül vagy közvetve. Ez a táblázat a szolgáltatások listáját jeleníti meg, hogy azt dolgozhat a Data Lake Storage Gen1 úgy módosítottuk, és látható, melyeket jelenleg kompatibilis a Data Lake Storage Gen2.

| **Terület**             | **Data Lake Storage Gen1 rendelkezésre állása**                                                                                                                                    | **Data Lake Storage Gen2 rendelkezésre állása – megosztott kulcsos hitelesítéssel**                                                                                                           | **Data Lake Storage Gen2 rendelkezésre állása – a OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Elemzési keretrendszer  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Most már elérhető*                                                                                                                                                              | *Most már elérhető*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6 – *jelenleg elérhető* HDInsight 4,0 – *még nem érhető* el      | HDInsight 3,6 ESP – *most elérhető* <br><br>  HDInsight 4,0 ESP – *még nem érhető el*                                                                 |
|                      | Databricks futtatókörnyezete 3.1-es vagy újabb verzió                                                                                                                                               | [Databricks Runtime 5,1 és újabb](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *verzió érhető el* | [Databricks Runtime 5,1 és újabb](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) verziók – *mostantól elérhető*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nem támogatott*                                                                                                                                                              | *Elérhető most* [hivatkozás](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Adatintegráció     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [2. verzió](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – az 1. verzió *már elérhető* – *nem támogatott*                               | [2. verzió](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *jelenleg elérhető* <br><br> 1\. verzió – *nem támogatott*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nem támogatott*                                                                                                                                                              | *Nem támogatott*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
| IoT                  | [Event Hubs – rögzítés](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
| Használat          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
| Termelékenység         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nem támogatott*                                                                                                                                                              | Fiókkezelés *– jelenleg elérhető* <br><br>Adatműveletek *–*  *még nem érhető el*                                                                   |
|                      | [A Visual studióhoz készült Data Lake eszközök](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Most már elérhető*                                                                                                                                                              | *Most már elérhető*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partneri ökoszisztéma

Ez a táblázat a harmadik féltől származó szolgáltatásokkal és a módosított dolgozhat a Data Lake Storage Gen1 termékek listáját jeleníti meg. Azt is ismerteti, melyek jelenleg kompatibilisek, a Data Lake Storage Gen2.

| Terület            | Partnerek  | Termék vagy szolgáltatás  | Data Lake Storage Gen1 rendelkezésre állása                                                                                                                                               | Megosztott kulcsos hitelesítés a Data Lake Storage Gen2 – rendelkezésre állás                                                   | Rendelkezésre állás, a Data Lake Storage Gen2 – Oauth-val                                                             |
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
| Számlázási adatok - mérőszámok kereskedelmi küldött csapat a számlázási és majd szeretné elérhetővé tenni az ügyfelek számára  | *Most már elérhető*                                                                                             | *Most már elérhető*                                                                                                                           |
| Tevékenységnaplók                                                                                          | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Egyszeri kérések egy adott időtartamra vonatkozó naplókhoz támogatási jegy használatával – *mostantól elérhető* az Azure monitorozási integrációja – *még nem érhető el* |
| Diagnosztikai naplók                                                                                        | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Elérhető most* (előzetes verzió). Tekintse meg az [ismert problémákat](data-lake-storage-known-issues.md). <br>Azure monitoring-integráció – *még nem érhető el* |
| Mérőszámok                                                                                                | *Nem támogatott*                                                                                               | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Frissítés tervezése

Ez a szakasz azt feltételezi, hogy áttekintette az útmutató [frissítési készültségének felmérése](#assess-your-upgrade-readiness) című szakaszát, és az összes függőség teljesül. Ha továbbra sem érhetők el a Data Lake Storage Gen2-képességeihez, folytassa csak akkor, ha ismeri a megfelelő megoldások. A következő szakaszok útmutatást nyújtanak a megtervezheti, hogyan a frissítésre, a folyamatok. A tényleges frissítés végrehajtásáról az útmutató [frissítésének végrehajtása](#performing-the-upgrade) című szakaszában olvashat.

### <a name="upgrade-strategy"></a>A frissítési stratégia

A frissítés a legfontosabb részét el kell döntenie a stratégia. Ezt a döntést az elérhető lehetőségek határozza meg.

Ez a táblázat felsorolja azokat a jól ismert stratégiákat, amelyeket az adatbázisok, Hadoop-fürtök stb. áttelepíteni használtak. Az útmutatóban hasonló stratégiákat fogunk alkalmazni, és alkalmazkodunk a kontextushoz.

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

A frissítés elvégzéséhez használt általános stratégia (lásd az útmutató [frissítési stratégia](#upgrade-strategy) című szakaszát) határozza meg az adatok frissítéséhez használható eszközöket. Az alábbiakban az eszközök aktuális információk alapulnak, és javaslatok. 

#### <a name="tools-guidance"></a>Útmutató eszközök

| Stratégia                       | Eszközök                                                                                                             | Szakemberek számára                                                                                                                             | Megfontolandó szempontok                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Emelés és váltás**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Felügyelt felhőbeli szolgáltatás                                                                                                                | A jelenleg tárolt adat-és ACL-ek is átmásolhatók.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Az ismert Hadoop által biztosított eszköz például az ACL-ek engedélyek lehet másolni az ezzel az eszközzel                                                   | Szükséges egy fürtöt, amely egyszerre Data Lake Storage Gen1 és Gen2 is csatlakozhatnak.                                                                                                                                                                                   |
| **Másolási és másolási növekmény** | Azure Data Factory                                                                                                    | Felügyelt felhőbeli szolgáltatás                                                                                                                | A jelenleg tárolt adat-és ACL-ek is átmásolhatók. Támogatja a növekményes másolása az ADF-ben, adatokat kell egy idősorozat-módon rendszerezhetők. A növekményes másolatok közötti legrövidebb intervallum [15 perc](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Párhuzamos elfogadás**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Támogatási konzisztens replikációs Ha egy tiszta Hadoop környezet használatával csatlakozik az Azure Data Lake Storage támogatja a kétirányú replikációt | Ha nem használ egy tiszta Hadoop-környezetben, lehet késleltetés a replikációs.                                                                                                                                                                                                                                                  |

Vegye figyelembe, hogy vannak olyan harmadik felek, akik a fenti adatok/meta-adatmásolási eszközök (például: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)) nélkül képesek kezelni a Data Lake Storage Gen1t a frissítéshez Data Lake Storage Gen2. Adatok áttelepítése, valamint a számítási feladatok migrálását végző egy "egyablakos" élményt nyújtanak. Előfordulhat, hogy rendelkezik olyan eszközökkel, amelyek ökoszisztémát kívül a sávon kívüli frissítés végrehajtásához.

#### <a name="considerations"></a>Megfontolandó szempontok

* Manuálisan hozza létre a Data Lake Storage Gen2 fiókot először, mivel az aktuális útmutató nem tartalmaz minden Gen1 fiókadatai alapján automatikus Gen2 fiók folyamat bármely része a frissítés megkezdése előtt kell. Győződjön meg arról, hogy összehasonlítja a [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) és a [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account)tartozó fiókok létrehozási folyamatait.

* Data Lake Storage Gen2 támogat fájlok akár 5 TB-os méret. A Data Lake Storage Gen2ra való frissítéshez előfordulhat, hogy a fájlokat át kell méreteznie Data Lake Storage Gen1 hogy az 5 TB-nál kisebb méretben legyenek.

* Ha egy eszköz, amely nem másolja át a hozzáférés-vezérlési listák használhatja, vagy másolja át a hozzáférés-vezérlési listák nem kívánja, majd szüksége a hozzáférés-vezérlési listák beállítása a cél a megfelelő legfelső szintű manuálisan. Megteheti a Storage Explorer használatával. Győződjön meg arról, hogy ezen ACL-ek az alapértelmezett ACL-EK, hogy a fájlokat és mappákat, másolja át őket öröklik.

* A Data Lake Storage Gen1 is megadhatja a hozzáférés-vezérlési listák a legmagasabb szintű jelenleg a fiók gyökérmappájában. Data Lake Storage Gen2 azonban a legmagasabb szintű ACL-eket a tároló gyökérkönyvtárában, nem pedig a teljes fiókban lehet beállítani. Ezért ha azt szeretné, a fiók szintjén beállított alapértelmezett ACL-EK, a Data Lake Storage Gen2-fiókjában található összes fájl rendszer jeggyel ismétlődő kell.

* Fájl vonatkozó elnevezési korlátozás különböznek a két tárolási rendszerek között. Ezek a különbségek vannak különösen érintő bemásolja a Data Lake Storage Gen2 Data Lake Storage Gen1 mivel ez utóbbi van több korlátozott korlátozások.

### <a name="application-upgrade"></a>Alkalmazás frissítése

Alkalmazásokat hozhat létre a Data Lake Storage Gen1 vagy a Data Lake Storage Gen2 van szüksége, amikor kell választania a megfelelő alkalmazásprogramozási felületet. API-k az adott csatolón hívásakor kell a megfelelő URI-t és a megfelelő hitelesítő adatokat. Leképezése az felsorolt három elemet, az API URI-t, és hogyan a hitelesítő adatok vannak megadva, a másik Data Lake Storage Gen1 és a Data Lake Storage Gen2.So között az alkalmazásfrissítés részeként kell ezen három szerkezeteket megfelelően leképezni.

#### <a name="uri-changes"></a>URI-módosítások

A fő feladat az URI-k lefordítása, amelyeknek a `adl://` előtagját a `abfss://` előtaggal rendelkező URI-k.

A Data Lake Storage Gen1 [URI-sémája részletesen szerepel,](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) de általánosságban véve *adl://mydatalakestore.azuredatalakestore.net/\<FILE_PATH\>.*

A Data Lake Storage Gen2 fájlok eléréséhez szükséges URI-sémát részletesen ismertetjük, de [általánosságban a `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Haladjon végig a meglévő alkalmazásokkal, és győződjön meg arról, hogy módosította az URI-k megfelelően, Data Lake Storage Gen2-re mutasson kell megjelennek. Emellett szüksége adja hozzá a megfelelő hitelesítő adatokat. Végül hogyan az eredeti telepítéséhez, és cserélje le az új alkalmazást kell szorosan össze kell hangolni a teljes frissítési stratégia.

#### <a name="custom-applications"></a>Egyéni alkalmazások

Az alkalmazás használja a Data Lake Storage Gen1 felületen, attól függően kell azt mindig a Data Lake Storage Gen2-re módosítja.

##### <a name="rest-apis"></a>REST API-k

Az alkalmazás a Data Lake Storage REST API-kat használja, ha módosítania kell az alkalmazás használhatja a Data Lake Storage Gen2 REST API-k. A hivatkozásokat a *programozási felületek* szakaszban találja.

##### <a name="sdks"></a>SDK-k

Ahogy az a *frissítési készültség felmérése* című szakaszban leírtak szerint, az SDK-k jelenleg nem érhetők el. Ha azt szeretné, hogy a port az alkalmazásokon keresztül Data Lake Storage Gen2, javasoljuk, hogy várjon, amíg a támogatott SDK-k elérhetővé válnak.

##### <a name="powershell"></a>PowerShell

Mivel feltüntettük az a felmérési az upgrade Readiness-szel szakaszban, PowerShell-támogatása jelenleg nem érhető az adatsík az.

Felügyeleti sík parancsmagok sikerült cserélje a Data Lake Storage Gen2 megfelelő rétegében. A hivatkozásokat a *programozási felületek* szakaszban találja.

##### <a name="cli"></a>parancssori felület

Ahogy az *a frissítés készültségének felmérése* című szakaszban leírtak szerint, a CLI-támogatás jelenleg nem érhető el az adatsíkon.

Felügyeleti sík parancsok sikerült cserélje a Data Lake Storage Gen2 megfelelő rétegében. A hivatkozásokat a *programozási felületek* szakaszban találja.

### <a name="analytics-frameworks-upgrade"></a>Elemzési keretrendszerek frissítése

Ha az alkalmazás metaadatai információkat a tárolóban, például explicit fájl és mappa elérési útjának hoz létre, szüksége további műveletek elvégzésére, az áruházbeli adatok és metaadatok a frissítés után. Ez különösen igaz az elemzési keretrendszerek, például Azure HDInsight, általában létre eseménykatalógus-adatok a tároló adatainak Databricks stb.

Elemzési keretrendszerek adatok és metaadatok tárolódnak a távoli tárolókat, mint a Data Lake Storage Gen1 és Gen2 működik. Így elméletben a motorok lehet, hogy a rövid élettartamú, és csak akkor, ha a feladatok futtatásához a tárolt adatok kell leállását.

Azonban a teljesítmény optimalizálása az elemzési keretrendszerek előfordulhat, hogy a fájlok és mappák, a távoli tárolja mutató explicit hivatkozásokat létrehozása, és majd hozzon létre egy gyorsítótárat is tartja őket. Kell URI-ját a távoli adatok módosítása, például egy fürtöt, amely volt adattárolás a Data Lake Storage Gen1 korábban, és most már, aki a Data Lake Storage Gen2 tárolja az URI-t ugyanazon másolt tartalom eltérő lesz. Így az adatok és metaadatok a gyorsítótárak ezek motorok frissítése után is kell frissítése vagy újbóli inicializálása

A tervezési folyamat részeként a következőket kell tennie azonosítják az alkalmazást, és döntse el, hogyan lehet inicializálásakor a Data Lake Storage Gen2 most tárolt adatok metaadatot tartalmaz információkat. Az alábbi, az útmutató segítséget nyújt a frissítési lépéseket általánosan elfogadott elemzési keretrendszerek.

#### <a name="azure-databricks"></a>Azure Databricks

Attól függően, a frissítési stratégia választja a lépések eltérőek lehetnek. Az aktuális szakasz azt feltételezi, hogy kiválasztotta-e a "Élettartam-and-shift" stratégia. Emellett a meglévő Databricks-munkaterület, amely egy Data Lake Storage Gen1-fiókban lévő adatok eléréséhez használt várhatóan az adatok, Data Lake Storage Gen2 fiók át van másolva.

Először is győződjön meg arról, hogy létrehozta a Gen2-fiókot, és ezután átmásolja keresztül adatok és meta Gen1 Gen2-re megfelelő eszköz használatával. Ezek az eszközök az útmutató [Adatfrissítés](#data-upgrade) szakaszában találhatók.

Ezután [frissítse](https://docs.azuredatabricks.net/user-guide/clusters/index.html) a meglévő Databricks-fürtöt az Databricks Runtime 5,1-es vagy újabb verziójának használatának megkezdéséhez, amely támogatja a Data Lake Storage Gen2.

A lépések azt követően alapulnak hogyan a meglévő Databricks-munkaterülethez a Data Lake Storage Gen1 fiókban adatokhoz fér hozzá. A adl://URI-k [közvetlenül](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) a jegyzetfüzetekről vagy a [csatolási](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)-n keresztül történő meghívásával is elvégezhető.

Ha a közvetlenül a notebookok azáltal, hogy a teljes adl hozzáférés: / / URI-k, kell haladjon végig az egyes jegyzetfüzet és elérni a megfelelő Data Lake Storage Gen2 URI úgy módosítaná a konfigurációt.

Jövőben kell újból konfigurálnia kell, hogy a Data Lake Storage Gen2 fiók mutasson. Nincsenek további módosítások szükségesek, és a notebookok dolgozhat, mint korábban képesnek kell lennie.

Ha a más frissítési stratégiák bármelyike használ, létrehozhat egy változata, a fenti lépéseket az igényeknek.

### <a name="azure-ecosystem-upgrade"></a>Azure-ökoszisztéma frissítése

Ennek az útmutatónak az [Azure ökoszisztéma](#azure-ecosystem) című szakaszában ismertetett eszközöket és szolgáltatásokat úgy kell konfigurálni, hogy működjenek a Data Lake Storage Gen2.

Először győződjön meg arról, hogy nincs integráció a Data Lake Storage Gen2.

Ezután fent feltüntettük az elemeket (például: URI-t és a hitelesítő adatok), módosítani kell. A meglévő példányt, amely együttműködik a Data Lake Storage Gen1, módosításával, vagy létrehozhat egy új példányt, amely a Data Lake Storage Gen2 működne.

### <a name="partner-ecosystem-upgrade"></a>Partneri ökoszisztéma frissítése

Együttműködve biztosít az összetevő és az eszközök való gondoskodás a partner a Data Lake Storage Gen2 is működhetnek. 

## <a name="performing-the-upgrade"></a>A frissítés

### <a name="pre-upgrade"></a>Frissítés előtti

Ennek részeként elvégezte volna a *frissítési készültségi szakasz értékelését* és az útmutató [Verziófrissítési szakaszának megtervezését](#planning-for-an-upgrade) , az összes szükséges információt megkapta, és létrehozott egy olyan csomagot, amely megfelel az igényeinek. Valószínűleg rendelkezni fog egy tesztelési feladat ebben a fázisban.

### <a name="in-upgrade"></a>A frissítés

A stratégia függően válassza ki, és kiküszöböli, hogy a megoldás, az ebben a fázisban lehet egy rövid vagy kiterjesztett egy ahol több számítási feladat Növekményesen átléptetjük a Data Lake Storage Gen2-re vár. Ez a frissítés a kritikus fontosságú része lesz.

### <a name="post-upgrade"></a>Frissítés után

Miután elkészült, az átmenet művelettel, az utolsó lépéseit alapos ellenőrzési magában foglalja. Ebbe beletartozik, de nem korlátozható, hogy a rendszer megbízhatóan másolta az adatátvitelt, és ellenőrizze, hogy az ACL-ek megfelelően vannak-e beállítva, és hogy a végpontok közötti folyamatok megfelelően működnek-e stb. Az ellenőrzések befejezése után mostantól kikapcsolhatja a régi folyamatokat, törölheti a forrás Data Lake Storage Gen1 fiókokat, és teljes sebességgel megtekintheti Data Lake Storage Gen2-alapú megoldásait.

## <a name="conclusion"></a>Összegzés

A jelen dokumentumban leírt útmutatást kell segítették megoldását szeretné használni a Data Lake Storage Gen2 frissítését. 

Ha további kérdése van, vagy visszajelzést szeretne küldeni, adja meg az alábbi megjegyzéseket, vagy küldjön visszajelzést az [Azure feedback fórumában](https://feedback.azure.com/forums/327234-data-lake).
