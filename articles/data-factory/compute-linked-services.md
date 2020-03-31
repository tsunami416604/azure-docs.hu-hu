---
title: Az Azure Data Factory által támogatott számítási környezetek
description: Ismerje meg az Azure Data Factory-folyamatokban (például az Azure HDInsightban) használható számítási környezeteket az adatok átalakításához vagy feldolgozásához.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 4545a75cc2082c21dcb87986eba819ebe39adf7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246343"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Az Azure Data Factory által támogatott számítási környezetek
Ez a cikk ismerteti a különböző számítási környezetek, amelyek segítségével az adatok feldolgozásához vagy átalakításához. Emellett a Data Factory által támogatott különböző konfigurációk (igény szerinti és saját) konfigurációk részleteit is tartalmazza, amikor összekapcsolt szolgáltatásokat állít be, amelyek ezeket a számítási környezeteket egy Azure-adat-előállítóhoz kapcsolják.

Az alábbi táblázat a Data Factory által támogatott számítási környezetek és a rajtuk futtatható tevékenységek listáját tartalmazza. 

| Számítási környezet                                          | tevékenységek                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Igény szerinti HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](#azure-hdinsight-linked-service) | [Kaptár](transform-data-using-hadoop-hive.md), [Sertés](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Egyéni](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning-tevékenységek: kötegelt végrehajtás és az erőforrás frissítése](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure machine learning-végrehajtási folyamat](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure machine learning-végrehajtási folyamat](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Tárolt eljárás](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Jegyzetfüzet](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure-függvény](#azure-function-linked-service)         | [Azure-függvény tevékenysége](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>Igény szerinti HDInsight számítási környezet
Az ilyen típusú konfiguráció, a számítástechnikai környezet teljes körűen kezeli az Azure Data Factory szolgáltatás. A Data Factory szolgáltatás automatikusan létrehozza, mielőtt egy feladatot elküldenének az adatok feldolgozásához, és a feladat befejezésekor eltávolítanák. Létrehozhat egy összekapcsolt szolgáltatást az igény szerinti számítási környezethez, konfigurálhatja és szabályozhatja a feladatvégrehajtás, a fürtkezelés és a rendszerindítási műveletek részletes beállításait.

> [!NOTE]
> Az igény szerinti konfiguráció jelenleg csak az Azure HDInsight-fürtök támogatott. Az Azure Databricks is támogatja az igény szerinti feladatok feladatfürtök használatával, további részletekért tekintse meg az [Azure databricks kapcsolódó szolgáltatás.](#azure-databricks-linked-service)

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight igény szerinti társított szolgáltatás
Az Azure Data Factory szolgáltatás automatikusan létrehozhat egy igény szerinti HDInsight-fürtöt az adatok feldolgozásához. A fürt ugyanabban a régióban jön létre, mint a fürthöz társított storage-fiók (linkedServiceName tulajdonság a JSON-ban). A tárfióknak általános célú szabványos Azure-tárfióknak kell lennie. 

Vegye figyelembe az alábbi **fontos** pontokat az igény szerinti HDInsight-hivatkozott szolgáltatással kapcsolatban:

* Az igény szerinti HDInsight-fürt az Azure-előfizetés alatt jön létre. Láthatja a fürtöt az Azure Portalon, amikor a fürt működik. 
* Az igény szerinti HDInsight-fürtön futó feladatok naplói a HDInsight-fürthöz társított tárfiókba kerülnek. A fürtfelhasználónév, clusterPassword, clusterSshUserName, fürtSshPassword a csatolt szolgáltatásdefinícióban definiált fürt segítségével jelentkezik be a fürtbe a fürt életciklusa során történő részletes hibaelhárításhoz. 
* Csak akkor kell fizetnie, amikor a HDInsight-fürt működik, és fut feladatok.
* **Parancsfájl-műveletet** használhat az Igény szerinti Azure HDInsight-szolgáltatással.  

> [!IMPORTANT]
> Az Azure HDInsight-fürt igény szerinti kiépítése általában **20 percet** vagy annál többet vesz igénybe.

### <a name="example"></a>Példa
A következő JSON egy Linux-alapú HDInsight-kapcsolt szolgáltatást határoz meg. A Data Factory szolgáltatás automatikusan létrehoz egy **Linux-alapú HDInsight-fürtöt** a szükséges tevékenység feldolgozásához. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer mindig létrehoz egy HDInsight-fürt, amikor fel kell dolgozni egy szeletet, kivéve, ha van meglévő élő fürt (**timeToLive**), majd a feldolgozás végén a rendszer törli a fürtöt. 
>
> Ahogy egyre több tevékenység fut, az Azure blob storage-ban számos tároló tetszhet. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](https://storageexplorer.com/).
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Kötelező |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A típustulajdonságot **HDInsightOnDemand (HDInsightOnDemand**) tulajdonságra kell állítani. | Igen      |
| clusterSize                  | A fürt dolgozói/adatcsomópontjainak száma. A HDInsight-fürt kétfej-csomópontossal, valamint a tulajdonsághoz megadott munkavégző csomópontok számával jön létre. A csomópontok mérete Standard_D3, amely 4 maggal rendelkezik, így egy 4 feldolgozócsomópont-fürt 24 magot vesz fel\*\*(4 4 = 16 mag a munkavégző csomópontokhoz, plusz 2 4 = 8 mag a fő csomópontokhoz). További információt a [Fürtök beállítása a HDInsightban a Hadoop, a Spark, a Kafka és egyebek](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) között. | Igen      |
| linkedServiceName            | Az Azure Storage-hoz kapcsolódó szolgáltatás, amelyet az igény szerinti fürt az adatok tárolására és feldolgozására használ. A HDInsight-fürt ugyanabban a régióban jön létre, mint ez az Azure Storage-fiók. Az Azure HDInsightban korlátozott azon magok száma, amelyek az egyes támogatott Azure-régiókban felhasználhatók. Győződjön meg arról, hogy elegendő alapkvótával rendelkezik az adott Azure-régióban a szükséges fürtméret teljesítéséhez. További részletek: [Fürtök beállítása a HDInsightban a Hadoop, a Spark, a Kafka és egyebek segítségével.](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Jelenleg nem hozhat létre olyan igény szerinti HDInsight-fürtöt, amely egy Azure Data Lake Store-t használ tárolóként. Ha a HDInsight-feldolgozásból származó eredményadatokat egy Azure Data Lake Store-ban szeretné tárolni, egy másolási tevékenység segítségével másolja az adatokat az Azure Blob Storage-ból az Azure Data Lake Store-ba. </p> | Igen      |
| fürterőforrás-csoport         | A HDInsight-fürt ebben az erőforráscsoportban jön létre. | Igen      |
| timetolive                   | Az igény szerinti HDInsight-fürt engedélyezett szabad ideje. Itt adható meg, hogy az igény szerinti HDInsight-fürt mennyi ideig maradjon életben egy tevékenységfuttatás befejezése után, ha nincs más aktív feladat a fürtben. A minimálisan megengedett érték 5 perc (00:05:00).<br/><br/>Ha például egy tevékenység futtatása 6 percet vesz igénybe, és a timetolive 5 perc, a fürt a tevékenység futtatásának 6 percután 5 percig életben marad. Ha egy másik tevékenységfuttatást hajt végre a 6 perces ablakkal, azt ugyanaz a fürt dolgozza fel.<br/><br/>Az igény szerinti HDInsight-fürt létrehozása költséges művelet (eltarthat egy ideig), ezért szükség szerint használja ezt a beállítást az adat-előállító teljesítményének javítására egy igény szerinti HDInsight-fürt újrafelhasználásával.<br/><br/>Ha az idő-élő értéket 0-ra állítja, a fürt a tevékenység futásának befejeződése után azonnal törlődik. Mivel ha magas értéket állít be, előfordulhat, hogy a fürt nem működik, hogy valamilyen hibaelhárítási célból bejelentkezzen, de ez magas költségeket eredményezhet. Ezért fontos, hogy a megfelelő értéket az igényei nek megfelelően állítsa be.<br/><br/>Ha a timetolive tulajdonság értéke megfelelően be van állítva, több folyamat is megoszthatja az igény szerinti HDInsight-fürt példányát. | Igen      |
| fürttípus                  | A létrehozandó HDInsight-fürt típusa. Az engedélyezett értékek a "hadoop" és a "szikra". Ha nincs megadva, az alapértelmezett érték hadoop. A vállalati biztonsági csomaggal engedélyezett fürt nem hozható létre igény szerint, hanem használjon [meglévő fürtöt/ saját számítást hozzon létre.](#azure-hdinsight-linked-service) | Nem       |
| version                      | A HDInsight-fürt verziója. Ha nincs megadva, akkor a jelenlegi HDInsight által definiált alapértelmezett verziót használja. | Nem       |
| hostSubscriptionId           | A HDInsight-fürt létrehozásához használt Azure-előfizetés-azonosító. Ha nincs megadva, az Azure bejelentkezési környezet előfizetési azonosítóját használja. | Nem       |
| fürtnévelőfix           | A HDI-fürt nevének előtagja, az időbélyeg automatikusan hozzáfűzi a fürtnév végén| Nem       |
| sparkVersion                 | A spark verziója, ha a fürt típusa "Spark" | Nem       |
| továbbiLinkedServiceNames | További tárfiókokat a HDInsight-csatolt szolgáltatás, hogy a Data Factory szolgáltatás regisztrálhatja őket az Ön nevében. Ezeka tárfiókok ugyanabban a régióban kell lennie, mint a HDInsight-fürt, amely ugyanabban a régióban jön létre, mint a linkedServiceName által megadott tárfiók. | Nem       |
| osType típus                       | Az operációs rendszer típusa. Megengedett értékek: Linux és Windows (csak HDInsight 3.3 esetén). Az alapértelmezett érték a Linux. | Nem       |
| hcatalogLinkedServiceName    | Az Azure SQL-hez csatolt szolgáltatás neve, amely a HCatalog-adatbázisra mutat. Az igény szerinti HDInsight-fürt az Azure SQL-adatbázis metatárolóként való használatával jön létre. | Nem       |
| connectVia                   | A tevékenységek e HDInsight-kapcsolt szolgáltatásba történő küldéséhez használt integrációs futásidő. Igény szerinti HDInsight-kapcsolt szolgáltatás esetén csak az Azure Integration Runtime-ot támogatja. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem       |
| fürtfelhasználónév                   | A fürt eléréséhez. | Nem       |
| clusterPassword                   | A fürt eléréséhez biztonságos karakterlánc típusú jelszó. | Nem       |
| clusterSshUserName         | Az SSH felhasználóneve távolról csatlakozik a fürt csomópontjához (Linux esetén). | Nem       |
| clusterSshPassword         | A jelszó típusú biztonságos karakterlánc SSH távolról csatlakozni fürt csomópont (Linux). | Nem       |
| scriptActions | Parancsfájl megadása a [HDInsight-fürt testreszabásához](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) az igény szerinti fürt létrehozása során. <br />Jelenleg az Azure Data Factory felhasználói felülete készítő eszköz támogatja a csak 1 parancsfájl-művelet megadását, de ezt a korlátozást a JSON-ban is áttudja venni (több parancsfájlműveletet adhat meg a JSON-ban). | Nem |


> [!IMPORTANT]
> A HDInsight több Hadoop-fürttelepítést is támogat, amelyek telepíthetők. Minden verzióválasztás létrehoz egy adott verziót a Hortonworks Data Platform (HDP) disztribúcióból, és az adott disztribúcióban található összetevők készletét. A támogatott HDInsight-verziók listája folyamatosan frissül, hogy a legújabb Hadoop ökoszisztéma-összetevőket és javításokat biztosítson. Győződjön meg arról, hogy mindig a [támogatott HDInsight-verzió és az operációsrendszer-típus](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) legújabb információira hivatkozik, hogy biztosan a HDInsight támogatott verzióját használja. 
>
> [!IMPORTANT]
> Jelenleg a HDInsight-hivatkozott szolgáltatások nem támogatják a HBase, Interactive Query (Hive LLAP), Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>továbbiLinkedServiceNames JSON példa

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az igény szerinti HDInsight-csatolt szolgáltatás egyszerű hitelesítést igényel a HDInsight-fürtök létrehozásához az Ön nevében. A szolgáltatás egyszerű hitelesítés, regisztráljon egy alkalmazásentitást az Azure Active Directoryban (Azure AD), és adja meg az előfizetés **közreműködői** szerepkört vagy az erőforráscsoportot, amelyben a HDInsight-fürt jön létre. A részletes lépésekért olvassa el a [Portál használata az erőforrásokhoz hozzáférő Azure Active Directory-alkalmazások és egyszerű szolgáltatás létrehozása című témakört.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

- Alkalmazásazonosító
- Alkalmazáskulcs 
- Bérlőazonosító

A szolgáltatásegyszerű hitelesítés használata a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Kötelező |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Adja meg az alkalmazás ügyfélazonosítóját.     | Igen      |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsát.           | Igen      |
| **Bérlő**              | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | Igen      |

### <a name="advanced-properties"></a>Speciális tulajdonságok

Az igény szerinti HDInsight-fürt részletes konfigurációjához a következő tulajdonságokat is megadhatja.

| Tulajdonság               | Leírás                              | Kötelező |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration (magkonfiguráció)      | Megadja a létrehozandó HDInsight-fürt alapvető konfigurációs paramétereit (a core-site.xml fájlhoz. | Nem       |
| hBaseConfiguration     | Megadja a HDInsight-fürt HBase konfigurációs paramétereit (hbase-site.xml). | Nem       |
| hdfsConfiguration      | Megadja a HDInsight-fürt HDFS konfigurációs paramétereit (hdfs-site.xml). | Nem       |
| struktúrakonfiguráció      | Megadja a HIV-konfigurációs paramétereket (hive-site.xml) a HDInsight-fürthöz. | Nem       |
| mapReduceConfiguration | Megadja a HDInsight-fürt MapReduce konfigurációs paramétereit (mapred-site.xml). | Nem       |
| oozieConfiguration     | Megadja a HDInsight-fürt Oozie konfigurációs paramétereit (oozie-site.xml). | Nem       |
| viharConfiguration     | Megadja a Storm konfigurációs paramétereit (storm-site.xml) a HDInsight-fürthöz. | Nem       |
| fonalkonfiguráció      | Megadja a HDInsight-fürt fonalkonfigurációs paramétereit (fonal-site.xml). | Nem       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Példa – Igény szerinti HDInsight-fürtkonfiguráció speciális tulajdonságokkal

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Csomópontméretek
A fej-, adat- és állattartócsomópontok méretét a következő tulajdonságok használatával adhatja meg: 

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| headNodeMéret      | A fejcsomópont méretét adja meg. Az alapértelmezett érték: Standard_D3. A **részleteket a Csomópontméretek megadása** című szakaszban találja. | Nem       |
| dataNodeSize (dataNodeSize)      | Megadja az adatcsomópont méretét. Az alapértelmezett érték: Standard_D3. | Nem       |
| zookeeperNodeMéret | Az Állatkert üzembentartója csomópont méretét adja meg. Az alapértelmezett érték: Standard_D3. | Nem       |

#### <a name="specifying-node-sizes"></a>Csomópontméretek megadása
Tekintse meg a [virtuális gépek méretei](../virtual-machines/linux/sizes.md) cikket az előző szakaszban említett tulajdonságokhoz megadandó karakterláncértékekről. Az értékeknek meg kell felelniük a cikkben hivatkozott **CMDLET-knek & API-knak.** Mint látható a cikkben, az adatcsomópont nagy (alapértelmezett) méretű 7 GB-os memória, amely nem elég jó a forgatókönyvhöz. 

Ha D4 méretű fejcsomópontokat és munkavégző csomópontokat szeretne létrehozni, adja meg **a Standard_D4** a headNodeSize és a dataNodeSize tulajdonságok értékeként. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ha rossz értéket ad meg ezekhez a tulajdonságokhoz, a következő hibaüzenet jelenhet **meg:** Nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. A fürt állapota: „hiba”. Üzenet: "PreClusterCreationValidationFailure". A hibaüzenet megjelenésekor győződjön meg arról, hogy a **CMDLET & APIS-nevet** használja a [Virtuális gépek méretei](../virtual-machines/linux/sizes.md) cikk táblájából.        

## <a name="bring-your-own-compute-environment"></a>Hozza magával saját számítási környezetét
Az ilyen típusú konfigurációban a felhasználók regisztrálhatnak egy már meglévő számítástechnikai környezetet csatolt szolgáltatásként a Data Factory-ban. A számítási környezetet a felhasználó kezeli, és a Data Factory szolgáltatás használja a tevékenységek végrehajtásához.

Az ilyen típusú konfiguráció a következő számítási környezetekben támogatott:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight-kapcsolt szolgáltatás
Létrehozhat egy Azure HDInsight-kapcsolt szolgáltatást, amely regisztrálja saját HDInsight-fürtjeit a Data Factory szolgáltatással.

### <a name="example"></a>Példa

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                                                  | Kötelező |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | A típustulajdonságot **HDInsight (HDInsight**) tulajdonságra kell állítani.            | Igen      |
| clusterUri        | A HDInsight-fürt URI-ja.                            | Igen      |
| felhasználónév          | Adja meg a meglévő HDInsight-fürthöz való csatlakozáshoz használandó felhasználó nevét. | Igen      |
| jelszó          | Adja meg a felhasználói fiók jelszavát.                       | Igen      |
| linkedServiceName | Az Azure Storage-hoz kapcsolódó szolgáltatás neve, amely a HDInsight-fürt által használt Azure blob storage hivatkozik. <p>Jelenleg nem adhat meg Azure Data Lake Store-hoz kapcsolt szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Store-hoz, az Azure Data Lake Store-ban lévő adatokhoz hozzáférhet a Hive/Pig parancsfájlokból. </p> | Igen      |
| isEspEnabled      | Adja meg a '*true*' értéket, ha a HDInsight-fürt [enterprise security package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) engedélyezve van. Az alapértelmezett érték : "*hamis*". | Nem       |
| connectVia        | A tevékenységek e kapcsolt szolgáltatásba történő elküldéséhez használt integrációs futásidő. Használhatja az Azure-integrációs futásidejű vagy saját üzemeltetésű integrációs runtime. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. <br />Az Enterprise Security Package (ESP) engedélyezett HDInsight-fürt használata saját üzemeltetésű integrációs futásidejű, amely egy látótávolság a fürt, vagy azt kell telepíteni ugyanabban a virtuális hálózaton belül, mint az ESP HDInsight-fürt. | Nem       |

> [!IMPORTANT]
> A HDInsight több Hadoop-fürttelepítést is támogat, amelyek telepíthetők. Minden verzióválasztás létrehoz egy adott verziót a Hortonworks Data Platform (HDP) disztribúcióból, és az adott disztribúcióban található összetevők készletét. A támogatott HDInsight-verziók listája folyamatosan frissül, hogy a legújabb Hadoop ökoszisztéma-összetevőket és javításokat biztosítson. Győződjön meg arról, hogy mindig a [támogatott HDInsight-verzió és az operációsrendszer-típus](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) legújabb információira hivatkozik, hogy biztosan a HDInsight támogatott verzióját használja. 
>
> [!IMPORTANT]
> Jelenleg a HDInsight-hivatkozott szolgáltatások nem támogatják a HBase, Interactive Query (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch-alapú szolgáltatás

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Létrehozhat egy Azure Batch-alapú szolgáltatást a virtuális gépek (VM-ek) batch készletének regisztrálásához egy adat-előállítóba. Egyéni tevékenység et futtathat az Azure Batch használatával.

Tekintse meg a következő cikkeket, ha most jön az Azure Batch szolgáltatás:

* [Az Azure Batch alapjai](../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintéséhez.
* [New-AzBatchAccount-parancsmag](/powershell/module/az.batch/New-azBatchAccount) egy Azure Batch-fiók (vagy) [Azure Portal](../batch/batch-account-create-portal.md) létrehozásához az Azure Batch-fiók az Azure Portal használatával. A parancsmag használatával kapcsolatos részletes útmutatásért olvassa el a PowerShell használata az [Azure Batch-fiók](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) kalkulált cikkét.
* [New-AzBatchPool-parancsmag](/powershell/module/az.batch/New-AzBatchPool) az Azure Batch-készlet létrehozásához.

### <a name="example"></a>Példa

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| type              | A típustulajdonságot **Az AzureBatch beállításra**kell állítani. | Igen      |
| accountName       | Az Azure Batch-fiók neve.         | Igen      |
| accessKey (accessKey)         | Hozzáférési kulcs az Azure Batch-fiókhoz.  | Igen      |
| batchUri          | URL-címet az Azure Batch-fiókhoz, https://*batchaccountname.region*.batch.azure.com formátumban. | Igen      |
| készletneve          | A virtuális gépek készletének neve.    | Igen      |
| linkedServiceName | Az Azure Storage-hoz csatolt szolgáltatás neve ehhez az Azure Batch-hez csatolt szolgáltatáshoz társítva. Ez a csatolt szolgáltatás a tevékenység futtatásához szükséges átmeneti fájlokhoz használatos. | Igen      |
| connectVia        | A tevékenységek e kapcsolt szolgáltatásba történő elküldéséhez használt integrációs futásidő. Használhatja az Azure-integrációs futásidejű vagy saját üzemeltetésű integrációs runtime. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem       |

## <a name="azure-machine-learning-studio-linked-service"></a>Azure Machine Learning Studio csatolt szolgáltatás
Hozzon létre egy Azure Machine Learning Studio kapcsolt szolgáltatás egy Machine Learning kötegpontozási végpont regisztrálásához egy adat-előállító.

### <a name="example"></a>Példa

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság               | Leírás                              | Kötelező                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Típus                   | A típustulajdonságot a következőre kell állítani: **AzureML**. | Igen                                      |
| mlVégpont             | A kötegelt pontozási URL-cím.                   | Igen                                      |
| apiKey (apiKey)                 | A közzétett munkaterületi modell API-ja.     | Igen                                      |
| updateResourceEndpoint | Az Azure Machine Learning webszolgáltatás végpontjának erőforrás-frissítése url-címe a prediktív webszolgáltatás betanított modellfájllal történő frissítéséhez | Nem                                       |
| servicePrincipalId     | Adja meg az alkalmazás ügyfélazonosítóját.     | FrissítésResourceEndpoint megadása esetén szükséges |
| servicePrincipalKey    | Adja meg az alkalmazás kulcsát.           | FrissítésResourceEndpoint megadása esetén szükséges |
| Bérlő                 | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | FrissítésResourceEndpoint megadása esetén szükséges |
| connectVia             | A tevékenységek e kapcsolt szolgáltatásba történő elküldéséhez használt integrációs futásidő. Használhatja az Azure-integrációs futásidejű vagy saját üzemeltetésű integrációs runtime. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning csatolt szolgáltatás
Hozzon létre egy Azure Machine Learning-alapú szolgáltatást, amely összeköti az Azure Machine Learning-munkaterületet egy adat-előállítóval.

> [!NOTE]
> Jelenleg csak a szolgáltatás egyszerű hitelesítés támogatott az Azure Machine Learning csatolt szolgáltatás.

### <a name="example"></a>Példa

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság               | Leírás                              | Kötelező                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Típus                   | A típustulajdonságot a következőre kell állítani: **AzureMLService**. | Igen                                      |
| subscriptionId         | Azure-előfizetés azonosítója              | Igen                                      |
| resourceGroupName      | név | Igen                                      |
| mlWorkspaceName        | Az Azure Machine Learning munkaterületének neve | Igen  |
| servicePrincipalId     | Adja meg az alkalmazás ügyfélazonosítóját.     | Nem |
| servicePrincipalKey    | Adja meg az alkalmazás kulcsát.           | Nem |
| Bérlő                 | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | FrissítésResourceEndpoint megadása esetén szükséges | Nem |
| connectVia             | A tevékenységek e kapcsolt szolgáltatásba történő elküldéséhez használt integrációs futásidő. Használhatja az Azure-integrációs futásidejű vagy saját üzemeltetésű integrációs runtime. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem |    

## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics kapcsolt szolgáltatása
Hozzon létre egy **Azure Data Lake Analytics-alapú** szolgáltatást, amely egy Azure Data Lake Analytics-számítási szolgáltatást kapcsol össze egy Azure-adat-előállítóval. A Data Lake Analytics U-SQL tevékenység a folyamatban hivatkozik erre a csatolt szolgáltatás. 

### <a name="example"></a>Példa

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Tulajdonságok

| Tulajdonság             | Leírás                              | Kötelező                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | A típustulajdonságot a következőre kell állítani: **AzureDataLakeAnalytics**. | Igen                                      |
| accountName          | Azure Data Lake Analytics-fiók neve.  | Igen                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI.           | Nem                                       |
| subscriptionId       | Azure-előfizetés azonosítója                    | Nem                                       |
| resourceGroupName    | Azure-erőforráscsoport neve                | Nem                                       |
| servicePrincipalId   | Adja meg az alkalmazás ügyfélazonosítóját.     | Igen                                      |
| servicePrincipalKey  | Adja meg az alkalmazás kulcsát.           | Igen                                      |
| Bérlő               | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | Igen                                      |
| connectVia           | A tevékenységek e kapcsolt szolgáltatásba történő elküldéséhez használt integrációs futásidő. Használhatja az Azure-integrációs futásidejű vagy saját üzemeltetésű integrációs runtime. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks kapcsolt szolgáltatás
**Azure Databricks csatolt szolgáltatás** regisztrálásához Databricks munkaterület, amely a Databricks számítási feladatok (notebook, jar, python) futtatásához használható databricks munkaterületet. 
> [!IMPORTANT]
> A Databricks kapcsolt szolgáltatások támogatják [a példánykészleteket.](https://aka.ms/instance-pools) 

### <a name="example---using-new-job-cluster-in-databricks"></a>Példa – Új feladatfürt használata a Databricks-ben

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Példa – Meglévő interaktív fürt használata a Databricks-ben

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Tulajdonságok

| Tulajdonság             | Leírás                              | Kötelező                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| név                 | A csatolt szolgáltatás neve               | Igen   |
| type                 | A típus tulajdonságot a következőre kell állítani: **Azure Databricks**. | Igen                                      |
| domain               | Adja meg ennek megfelelően az Azure-régiót a Databricks munkaterület régiója alapján. Például: https://eastus.azuredatabricks.net | Igen                                 |
| accessToken          | Hozzáférési jogkivonat szükséges a Data Factory az Azure Databricks hitelesítéséhez. Hozzáférési jogkivonatot kell generálni a databricks munkaterületről. A hozzáférési jogkivonat megtalálásának részletesebb lépései [itt](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token) találhatók  | Igen                                       |
| existingClusterId    | Egy meglévő fürt fürtazonosítója az összes feladat futtatásához ezen. Ennek egy már létrehozott interaktív fürtnek kell lennie. Előfordulhat, hogy manuálisan kell újraindítania a fürtöt, ha nem válaszol. Databricks javasoljuk, hogy a feladatok futtatása az új fürtök önnagyobb megbízhatóság érdekében. A Databricks-munkaterületen található interaktív fürt fürtazonosítója > fürt -> interaktív fürtnév -> konfiguráció -> címkék. [További részletek](https://docs.databricks.com/user-guide/clusters/tags.html) | Nem 
| instancePoolId    | Egy databricks munkaterület meglévő készletpéldánykészlet-azonosítója.  | Nem  |
| newClusterVersion    | A fürt Spark-verziója. Létrehoz egy feladatfürtet a databricks.It will create a job cluster in databricks. | Nem  |
| newClusterNumOfWorker| A fürthöz vezető munkavégző csomópontok száma. A fürt rendelkezik egy Spark-illesztőprogram és num_workers végrehajtók összesen num_workers + 1 Spark-csomópontok. Az Int32 formátumú karakterláncok, például az "1" azt jelenti, hogy a numOfWorker értéke 1 vagy "1:10": automatikus skálázás 1-től perc, 10 pedig max értékként.  | Nem                |
| newClusterNodeType típus   | Ez a mező egyetlen értéken keresztül kódolja a fürt minden egyes Spark-csomópontja számára elérhető erőforrásokat. Például a Spark-csomópontok kiépíthetők és optimalizálhatók memória- vagy nagy számítási feladatokhoz. Ez a mező szükséges az új fürthöz                | Nem               |
| újClusterSparkConf  | opcionális, felhasználó által megadott Spark-konfigurációs kulcs-érték párok készlete. A felhasználók is át egy sor extra JVM-beállítások az illesztőprogram és a végrehajtók keresztül spark.driver.extraJavaOptions és spark.executor.extraJavaOptions, illetve spark.executor.extraJavaOptions keresztül. | Nem  |
| newClusterInitScripts| választható, felhasználó által definiált inicializálási parancsfájlok készlete az új fürthöz. Az init parancsfájlok DBFS elérési útjának megadása. | Nem  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás
Hozzon létre egy Azure SQL-kapcsolt szolgáltatást, és használja azt a [tárolt eljárás tevékenység](transform-data-using-stored-procedure.md) meghívására egy tárolt eljárást egy Data Factory-folyamat. Az [Azure SQL Connector](connector-azure-sql-database.md#linked-service-properties) cikke a hivatkozott szolgáltatással kapcsolatos részletekért.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse kapcsolt szolgáltatás
Hozzon létre egy Azure SQL Data Warehouse kapcsolt szolgáltatást, és használja azt a [tárolt eljárás tevékenység](transform-data-using-stored-procedure.md) meghívására tárolt eljárást egy Data Factory-folyamat. A csatolt szolgáltatással kapcsolatos részletekért tekintse meg az [Azure SQL Data Warehouse Connector](connector-azure-sql-data-warehouse.md#linked-service-properties) cikkét.

## <a name="sql-server-linked-service"></a>SQL Server csatolt szolgáltatás
Hozzon létre egy SQL Server csatolt szolgáltatást, és használja azt a [tárolt eljárás tevékenység](transform-data-using-stored-procedure.md) meghívására tárolt eljárás egy Data Factory folyamat. A csatolt szolgáltatással kapcsolatos részleteket az [SQL Server-összekötő](connector-sql-server.md#linked-service-properties) ről szóló cikkben találja.

## <a name="azure-function-linked-service"></a>Azure-függvényhez csatolt szolgáltatás
Hozzon létre egy Azure-függvényhez kapcsolódó szolgáltatást, és használja azt az [Azure-függvény tevékenységhasználatával](control-flow-azure-function-activity.md) azure-függvények egy data factory folyamat. Az Azure függvény visszatérési típusának `JObject`érvényesnek kell lennie. (Ne feledje, hogy a `JObject` [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *nem* .) A sikertelenektől `JObject` eltérő visszatérési típus nem felel meg, és a felhasználói hiba *választartalma nem érvényes JObject*objektum.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| type   | A típustulajdonságot a következőre kell állítani: **AzureFunction** | igen |
| függvény alkalmazás url-címe | Az Azure Függvényalkalmazás URL-címe. A `https://<accountname>.azurewebsites.net`formátum a . Ez az URL-cím az **URL-cím** alatt található érték, amikor a Függvényalkalmazást az Azure Portalon tekinti meg.  | igen |
| funkcióbillentyű | Hozzáférési kulcs az Azure-függvényhez. Kattintson a megfelelő funkció **Manage** szakaszára, és másolja a **Függvénykulcsot** vagy a **Gazdakulcsot**. További információ: [Az Azure Functions HTTP-eseményindítók és kötések](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | igen |
|   |   |   |

## <a name="next-steps"></a>További lépések
Az Azure Data Factory által támogatott átalakítási tevékenységek listáját az Adatok átalakítása című témakörben [található.](transform-data.md)
