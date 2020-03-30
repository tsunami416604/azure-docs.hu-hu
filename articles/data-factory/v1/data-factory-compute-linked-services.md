---
title: Az Azure Data Factory által támogatott számítási környezetek
description: Ismerje meg az Azure Data Factory-folyamatokban (például az Azure HDInsightban) használható számítási környezeteket az adatok átalakításához vagy feldolgozásához.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0cc7c3b7d8b364e0bcca671efaff2cf324695428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281547"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Az Azure Data Factory által támogatott számítási környezetek
> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a Csatolt szolgáltatások számítása a alkalmazásban című [témakört.](../compute-linked-services.md)

Ez a cikk ismerteti a számítási környezetek, amelyek segítségével az adatok feldolgozásához vagy átalakításához. Emellett részletesen ismerteti a Data Factory által támogatott különböző konfigurációkat (igény szerinti és saját bring-your-own) is, amikor olyan összekapcsolt szolgáltatásokat konfigurál, amelyek ezeket a számítási környezeteket egy Azure-adat-előállítóhoz kapcsolják.

Az alábbi táblázat a Data Factory által támogatott számítási környezetek és az azokon futtatható tevékenységek listáját tartalmazza. 

| Számítási környezet                      | Tevékenységek                               |
| ---------------------------------------- | ---------------------------------------- |
| [Igény szerinti Azure HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-tevékenységek: kötegelt végrehajtás és az erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), SQL [Server](#sql-server-linked-service) | [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>A Data Factory által támogatott HDInsight-verziók
Az Azure HDInsight több Hadoop-fürtverziót is támogat, amelyeket bármikor üzembe helyezhet. Minden támogatott verzió létrehoz egy adott verzióját a Hortonworks Data Platform (HDP) disztribúcióés egy sor összetevőt a disztribúcióban. 

A Microsoft frissíti a támogatott HDInsight-verziók listáját a legújabb Hadoop ökoszisztéma-összetevőkkel és -javításokkal. Részletes információt a [Támogatott HDInsight-verziók című témakörben](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)talál.

> [!IMPORTANT]
> 2017. július 31-én a Linux-alapú HDInsight 3.3-as verzióját megszüntették. 2017. december 15-ig a Data Factory 1-es verziója HDInsight-szolgáltatásokkal kapcsolatos ügyfeleket kaptak a HDInsight egy későbbi verziójának teszteléséhez és frissítéséhez. 2018. július 31-én a Windows-alapú HDInsight megszűnik.
>
> 

### <a name="after-the-retirement-date"></a>A nyugdíjazási dátum után 

2017. december 15.-e után:

- A Data Factory 1-es verziójában igény szerinti HDInsight-csatolt szolgáltatás használatával már nem hozhat létre Linux-alapú HDInsight 3.3-as (vagy korábbi verziójú) fürtöket. 
- Ha az [ **osType** és **a Version** tulajdonságai](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nincsenek kifejezetten megadva a JSON-definícióban egy meglévő Data Factory 1-es verziójú HDInsight csatolt szolgáltatáshoz, az alapértelmezett érték **Version=3.1, osType=Windows** **to Version=\<legújabb HDI alapértelmezett verzióra\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux**.

2018. július 31.-e után:

- A Data Factory 1-es verziójában igény szerinti HDInsight-csatolt szolgáltatás használatával már nem hozhat létre Windows-alapú HDInsight-fürtök et. 

### <a name="recommended-actions"></a>Ajánlott műveletek 

- Annak érdekében, hogy a legújabb Hadoop ökoszisztéma-összetevőket és javításokat használhassa, frissítse az érintett Data Factory 1-es verziójú 1-es verziójának [ **osType** és **verzió** tulajdonságait](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) az újabb Linux-alapú HDInsight-verziókra (HDInsight 3.6). 
- 2017. december 15., 15. Győződjön meg arról, hogy azok kompatibilisek az új **osType** és **Version** alapértelmezett értékekkel (**Version=3.6**, **osType=Linux**) vagy a hdinsight-verzióval és operációs rendszer típusával, amelyre frissít. 
  A kompatibilitásról az [Áttelepítés Windows-alapú HDInsight-fürtről Linux alapú fürtre](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) című témakörben olvashat bővebben, és [milyen Hadoop-összetevők és -verziók érhetők el a HDInsight szolgáltatással?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) 
- Ha továbbra is használni szeretné a Data Factory 1-es verzióját igény szerinti HDInsight-alapú szolgáltatással windowsos HDInsight-fürtök létrehozásához, explicit módon állítsa be **az osType-ot** **Windows-ra** 2017. 2018. július 31-e előtt javasoljuk, hogy migráljon Linux-alapú HDInsight-fürtökre. 
- Ha igény szerinti HDInsight-csatolt szolgáltatást használ a Data Factory 1-es verziójú DotNet egyéni tevékenység végrehajtásához, frissítse a DotNet egyéni tevékenység JSON-definícióját, hogy ehelyett egy Azure Batch-kapcsolt szolgáltatást használjon. További információ: [Egyéni tevékenységek használata adatfeldolgozó folyamatokban.](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) 

> [!Note]
> Ha a meglévő, bring-your-own fürt HDInsight csatolt eszköz a Data Factory 1-es verzió, vagy egy bring-your-own és az igény szerinti HDInsight csatolt szolgáltatás az Azure Data Factory, nincs szükség műveletre. Ezekben az esetekben a HDInsight-fürtök legújabb verziótámogatási házirendje már érvényben van. 
>
> 


## <a name="on-demand-compute-environment"></a>Igény szerinti számítási környezet
Igény szerinti konfigurációban a Data Factory teljes mértékben kezeli a számítási környezetet. A Data Factory automatikusan létrehozza a számítási környezetet, mielőtt egy feladat adatok feldolgozására kerül elküldésre. A feladat befejezése után a Data Factory eltávolítja a számítási környezetet. 

Igény szerinti számítási környezethez összekapcsolt szolgáltatást hozhat létre. A csatolt szolgáltatás segítségével konfigurálhatja a számítási környezetet, és szabályozhatja a feladatvégrehajtás, a fürtkezelés és a rendszerindítási műveletek részletes beállításait.

> [!NOTE]
> Jelenleg az igény szerinti konfiguráció csak HDInsight-fürtök esetén támogatott.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight igény szerinti társított szolgáltatás
A Data Factory automatikusan létrehozhat egy Windows-alapú vagy Linux alapú HDInsight-fürtöt az adatok feldolgozásához. A fürt ugyanabban a régióban jön létre, mint a fürthöz társított tárfiók. A fürt létrehozásához használja a JSON **linkedServiceName** tulajdonságot.

Vegye figyelembe az alábbi *kulcsfontosságú* pontokat az igény szerinti HDInsight-hivatkozott szolgáltatással kapcsolatban:

* Az igény szerinti HDInsight-fürt nem jelenik meg az Azure-előfizetésben. A Data Factory szolgáltatás kezeli az igény szerinti HDInsight-fürtöt az Ön nevében.
* Az igény szerinti HDInsight-fürtön futó feladatok naplói a HDInsight-fürthöz társított tárfiókba kerülnek. Ezek a naplók eléréséhez az Azure Portalon nyissa meg a **Tevékenység futtatása részletei** ablakot. További információt a [Folyamatok figyelése és kezelése című témakörben talál.](data-factory-monitor-manage-pipelines.md)
* Csak azért az időért számítunk fel díjat, amikor a HDInsight-fürt működik és működik.

> [!IMPORTANT]
> Az igény szerinti HDInsight-fürt kiépítése általában *20 percet* vagy annál többet vesz igénybe.
>
> 

### <a name="example"></a>Példa
A következő JSON egy Linux-alapú HDInsight-kapcsolt szolgáltatást határoz meg. A Data Factory automatikusan létrehoz egy *Linux-alapú* HDInsight-fürtöt, amikor egy adatszeletet dolgoz fel. 

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
> A HDInsight-fürt létrehoz egy *alapértelmezett tárolót* az Azure Blob storage-ban, amelyet a JSON **linkedServiceName** tulajdonságban ad meg. A HDInsight a fürt törlésekor a hdinsight nem törli ezt a tárolót. Egy igény szerinti HDInsight-kapcsolt szolgáltatásban egy HDInsight-fürt jön létre minden alkalommal, amikor egy szeletet fel kell dolgozni, kivéve, ha létezik egy meglévő élő fürt **(timeToLive).** A fürt a feldolgozás befejezésekor törlődik. 
>
> A hogy több szeletet dolgoz fel a rendszer, sok tárolót láthat a Blob storage-ban. Ha nincs szüksége a tárolók hibaelhárítási feladatok, érdemes törölni a tárolók a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf<your Data Factory name>-<linked service name>-<date and time>`. A Microsoft Storage [Explorerhez](https://storageexplorer.com/) hasonló eszközökkel törölheti a tárolókat a Blob storage-ban.
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Kötelező |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Állítsa a típustulajdonságot **HDInsightOnDemand tulajdonságra.** | Igen      |
| clusterSize                  | A dolgozó és az adatcsomópontok száma a fürtben. A HDInsight-fürt kétfej-csomóponttal jön létre, a tulajdonsághoz megadott munkavégző csomópontok száma mellett. A csomópontok mérete Standard_D3, amely 4 maggal rendelkezik. Egy 4 munkavégző csomópontfürt 24 magból áll (4\*4 = 16\*mag a munkavégző csomópontokhoz, plusz 2 4 = 8 mag a főcsomópontokhoz). A Standard_D3 rétegről a [Linux-alapú Hadoop-fürtök létrehozása a HDInsightban című témakörben talál.](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) | Igen      |
| timeToLive                   | Az igény szerinti HDInsight-fürt engedélyezett szabad ideje. Itt adható meg, hogy az igény szerinti HDInsight-fürt mennyi ideig maradjon életben, amikor egy tevékenység futtatása befejeződik, ha nincs más aktív feladat a fürtben.<br /><br />Ha például egy tevékenység futtatása 6 percet vesz igénybe, és az **időToLive** 5 percre van állítva, a fürt a tevékenység futtatásának 6 percután 5 percig életben marad. Ha egy másik tevékenységfuttatása a 6 perces ablakban történik, azt ugyanaz a fürt dolgozza fel.<br /><br />Egy igény szerinti HDInsight-fürt létrehozása költséges művelet (eltarthat egy ideig). Ezt a beállítást szükség szerint használhatja az adat-előállító teljesítményének növeléséhez egy igény szerinti HDInsight-fürt újrafelhasználásával.<br /><br />Ha az **timeToLive** értéket **0-ra**állítja, a fürt törlődik, amint a tevékenység futása befejeződik. Ha azonban magas értéket állít be, a fürt tétlen maradhat, szükségtelenül magas költségeket eredményezve. Fontos, hogy a megfelelő értéket az ön igényei nek megfelelően állítsa be.<br /><br />Ha az **timeToLive** érték megfelelően be van állítva, több folyamat is megoszthatja az igény szerinti HDInsight-fürt példányát. | Igen      |
| version                      | A HDInsight-fürt verziója. Az engedélyezett HDInsight-verziókról a [Támogatott HDInsight-verziók című témakörben lehet.](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions) Ha ez az érték nincs megadva, a rendszer a [legújabb HDI alapértelmezett verziót](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) használja. | Nem       |
| linkedServiceName            | Az Azure Storage-hoz kapcsolódó szolgáltatás, amelyet az igény szerinti fürt az adatok tárolására és feldolgozására használ. A HDInsight-fürt ugyanabban a régióban jön létre, mint ez a tárfiók.<p>Jelenleg nem hozhat létre olyan igény szerinti HDInsight-fürtöt, amely az Azure Data Lake Store-t használja tárolóként. Ha a HDInsight-feldolgozás ból származó eredményadatokat a Data Lake Store-ban szeretné tárolni, a Másolási tevékenység segítségével másolja az adatokat a Blob storage-ból a Data Lake Store-ba. </p> | Igen      |
| továbbiLinkedServiceNames | A HDInsight-csatolt szolgáltatás további tárfiókokat határoz meg. A Data Factory regisztrálja a tárfiókokat az Ön nevében. Ezek a tárfiókok kell ugyanabban a régióban, mint a HDInsight-fürt. A HDInsight-fürt ugyanabban a régióban jön létre, mint a **linkedServiceName** tulajdonság által megadott tárfiók. | Nem       |
| osType típus                       | Az operációs rendszer típusa. Az engedélyezett értékek **linuxos** és windowsosak. **Windows** Ha ez az érték nincs megadva, a **Linux** használatos.  <br /><br />Javasoljuk, hogy linuxos HDInsight-fürtöket használjon. A HDInsight windowsos nyugdíjazási dátuma 2018. | Nem       |
| hcatalogLinkedServiceName    | Az Azure SQL-hez csatolt szolgáltatás neve, amely a HCatalog-adatbázisra mutat. Az igény szerinti HDInsight-fürt az SQL-adatbázis metatárolóként való használatával jön létre. | Nem       |

#### <a name="example-linkedservicenames-json"></a>Példa: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Speciális tulajdonságok
Az igény szerinti HDInsight-fürt részletes konfigurációjához a következő tulajdonságokadhatók meg:

| Tulajdonság               | Leírás                              | Kötelező |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration (magkonfiguráció)      | Megadja a létrehozandó HDInsight-fürt alapvető konfigurációs paramétereit (core-site.xml). | Nem       |
| hBaseConfiguration     | Megadja a HDInsight-fürt HBase konfigurációs paramétereit (hbase-site.xml). | Nem       |
| hdfsConfiguration      | Megadja a HDInsight-fürt HDFS konfigurációs paramétereit (hdfs-site.xml). | Nem       |
| struktúrakonfiguráció      | Megadja a HIVe konfigurációs paramétereit (hive-site.xml) a HDInsight-fürthöz. | Nem       |
| mapReduceConfiguration | Megadja a HDInsight-fürt MapReduce konfigurációs paramétereit (mapred-site.xml). | Nem       |
| oozieConfiguration     | Megadja a HDInsight-fürt Oozie konfigurációs paramétereit (oozie-site.xml). | Nem       |
| viharConfiguration     | Megadja a Storm konfigurációs paramétereit (storm-site.xml) a HDInsight-fürthöz. | Nem       |
| fonalkonfiguráció      | Megadja a YARN konfigurációs paramétereit (yarn-site.xml) a HDInsight-fürthöz. | Nem       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Példa: Igény szerinti HDInsight-fürtkonfiguráció speciális tulajdonságokkal

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

### <a name="node-sizes"></a>Csomópontméretek
A fej-, adat- és ZooKeeper-csomópontok méretének megadásához használja a következő tulajdonságokat: 

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| headNodeMéret      | Beállítja a fejcsomópont méretét. Az alapértelmezett érték **a Standard_D3**. További információt a [Csomópontméretek megadása .](#specify-node-sizes) | Nem       |
| dataNodeSize (dataNodeSize)      | Az adatcsomópont méretét állítja be. Az alapértelmezett érték **a Standard_D3**. | Nem       |
| zookeeperNodeMéret | A ZooKeeper csomópont méretét állítja be. Az alapértelmezett érték **a Standard_D3**. | Nem       |

#### <a name="specify-node-sizes"></a>Csomópontméretek megadása
Az előző szakaszban ismertetett tulajdonságokhoz megadandó karakterláncértékekről a [Virtuális gép méretei](../../virtual-machines/linux/sizes.md)című témakörben található. Az értékeknek meg kell felelniük a [virtuális gép méretekben](../../virtual-machines/linux/sizes.md)hivatkozott parancsmagoknak és API-knak. A Nagy (alapértelmezett) adatcsomópont mérete 7 GB memóriával rendelkezik. Ez nem feltétlenül elegendő a forgatókönyvhöz. 

Ha D4-es méretű fejcsomópontokat és munkavégző csomópontokat szeretne létrehozni, adja meg **Standard_D4** a **headNodeSize** és **dataNodeSize** tulajdonságok értékeként: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ha helytelen értéket állít be ezekhez a tulajdonságokhoz, a következő üzenet jelenhet meg:

  Nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. A fürt állapota: „hiba”. Üzenet: "PreClusterCreationValidationFailure". 
  
Ha ez az üzenet jelenik meg, győződjön meg arról, hogy a tábla parancsmag- és API-neveit használja [a virtuális gép méretekben.](../../virtual-machines/linux/sizes.md)  

> [!NOTE]
> Jelenleg a Data Factory nem támogatja a HDInsight-fürtöket, amelyek a Data Lake Store-t használják elsődleges tárolóként. Használja az Azure Storage-t a HDInsight-fürtök elsődleges tárolójaként. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Hozza magával saját számítási környezetét
A Data Factory ban egy meglévő számítási környezetet összekapcsolt szolgáltatásként regisztrálhat. Ön kezeli a számítási környezetet. A Data Factory szolgáltatás a számítási környezetet használja a tevékenységek végrehajtásához.

Az ilyen típusú konfiguráció a következő számítási környezetekben támogatott:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Adatbázis, Azure SQL Adattárház, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight-kapcsolt szolgáltatás
Létrehozhat egy HDInsight-csatolt szolgáltatást, amely regisztrálja saját HDInsight-fürtjeit a Data Factory szolgáltatással.

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
| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| type              | Állítsa a típustulajdonságot **HDInsight**tulajdonságra. | Igen      |
| clusterUri        | A HDInsight-fürt URI-ja.        | Igen      |
| felhasználónév          | A meglévő HDInsight-fürthöz való csatlakozáshoz használandó felhasználói fiók neve. | Igen      |
| jelszó          | A felhasználói fiók jelszava.   | Igen      |
| linkedServiceName | A tárolóhoz csatolt szolgáltatás neve, amely a HDInsight-fürt által használt Blob-tárolóra hivatkozik. <p>Jelenleg nem adhat meg Data Lake Store-hoz csatolt szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Store-hoz, előfordulhat, hogy a Data Lake Store-beli adatokhoz hive vagy Pig parancsfájlokból fér hozzá. </p> | Igen      |

## <a name="azure-batch-linked-service"></a>Azure Batch-alapú szolgáltatás
Létrehozhat egy Batch-hez csatolt szolgáltatást a virtuális gépek (VM-ek) kötegkészletének adat-előállítóba történő regisztrálásához. A Microsoft .NET egyéni tevékenységeket a Batch vagy a HDInsight használatával is futtathatja.

Ha még nem használja a Batch szolgáltatást:

* Ismerje meg az [Azure Batch alapjait.](../../batch/batch-technical-overview.md)
* Ismerje meg a [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) parancsmag. Ezzel a parancsmagból batch-fiókot hozhat létre. Vagy létrehozhatja a Batch-fiókot az [Azure Portal](../../batch/batch-account-create-portal.md)használatával. A parancsmag használatáról a [PowerShell használata kötegfiók kezeléséhez](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)című témakörben talál részletes információt.
* Ismerje meg a [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) parancsmag. Ezzel a parancsmagból kötegkészletet hozhat létre.

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

Az **accountName** tulajdonsághoz fűzzék hozzá **a\< . régió\> nevét** a kötegszámla nevéhez. Példa:

```json
"accountName": "mybatchaccount.eastus"
```

Egy másik lehetőség a **batchUri-végpont** biztosítása. Példa:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| type              | Állítsa a típustulajdonságot **az AzureBatch tulajdonságra.** | Igen      |
| accountName       | A Batch-fiók neve.         | Igen      |
| accessKey (accessKey)         | A Batch-fiók hozzáférési kulcsa.  | Igen      |
| készletneve          | A virtuális gépek készletének neve.    | Igen      |
| linkedServiceName | A Batch-alapú szolgáltatáshoz társított tárolókapcsolt szolgáltatás neve. Ez a csatolt szolgáltatás a tevékenység futtatásához és a tevékenységvégrehajtási naplók tárolásához szükséges átmeneti fájlokhoz használatos. | Igen      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning csatolt szolgáltatás
Létrehozhat egy Machine Learning-hez kapcsolt szolgáltatást a Machine Learning-kötegpontozási végpont regisztrálásához egy adat-előállítóba.

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
| Tulajdonság   | Leírás                              | Kötelező |
| ---------- | ---------------------------------------- | -------- |
| Típus       | Állítsa be a típustulajdonságot **Az AzureML**. | Igen      |
| mlVégpont | A kötegelt pontozási URL-cím.                   | Igen      |
| apiKey (apiKey)     | A közzétett munkaterületi modell API-ja.     | Igen      |

## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics kapcsolt szolgáltatása
Létrehozhat egy Data Lake Analytics-alapú szolgáltatást, amely egy Data Lake Analytics-számítási szolgáltatást egy Azure-adatfeldolgozóhoz kapcsol. A Data Lake Analytics U-SQL tevékenység a folyamatban hivatkozik erre a csatolt szolgáltatás. 

Az alábbi táblázat a JSON-definícióban használt általános tulajdonságokat ismerteti:

| Tulajdonság                 | Leírás                              | Kötelező                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Állítsa be a típustulajdonságot **az AzureDataLakeAnalytics**tulajdonságra. | Igen                                      |
| accountName          | A Data Lake Analytics-fiók neve.  | Igen                                      |
| dataLakeAnalyticsUri | A Data Lake Analytics URI.           | Nem                                       |
| subscriptionId       | Az Azure-előfizetés azonosítója.                    | Nem<br /><br />(Ha nincs megadva, a rendszer az adat-előállító előfizetést használja.) |
| resourceGroupName    | Az Azure erőforráscsoport neve.                | Nem<br /><br /> (Ha nincs megadva, a rendszer az adat-előállító erőforráscsoportot használja.) |

### <a name="authentication-options"></a>Hitelesítési lehetőségek
A Data Lake Analytics csatolt szolgáltatás, választhat a hitelesítés egyszerű szolgáltatás vagy egy felhasználói hitelesítő adatok használatával.

#### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatáshitelesítés (ajánlott)
Az egyszerű szolgáltatáshitelesítés használatához regisztráljon egy alkalmazásentitást az Azure Active Directoryban (Azure AD). Ezután adjon hozzáférést az Azure AD-nek a Data Lake Store-hoz. A részletes lépéseket a [Szolgáltatás-szolgáltatás hitelesítése című témakörben található.](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md) Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:
* Alkalmazásazonosító
* Alkalmazáskulcs 
* Bérlőazonosító

A szolgáltatásegyszerű hitelesítés használata a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Kötelező |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Az alkalmazás ügyfélazonosítója.     | Igen      |
| servicePrincipalKey | Az alkalmazás kulcsa.           | Igen      |
| Bérlő              | A bérlői adatok (tartománynév vagy bérlőazonosító), ahol az alkalmazás található. Ezen információk leése érdekében vigye az egeret az Azure Portal jobb felső sarkába. | Igen      |

**Példa: Egyszerű szolgáltatáshitelesítés**
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
A Data Lake Analytics felhasználói hitelesítő adatainak hitelesítéséhez adja meg a következő tulajdonságokat:

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| engedélyezés | A Data Factory Editor alkalmazásban válassza az **Engedélyezés** gombot. Adja meg az automatikusan létrehozott engedélyezési URL-címet hozzárendelt hitelesítő adatokat ehhez a tulajdonsághoz. | Igen      |
| Munkamenet     | Az OAuth-munkamenet azonosítója az OAuth engedélyezési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan létrejön a Data Factory Editor használatakor. | Igen      |

**Példa: Felhasználói hitelesítő adatok hitelesítése**
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

#### <a name="token-expiration"></a>Token lejárata
Az **Engedélyezés** gomb kiválasztásával létrehozott engedélyezési kód egy meghatározott időköz után lejár. 

A hitelesítési jogkivonat lejártakor a következő hibaüzenet jelenhet meg: 

  Hitelesítő adatok működési hibája: invalid_grant - AADSTS70002: Hiba a hitelesítő adatok érvényesítése közben. AADSTS70008: A megadott hozzáférési támogatás lejárt vagy visszavonásra került. Nyomazonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelációs azonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Időbélyeg: 2015-12-15 21:09:31Z

Az alábbi táblázat a lejáratokat mutatja a felhasználói fiók típusa szerint: 

| Felhasználó típusa                                | Lejárat után                            |
| :--------------------------------------- | :--------------------------------------- |
| Az Azure AD által *nem* kezelt felhasználói fiókok (Hotmail, Live és így tovább) | 12 órája.                                 |
| Az Azure AD által *kezelt* felhasználói fiókok | 14 nappal az utolsó szelet futtatása után. <br /><br />90 nap, ha egy oauth-alapú csatolt szolgáltatáson alapuló szelet 14 naponta legalább egyszer fut. |

A hiba elkerülése vagy megoldása érdekében engedélyezze újra az **Engedélyezés** gombot, amikor a token lejár. Ezután telepítse újra a csatolt szolgáltatást. A **munkamenetazonosító** és az **engedélyezési** tulajdonságokhoz programozott módon is létrehozhat értékeket a következő kód használatával:

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

A példában szereplő Data Factory-osztályokról a következő témakörökben talál részleteket:
* [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Hivatkozás hozzáadása a Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll fájlhoz a **WindowsFormsWebAuthenticationDialog** osztályhoz. 

## <a name="azure-sql-linked-service"></a>Azure SQL Database társított szolgáltatás
Sql-csatolt szolgáltatást hozhat létre, és a [Tárolt eljárási tevékenységgel használhatja](data-factory-stored-proc-activity.md) a data factory-folyamatból tárolt eljárás meghívásához. További információ: [Azure SQL connector.](data-factory-azure-sql-connector.md#linked-service-properties)

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse kapcsolt szolgáltatás
Létrehozhat egy SQL Data Warehouse csatolt szolgáltatást, és a [tárolt eljárási tevékenységgel](data-factory-stored-proc-activity.md) együtt meghívhat egy tárolt eljárást egy Data Factory-folyamatból. További információ: [Azure SQL Data Warehouse connector.](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)

## <a name="sql-server-linked-service"></a>SQL Server csatolt szolgáltatás
Létrehozhat egy SQL Server-csatolt szolgáltatást, és a [Tárolt eljárási tevékenységgel](data-factory-stored-proc-activity.md) együtt meghívhat egy tárolt eljárást egy Data Factory-folyamatból. További információt az [SQL Server-összekötő című témakörben talál.](data-factory-sqlserver-connector.md#linked-service-properties)

