---
title: Azure Data Factory által támogatott környezetek számítási |} Microsoft Docs
description: További információk a számítási környezetek esetén, amelyek segítségével az Azure Data Factory folyamatok (például az Azure HDInsight)-átalakítási és folyamat-adatokat.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: fe4a4962acce06a6448cef8d5c1af398e3965a33
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Számítási környezetek Azure Data Factory által támogatott
Ez a cikk ismerteti a különböző számítási környezeteket, melyekkel folyamat vagy átalakítási adatok. Emellett biztosítja az adat-előállító támogatott, ha ezek linking összekapcsolt szolgáltatások konfigurálása (igény szerinti és kapcsolja a saját) különböző konfigurációkkal kapcsolatos részletek számítási környezetek számára egy Azure data factory.

A következő táblázat felsorolja a Data Factory és az ezeken futó tevékenységek által támogatott számítási környezetek. 

| Számítási környezet                      | tevékenységek                               |
| ---------------------------------------- | ---------------------------------------- |
| [Igény szerinti HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streamelési](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [Egyéni](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-tevékenységek: kötegelt végrehajtás és erőforrás frissítése](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Tárolt eljárás](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Igény szerinti számítási környezet
Az ilyen típusú konfigurációs a számítógépes környezet teljes kezeli az Azure Data Factory szolgáltatásnak. Az automatikusan hozta létre a Data Factory szolgáltatásnak egy feladat folyamata adatokat küldött, és eltávolítja a feladat befejezése előtt. Csatolt szolgáltatást az igény szerinti számítási környezetet hozhat létre, konfigurálja és feladatok végrehajtásának, a kiszolgálófürt-felügyelet és a műveletek rendszerindítása részletes beállításait.

> [!NOTE]
> Az igény szerinti konfigurációs jelenleg csak az Azure HDInsight-fürtök támogatott.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Az Azure HDInsight igény társított szolgáltatás
Az Azure Data Factory szolgáltatásnak automatikusan adatfeldolgozásra történő igény szerinti HDInsight-fürtöt hozhat létre. A fürt létrehozása a tárfiók (linkedServiceName tulajdonságot a JSON-ban) a fürthöz tartozó ugyanabban a régióban. A tárfiók egy általános célú standard Azure-tárfiókot kell lennie. 

Vegye figyelembe a következőket **fontos** kapcsolatos igény szerinti HDInsight pontok társított szolgáltatás:

* Az igény szerinti HDInsight-fürt létrehozása az Azure-előfizetéshez tartozó. Biztos, hogy a fürt az Azure-portálon láthatók, ha a fürt működik-e és fut. 
* A naplók az igény szerinti HDInsight-fürtök a futó feladatok a tárfiókot, a HDInsight-fürthöz társított lesz másolva. A clusterUserName, clusterPassword, clusterSshUserName, a társított szolgáltatás definíciójának definiált clusterSshPassword használatával jelentkezzen be a fürt részletes hibaelhárítási a fürt az életciklus során. 
* Van szó, csak a az időpontot, amikor a HDInsight-fürt mentése és a futó feladatok.

> [!IMPORTANT]
> Általában tart **20 perc** vagy nagyobb az igény szerinti Azure HDInsight-fürtök kiépítéséhez.
>
> 

### <a name="example"></a>Példa
A következő JSON igény kapcsolódó HDInsight Linux-alapú szolgáltatás határozza meg. A Data Factory szolgáltatásnak automatikusan létrehoz egy **Linux-alapú** HDInsight-fürt tevékenység feldolgozni. 

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
> További tevékenység fut, mint az Azure blob storage számos tárolók látható. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](http://storageexplorer.com/).
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Szükséges |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A type tulajdonságot kell megadni **HDInsightOnDemand**. | Igen      |
| clusterSize                  | A fürt munkavégző/adatok csomópontok száma. A HDInsight-fürt együtt ez a tulajdonság a megadott munkavégző csomópontok száma 2 átjárócsomópontokkal hozza létre. A csomópontok egy 4 munkavégző csomópontot tartalmazó fürtben veszi 24 mag, 4 mag, rendelkező standard, D3 méretű vannak (4\*a munkavégző csomópontokról, valamint 2 processzormag, 4 = 16\*az átjárócsomópontokkal processzormag, 4 = 8). Lásd: [állítsa be a HDInsight Hadoop, Spark, Kafka és több fürt](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) részleteiről. | Igen      |
| linkedServiceName            | Az Azure tárolás társított szolgáltatásának történő tárolására és feldolgozására adatok az igény szerinti fürt által használható. A HDInsight-fürt létrehozása az Azure Storage-fiók ugyanabban a régióban. Az Azure HDInsightban korlátozott azon magok száma, amelyek az egyes támogatott Azure-régiókban felhasználhatók. Győződjön meg arról, hogy elegendő core kvóták az adott Azure-régió, hogy megfeleljen a szükséges nagyobbnak. További információkért tekintse meg [hdinsight Hadoop, Spark, Kafka és több fürt beállítása](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Jelenleg nem hozható létre, amely egy Azure Data Lake Store használ a tárolási igény szerinti HDInsight-fürtöt. Ha szeretné tárolni az eredményadatok a HDInsight-feldolgozás alatt álló egy Azure Data Lake Store-ból, a másolási tevékenység segítségével az adatok másolása az Azure Blob Storage-ból az Azure Data Lake Store. </p> | Igen      |
| clusterResourceGroup         | A HDInsight-fürt létrehozása az erőforráscsoportban. | Igen      |
| timetolive                   | A megengedett üresjárati idő az igény szerinti HDInsight-fürthöz. Meghatározza, mennyi ideig az igény szerinti HDInsight-fürt aktív marad egy tevékenység fut, ha nincsenek a fürt más aktív feladatok befejezése után. A minimális megengedett érték érték 5 perc (00: 05:00).<br/><br/>Például ha egy tevékenység futott 6 percig tart, és az élettartam értéke 5 perc, a fürt marad, a figyelő életben 5 perc, a 6 percnél feldolgozásának a tevékenység futtatása után. Ha egy másik tevékenységfuttatási 6-perc időkeretet, dolgoz fel ugyanabban a fürtben.<br/><br/>Igény szerinti HDInsight fürtök létrehozásával egy (igénybe vehet) drága művelet, ezt a beállítást, mint egy adat-előállító teljesítményének javításával újból felhasználja az igény szerinti HDInsight-fürtök által szükséges Igen használja.<br/><br/>A TimeToLive tulajdonság értékét 0-ra állítja be, ha törölni a fürtöt, amint a tevékenység futtatása befejeződött. Mivel ha a magas érték, a fürt néhány hibaelhárítási bejelentkezést inaktív felfüggesztheti a céllal, de az eredményezhet nagy költségek. Ezért fontos, hogy beállította-e a megfelelő értéket a igényei szerint.<br/><br/>A timetolive tulajdonság értékének megfelelően van beállítva, ha több folyamatok megoszthatja az igény szerinti HDInsight-fürt példányának. | Igen      |
| clusterType                  | A HDInsight-fürtöt létrehozni típusa. Megengedett értékek: "hadoop" és "külső". Ha nincs megadva, az alapértelmezett érték: hadoop. | Nem       |
| verzió:                      | A HDInsight-fürt verziószáma. Ha nincs megadva, az aktuális HDInsight meghatározott alapértelmezett verzióját használja. | Nem       |
| hostSubscriptionId           | A HDInsight-fürt létrehozásához használt Azure-előfizetése Azonosítóját. Ha nincs megadva, akkor használja az Azure bejelentkezési környezet előfizetés-azonosítója. | Nem       |
| clusterNamePrefix           | Az előtag HDI-fürt nevét, egy Timestamp típusú automatikusan hozzá lesznek fűzve a fürt nevének végén| Nem       |
| sparkVersion                 | Ha a fürt típusa "Külső" spark verziója | Nem       |
| additionalLinkedServiceNames | Adja meg a további tárfiókok a HDInsight a társított szolgáltatás, hogy a Data Factory szolgáltatásnak is regisztrálja őket az Ön nevében. Ezekre a tárfiókokra a HDInsight-fürthöz, és a linkedServiceName által megadott tárfiók ugyanabban a régióban létrehozott ugyanabban a régióban kell lennie. | Nem       |
| osType                       | Az operációs rendszer típusát. Két érték engedélyezett: a Linux és Windows (a HDInsight 3.3 csak). Alapértelmezett érték a Linux. | Nem       |
| hcatalogLinkedServiceName    | A név az Azure SQL társított szolgáltatásnak, mutasson az HCatalog-adatbázisra. Az igény szerinti HDInsight-fürt létrehozása az Azure SQL database segítségével a metaadattárhoz. | Nem       |
| connectVia                   | Az integrációs futásidejű átirányítani a HDInsight a tevékenységek használt társított szolgáltatás. Igény szerinti HDInsight kapcsolódó szolgáltatás csak a támogatott Azure integrációs futásidejű. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem       |
| clusterUserName                   | A felhasználónév, a fürt eléréséhez. | Nem       |
| clusterPassword                   | A biztonságos karakterláncot kell megadnia a fürt eléréséhez típusú jelszót. | Nem       |
| clusterSshUserName         | Az SSH felhasználónév távolról csatlakoztatni a fürt csomópont (a Linux). | Nem       |
| clusterSshPassword         | A jelszót a biztonságos karakterláncot SSH típusú fürt csomópont távolról csatlakozzon (a Linux). | Nem       |


> [!IMPORTANT]
> HDInsight Hadoop fürt több verziója, amely telepíthető támogatja. Minden egyes verzió choice hoz létre, egy adott verziójához a Hortonworks Data Platform (HDP) telepítési és összetevők belüli, hogy a terjesztési. A HDInsight támogatott verziók listáját tartja a legújabb Hadoop ökoszisztémájának összetevőit és javításokat frissítése során. Győződjön meg arról, hogy a legfrissebb információk mindig hivatkozik [támogatott HDInsight-verzió és az operációs rendszer típusa](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) annak érdekében, hogy a HDInsight támogatott verzióját használja. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON example

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Az On-Demand HDInsight kapcsolódó szolgáltatás egy szolgáltatás egyszerű hitelesítést a nevünkben a HDInsight-fürtök létrehozásához szükséges. Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és adja meg azt a **közreműködő** szerepkört az előfizetés vagy az erőforráscsoport, amelyben a HDInsight-fürt létrehozása. Részletes útmutató: [használata portálon hozzon létre egy Azure Active Directory erőforrásokat elérő alkalmazás és szolgáltatás egyszerű](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:

- Alkalmazásazonosító
- Alkalmazás kulcs 
- Bérlőazonosító

Szolgáltatás egyszerű hitelesítés használatára a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Szükséges |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Adja meg az alkalmazás ügyfél-azonosítót.     | Igen      |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsot.           | Igen      |
| **Bérlői**              | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen      |

### <a name="advanced-properties"></a>A Speciális tulajdonságok

Az alábbi tulajdonságokat az igény szerinti HDInsight-fürt részletes konfigurációs is megadható.

| Tulajdonság               | Leírás                              | Szükséges |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Meghatározza a fő konfigurációs paramétereket (ahogy a core-site.xml) a HDInsight-fürtöt létrehozni. | Nem       |
| hBaseConfiguration     | Meghatározza a HBase konfigurációs paramétereket (a hbase-site.xml) a HDInsight-fürthöz. | Nem       |
| hdfsConfiguration      | Meghatározza a HDFS konfigurációs paramétereket (hdfs-site.xml) a HDInsight-fürthöz. | Nem       |
| hiveConfiguration      | Meghatározza a hive konfigurációs paramétereket (hive-site.xml) a HDInsight-fürthöz. | Nem       |
| mapReduceConfiguration | Meghatározza a MapReduce konfigurációs paramétereket (mapred-site.xml) a HDInsight-fürthöz. | Nem       |
| oozieConfiguration     | Meghatározza a Oozie konfigurációs paramétereket (oozie-site.xml) a HDInsight-fürthöz. | Nem       |
| stormConfiguration     | Meghatározza a Storm konfigurációs paramétereket (a storm-site.xml) a HDInsight-fürthöz. | Nem       |
| yarnConfiguration      | Meghatározza a Yarn konfigurációs paramétereket (yarn-site.xml) a HDInsight-fürthöz. | Nem       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>A Speciális tulajdonságok igény szerinti HDInsight-fürt konfiguráció – – példa

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

### <a name="node-sizes"></a>Csomópont-méretek
A méret, head, az adatok és a zookeeper csomópontok tulajdonságok a következők adhatók meg: 

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Meghatározza az átjárócsomópont méretét. Az alapértelmezett érték: Standard, D3. Tekintse meg a **csomópont méretét megadó** című szakaszban talál információt. | Nem       |
| dataNodeSize      | Az adatcsomóponton méretét határozza meg. Az alapértelmezett érték: Standard, D3. | Nem       |
| zookeeperNodeSize | Az itt üzemeltetőjének csomópont méretét adja meg. Az alapértelmezett érték: Standard, D3. | Nem       |

#### <a name="specifying-node-sizes"></a>Csomópont méret megadása
Tekintse meg a [virtuálisgép-méretek](../virtual-machines/linux/sizes.md) meg kell adnia az előző szakaszban említett tulajdonságok a karakterlánc-értékek a cikkben találhat. Az értékeket meg kell felelniük a a **parancsmagok & API-k** a cikk hivatkozik. Ahogy látja, a cikkben, az adatok (alapértelmezett) nagy méretű fürtcsomópont 7 GB memória, amely nem lehet adott esetben elég jó. 

Ha szeretne létrehozni, D4 méretű átjárócsomópontokkal és feldolgozó csomópontokat, adja meg **standard szintű, D4** headNodeSize és dataNodeSize tulajdonság értéke. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ezeket a tulajdonságokat a hibás érték megadása esetén is megjelenhet a következő **hiba:** nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. A fürt állapota: „hiba”. Üzenet: "PreClusterCreationValidationFailure". Ha ez a hibaüzenet jelenik meg, győződjön meg arról, hogy használja a **PARANCSMAG & API-k** nevét a táblázatból a [virtuálisgép-méretek](../virtual-machines/linux/sizes.md) cikk.        

## <a name="bring-your-own-compute-environment"></a>Kapcsolja a saját számítási környezet
Az ilyen típusú konfigurációs a felhasználók regisztrálhatják egy már meglévő informatikai környezete a Data Factory kapcsolt szolgáltatásként. A felhasználó a számítógépes környezet kezeli, és a Data Factory szolgáltatásnak a tevékenységek végrehajtásához használja.

Ez a fajta konfiguráció a következő számítási környezetek esetén támogatott:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Az Azure HDInsight társított szolgáltatás
Létrehozhat saját HDInsight-fürt regisztrálni a Data Factory kapcsolt Azure HDInsight szolgáltatásnak.

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
| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| type              | A type tulajdonságot kell megadni **HDInsight**. | Igen      |
| clusterUri        | Az URI-je a HDInsight-fürthöz.        | Igen      |
| felhasználónév          | Adja meg a felhasználó egy meglévő HDInsight-fürtre való kapcsolódáshoz használt nevét. | Igen      |
| jelszó          | Adja meg a felhasználói fiók jelszavát.   | Igen      |
| linkedServiceName | Az Azure blob storage a HDInsight-fürt által használt az Azure Storage társított szolgáltatás neve. <p>Jelenleg nem adhat meg egy Azure Data Lake Store társított szolgáltatás ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Store, előfordulhat, hogy éri az az Azure Data Lake Store Hive/Pig-parancsfájlok. </p> | Igen      |
| connectVia        | A szolgáltatásnak a tevékenységek átirányítani használandó integrációs futásidejű. Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű is használhatja. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem       |

> [!IMPORTANT]
> HDInsight Hadoop fürt több verziója, amely telepíthető támogatja. Minden egyes verzió choice hoz létre, egy adott verziójához a Hortonworks Data Platform (HDP) telepítési és összetevők belüli, hogy a terjesztési. A HDInsight támogatott verziók listáját tartja a legújabb Hadoop ökoszisztémájának összetevőit és javításokat frissítése során. Győződjön meg arról, hogy a legfrissebb információk mindig hivatkozik [támogatott HDInsight-verzió és az operációs rendszer típusa](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) annak érdekében, hogy a HDInsight támogatott verzióját használja. 
>

## <a name="azure-batch-linked-service"></a>Azure Batch társított szolgáltatás

Egy adat-előállító hozhat létre egy csatolt Azure Batch szolgáltatás regisztrálása virtuális gépek (VM) kötegelt készletét. Azure Batch egyéni tevékenység is futtathatja.

Lásd az alábbi témakörök, ha most ismerkedik az Azure Batch szolgáltatás:

* [Azure Batch alapjai](../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintését.
* [Új AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) parancsmaggal hozhat létre Azure Batch-fiók (vagy) [Azure-portálon](../batch/batch-account-create-portal.md) az Azure portál használata az Azure Batch-fiók létrehozásához. Lásd: [PowerShell használatával kezelheti az Azure Batch-fiók](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) témakör részletes útmutatást a parancsmag segítségével.
* [Új AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) parancsmaggal hozhat létre Azure Batch-készlet.

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
| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| type              | A type tulajdonságot kell megadni **AzureBatch**. | Igen      |
| Fióknév       | Az Azure Batch-fiók neve.         | Igen      |
| accessKey         | Az Azure Batch-fiók elérési kulcsának.  | Igen      |
| batchUri          | URL-CÍMÉT az Azure Batch-fiókhoz, https:// formátumban*batchaccountname.region*. batch.azure.com. | Igen      |
| poolName          | A virtuálisgép-készlet neve.    | Igen      |
| linkedServiceName | Neve az Azure Storage társított szolgáltatásnak a kapcsolódó Azure-köteg szolgáltatással kapcsolatos. A tevékenység futtatásához szükséges fájlok átmeneti szolgáltatásnak szolgál. | Igen      |
| connectVia        | A szolgáltatásnak a tevékenységek átirányítani használandó integrációs futásidejű. Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű is használhatja. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem       |

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning társított szolgáltatás
A Machine Learning kötegelt scoring-végpontja számára egy adat-előállító regisztrálni Azure Machine Learning társított szolgáltatás létrehozása.

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
| Tulajdonság               | Leírás                              | Szükséges                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Típus                   | A type tulajdonságot kell megadni: **AzureML**. | Igen                                      |
| mlEndpoint             | A kötegelt pontozás URL-CÍMÉT.                   | Igen                                      |
| apiKey                 | A közzétett munkaterület-modell API.     | Igen                                      |
| updateResourceEndpoint | A frissítés forrás URL-cím a prediktív webszolgáltatás frissítheti a betanított modell fájl az Azure ML Web Service végpont | Nem                                       |
| servicePrincipalId     | Adja meg az alkalmazás ügyfél-azonosítót.     | Szükséges, ha updateResourceEndpoint van megadva |
| servicePrincipalKey    | Adja meg az alkalmazás kulcsot.           | Szükséges, ha updateResourceEndpoint van megadva |
| bérlő                 | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Szükséges, ha updateResourceEndpoint van megadva |
| connectVia             | A szolgáltatásnak a tevékenységek átirányítani használandó integrációs futásidejű. Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű is használhatja. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics társított szolgáltatás
Létrehozhat egy **Azure Data Lake Analytics** társított szolgáltatás az Azure Data Lake Analytics csatolásához számítási egy az Azure data factory szolgáltatást. A Data Lake Analytics U-SQL-tevékenység a feldolgozási szolgáltatásnak hivatkozik. 

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
            "subscriptionId": "<optional, subscription id of ADLA>",
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

| Tulajdonság             | Leírás                              | Szükséges                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | A type tulajdonságot kell megadni: **AzureDataLakeAnalytics**. | Igen                                      |
| Fióknév          | Az Azure Data Lake Analytics-fiók neve.  | Igen                                      |
| dataLakeAnalyticsUri | Az Azure Data Lake Analytics URI.           | Nem                                       |
| subscriptionId       | Az Azure előfizetés-azonosító                    | Nem (Ha nincs megadva, a data factory-előfizetése szerepel). |
| resourceGroupName    | Azure erőforráscsoport-név                | Nem (Ha nincs megadva, az adat-előállító erőforráscsoport szerepel). |
| servicePrincipalId   | Adja meg az alkalmazás ügyfél-azonosítót.     | Igen                                      |
| servicePrincipalKey  | Adja meg az alkalmazás kulcsot.           | Igen                                      |
| bérlő               | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen                                      |
| connectVia           | A szolgáltatásnak a tevékenységek átirányítani használandó integrációs futásidejű. Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű is használhatja. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem                                       |



## <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás
Hozzon létre egy Azure SQL társított szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](transform-data-using-stored-procedure.md) meghívni a Data Factory-folyamat a tárolt eljárást. Lásd: [Azure SQL-összekötő](connector-azure-sql-database.md#linked-service-properties) szóló cikkben olvashat a szolgáltatásnak.

## <a name="azure-sql-data-warehouse-linked-service"></a>Az Azure SQL Data Warehouse társított szolgáltatás
Hozzon létre egy Azure SQL Data Warehouse társított szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](transform-data-using-stored-procedure.md) meghívni a Data Factory-folyamat a tárolt eljárást. Lásd: [Azure SQL Data Warehouse-összekötő](connector-azure-sql-data-warehouse.md#linked-service-properties) szóló cikkben olvashat a szolgáltatásnak.

## <a name="sql-server-linked-service"></a>Kapcsolódó SQL Server szolgáltatás
Hozzon létre csatolt SQL Server szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](transform-data-using-stored-procedure.md) meghívni a Data Factory-folyamat a tárolt eljárást. Lásd: [SQL Server-összekötő](connector-sql-server.md#linked-service-properties) szóló cikkben olvashat a szolgáltatásnak.

## <a name="azure-data-factory---naming-rules"></a>Az Azure Data Factory - elnevezési szabályok
A következő táblázat elnevezési szabályoknak az adat-előállító összetevők.

| Name (Név)                             | Név egyedisége                          | Érvényességi ellenőrzéseket                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data Factory                     | Egyedi Microsoft Azure között. Nevek nem különböztetik meg, ez azt jelenti, hogy `MyDF` és `mydf` adat-előállító hivatkozik. | <ul><li>Minden adat-előállító pontosan egy Azure-előfizetés van kötve.</li><li>Objektumnevek betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert legyen azonnal előtt, és betűvel vagy számmal követ. A tároló neve nem szerepelhetnek egymást követő kötőjeleket.</li><li>Neve 3 – 63 karakter hosszú lehet.</li></ul> |
| Szolgáltatások/táblák/folyamatok csatolt | Egyedi az adat-előállítóban. Nevek nem különböztetik meg. | <ul><li>A táblanév maximális karakterszámot: 260.</li><li>Objektumnevek betű, szám vagy aláhúzásjel (_) kell kezdődnie.</li><li>Következő karakterek nem engedélyezettek: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Erőforráscsoport                   | Egyedi Microsoft Azure között. Nevek nem különböztetik meg. | <ul><li>Karakterek maximális száma: 1000.</li><li>Név tartalmazhat betűket, számjegyeket és a következő karaktereket: "-", "_",","és"."</li></ul> |

## <a name="next-steps"></a>További lépések
Azure Data Factory által támogatott átalakítása tevékenységek listája, [adatok](transform-data.md).
