---
title: Azure Data Factory által támogatott környezetek számítási |} Microsoft Docs
description: További információk a számítási környezetek esetén, amelyek segítségével az Azure Data Factory folyamatok (például az Azure HDInsight)-átalakítási és folyamat-adatokat.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 51a0f43587b9d34a3693eb4a2927d10c71bd95d1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621751"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Számítási környezetek Azure Data Factory által támogatott
> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [2-es társított szolgáltatások számítási](../compute-linked-services.md).

Ez a cikk a számítási környezetek használható adatainak feldolgozása vagy átalakítási mutatja be. Is biztosít a különböző konfigurációt (a tárolt és kapcsolása-a-saját) adatait, hogy a Data Factory támogatja ezek hivatkozó összekapcsolt szolgáltatások konfigurálásakor számítási környezetek számára egy Azure data factory.

A következő táblázat felsorolja az adat-előállítót, és a rajtuk futó tevékenységek által támogatott számítási környezetek. 

| Számítási környezet                      | Tevékenységek                               |
| ---------------------------------------- | ---------------------------------------- |
| [Igény szerinti Azure HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streamelési](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-tevékenységek: kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Az Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Az Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>A Data Factory támogatott HDInsight-verziókról
Az Azure HDInsight Hadoop fürt több verziója, amely központilag telepíthető bármikor támogatja. Minden támogatott verziót hoz létre egy adott verziójához a Hortonworks Data Platform (HDP) telepítési és összetevők a terjesztési. 

A Microsoft a HDInsight támogatott verziók listáját frissítése a legújabb Hadoop-ökoszisztémával összetevők és javításokat. Részletes információkért lásd: [támogatott HDInsight-verziókról](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-alapú HDInsight 3.3-as verzió 2017. július 31 lett visszavonva. Adat-előállító 1-es verziójú igény szerinti HDInsight kapcsolódó szolgáltatások ügyfelek kaptak, amíg December 15, 2017, teszteléséhez, és a HDInsight újabb verziójára. Windows-alapú HDInsight 2018 július 31 rendszerből.
>
> 

### <a name="after-the-retirement-date"></a>A kivezetési dátum után 

Miután 2017. December 15.:

- Már nem hozhatók létre Linux-alapú HDInsight 3.3-as verzió (vagy korábbi verziójú) segítségével egy igény szerinti HDInsight fürtök társított adat-előállítóban 1-es verziójú szolgáltatás. 
- Ha a [ **osType** és **verzió** tulajdonságok](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nincs explicit módon megadott egy meglévő adat-előállító 1-es verziójú igény szerinti HDInsight társított szolgáltatás JSON-definícióból , az alapértelmezett érték változtatják **verzió 3.1, osType = = Windows** való **verzió =\<HDI-alapértelmezett legújabb verziója\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions), osType = Linux**.

Miután július 31, 2018:

- Adat-előállítóban 1-es verziójú egy igény szerinti HDInsight kapcsolódó szolgáltatás használatával nem hozhat létre a Windows-alapú HDInsight-fürtök bármely verzióját. 

### <a name="recommended-actions"></a>Javasolt teendők 

- Győződjön meg arról, hogy használhatja-e a legújabb Hadoop-ökoszisztémával összetevők és javításokat, frissítse a [ **osType** és **verzió** tulajdonságok](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) az érintett Data Factory 1-es verziójú igény HDInsight kapcsolódó szolgáltatás definíciók újabb Linux-alapú HDInsight-verziókról (HDInsight 3.6). 
- 2017. December 15. előtt teszt Data Factory 1-es verziójú Hive, Pig, a MapReduce és a Hadoop streamelési tevékenységek, amelyek hivatkoznak az érintett társított szolgáltatás. Győződjön meg arról, hogy kompatibilisek az új **osType** és **verzió** alapértelmezett értéket (**verzió = 3.6**, **osType = Linux**) vagy a explicit HDInsight-verzió és az operációs rendszer adja meg, hogy frissíti. 
  Kompatibilitási kapcsolatos további információkért lásd: [áttelepítése egy Windows-alapú HDInsight-fürtöt a Linux-alapú fürtre](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) és [Mik azok a Hadoop-összetevők és verziók a hdinsight eszközzel?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Windows-alapú HDInsight-fürtök létrehozása a Data Factory 1-es verziójú igény szerinti HDInsight kapcsolódó szolgáltatás használatának folytatásához explicit módon beállítva **osType** való **Windows** 2017. decemberi 15 előtt. Azt javasoljuk, hogy át Linux-alapú HDInsight-fürtök 2018 július 31 előtt. 
- Ha egy igény szerinti HDInsight kapcsolódó szolgáltatás segítségével hajtható végre az adat-előállító 1-es verziójú DotNet egyéni tevékenység, frissítés a DotNet egyéni tevékenység JSON-definíció, használja az Azure Batch társított szolgáltatás. További információkért lásd: [egyéni tevékenységeket felhasználni a Data Factory-folyamathoz](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Ha használja a meglévő, a HDInsight fürt kerüljön-a-saját kapcsolódó adat-előállítóban az 1-es eszköz, vagy az Azure Data Factoryben 2,-es intézkedés kerüljön-a-tulajdonosai, és igény szerinti HDInsight kapcsolódó szolgáltatás szükséges. A legújabb verzió támogatási szabályzata a HDInsight-fürtök adott esetben már érvényesíti. 
>
> 


## <a name="on-demand-compute-environment"></a>Igény szerinti számítási környezet
Igény szerinti konfigurációban a Data Factory teljesen a számítási környezet kezeli. Adat-előállító automatikusan létrehozza a számítási környezet, a feladatok elküldött adatok feldolgozása előtt. Ha a feladat befejeződött, a Data Factory eltávolítja a számítási környezet. 

Létrehozhat egy igény szerinti számítási környezet összekapcsolt szolgáltatás. A társított szolgáltatás használja, a számítási környezet konfigurálása, valamint a feladat végrehajtása, a kiszolgálófürt-felügyelet és a műveletek rendszerindítása részletes beállításainak ellenőrzéséhez.

> [!NOTE]
> Az igény szerinti konfigurációs jelenleg csak a HDInsight-fürtök használata támogatott.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Az Azure HDInsight igény társított szolgáltatás
Adat-előállító automatikusan hozhat létre egy Windows-alapú vagy Linux-alapú igény szerinti HDInsight-fürt adatok feldolgozására. A fürt létrehozása a fürt társított storage-fiók ugyanabban a régióban. Használja a JSON **linkedServiceName** tulajdonság a fürt létrehozásához.

Vegye figyelembe a következőket *kulcs* igény szerinti HDInsight kapcsolatos pontok társított szolgáltatás:

* Az igény szerinti HDInsight-fürt nem jelenik meg az Azure-előfizetéshez. A Data Factory szolgáltatásnak kezeli az igény szerinti HDInsight-fürthöz az Ön nevében.
* A naplók az igény szerinti HDInsight-fürtök a futó feladatok a tárfiók a HDInsight-fürthöz társított lesz másolva. Ezek a naplók az Azure-portál eléréséhez lépjen a **tevékenység futtatása részletei** ablaktáblán. További információkért lásd: [figyelése és kezelése a folyamatok](data-factory-monitor-manage-pipelines.md).
* Van szó, csak a az, hogy a HDInsight-fürt- és futó feladatot.

> [!IMPORTANT]
> Általában tart *20 perc* vagy több igény szerinti HDInsight-fürtök kiépítéséhez.
>
> 

### <a name="example"></a>Példa
A következő JSON igény kapcsolódó HDInsight Linux-alapú szolgáltatás határozza meg. Adat-előállító automatikusan létrehoz egy *Linux-alapú* HDInsight-fürtöt egy adatszelet feldolgozásakor. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> A HDInsight-fürtöt hoz létre egy *alapértelmezett tároló* az Azure Blob Storage a JSON-ban megadott **linkedServiceName** tulajdonság. Úgy lett kialakítva HDInsight nem törli a tárolót, a fürt törlésekor. Egy igény szerinti HDInsight kapcsolódó szolgáltatás, a HDInsight-fürtök minden alkalommal létrejön a szelet kell feldolgozni, kivéve, ha egy meglévő élő fürthöz (**timeToLive**). A fürt törlődik, ha feldolgozása befejeződött. 
>
> További szeletek dolgozzák fel, mert a Blob-tároló sok tárolók látható. Ha nincs szüksége a tárolók feladatok hibaelhárításhoz, előfordulhat, hogy törölni kívánja a tárolási költségek csökkentése a tárolók. A tárolók neve a következő mintát követi: `adf<your Data Factory name>-<linked service name>-<date and time>`. Egy eszköz, például használhatja [Microsoft Tártallózó](http://storageexplorer.com/) Blob Storage tárolók törlése.
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Szükséges |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A type tulajdonság beállítása **HDInsightOnDemand**. | Igen      |
| clusterSize                  | A fürt munkavégző és az adatok csomópontok száma. A HDInsight-fürt 2 átjárócsomópontokkal, ehhez a tulajdonsághoz megadott feldolgozó csomópontok száma hozza létre. A csomópontok méretű standard, D3, amelynek 4 mag van. A 4-munkavégző csomópontot tartalmazó fürtben veszi 24 mag (4\*a munkavégző csomópontokról, valamint 2 processzormag, 4 = 16\*az átjárócsomópontokkal processzormag, 4 = 8). A standard, D3 réteg kapcsolatos részletekért lásd: [hdinsight létrehozása Linux-alapú Hadoop-fürtök](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Igen      |
| timeToLive                   | A megengedett üresjárati idő az igény szerinti HDInsight-fürthöz. Meghatározza, mennyi ideig az igény szerinti HDInsight-fürt marad aktív, ha egy tevékenység futtatás befejeződött, ha nincsenek a fürt más aktív feladatok.<br /><br />Például ha egy tevékenység futott 6 percig tart, és **timeToLive** értéke 5 perc, a fürt a tevékenységfuttatási feldolgozásának 6 perc után 5 percig aktív marad. Ha a 6 percnél ablakban futtassa egy másik tevékenységgel, dolgoz fel ugyanabban a fürtben.<br /><br />Igény szerinti HDInsight-fürtök létrehozása során költséges (eltarthat egy ideig). A beállítás segítségével szükség esetén újból felhasználja az igény szerinti HDInsight-fürtök által egy adat-előállító teljesítményének javítása.<br /><br />Ha a **timeToLive** egy érték **0**, a fürtök törlése, amint a tevékenység futtatása befejeződött. Azonban ha nagy érték van beállítva, a fürt maradhatnak, inaktív, feleslegesen magas költségeket eredményez. Fontos a megfelelő érték a igények alapján.<br /><br />Ha a **timeToLive** értékének megfelelően van beállítva, több folyamatok megoszthatja az igény szerinti HDInsight-fürt példányának. | Igen      |
| verzió:                      | A HDInsight-fürt verziószáma. Engedélyezett HDInsight verzióiért lásd: [támogatott HDInsight-verziókról](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Ha az érték nincs megadva, a [HDI alapértelmezett legfrissebb](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions) szolgál. | Nem       |
| linkedServiceName            | Az Azure tárolás társított szolgáltatásának történő tárolására és feldolgozására adatok az igény szerinti fürt által használható. A HDInsight-fürt létrehozása a tárfiók ugyanabban a régióban.<p>Jelenleg nem hozható létre, amely az Azure Data Lake Store használ a tárolási igény szerinti HDInsight-fürtöt. A HDInsight a Data Lake Store feldolgozása eredmény adatokat tárolni szeretné, ha az adatok másolása a Blob storage Data Lake Store-másolási tevékenység segítségével. </p> | Igen      |
| additionalLinkedServiceNames | Adja meg a további tárfiókok a HDInsight társított szolgáltatás. Adat-előállító regisztrálja a storage-fiókok az Ön nevében. Ezekre a tárfiókokra a HDInsight-fürt ugyanabban a régióban kell lennie. A HDInsight-fürt létrehozása a megadott tárfiók ugyanabban a régióban a **linkedServiceName** tulajdonság. | Nem       |
| osType                       | Operációs rendszer típusa. Két érték engedélyezett **Linux** és **Windows**. Ha az érték nincs megadva, **Linux** szolgál.  <br /><br />Erősen ajánlott Linux-alapú HDInsight-fürtök használata. A Windows a HDInsight a kivezetési dátum 2018 július 31. | Nem       |
| hcatalogLinkedServiceName    | Az Azure SQL társított szolgáltatás mutat, az HCatalog-adatbázis neve. Az igény szerinti HDInsight-fürt létrehozása a metaadattárhoz az SQL-adatbázis segítségével. | Nem       |

#### <a name="example-linkedservicenames-json"></a>Példa: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>A Speciális tulajdonságok
Az igény szerinti HDInsight-fürt részletes konfigurációs megadhatja a következő tulajdonságokkal:

| Tulajdonság               | Leírás                              | Szükséges |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Meghatározza a fő konfigurációs paramétereket (core-site.xml) a HDInsight-fürtöt létrehozni. | Nem       |
| hBaseConfiguration     | Meghatározza a HBase konfigurációs paramétereket (a hbase-site.xml) a HDInsight-fürthöz. | Nem       |
| hdfsConfiguration      | Meghatározza a HDFS konfigurációs paramétereket (hdfs-site.xml) a HDInsight-fürthöz. | Nem       |
| hiveConfiguration      | Meghatározza a Hive konfigurációs paramétereket (hive-site.xml) a HDInsight-fürthöz. | Nem       |
| mapReduceConfiguration | Meghatározza a MapReduce konfigurációs paramétereket (mapred-site.xml) a HDInsight-fürthöz. | Nem       |
| oozieConfiguration     | Meghatározza a Oozie konfigurációs paramétereket (oozie-site.xml) a HDInsight-fürthöz. | Nem       |
| stormConfiguration     | Meghatározza a Storm konfigurációs paramétereket (a storm-site.xml) a HDInsight-fürthöz. | Nem       |
| yarnConfiguration      | Meghatározza a YARN konfigurációs paramétereket (yarn-site.xml) a HDInsight-fürthöz. | Nem       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Példa: Igény szerinti HDInsight fürt-konfiguráció speciális tulajdonságai

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
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
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Csomópont-méretek
Adja meg a méretet, head, az adatok és a ZooKeeper csomópontok, használja a következő tulajdonságokkal: 

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Beállítja az átjárócsomópont méretét. Az alapértelmezett érték **standard, D3**. További információkért lásd: [csomópont méretének megadásához](#specify-node-sizes). | Nem       |
| dataNodeSize      | Az adatcsomóponton állítja be. Az alapértelmezett érték **standard, D3**. | Nem       |
| zookeeperNodeSize | A ZooKeeper csomópont méretének beállítása. Az alapértelmezett érték **standard, D3**. | Nem       |

#### <a name="specify-node-sizes"></a>Adja meg a csomópont-méretek
Meg kell adnia az előző szakaszban leírt tulajdonságokkal karakterlánc-értékek, lásd: [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md). Az értékeket meg kell felelnie a parancsmagok, és API-k hivatkozott [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md). A nagy (alapértelmezett) csomópont adatméret 7 Gigabájt memóriát rendelkezik. Ez nem feltétlenül a forgatókönyvnek megfelelő. 

Ha szeretne létrehozni, D4 méretű átjárócsomópontokkal és feldolgozó csomópontokat, adja meg **standard szintű, D4** értéke a **headNodeSize** és **dataNodeSize** tulajdonságok: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ha ezek a Tulajdonságok értékkel, a következő üzenetet láthatja:

  Nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. A fürt állapota: „hiba”. Üzenet: "PreClusterCreationValidationFailure". 
  
Ez az üzenet jelenik meg, ha gondoskodjon arról, hogy a parancsmag és a tábla API-nevek használatával [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Adat-előállító jelenleg nem támogatja az elsődleges tár Data Lake Store használata a HDInsight-fürtök. Azure Storage használata az elsődleges tár a HDInsight-fürtök. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Bring-a-saját számítási környezet
Egy meglévő számítási környezet regisztrálhatja a Data Factory kapcsolt szolgáltatásként. A számítási környezet kezelése A Data Factory szolgáltatásnak a számítási környezetet használ a tevékenységek végrehajtásához.

Ez a fajta konfiguráció a következő számítási környezetek esetén támogatott:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Az Azure SQL-adatbázis, az Azure SQL Data Warehouse szolgáltatásban SQL-kiszolgáló

## <a name="azure-hdinsight-linked-service"></a>Az Azure HDInsight társított szolgáltatás
Létrehozhat saját HDInsight-fürt regisztrálni a Data Factory kapcsolt HDInsight szolgáltatásnak.

### <a name="example"></a>Példa

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| type              | A type tulajdonság beállítása **HDInsight**. | Igen      |
| clusterUri        | Az URI-je a HDInsight-fürthöz.        | Igen      |
| felhasználónév          | A felhasználói fiók egy meglévő HDInsight-fürtre való kapcsolódáshoz használandó neve. | Igen      |
| jelszó          | A felhasználói fiók jelszavát.   | Igen      |
| linkedServiceName | A Blob Storage a HDInsight-fürt által használt a tárolás társított szolgáltatás neve. <p>Jelenleg nem adhat meg a Data Lake Store társított szolgáltatás ehhez a tulajdonsághoz. Van-e a HDInsight-fürt hozzáférése Data Lake Store-ba, előfordulhat, hogy a Data Lake Store adatelérés a Hive vagy a Pig-parancsfájlok alapján. </p> | Igen      |

## <a name="azure-batch-linked-service"></a>Azure Batch társított szolgáltatás
A kapcsolódó Batch szolgáltatás regisztrálása virtuális gépek (VM) az adat-előállító kötegelt készletét hozhat létre. A Microsoft .NET-egyéni tevékenységek kötegelt vagy HDInsight segítségével is futtathatja.

Ha most ismerkedik a Batch szolgáltatás használatával:

* További tudnivalók [Azure Batch alapjai](../../batch/batch-technical-overview.md).
* További tudnivalók a [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) parancsmag. Ez a parancsmag segítségével Batch-fiók létrehozása. Vagy, a Batch-fiók segítségével létrehozható a [Azure-portálon](../../batch/batch-account-create-portal.md). A parancsmag használatával kapcsolatos részletes információkért lásd: [PowerShell használatával való kezelése a Batch-fiók](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* További tudnivalók a [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) parancsmag. Ez a parancsmag segítségével hozzon létre egy Batch-készlet.

### <a name="example"></a>Példa

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Az a **accountName** tulajdonság, hozzáfűző **.\< régió neve\>**  a batch-fiók nevét. Példa:

```json
"accountName": "mybatchaccount.eastus"
```

Adja meg úgy a **batchUri** végpont. Példa:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| type              | A type tulajdonság beállítása **AzureBatch**. | Igen      |
| Fióknév       | A Batch-fiók neve.         | Igen      |
| accessKey         | A Batch-fiók elérési kulcsának.  | Igen      |
| poolName          | A virtuális gépek készlet neve.    | Igen      |
| linkedServiceName | A kötegelt társított tároló társított szolgáltatás neve társított szolgáltatás. A társított szolgáltatás átmeneti fájlok, amelyek szükségesek a tevékenység futtatásához, és a tevékenység végrehajtási naplók tárolására szolgál. | Igen      |

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning társított szolgáltatás
A Machine Learning kötegelt scoring-végpontja egy adat-előállító való regisztrálásához kapcsolódó Machine Learning szolgáltatás hozhat létre.

### <a name="example"></a>Példa

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság   | Leírás                              | Szükséges |
| ---------- | ---------------------------------------- | -------- |
| Típus       | A type tulajdonság beállítása **AzureML**. | Igen      |
| mlEndpoint | A kötegelt pontozás URL-CÍMÉT.                   | Igen      |
| apiKey     | A közzétett munkaterület-modell API.     | Igen      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics társított szolgáltatás
Létrehozhat egy Data Lake Analytics számítási szolgáltatás összekapcsolása egy Azure data factory kapcsolt Data Lake Analytics szolgáltatás. A Data Lake Analytics U-SQL-tevékenység a feldolgozási szolgáltatásnak hivatkozik. 

Az alábbi táblázatban szerepelnek a JSON-definícióból általános tulajdonságok:

| Tulajdonság                 | Leírás                              | Szükséges                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | A type tulajdonság beállítása **AzureDataLakeAnalytics**. | Igen                                      |
| Fióknév          | A Data Lake Analytics-fiók nevét.  | Igen                                      |
| dataLakeAnalyticsUri | A Data Lake Analytics URI.           | Nem                                       |
| subscriptionId       | Az Azure előfizetés-azonosító.                    | Nem<br /><br />(Ha nincs megadva, az adat-előállító előfizetés szerepel.) |
| resourceGroupName    | Az Azure erőforráscsoport neve.                | Nem<br /><br /> (Ha nincs megadva, a data factory erőforráscsoport használja.) |

### <a name="authentication-options"></a>Hitelesítési lehetőségek
A Data Lake Analytics kapcsolódó szolgáltatás hitelesítés egy egyszerű szolgáltatást vagy a felhasználó hitelesítő adatainak használatával választhat.

#### <a name="service-principal-authentication-recommended"></a>Szolgáltatás egyszerű hitelesítés (ajánlott)
Szolgáltatás egyszerű hitelesítést használ, regisztráljon az Azure Active Directory (Azure AD) alkalmazás entitás. Ezt követően az Azure AD hozzáférési jogot Data Lake Store. Részletes útmutató: [szolgáltatások közötti hitelesítési](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:
* Alkalmazásazonosító
* Alkalmazás kulcs 
* Bérlőazonosító

Szolgáltatás egyszerű hitelesítés használatára a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Szükséges |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Az alkalmazás ügyfél-azonosítót.     | Igen      |
| servicePrincipalKey | A kulcsát.           | Igen      |
| bérlő              | A bérlői adatait (tartomány nevét vagy a bérlő azonosító) ahol az alkalmazás megtalálható-e. Ahhoz, hogy ezt az információt, az egérrel az Azure-portál jobb felső sarkában. | Igen      |

**Példa: Szolgáltatás egyszerű hitelesítés**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Felhasználói hitelesítő adatok hitelesítése
A Data Lake Analytics felhasználói hitelesítő adja meg a következő tulajdonságokat:

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| Engedélyezési | A Data Factory Editor, válassza ki a **engedélyezés** gombra. Adja meg a hitelesítő adatokat, hogy az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ehhez a tulajdonsághoz. | Igen      |
| sessionId     | Az OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan létrejön a Data Factory Editor használatakor. | Igen      |

**Példa: Felhasználók hitelesítő adatok hitelesítése**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Az engedélyezési kód kiválasztásával okozó a **engedélyezés** gombra a készlet időköz után lejár. 

A hitelesítési jogkivonat lejártakor, megjelenhet a következő hibaüzenet: 

  Hitelesítőadat-műveleti hiba: invalid_grant - AADSTS70002: Hiba történt a hitelesítő adatok ellenőrzése. AADSTS70008: A megadott hozzáférés biztosítása lejárt vagy visszavonták. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelációazonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 időbélyegző: 2015-12-15 21:09:31Z

Az alábbi táblázat a felhasználó által lejáratok: 

| Felhasználó típusa                                | Után lejár                            |
| :--------------------------------------- | :--------------------------------------- |
| Felhasználói fiókok, amelyek *nem* kezeli az Azure AD (Hotmail, Live, és így tovább) | 12 óra.                                 |
| Felhasználói fiókok *vannak* az Azure AD által felügyelt | Futtassa a 14 nap után utolsó újrapróbálása. <br /><br />90 nap, ha olyan szelet, amely azon alapul, az OAuth-alapú társított szolgáltatás fut. 14 naponta legalább egyszer |

Elkerülése érdekében, vagy hárítsa el a hibát, ismét engedélyezheti kiválasztásával a **engedélyezés** gombra kattint, amikor a jogkivonat lejár. Ezután telepítse újra a társított szolgáltatás. Értékek is létrehozhat a **sessionId** és **engedélyezési** tulajdonságok programozott módon az alábbi kód használatával:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

A Data Factory osztályok, ez a Kódpélda használt kapcsolatos részletekért lásd:
* [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Adjon hozzá egy hivatkozást, a Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll a **WindowsFormsWebAuthenticationDialog** osztály. 

## <a name="azure-sql-linked-service"></a>Az Azure SQL-alapú társított szolgáltatás
Hozzon létre egy SQL társított szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. További információkért lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Az Azure SQL Data Warehouse társított szolgáltatás
Hozzon létre csatolt SQL Data Warehouse szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. További információkért lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Kapcsolódó SQL Server szolgáltatás
Hozzon létre csatolt SQL Server szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#linked-service-properties).

