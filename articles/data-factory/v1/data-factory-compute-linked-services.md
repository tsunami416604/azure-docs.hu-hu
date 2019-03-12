---
title: Számítási környezetek Azure Data Factory által támogatott |} A Microsoft Docs
description: Tudnivalók a számítási környezetek, amelyek segítségével az Azure Data Factory-folyamatok (például Azure HDInsight) adatok átalakítás vagy folyamat.
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
ms.openlocfilehash: 0e0a249c53c90d3d8d03dcdb5fbb4f11f31c54df
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545149"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Számítási környezetek Azure Data Factory által támogatott
> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a társított szolgáltatások számítása](../compute-linked-services.md).

Ez a cikk ismerteti a használható számítási környezetek folyamat vagy átalakítási adatok. Azt is részletesen különböző konfigurációt (igény szerinti és bring-your-own), hogy a Data Factory támogatja a társított szolgáltatások, amelyek összekapcsolhatók konfigurálásakor számítási környezeteket az Azure data factoryt.

Az alábbi táblázat a Data Factory és a rajtuk futó tevékenységek által támogatott számítási környezetek listáját tartalmazza. 

| Számítási környezet                      | Tevékenységek                               |
| ---------------------------------------- | ---------------------------------------- |
| [Igény szerinti Azure HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [a saját HDInsight-fürt](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streamelési](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-tevékenységek: Kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Az Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Az Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [az SQL Server](#sql-server-linked-service) | [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Támogatott Data Factory a HDInsight-verziók
Az Azure HDInsight Hadoop fürt több verzió telepítését követően bármikor támogatja. Minden támogatott verziót hoz létre egy adott verzióját a Hortonworks Data Platform (HDP) telepítési és összetevők a terjesztési. 

A Microsoft a legújabb Hadoop-ökoszisztéma összetevők és frissíti a támogatott HDInsight-verziók listáját. Részletes információkért lásd: [HDInsight támogatott verziók](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux-alapú HDInsight 3.3-as verziója 2017. július 31-ig volt elavult. A Data Factory 1. verziójának igény szerinti HDInsight társított szolgáltatások ügyfelek kaptak, amíg 15 2017 December, tesztelése és frissítése a HDInsight újabb verziója. 2018. július 31-ig. a Windows-alapú HDInsight megszűnik.
>
> 

### <a name="after-the-retirement-date"></a>A kivezetési dátum után 

2017. December 15.:

- Már nem hozhat létre Linux-alapú HDInsight 3.3-as verziója (vagy korábbi verziók) fürtök segítségével egy igény szerinti HDInsight társított szolgáltatás a Data Factory 1. verzió. 
- Ha a [ **osType** és **verzió** tulajdonságok](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nem explicit módon szerepelnek a JSON-definíciót egy meglévő Data Factory 1. verziójának igény szerinti HDInsight társított szolgáltatás , módosította az alapértelmezett érték **verziója 3.1 osType = = Windows** való **verzió =\<HDI alapértelmezett legújabb\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType = Linux**.

2018. július 31-ig.: után

- Windows-alapú HDInsight-fürtök bármely verzióját egy igény szerinti HDInsight társított szolgáltatás használatával a Data Factory 1. verzió már nem készíthetők. 

### <a name="recommended-actions"></a>Javasolt műveletek 

- Győződjön meg arról, hogy használhatja-e a legújabb Hadoop-ökoszisztéma összetevők és javítások, frissítse a [ **osType** és **verzió** tulajdonságok](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) az érintett a Data Factory 1. verziójának igény szerinti HDInsight kapcsolódó szolgáltatás definíciók újabb Linux-alapú HDInsight-verziók (HDInsight 3.6). 
- 2017. December 15. tesztelje a Data Factory 1. verziójának Hive, Pig, MapReduce és a Hadoop streamelési tevékenységeket, hogy az érintett társított szolgáltatás. Győződjön meg arról, hogy kompatibilisek, az új **osType** és **verzió** alapértelmezett értéket (**verzió = 3.6-os**, **osType = Linux**) vagy a explicit HDInsight verzió és az operációs rendszer írja be, hogy szeretne frissíteni. 
  Kompatibilitási kapcsolatos további információkért lásd: [áttelepítése egy Windows-alapú HDInsight-fürtről egy Linux-alapú fürt](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) és [Mik azok a Hadoop-összetevők és a HDInsight-verziók?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Windows-alapú HDInsight-fürtök létrehozása a Data Factory 1. verziójának igény szerinti HDInsight társított szolgáltatás használatának folytatásához, explicit módon beállíthat egy **osType** való **Windows** 2017. December 15. előtt. Azt javasoljuk, hogy át a Linux-alapú HDInsight-fürtök előtt 2018. július 31-ig. 
- Egy igény szerinti HDInsight társított szolgáltatás végrehajtása a Data Factory 1. verziójának használatakor DotNet egyéni tevékenységei frissítés az DotNet egyéni tevékenység JSON-definíciót használja inkább az Azure Batch-társított szolgáltatást. További információkért lásd: [egyéni tevékenységek használata egy Data Factory-folyamatot](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Ha használja a meglévő, fürt bring-your-own HDInsight társított eszköz az 1-es verziójú Data Factory, vagy az Azure Data Factoryben, nincs művelet bring-your-own és igény szerinti HDInsight társított szolgáltatás megadása kötelező. Ezen esetekben a legújabb verzió támogatása a HDInsight-fürtök már lép életbe. 
>
> 


## <a name="on-demand-compute-environment"></a>Igény szerinti számítási környezet
A Data Factory egy igény szerinti konfigurációban teljes körűen felügyeli a számítási környezetet. Data Factory automatikusan létrehozza a számítási környezetet, mielőtt a rendszer elküld egy feladatot az adatok feldolgozása. Ha a feladat befejeződött, a Data Factory eltávolítja a számítási környezet. 

Társított szolgáltatás egy igény szerinti számítási környezetet hozhat létre. A társított szolgáltatást használja, a számítási környezet konfigurálása, valamint a feladat-végrehajtási, a kezelő és a műveletek rendszerindítása részletes beállításainak ellenőrzéséhez.

> [!NOTE]
> Az igény szerinti konfiguráció jelenleg csak a HDInsight-fürtök használata támogatott.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Az Azure HDInsight igény szerinti társított szolgáltatás
A Data Factory automatikusan létrehozhat egy Windows-alapú vagy Linux-alapú igény szerinti HDInsight-fürt adatok feldolgozására. A fürt létrehozása, amely rendelkezik a fürthöz társított storage-fiók ugyanabban a régióban. Használja a JSON **linkedServiceName** tulajdonság a fürt létrehozásához.

Vegye figyelembe a következőket *kulcs* pontokra vonatkozó igény szerinti HDInsight társított szolgáltatást:

* Az igény szerinti HDInsight-fürt nem jelenik meg az Azure-előfizetésében. A Data Factory szolgáltatás az Ön nevében az igény szerinti HDInsight-fürt kezeli.
* A naplók egy igény szerinti HDInsight-fürtön futó feladatok által a HDInsight-fürthöz társított tárfiókban lesz másolva. Ezek a naplók az Azure Portal eléréséhez a **tevékenység Futtatás részletei** ablaktáblán. További információkért lásd: [figyelése és kezelése a folyamatok](data-factory-monitor-manage-pipelines.md).
* Az időt, amely a HDInsight-fürt működik és futó feladatok számítunk fel.

> [!IMPORTANT]
> Vesz *20 perc* legalább egy igény szerinti HDInsight-fürt kiépítéséhez.
>
> 

### <a name="example"></a>Példa
A következő JSON egy Linux-alapú igény szerinti HDInsight társított szolgáltatás határozza meg. Data Factory automatikusan létrehoz egy *Linux-alapú* HDInsight-fürt adatszelet feldolgozásakor. 

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
> A HDInsight-fürt létrehoz egy *alapértelmezett tároló* az Azure Blob Storage, a JSON-megadott **linkedServiceName** tulajdonság. A kialakításból fakadóan HDInsight nem törli ezt a tárolót, a fürt törlésekor. Az egy igény szerinti HDInsight társított szolgáltatás egy HDInsight-fürt jön létre minden alkalommal, amikor egy szeletet kell feldolgozni, kivéve, ha van meglévő élő fürt (**timeToLive**). A fürt törlődik a feldolgozás végeztével. 
>
> Ahogy több szelet lesz feldolgozva, több tároló jelenik a Blob storage. Ha már nincs szüksége a tárolók feladatok hibaelhárításhoz, előfordulhat, hogy törölni kívánja a tárolók a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf<your Data Factory name>-<linked service name>-<date and time>`. Egy hasonló eszközzel [Microsoft Storage Explorer](https://storageexplorer.com/) törölheti a Blob Storage-tárolókat.
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Szükséges |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A type tulajdonság beállítása **HDInsightOnDemand**. | Igen      |
| clusterSize                  | A fürt feldolgozó- és adatcsomópontok száma. A HDInsight-fürt létrehozása 2 fő csomóponttal, ehhez a tulajdonsághoz megadott munkavégző csomópontok száma mellett. A csomópontok 4 maggal rendelkező, D3 méretű vannak. Egy 4-feldolgozó csomópontot tartalmazó fürtben 24 mag vesz igénybe (4\*4 = 16 mag, a feldolgozó csomópontokat, valamint 2\*4 = 8 mag fő csomópontok esetében). További információk a D3 csomag: [Linux-alapú Hadoop-fürtök a HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Igen      |
| timeToLive                   | A megengedett üresjárati idő az igény szerinti HDInsight-fürt számára. Itt adhatja meg, mennyi ideig az igény szerinti HDInsight-fürt marad életben, ha egy tevékenység Futtatás befejeződik, ha nincsenek más aktív feladatok a fürtben.<br /><br />Például ha egy tevékenység fut 6 percig tart, és **timeToLive** értéke 5 perc, a fürt a 6 percnek tevékenységfuttatási feldolgozása után 5 percig aktív marad. Ha egy másik tevékenység-végrehajtásonként a 6 perces ablakban, dolgoz fel ugyanazon a fürtön.<br /><br />Egy igény szerinti HDInsight-fürt létrehozása (Ez eltarthat egy ideig) drága művelet. A beállítás segítségével igény szerint a jobb teljesítmény érdekében egy adat-előállító szakember újból felhasználja az igény szerinti HDInsight-fürt.<br /><br />Ha a **timeToLive** értéket a következőre **0**, a fürt törlődik, amint az a tevékenység futtatása befejeződik. Azonban ha a magas érték, a fürt még aktívak maradhatnak, inaktív, szükségtelenül magas költségeket eredményez. Fontos a megfelelő értéket a saját igényei szerint.<br /><br />Ha a **timeToLive** értéke megfelelően van beállítva, több folyamatot is megoszthatja az igény szerinti HDInsight-fürt példányának. | Igen      |
| version                      | A HDInsight-fürt verzióját. Engedélyezett a HDInsight-verziók, lásd: [HDInsight támogatott verziók](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Ha ez az érték nincs megadva, a [HDI alapértelmezett legújabb](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) szolgál. | Nem       |
| linkedServiceName            | Az Azure Storage társított szolgáltatás, tárolására és az adatok feldolgozása az igény szerinti fürt használni. Ez a tárfiók ugyanabban a régióban a HDInsight-fürt jön létre.<p>Jelenleg nem hozható létre egy igény szerinti HDInsight-fürtöt, amely az Azure Data Lake Store használ a tároló. A feldolgozása a Data Lake Store HDInsight eredmény adatokat tárolni szeretné, ha a másolási tevékenység használatával az adatok másolása Blob storage-ból a Data Lake Store. </p> | Igen      |
| additionalLinkedServiceNames | Megadja a HDInsight társított szolgáltatás további tárfiókok. Data Factoryt a storage-fiókok az Ön nevében regisztrál. A storage-fiókok a HDInsight-fürt ugyanabban a régióban kell lennie. A HDInsight-fürt létrehozása a megadott tárfiók ugyanabban a régióban a **linkedServiceName** tulajdonság. | Nem       |
| osType                       | Operációs rendszer típusa. Engedélyezett értékek a következők **Linux** és **Windows**. Ha ez az érték nincs megadva, **Linux** szolgál.  <br /><br />Javasoljuk, hogy Linux-alapú HDInsight-fürtök használatával. A kivezetési dátum, a HDInsight, a Windows rendszer 2018. július 31-ig. | Nem       |
| hcatalogLinkedServiceName    | Az Azure SQL társított szolgáltatás, amely a HCatalog-adatbázis neve. Az igény szerinti HDInsight-fürtöt a metaadattár, az SQL database használatával jön létre. | Nem       |

#### <a name="example-linkedservicenames-json"></a>Példa: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Speciális tulajdonságok
Az igény szerinti HDInsight-fürt részletes konfigurációjáig megadhatja a következő tulajdonságokkal:

| Tulajdonság               | Leírás                              | Szükséges |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Megadja a HDInsight-fürt létrehozása az alapvető konfigurációs paramétereket (core-site.xml). | Nem       |
| hBaseConfiguration     | Megadja a HDInsight-fürt HBase-konfigurációs paramétereket (a hbase-site.xml). | Nem       |
| hdfsConfiguration      | Megadja a HDInsight-fürt HDFS-konfigurációs paramétereket (hdfs-site.xml). | Nem       |
| hiveConfiguration      | Megadja a HDInsight-fürtben Hive-konfigurációs paramétereket (hive-site.xml). | Nem       |
| mapReduceConfiguration | Megadja a HDInsight-fürt MapReduce-konfigurációs paramétereket (mapred-site.xml). | Nem       |
| oozieConfiguration     | Megadja a HDInsight-fürt az Oozie-konfigurációs paramétereket (az oozie-site.xml). | Nem       |
| stormConfiguration     | Megadja a HDInsight-fürt Storm-konfigurációs paramétereket (a storm-site.xml). | Nem       |
| yarnConfiguration      | Megadja a HDInsight-fürt YARN-konfigurációs paramétereket (yarn-site.xml). | Nem       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Példa: Igény szerinti HDInsight-fürt konfigurációját speciális tulajdonságokkal

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

### <a name="node-sizes"></a>Csomópontméret
Adja meg a fő, az adatok és a ZooKeeper-csomópontok méretét, használja a következő tulajdonságokkal: 

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Beállítja a fő csomópont méretét. Az alapértelmezett érték **Standard_D3**. További információkért lásd: [csomópontméretűek megadása](#specify-node-sizes). | Nem       |
| dataNodeSize      | Beállítja az adatok csomópont méretét. Az alapértelmezett érték **Standard_D3**. | Nem       |
| zookeeperNodeSize | Beállítja a ZooKeeper-csomópont méretét. Az alapértelmezett érték **Standard_D3**. | Nem       |

#### <a name="specify-node-sizes"></a>Adja meg a csomópontok méretei
Meg kell adnia az előző szakaszban leírt tulajdonságokkal karakterlánc-értékek, lásd: [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md). Az értékeket meg kell felelnie a parancsmagok és API-k hivatkozott [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md). A nagy (alapértelmezett) adatok csomópontméret 7 GB memóriával rendelkezik. Ez nem elegendő a forgatókönyvhöz. 

Ha szeretne létrehozni, D4 méretű fő csomópontból és a feldolgozó csomópontokat, adja meg **Standard_D4** értékeként a **headNodeSize** és **dataNodeSize** tulajdonságai: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ha ezek a Tulajdonságok értékkel, a következő üzenetet láthatja:

  Nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. Fürt állapota: "Error". Üzenet: 'PreClusterCreationValidationFailure'. 
  
Ha ezt az üzenetet látja, biztosítása érdekében, hogy használja a parancsmagot, és a tábla API nevei [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> A Data Factory jelenleg nem támogatja az elsődleges tárolóként Data Lake Store használó HDInsight-fürtök. Az Azure Storage használata az elsődleges tárolóként HDInsight-fürtök. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Bring-your-own számítási környezet
A Data Factory társított szolgáltatásként regisztrálhat egy meglévő számítási környezettel. A számítási környezet kezelhető. A Data Factory szolgáltatás használ a számítási környezet tevékenységek végrehajtásához.

Ezt a konfigurációtípust támogatott a következő számítási környezetekben:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL Database, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Az Azure HDInsight társított szolgáltatás
Létrehozhat egy HDInsight társított szolgáltatás regisztrálni a saját HDInsight-fürt a Data Factory.

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
| clusterUri        | A HDInsight-fürt URI azonosítója.        | Igen      |
| felhasználónév          | Egy meglévő HDInsight-fürthöz való kapcsolódáshoz használandó felhasználói fiók neve. | Igen      |
| jelszó          | A felhasználói fiók jelszava.   | Igen      |
| linkedServiceName | A Blob Storage a HDInsight-fürt által használt a storage-beli társított szolgáltatás neve. <p>Jelenleg nem adhat meg egy Data Lake Store-beli társított szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt a Data Lake Store hozzáférése van, előfordulhat, hogy a Data Lake Store adatigénylésekre a Hive és Pig-parancsfájlok alapján. </p> | Igen      |

## <a name="azure-batch-linked-service"></a>Az Azure Batch-beli társított szolgáltatás
Regisztrálja a Batch-készlet, a virtuális gépek (VM) egy adat-előállítóhoz társított Batch szolgáltatás is létrehozhat. A Microsoft .NET egyéni tevékenység kötegelt vagy a HDInsight segítségével is futtathatja.

Ha most ismerkedik a Batch szolgáltatás segítségével:

* Ismerje meg [Azure Batch alapjai](../../batch/batch-technical-overview.md).
* További információ a [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) parancsmagot. Ez a parancsmag segítségével hozzon létre egy Batch-fiókot. Vagy, a Batch-fiók használatával is létrehozhat a [az Azure portal](../../batch/batch-account-create-portal.md). A parancsmag használatával kapcsolatos részletes információkért lásd: [PowerShell használata kezelheti a Batch-fiók](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* További információ a [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) parancsmagot. Ez a parancsmag segítségével hozzon létre egy Batch-készletet.

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

Az a **accountName** tulajdonság, Hozzáfűzés **.\< régió neve\>**  a batch-fiók nevére. Példa:

```json
"accountName": "mybatchaccount.eastus"
```

Egy másik lehetőség az, hogy adja meg a **batchUri** végpont. Példa:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| type              | A type tulajdonság beállítása **AzureBatch**. | Igen      |
| accountName       | A Batch-fiók neve.         | Igen      |
| accessKey         | A Batch-fiók hozzáférési kulcsára.  | Igen      |
| poolName          | Virtuális gépek készletének neve.    | Igen      |
| linkedServiceName | Az ebben a kötegben társított storage társított szolgáltatás neve társított szolgáltatást. A társított szolgáltatás átmeneti fájlok, amelyek szükségesek a tevékenység futtatásához, és a tevékenység végrehajtási naplók tárolására szolgál. | Igen      |

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning-beli társított szolgáltatás
Létrehozhat egy Machine Learning társított szolgáltatás egy Machine Learning kötegelt pontozási végpontjához adat-előállítóval történő regisztrálásához.

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
| Typo       | A type tulajdonság beállítása **AzureML**. | Igen      |
| mlEndpoint | A kötegelt pontozás URL-CÍMÉT.                   | Igen      |
| apiKey     | A közzétett munkaterület-modell API-t.     | Igen      |

## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics hivatkozott szolgáltatást
Létrehozhat egy Data Lake Analytics hivatkozott szolgáltatást, egy Data Lake Analytics számítási szolgáltatás egy Azure data factoryt. A Data Lake Analytics U-SQL-tevékenység, a folyamat a társított szolgáltatásra vonatkozik. 

A következő táblázat ismerteti az általános tulajdonságokat a JSON-definíciójában használt:

| Tulajdonság                 | Leírás                              | Szükséges                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | A type tulajdonság beállítása **AzureDataLakeAnalytics**. | Igen                                      |
| accountName          | A Data Lake Analytics-fiók nevét.  | Igen                                      |
| dataLakeAnalyticsUri | A Data Lake Analytics URI azonosítója.           | Nem                                       |
| subscriptionId       | Az Azure-előfizetés azonosítóját.                    | Nem<br /><br />(Ha nincs megadva, a data factory előfizetés szerepel.) |
| resourceGroupName    | Az Azure-erőforráscsoport nevét.                | Nem<br /><br /> (Ha nincs megadva, a data factory erőforráscsoport szerepel.) |

### <a name="authentication-options"></a>Hitelesítési lehetőségek
A Data Lake Analytics hivatkozott szolgáltatást a hitelesítés egy egyszerű szolgáltatást, vagy egy felhasználói hitelesítő adatok használatával választhat.

#### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)
Egyszerű szolgáltatásnév hitelesítésével használni, regisztrálja az Azure Active Directoryban (Azure AD) egy alkalmazás entitás. Ezt követően az Azure AD hozzáférési jogot Data Lake Store. Részletes lépéseiért lásd: [szolgáltatások közötti hitelesítés](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:
* Alkalmazásazonosító
* Alkalmazáskulcs 
* Bérlőazonosító

Egyszerű szolgáltatásnév hitelesítése használja a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Szükséges |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Az alkalmazás ügyfél-azonosítót.     | Igen      |
| servicePrincipalKey | Az alkalmazás kulcsa.           | Igen      |
| bérlő              | Ahol az alkalmazás megtalálható bérlői adatok (tartomány neve vagy a bérlő azonosítója). Ezek az információk lekéréséhez az egérrel az Azure portal jobb felső sarkában. | Igen      |

**Példa: Egyszerű szolgáltatásnév hitelesítése**
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
A Data Lake Analytics felhasználói hitelesítő adja meg a következő tulajdonságokkal:

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| Engedélyezési | A Data Factory Editorban válassza a **engedélyezés** gombra. Adja meg a hitelesítő adat, amelyet az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ezt a tulajdonságot. | Igen      |
| sessionId     | Az OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használhatók fel. Ez a beállítás automatikusan jön létre Data Factory Editor használata esetén. | Igen      |

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

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Az engedélyezési kód kiválasztásával létrehozott a **engedélyezés** gomb beállított idő után jár le. 

Láthatja a következő hibaüzenetet kapja, amikor a hitelesítési jogkivonat lejár: 

  Hitelesítőadat-műveleti hiba: invalid_grant - AADSTS70002: Hiba történt a hitelesítő érvényesítésekor. AADSTS70008: A megadott hozzáférési engedély lejárt vagy visszavont. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

Az alábbi táblázat lejárhat felhasználói fiók típusa szerint: 

| Felhasználó típusa                                | Tétlenség után lejár                            |
| :--------------------------------------- | :--------------------------------------- |
| Felhasználói fiókok, amelyek *nem* kezeli az Azure ad-ben (Hotmail, élő és így tovább) | 12 óra.                                 |
| Felhasználói fiókok, amelyek *vannak* kezeli az Azure ad-ben | Futtassa az utolsó szelet 14 nappal. <br /><br />90 nap, ha olyan szelet, amely alapján az OAuth-alapú társított szolgáltatás fut. 14 naponta legalább egyszer |

Elkerülése érdekében, vagy a hiba megoldásában, engedélyezze újra kiválasztásával a **engedélyezés** gombot, ha a jogkivonat lejár. Ezután telepítse újra a társított szolgáltatást. Értékeit is létrehozhat a **sessionId** és **engedélyezési** tulajdonságok programozott módon az alábbi kód használatával:

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

A Data Factory kód ebben a példában használt osztályok, lásd:
* [AzureDataLakeStoreLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse osztályban](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Vegyen fel egy hivatkozást a Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll a **WindowsFormsWebAuthenticationDialog** osztály. 

## <a name="azure-sql-linked-service"></a>Az Azure SQL társított szolgáltatás
Hozzon létre egy SQL társított szolgáltatást, és együtt használja, a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. További információkért lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Az Azure SQL Data Warehouse-beli társított szolgáltatás
Hozzon létre egy SQL Data Warehouse-beli társított szolgáltatást, és együtt használja, a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. További információkért lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Az SQL Server-alapú társított szolgáltatás
Az SQL Server-alapú társított szolgáltatás létrehozása, és együtt használja, a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. További információkért lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#linked-service-properties).

