---
title: Frissítse big data Analytics-megoldásait Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2
description: A megoldás frissítése Azure Data Lake Storage Gen2 használatára
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 27d752b8ff7eafbb92930b19e17890ace8a90b85
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750434"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Frissítse big data Analytics-megoldásait Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2

Ha Azure Data Lake Storage Gen1t használ a big data Analytics-megoldásokban, ez az útmutató segítséget nyújt a megoldásoknak a Azure Data Lake Storage Gen2 használatára való frissítéséhez. Ezt a dokumentumot felhasználhatja annak a függőségeknek az értékelésére, amelyeken a megoldás Data Lake Storage Gen1 van. Ez az útmutató azt is bemutatja, hogyan tervezheti meg és hajthatja végre a frissítést.

A következő feladatokon keresztül segítünk:

: heavy_check_mark: a frissítési készültség felmérése

: heavy_check_mark: a frissítés megtervezése

: heavy_check_mark: a frissítés végrehajtása

## <a name="assess-your-upgrade-readiness"></a>A frissítési készültség felmérése

Célunk, hogy a Data Lake Storage Gen1ban lévő összes funkció elérhetővé válik Data Lake Storage Gen2ban. Ezek a képességek, például az SDK-ban, a CLI-ben stb., a Data Lake Storage Gen1 és a Data Lake Storage Gen2 között eltérőek lehetnek. A Data Lake Storage Gen1t használó alkalmazásoknak és szolgáltatásoknak a Data Lake Storage Gen2hoz hasonlóan kell működniük. Végül néhány funkció nem érhető el azonnal Data Lake Storage Gen2. Amint elérhetővé válnak, bejelentjük a jelen dokumentumban.

Ezek a következő fejezetek segítenek eldönteni, hogyan lehet a legjobban frissíteni Data Lake Storage Gen2re, és ha igen, akkor ez a legjobb megoldás.

### <a name="data-lake-storage-gen1-solution-components"></a>Megoldás-összetevők Data Lake Storage Gen1

A legtöbb esetben, ha az elemzési megoldásokban vagy folyamatokban Data Lake Storage Gen1 használ, számos további technológiára van szüksége, amelyet a teljes körű funkciók eléréséhez alkalmaz. Ez a [cikk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) az adatforgalom különböző összetevőit ismerteti, amelyek tartalmazzák az adatfeldolgozást,-feldolgozást és-felhasználást.

Ezen túlmenően ezen összetevők kiépítésére, kezelésére és figyelésére is kiterjedő összetevők állnak rendelkezésre. Az egyes összetevők a Data Lake Storage Gen1 használatával működnek, és a legmegfelelőbb felületet használják. Ha úgy tervezi, hogy a megoldást a Data Lake Storage Gen2ra frissíti, a használt felületekről tisztában kell lennie. A felügyeleti felületet és az adatillesztőket is frissítenie kell, mivel minden egyes csatoló külön követelményekkel rendelkezik.

![Megoldás-összetevők Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

Az **1. ábrán** a legtöbb analitikai megoldásban megjelenő funkció-összetevők láthatók.

A **2. ábrán** egy példa látható arra, hogy ezek az összetevők hogyan lesznek implementálva bizonyos technológiák használatával.

A **ábra** tárolási funkcióját a Data Lake Storage Gen1 biztosítja (**2. ábra**). Vegye figyelembe, hogy az adatfolyam különböző összetevői a REST API-k vagy a Java SDK használatával kommunikálnak Data Lake Storage Gen1ekkel. Azt is vegye figyelembe, hogy a kibővíthető funkciók összetevői hogyan hatnak a Data Lake Storage Gen1ra. A kiépítési összetevő az Azure Resource templates szolgáltatást használja, míg a Azure Monitor naplókat használó figyelési összetevő a Data Lake Storage Gen1ból származó operatív adatok használatát használja.

Ha Data Lake Storage Gen1 használatával szeretne frissíteni egy megoldást Data Lake Storage Gen2re, másolja az adatok és a metaadatok adatait, csatlakoztassa újra az adatforgalmat, majd az összes összetevőnek működnie kell a Data Lake Storage Gen2sal.

Az alábbi szakaszokban olyan információk találhatók, amelyek segítenek a jobb döntések elvégzésében:

: heavy_check_mark: platform-képességek

: heavy_check_mark: programozási felületek

: heavy_check_mark: Azure-ökoszisztéma

: heavy_check_mark: partner ökoszisztémája

: heavy_check_mark: operatív információk

Az egyes szakaszokban meg tudja határozni a frissítéshez szükséges "must-haves" kifejezést. Miután biztos abban, hogy elérhetők a képességek, vagy ha biztos abban, hogy ésszerű megkerülő megoldásokat tartalmaz, folytassa az útmutató [verziófrissítésének megtervezésével](#planning-for-an-upgrade) foglalkozó szakaszát.

### <a name="platform-capabilities"></a>Platform-képességek

Ez a szakasz azt ismerteti, hogy Data Lake Storage Gen1 platform mely funkciói érhetők el Data Lake Storage Gen2ban.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 – cél | Data Lake Storage Gen2 rendelkezésre állási állapot  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Adatszervezet| A mappákban és fájlokban tárolt adatfájlok támogatása | Az objektumok/Blobok, valamint a mappák és fájlok [hivatkozással](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) tárolt adattárolók támogatása | Támogatja a mappákban és fájlban tárolt, *jelenleg elérhetővé* tett fájlokat <br><br> Az objektumként/blobként tárolt, *még nem elérhető* adatkészleteket támogatja. |
| Névtér| Hierarchikus | Hierarchikus |  *Most már elérhető*  |
| API  | REST API HTTPS-kapcsolaton keresztül | REST API HTTP/HTTPS protokollon keresztül| *Most már elérhető* |
| Kiszolgálóoldali API| [WebHDFS – kompatibilis REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob Service REST API [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *jelenleg elérhető* <br><br> Azure Blob Service REST API – *még nem érhető el*       |
| Hadoop fájlrendszerbeli ügyfél | Igen ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Igen ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Most már elérhető*  |  
| Adatműveletek – engedélyezés  | Fájl-és mappa szintű POSIX Access Control listák (ACL-ek) Azure Active Directory identitások alapján  | A fájlok és mappák szintjének POSIX Access Control listái (ACL-ek) alapján Azure Active Directory identitások [megosztási kulcsa](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) a fiók szintű engedélyezési szerepkör-alapú Access Control ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) számára a tárolók eléréséhez | *Most már elérhető* |
| Adatműveletek – naplók  | Igen | Egyszeri kérések a naplókhoz adott időtartamra vonatkozóan a támogatási jegy Azure monitoring Integration használatával | Egyszeri kérések egy adott időtartamra vonatkozó naplókhoz támogatási jegy használatával – *jelenleg elérhető*<br><br> Azure monitoring-integráció – *még nem érhető el* |
| Inaktív adatok titkosítása | Transzparens, kiszolgálóoldali szolgáltatás által felügyelt kulcsokkal és az Azure kulcstartóban az ügyfél által felügyelt kulcsokkal | Transzparens, kiszolgálóoldali szolgáltatás által felügyelt kulcsokkal és az Azure kulcstartóban található ügyfél kulcsok által felügyelt kulcsokkal | Szolgáltatás által felügyelt kulcsok – *jelenleg elérhető*<br><br> Ügyfél által felügyelt kulcsok – *mostantól elérhető*  |
| Felügyeleti műveletek (például fiók létrehozása) | Az Azure által biztosított [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) a fiókok felügyeletéhez | Az Azure által biztosított [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) a fiókok felügyeletéhez | *Most már elérhető*|
| Fejlesztői SDK-k | .NET, Java, Python, Node. js  | .NET, Java, Python, Node. js, C++, Ruby, php, go, Android, iOS| *Még nem érhető el* |
| |Optimalizált teljesítmény párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebesség és IOPS. | Optimalizált teljesítmény párhuzamos elemzési számítási feladatokhoz. Nagy átviteli sebesség és IOPS. | *Most már elérhető* |
| Virtual Network (VNet) támogatása  | [Virtual Network integráció használata](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Szolgáltatási végpont használata az Azure Storage-hoz](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Most már elérhető* |
| Méretkorlát | Nincs korlátozás a fiókok méretétől, a fájlmérettől vagy a fájlok számától | A fiók mérete vagy a fájlok száma nincs korlátozva. A fájlméret a 5TB korlátozódik. | *Most már elérhető*|
| Geo-redundancia| Helyileg redundáns (LRS) | Helyileg redundáns (LRS) zóna redundáns (ZRS), Geo-redundáns (GRS) olvasási hozzáférés geo-redundáns (RA-GRS) további információért lásd [itt](https://docs.microsoft.com/azure/storage/common/storage-redundancy) .| *Most már elérhető* |
| Regionális elérhetőség | Lásd [itt](https://azure.microsoft.com/regions/) | Minden [Azure-régió](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Most már elérhető*                                                                                                                           |
| Ár                                       | [Díjszabás](https://azure.microsoft.com/pricing/details/data-lake-store/) megtekintése                                                                            | [Díjszabás](https://azure.microsoft.com/pricing/details/storage/data-lake/) megtekintése                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA szerinti rendelkezésre állás                            | [Lásd: SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Lásd: SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Most már elérhető*                                                                                                                           |
| Adatkezelés                             | Fájl lejárata                                                                                                                                        | Életciklus-szabályzatok                                                                                                                                                                                                                                                                                                                                                                                                          | *Még nem érhető el*                                                                                                                       |

### <a name="programming-interfaces"></a>Programozási felületek

Ez a táblázat az egyéni alkalmazásokhoz elérhető API-készleteket ismerteti. Ha egy kicsit világosabban szeretné elkészíteni a dolgokat, ezeket az API-készleteket két típusra osztjuk: felügyeleti API-k és fájlrendszer API-k.

A felügyeleti API-k segítségével kezelheti a fiókokat, míg a fájlrendszer API-k segítenek a fájlokon és mappákon való működésben.

|  API-készlet                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Data Lake Storage Gen2 rendelkezésre állása – megosztott kulcsos hitelesítéssel | Data Lake Storage Gen2 rendelkezésre állása – OAuth-hitelesítéssel                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK – felügyelet                  | [Hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Nem támogatott*                                                      | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK – fájlrendszer                  | [Hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| Java SDK – felügyelet                  | [Hivatkozás](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nem támogatott*                                                      | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – fájlrendszer                  | [Hivatkozás](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                          | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| Node. js – felügyelet                   | [Hivatkozás](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                 | Nem támogatott                                                      | *Elérhető most –* [hivatkozás](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node. js – fájlrendszer                   | [Hivatkozás](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                 | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| Python – felügyelet                    | [Hivatkozás](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                  | *Nem támogatott*                                                      | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – fájlrendszer                    | [Hivatkozás](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| REST API – felügyelet                  | [Hivatkozás](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nem támogatott*                                                      | *Most elérhető –*                                                                                                                                               |
| REST API-fájlrendszer                  | [Hivatkozás](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Most már elérhető*                                                    | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell – felügyelet és fájlrendszer | [Hivatkozás](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Felügyelet – nem támogatott fájlrendszer – *még nem érhető el*        | Felügyelet – *elérhető most –* [hivatkozás](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Fájlrendszer – *még nem érhető el* |
| CLI – felügyelet                       | [Hivatkozás](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nem támogatott*                                                      | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI-fájlrendszer                       | [Hivatkozás](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Még nem érhető el*                                                | *Még nem érhető el*                                                                                                                                             |
| Sablonok Azure Resource Manager – felügyelet             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)   | *Nem támogatott*                                                      | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure-ökoszisztéma

Data Lake Storage Gen1 használatakor számos Microsoft-szolgáltatást és-terméket használhat a végpontok közötti folyamatokban. Ezek a szolgáltatások és termékek közvetlenül vagy közvetve Data Lake Storage Gen1 működnek. Ez a táblázat azoknak a szolgáltatásoknak a listáját jeleníti meg, amelyeket a Data Lake Storage Gen1 használatával módosítottunk, és amelyekkel jelenleg kompatibilis a Data Lake Storage Gen2.

| **Terület**             | **Data Lake Storage Gen1 rendelkezésre állása**                                                                                                                                    | **Data Lake Storage Gen2 rendelkezésre állása – megosztott kulcsos hitelesítéssel**                                                                                                           | **Data Lake Storage Gen2 rendelkezésre állása – a OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Elemzési keretrendszer  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Most már elérhető*                                                                                                                                                              | *Most már elérhető*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6 – *jelenleg elérhető* HDInsight 4,0 – *még nem érhető* el      | HDInsight 3,6 ESP – *most elérhető* <br><br>  HDInsight 4,0 ESP – *még nem érhető el*                                                                 |
|                      | Databricks Runtime 3,1 és újabb verziók                                                                                                                                               | [Databricks Runtime 5,1 és újabb](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *verzió érhető el* | [Databricks Runtime 5,1 és újabb](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) verziók – *mostantól elérhető*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Nem támogatott*                                                                                                                                                              | *Elérhető most* [hivatkozás](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Adatintegráció     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [2. verzió](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – az 1. verzió *már elérhető* – *nem támogatott*                               | [2. verzió](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *jelenleg elérhető* <br><br> 1\. verzió – *nem támogatott*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Nem támogatott*                                                                                                                                                              | *Nem támogatott*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
| IoT                  | [Event Hubs – rögzítés](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
| Használatalapú          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Most már elérhető*                                                                                                                                                          | *Most már elérhető*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
| Termelékenység         | [Azure Portalra](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Nem támogatott*                                                                                                                                                              | Fiókkezelés *– jelenleg elérhető* <br><br>Adatműveletek *–*  *még nem érhető el*                                                                   |
|                      | [A Visual studióhoz készült Data Lake eszközök](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Most már elérhető*                                                                                                                                                              | *Most már elérhető*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Még nem érhető el*                                                                                                                                                          | *Még nem érhető el*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partneri ökoszisztéma

Ez a táblázat azoknak a harmadik féltől származó szolgáltatásoknak és termékeknek a listáját jeleníti meg, amelyeket a Data Lake Storage Gen1sal való együttműködésre módosítottak. Azt is megmutatja, hogy melyek jelenleg kompatibilisek a Data Lake Storage Gen2okkal.

| Terület            | Partnerek  | Termék/szolgáltatás  | Data Lake Storage Gen1 rendelkezésre állása                                                                                                                                               | Data Lake Storage Gen2 rendelkezésre állása – megosztott kulcsos hitelesítéssel                                                   | Data Lake Storage Gen2 rendelkezésre állása – a OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Elemzési keretrendszer | Cloudera     | CDH                  | [Hivatkozás](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Még nem érhető el*                                                                                                  | [Hivatkozás](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Hivatkozás](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Nem támogatott*                                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | HortonWorks  | HDP 3,0              | [Hivatkozás](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                        | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Qubole       |                      | [Hivatkozás](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
| ETL                 | StreamSets   |                      | [Hivatkozás](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Informatica  |                      | [Hivatkozás](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Attunity     |                      | [Hivatkozás](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | Alteryx      |                      | [Hivatkozás](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | ImanisData   |                      | [Hivatkozás](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Még nem érhető el*                                                                                                  | *Még nem érhető el*                                                                                                  |
|                     | WANdisco     |                      | [Hivatkozás](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Hivatkozás](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Hivatkozás](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Operatív információk

Data Lake Storage Gen1 leküldi a konkrét adatokat és adatokat más szolgáltatásoknak, amelyek segítségével működővé tenni a folyamatokat. Ez a táblázat a Data Lake Storage Gen2 megfelelő támogatásának rendelkezésre állását jeleníti meg.

| Adattípus                                                                                       | Data Lake Storage Gen1 rendelkezésre állása                                                                 | Data Lake Storage Gen2 rendelkezésre állása                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Számlázási információk – a kereskedelmi csapatnak elküldhető számlázási, majd az ügyfelek számára elérhetővé tett mérőszámok  | *Most már elérhető*                                                                                             | *Most már elérhető*                                                                                                                           |
| Tevékenységnaplók                                                                                          | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Egyszeri kérések egy adott időtartamra vonatkozó naplókhoz támogatási jegy használatával – *mostantól elérhető* az Azure monitorozási integrációja – *még nem érhető el* |
| Diagnosztikai naplók                                                                                        | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Egyszeri kérések egy adott időtartamra vonatkozó naplókhoz támogatási jegy használatával – *mostantól elérhető* az Azure monitorozási integrációja – *még nem érhető el* |
| Metrikák                                                                                                | *Nem támogatott*                                                                                               | *Elérhető most –* [hivatkozás](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>A frissítés megtervezése

Ez a szakasz azt feltételezi, hogy áttekintette az útmutató [frissítési készültségének felmérése](#assess-your-upgrade-readiness) című szakaszát, és az összes függőség teljesül. Ha vannak olyan képességek, amelyek továbbra sem érhetők el Data Lake Storage Gen2ban, akkor csak akkor folytassa, ha ismeri a megfelelő megoldásokat. A következő szakaszokban útmutatást talál a folyamatok verziófrissítésének megtervezéséhez. A tényleges frissítés végrehajtásáról az útmutató [frissítésének végrehajtása](#performing-the-upgrade) című szakaszában olvashat.

### <a name="upgrade-strategy"></a>Frissítési stratégia

A frissítés legkritikusabb része a stratégia döntése. Ez a döntés határozza meg az Ön számára elérhető döntéseket.

Ez a táblázat felsorolja azokat a jól ismert stratégiákat, amelyeket az adatbázisok, Hadoop-fürtök stb. áttelepíteni használtak. Az útmutatóban hasonló stratégiákat fogunk alkalmazni, és alkalmazkodunk a kontextushoz.

| Stratégia                   | Szakemberek                                                                                  | Hátrányok                                                           | Mikor kell használni?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Emelés és váltás                 | Legegyszerűbb.                                                                                 | Leállást igényel az adatok másolásához, a feladatok áthelyezéséhez, valamint a bejövő és kimenő forgalom áthelyezéséhez                                             | Az egyszerűbb megoldások esetében, ahol nem érhetők el több megoldás ugyanahhoz a Gen1-fiókhoz, így gyorsan vezérelt módon helyezhetők át.                                                  |
| Másolási és másolási növekmény | Csökkentse az állásidőt a másolás háttérben történő elküldésével, miközben a forrásrendszer még aktív állapotban van. | A bejövő és kimenő forgalom áthelyezéséhez állásidő szükséges.                   | Az átmásolható adatmennyiség nagy, és az élettartammal és a eltolással kapcsolatos állásidő nem elfogadható. Az áttérés előtt a megcélzott rendszeren jelentős termelési adatai szükségesek. |
| Párhuzamos elfogadás              | A minimális állásidő lehetővé teszi, hogy az alkalmazások saját belátása szerint migrálják az alkalmazásokat.           | A legtöbb megoldás a két rendszer közötti kétirányú szinkronizálásra van szükség. | Összetett forgatókönyvek esetén, ahol a Data Lake Storage Gen1-ra épülő alkalmazások nem átváltás egyszerre, és növekményes módon át kell helyezni őket.                                                      |

Az alábbiakban részletesen ismertetjük az egyes stratégiákkal kapcsolatos lépéseket. A lépések lista azt ismerteti, hogy mi történjen a frissítésben érintett összetevőkkel. Ez magában foglalja a teljes forrásrendszer, a teljes célként megadott rendszer, a forrásrendszer bejövő forrásai, a forrásrendszer kimenő helyei és a forrásoldali rendszeren futó feladatok részét.

Ezek a lépések nem tekinthetők előírásnak. A keretrendszer azt jelentette, hogy az egyes stratégiákról Hogyan gondolkodunk. Minden stratégia esetében biztosítunk esettanulmányokat, ahogy látjuk őket.

#### <a name="lift-and-shift"></a>Emelés és váltás

1. A forrásrendszer szüneteltetése – bejövő források, feladatok, kimenő célhelyek.
2. Másolja a forrásrendszer összes adatait a célként megadott rendszerbe.
3. Mutasson az összes bejövő forrásra a célként megadott rendszeren. Mutasson a kimenő forgalom célhelyére a célként megadott rendszerből.
4. Helyezze át, módosítsa, futtassa az összes feladatot a célként megadott rendszeren.
5. A forrásrendszer kikapcsolása.

#### <a name="copy-once-and-copy-incremental"></a>Másolás és másolás – növekményes

1. Másolja át az adatok átmásolását a forrásoldali rendszerből a célként megadott rendszerbe.
2. Rendszeres időközönként másolja át a növekményes adatok a forrás rendszerből a célként megadott rendszerbe.
3. Mutasson a kimenő forgalom célhelyére a célként megadott rendszerből.
4. Helyezze át, módosítsa, futtassa az összes feladatot a célként megadott rendszeren.
5. A kifelé irányuló adatforrások Növekményesen a célként megadott rendszeren keresztül áramlanak.
6. Ha az összes bejövő forrás a célként megadott rendszerre mutat.
    1. A növekményes másolás kikapcsolása.
    2. A forrásrendszer kikapcsolása.

#### <a name="parallel-adoption"></a>Párhuzamos elfogadás

1. Állítsa be a célként megadott rendszereket.
2. Hozzon létre kétirányú replikálást a forrásrendszer és a célként megadott rendszer között.
3. A kifelé irányuló források növekményes kiléptetése a célként megadott rendszeren.
4. A feladatokat Növekményesen helyezheti át, módosíthatja, futtathatja a célként megadott rendszerbe.
5. A kilépési célpontok növekményes átirányítása a célként megadott rendszerből.
6. Miután az összes eredeti bejövő forrás, feladat és kimenő cél működik a célrendszer, kapcsolja ki a forrásoldali rendszerét.

### <a name="data-upgrade"></a>Adatfrissítés

A frissítés elvégzéséhez használt általános stratégia (lásd az útmutató [frissítési stratégia](#upgrade-strategy) című szakaszát) határozza meg az adatok frissítéséhez használható eszközöket. Az alább felsorolt eszközök az aktuális információkon alapulnak, és javaslatok. 

#### <a name="tools-guidance"></a>Eszközök – útmutató

| Stratégia                       | Eszközök                                                                                                             | Szakemberek                                                                                                                             | Megfontolandó szempontok                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Emelés és váltás**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Felügyelt felhőalapú szolgáltatás                                                                                                                | A jelenleg tárolt adat-és ACL-ek is átmásolhatók.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Jól ismert, Hadoop által biztosított eszközök engedélyei, például ACL-ek másolhatók ezzel az eszközzel                                                   | Egy olyan fürtöt igényel, amely egyszerre tud csatlakozni a Data Lake Storage Gen1hoz és a Gen2.                                                                                                                                                                                   |
| **Másolási és másolási növekmény** | Azure Data Factory                                                                                                    | Felügyelt felhőalapú szolgáltatás                                                                                                                | A jelenleg tárolt adat-és ACL-ek is átmásolhatók. Az ADF növekményes másolásának támogatásához az adatoknak idősorozatos módon kell megszervezniük. A növekményes másolatok közötti legrövidebb intervallum [15 perc](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Párhuzamos elfogadás**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Konzisztens replikáció támogatása, ha Azure Data Lake Storagehoz csatlakoztatott tiszta Hadoop-környezettel rendelkezik, támogatja a kétirányú replikálást. | Ha nem tiszta Hadoop-környezetet használ, a replikálás késése is lehet.                                                                                                                                                                                                                                                  |

Vegye figyelembe, hogy vannak olyan harmadik felek, akik a fenti adatok/meta-adatmásolási eszközök (például: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)) nélkül képesek kezelni a Data Lake Storage Gen1t a frissítéshez Data Lake Storage Gen2. Az adatok áttelepítését és a munkaterhelések áttelepítését végző "egyablakos" élményt biztosítanak. Előfordulhat, hogy az ökoszisztémán kívüli eszközökön sávon kívüli frissítést kell végrehajtania.

#### <a name="considerations"></a>Megfontolandó szempontok

* Először manuálisan kell létrehoznia a Data Lake Storage Gen2 fiókot, mielőtt megkezdené a frissítés bármely részét, mert az aktuális útmutató nem tartalmaz automatikus Gen2-fiókot a Gen1-fiók adatai alapján. Győződjön meg arról, hogy összehasonlítja a [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) és a [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account)tartozó fiókok létrehozási folyamatait.

* Data Lake Storage Gen2 csak a legfeljebb 5 TB méretű fájlokat támogatja. A Data Lake Storage Gen2ra való frissítéshez előfordulhat, hogy a fájlokat át kell méreteznie Data Lake Storage Gen1 hogy az 5 TB-nál kisebb méretben legyenek.

* Ha olyan eszközt használ, amely nem másol ACL-eket, vagy nem szeretné átmásolni az ACL-eket, akkor a célhelyen a megfelelő legfelső szinten manuálisan kell megadnia az ACL-eket. Ezt a Storage Explorer használatával teheti meg. Győződjön meg arról, hogy ezek az ACL-ek az alapértelmezett ACL-ek, így a másolt fájlok és mappák öröklik azokat.

* Data Lake Storage Gen1 a legmagasabb szintű ACL-ek állíthatók be a fiók gyökerében. Data Lake Storage Gen2 azonban a legmagasabb szintű ACL-eket a tároló gyökérkönyvtárában, nem pedig a teljes fiókban lehet beállítani. Tehát ha a fiók szintjén szeretné beállítani az alapértelmezett ACL-eket, akkor a Data Lake Storage Gen2-fiókban lévő összes fájlrendszeren át kell másolnia azokat.

* A fájlok elnevezési korlátozásai eltérnek a két tárolási rendszer között. Ezek a különbségek különösen a Data Lake Storage Gen2ról a Data Lake Storage Gen1ra történő másoláskor vonatkoznak, mivel az utóbbi nagyobb mértékben korlátozott korlátozásokkal rendelkezik.

### <a name="application-upgrade"></a>Alkalmazás frissítése

Ha Data Lake Storage Gen1-vagy Data Lake Storage Gen2-alkalmazásokat kell létrehoznia, először ki kell választania egy megfelelő programozási felületet. Az API-nak az adott felületen való meghívásakor meg kell adnia a megfelelő URI-t és a megfelelő hitelesítő adatokat. Ennek a három elemnek, az API-nak, az URI-nak és a hitelesítő adatoknak a megjelenítése különbözik a Data Lake Storage Gen1 és Data Lake Storage Gen2.So között az alkalmazás frissítésének részeként, a három összeállítást megfelelően kell leképeznie.

#### <a name="uri-changes"></a>URI-változások

A fő feladat az URI-k lefordítása, amelyeknek a `adl://` előtagját a `abfss://` előtaggal rendelkező URI-k.

A Data Lake Storage Gen1 [URI-sémája részletesen szerepel,](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) de általánosságban a *ADL://mydatalakestore.azuredatalakestore.net/\<file_path \>.*

A Data Lake Storage Gen2 fájlok eléréséhez szükséges URI-sémát részletesen ismertetjük, de [általánosságban a `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) .

A meglévő alkalmazásain át kell lépnie, és meg kell győződnie arról, hogy megfelelően módosította az URI azonosítókat, hogy azok Data Lake Storage Gen2ra mutassanak. Emellett hozzá kell adnia a megfelelő hitelesítő adatokat. Végezetül az eredeti alkalmazások kivonásának és az új alkalmazásra való lecserélésének módját szorosan össze kell hangolni a teljes frissítési stratégiával.

#### <a name="custom-applications"></a>Egyéni alkalmazások

Attól függően, hogy az alkalmazás milyen felületen használja az Data Lake Storage Gen1-t, módosítania kell, hogy a Data Lake Storage Gen2hoz legyen igazítva.

##### <a name="rest-apis"></a>REST API-k

Ha az alkalmazás Data Lake Storage REST API-kat használ, módosítania kell az alkalmazást a Data Lake Storage Gen2 REST API-k használatához. A hivatkozásokat a *programozási felületek* szakaszban találja.

##### <a name="sdks"></a>SDK-k

Ahogy az a *frissítési készültség felmérése* című szakaszban leírtak szerint, az SDK-k jelenleg nem érhetők el. Ha azt szeretné, hogy a port az alkalmazásokon keresztül Data Lake Storage Gen2, javasoljuk, hogy várjon, amíg a támogatott SDK-k elérhetővé válnak.

##### <a name="powershell"></a>PowerShell

Ahogy az a frissítés készültségének felmérése szakaszban is látható, a PowerShell-támogatás jelenleg nem érhető el az adatsíkon.

A felügyeleti sík parancsmagok lecserélheti a megfelelő Data Lake Storage Gen2. A hivatkozásokat a *programozási felületek* szakaszban találja.

##### <a name="cli"></a>CLI

Ahogy az *a frissítés készültségének felmérése* című szakaszban leírtak szerint, a CLI-támogatás jelenleg nem érhető el az adatsíkon.

A felügyeleti sík parancsait lecserélheti a megfelelő Data Lake Storage Gen2. A hivatkozásokat a *programozási felületek* szakaszban találja.

### <a name="analytics-frameworks-upgrade"></a>Elemzési keretrendszerek frissítése

Ha az alkalmazás olyan metaadatokat hoz létre az áruházban található információkról, mint például az explicit fájl-és mappa elérési útja, további műveleteket kell végrehajtania az adatok tárolása/meta-adatok frissítése után. Ez különösen igaz az olyan elemzési keretrendszerek esetében, mint például az Azure HDInsight, a Databricks stb., amely általában katalógus-adattárolást hoz létre az áruházi adattárakban.

Az elemzési keretrendszerek a távoli áruházakban (például Data Lake Storage Gen1 és Gen2) tárolt adatmennyiséggel és metaadatokkal működnek. Tehát elméletileg a motorok elmúlóak lehetnek, és csak akkor hozhatók létre, ha a feladatokat a tárolt adatokon kell futtatni.

A teljesítmény optimalizálása érdekében azonban az elemzési keretrendszerek explicit hivatkozásokat is létrehozhatnak a távoli tárolóban tárolt fájlokra és mappákra, majd létrehozhatnak egy gyorsítótárat a tároláshoz. A távoli adatváltozások URI-ja, például egy olyan fürt, amely korábban Data Lake Storage Gen1 adatokat tárol, és most már a Data Lake Storage Gen2ban szeretné tárolni, a másolt tartalom URI-ja eltérő lesz. Ezért az adatkezelés és a metaadatok frissítése után a motorok gyorsítótárait is frissíteni kell, vagy újra kell inicializálni.

A tervezési folyamat részeként meg kell határoznia az alkalmazást, és meg kell állapítania, hogyan lehet újrainicializálni a metaadatokat, hogy azok az adatokra mutassanak, amelyek mostantól Data Lake Storage Gen2 tárolva vannak. Az alábbi útmutató a gyakran elfogadott elemzési keretrendszerek számára a frissítés lépéseinek elősegítésére.

#### <a name="azure-databricks"></a>Azure Databricks

Az Ön által választott frissítési stratégiától függően a lépések eltérhetnek. Az aktuális szakasz azt feltételezi, hogy a "Life-SHIFT" stratégiát választotta. Emellett a Data Lake Storage Gen1 fiókban található adateléréshez használt meglévő Databricks-munkaterületnek is működnie kell a Data Lake Storage Gen2 fiókba átmásolt adattal.

Először is győződjön meg arról, hogy létrehozta a Gen2-fiókot, majd átmásolta az adatok és a metaadatokat a Gen1-ről a Gen2-re egy megfelelő eszköz használatával. Ezek az eszközök az útmutató [Adatfrissítés](#data-upgrade) szakaszában találhatók.

Ezután [frissítse](https://docs.azuredatabricks.net/user-guide/clusters/index.html) a meglévő Databricks-fürtöt az Databricks Runtime 5,1-es vagy újabb verziójának használatának megkezdéséhez, amely támogatja a Data Lake Storage Gen2.

A következő lépések azon alapulnak, hogy a meglévő Databricks-munkaterület hogyan fér hozzá az Data Lake Storage Gen1-fiókban lévő adatszolgáltatásokhoz. A adl://URI-k [közvetlenül](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) a jegyzetfüzetekről vagy a [csatolási](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)-n keresztül történő meghívásával is elvégezhető.

Ha a teljes adl://URI-k megadásával közvetlenül a jegyzetfüzetekről fér hozzá, akkor az egyes jegyzetfüzeteken át kell lépnie, és módosítania kell a konfigurációt a megfelelő Data Lake Storage Gen2 URI eléréséhez.

A jövőben újra kell konfigurálnia, hogy Data Lake Storage Gen2 fiókra mutasson. Nincs szükség további módosításokra, és a jegyzetfüzeteknek a korábban is működniük kell.

Ha a többi frissítési stratégiát használja, a fenti lépések egy változatát is létrehozhatja a követelmények teljesítéséhez.

### <a name="azure-ecosystem-upgrade"></a>Azure ökoszisztéma frissítése

Ennek az útmutatónak az [Azure ökoszisztéma](#azure-ecosystem) című szakaszában ismertetett eszközöket és szolgáltatásokat úgy kell konfigurálni, hogy működjenek a Data Lake Storage Gen2.

Először is győződjön meg arról, hogy van elérhető integráció a Data Lake Storage Gen2.

Ezután meg kell változtatni a fentiekben ismertetett elemeket (például: URI és hitelesítő adatok). Módosíthatja a Data Lake Storage Gen1 használható meglévő példányt, vagy létrehozhat egy új példányt, amely együttműködik a Data Lake Storage Gen2val.

### <a name="partner-ecosystem-upgrade"></a>Partner ökoszisztémájának frissítése

Az összetevőt és az eszközöket biztosító partnerrel együttműködve gondoskodhat arról, hogy a Data Lake Storage Gen2 használatával működjenek. 

## <a name="performing-the-upgrade"></a>A frissítés végrehajtása

### <a name="pre-upgrade"></a>Frissítés előtti

Ennek részeként elvégezte volna a *frissítési készültségi szakasz értékelését* és az útmutató [Verziófrissítési szakaszának megtervezését](#planning-for-an-upgrade) , az összes szükséges információt megkapta, és létrehozott egy olyan csomagot, amely megfelel az igényeinek. Ebben a fázisban valószínűleg lesz tesztelési feladat.

### <a name="in-upgrade"></a>Frissítés folyamatban

A választott stratégiától és a megoldás bonyolultságának megfelelően ez a fázis lehet egy rövid, vagy egy kibővített, ahol több számítási feladat is vár a növekményes áthelyezésre a Data Lake Storage Gen2. Ez lesz a frissítés legkritikusabb része.

### <a name="post-upgrade"></a>Frissítés utáni

Az áttérési művelet elvégzése után a végső lépések alapos ellenőrzést tesznek elérhetővé. Ebbe beletartozik, de nem korlátozható, hogy a rendszer megbízhatóan másolta az adatátvitelt, és ellenőrizze, hogy az ACL-ek megfelelően vannak-e beállítva, és hogy a végpontok közötti folyamatok megfelelően működnek-e stb. Az ellenőrzések befejezése után mostantól kikapcsolhatja a régi folyamatokat, törölheti a forrás Data Lake Storage Gen1 fiókokat, és teljes sebességgel megtekintheti Data Lake Storage Gen2-alapú megoldásait.

## <a name="conclusion"></a>Összegzés

A jelen dokumentumban ismertetett útmutatásnak segített a megoldás frissítése Data Lake Storage Gen2 használatára. 

Ha további kérdése van, vagy visszajelzést szeretne küldeni, adja meg az alábbi megjegyzéseket, vagy küldjön visszajelzést az [Azure feedback fórumában](https://feedback.azure.com/forums/327234-data-lake).
