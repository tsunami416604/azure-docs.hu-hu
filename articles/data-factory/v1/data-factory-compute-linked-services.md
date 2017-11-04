---
title: "Azure Data Factory által támogatott környezetek számítási |} Microsoft Docs"
description: "További információk a számítási környezetek esetén, amelyek segítségével az Azure Data Factory folyamatok (például az Azure HDInsight)-átalakítási és folyamat-adatokat."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 1547b5c3a5c629b85ff5fa9de6b39b25531d9ec9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Számítási környezetek Azure Data Factory által támogatott
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [V2 társított szolgáltatások számítási](../compute-linked-services.md).

Ez a cikk ismerteti a különböző számítási környezeteket, melyekkel folyamat vagy átalakítási adatok. Emellett biztosítja az adat-előállító támogatott, ha ezek linking összekapcsolt szolgáltatások konfigurálása (igény szerinti és kapcsolja a saját) különböző konfigurációkkal kapcsolatos részletek számítási környezetek számára egy Azure data factory.

A következő táblázat felsorolja a Data Factory és az ezeken futó tevékenységek által támogatott számítási környezetek. 

| Számítási környezet                      | tevékenységek                               |
| ---------------------------------------- | ---------------------------------------- |
| [Igény szerinti HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streamelési](data-factory-hadoop-streaming-activity.md) |
| [Az Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-tevékenységek: kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Az Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Az Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Tárolt eljárás](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Azure Data Factory-HDInsight támogatott verziói
Az Azure HDInsight Hadoop fürt több verziója, amely bármikor telepíthető támogatja. Minden egyes verzió choice hoz létre, egy adott verziójához a Hortonworks Data Platform (HDP) telepítési és összetevők belüli, hogy a terjesztési. A Microsoft tartja a frissítések a HDInsight Hadoop-ökoszisztémával legújabb összetevők és javításokat a támogatott verziók listáját. Részletes információkért lásd: [HDInsight-verziókról támogatott](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> A Linux-alapú HDInsight 3.3-as verzió 2017. július 31 lett használatból. Data Factory v1 igény szerinti HDInsight összekapcsolt szolgáltatások ügyfelek kaptak, amíg December 15, 2017, teszteléséhez, és a HDInsight újabb verziójára. A Windows-alapú HDInsight 2018 július 31 a rendszerből.
>
> 

**Mi történik a kivezetési dátum után** 

Miután 2017. December 15.:

- Nem lehet létrehozni a Linux-alapú HDInsight 3.3-as verzió (vagy korábbi verziójú) igény szerinti HDInsight társított szolgáltatás használata az Azure Data Factory v1-fürtök. 

- Ha a [osType és/vagy Version tulajdonság](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) nincs explicit módon megadott meglévő Azure Data Factory v1 igény szerinti HDInsight társított szolgáltatás JSON-definíciók, az alapértelmezett érték változnak a **verzió = 3.1, osType = Windows** való **verzió 3.6, osType = = Linux**.

Miután július 31, 2018:

- Már nem lehet létrehozni a Windows-alapú HDInsight-fürtök igény szerinti HDInsight társított szolgáltatás az Azure Data Factory v1 bármilyen. 

 **Javasolt művelet** 

- Frissítés a [osType és/vagy Version tulajdonság](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) az érintett Azure Data Factory v1 igény szerinti HDInsight társított szolgáltatás definíciók újabb Linux-alapú HDInsight segítségével verziók (HDInsight 3.6), hogy biztosan a legfrissebb Hadoop használatával ökoszisztéma összetevők és javításokat. 
- 2017. December 15. az Azure Data Factory V1 Hive, Pig, MapReduce, és a Hadoop tesztelése előtt győződjön meg arról, hogy az érintett társított szolgáltatás hivatkozó tevékenységek streaming rendszerrel való kompatibilitás szempontjából az új *osType* és/vagy  *Verzió* alapértelmezett érték (verzió 3.6, osType = = Linux) vagy a explicit HDInsight-verzió, és frissíti a osType. Kompatibilitási kapcsolatos további információkért tekintse át a [áttelepítése egy Windows-alapú HDInsight-fürtöt a Linux-alapú fürtre](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) és [Mik azok a Hadoop-összetevők és verziók a hdinsight eszközzel?](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) dokumentáció weblapjain. 
- Explicit módon beállítva a osType Windows előtt 2017. December 15. Ha továbbra is a Windows-alapú HDInsight-fürtök létrehozása az Azure Data Factory v1On-igény szerinti HDInsight társított szolgáltatás segítségével szeretné. Azonban továbbra is ajánlott az áttelepítés Linux-alapú HDInsight-fürtök 2018 július 31 előtt. 
- Az DotNet egyéni tevékenység JSON-definíciót egy Azure Batch társított szolgáltatást használja, akkor frissítse, ha igény szerinti HDInsight társított szolgáltatás segítségével hajtható végre az Azure Data Factory v1DotNet egyéni tevékenység. A további a [egyéni tevékenységeket felhasználni egy Azure DataFactory folyamat](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-use-custom-activities) dokumentációs weblap. 

>[!Note]
>A már meglévő Bring Your Own fürt (BYOC) HDInsight társított szolgáltatást az Azure Data Factory v1 vagy azok, akik BYOC és igény szerinti HDInsightLinked szolgáltatás az Azure Data Factory v2 felhasználók a legújabb támogatási házirend ofAzure HDInsight fürtök már kényszerítése, ezért nincs szükség beavatkozásra; csak. 
>
> 


## <a name="on-demand-compute-environment"></a>Igény szerinti számítási környezet
Az ilyen típusú konfigurációs a számítógépes környezet teljes kezeli az Azure Data Factory szolgáltatásnak. Az automatikusan hozta létre a Data Factory szolgáltatásnak egy feladat folyamata adatokat küldött, és eltávolítja a feladat befejezése előtt. Csatolt szolgáltatást az igény szerinti számítási környezetet hozhat létre, konfigurálja és feladatok végrehajtásának, a kiszolgálófürt-felügyelet és a műveletek rendszerindítása részletes beállításait.

> [!NOTE]
> Az igény szerinti konfigurációs jelenleg csak az Azure HDInsight-fürtök támogatott.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Az Azure HDInsight igény társított szolgáltatás
Az Azure Data Factory szolgáltatásnak automatikusan hozhat létre egy Windows/Linux-alapú igény szerinti HDInsight-fürt adatfeldolgozásra történő. A fürt létrehozása a tárfiók (linkedServiceName tulajdonságot a JSON-ban) a fürthöz tartozó ugyanabban a régióban.

Vegye figyelembe a következőket **fontos** kapcsolatos igény szerinti HDInsight pontok társított szolgáltatás:

* Nem látja a igény szerinti HDInsight-fürt létrehozása az Azure-előfizetéshez. az Azure Data Factory szolgáltatásnak kezeli az igény szerinti HDInsight-fürthöz az Ön nevében.
* A naplók az igény szerinti HDInsight-fürtök a futó feladatok a tárfiókot, a HDInsight-fürthöz társított lesz másolva. Ezek a naplók az Azure-portálról végezheti el a **tevékenység futtatása részletei** panelen. Lásd: [figyelő és folyamatok kezelése](data-factory-monitor-manage-pipelines.md) cikkben alább.
* Van szó, csak a az időpontot, amikor a HDInsight-fürt mentése és a futó feladatok.

> [!IMPORTANT]
> Általában tart **20 perc** vagy nagyobb az igény szerinti Azure HDInsight-fürtök kiépítéséhez.
>
> 

### <a name="example"></a>Példa
A következő JSON igény kapcsolódó HDInsight Linux-alapú szolgáltatás határozza meg. A Data Factory szolgáltatásnak automatikusan létrehoz egy **Linux-alapú** HDInsight-fürt adatok szelet feldolgozása közben. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Windows-alapú HDInsight-fürtöt használ, állítsa be **osType** való **windows** , vagy ne használjon a tulajdonság, mert az alapértelmezett érték: windows.  

> [!IMPORTANT]
> A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer mindig létrehoz egy HDInsight-fürt, amikor fel kell dolgozni egy szeletet, kivéve, ha van meglévő élő fürt (**timeToLive**), majd a feldolgozás végén a rendszer törli a fürtöt. 
>
> Ahogy egyre több szelet lesz feldolgozva, egyre több tároló jelenik meg az Azure Blob Storage-tárban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](http://storageexplorer.com/).
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Szükséges |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A type tulajdonságot kell megadni **HDInsightOnDemand**. | Igen      |
| Nagyobbnak                  | A fürt munkavégző/adatok csomópontok száma. A HDInsight-fürt együtt ez a tulajdonság a megadott munkavégző csomópontok száma 2 átjárócsomópontokkal hozza létre. A csomópontok egy 4 munkavégző csomópontot tartalmazó fürtben veszi 24 mag, 4 mag, rendelkező standard, D3 méretű vannak (4\*a munkavégző csomópontokról, valamint 2 processzormag, 4 = 16\*az átjárócsomópontokkal processzormag, 4 = 8). Lásd: [hdinsight létrehozása Linux-alapú Hadoop-fürtök](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) a standard, D3 réteg vonatkozó további információért. | Igen      |
| a TimeToLive tulajdonság                   | A megengedett üresjárati idő az igény szerinti HDInsight-fürthöz. Meghatározza, mennyi ideig az igény szerinti HDInsight-fürt aktív marad egy tevékenység fut, ha nincsenek a fürt más aktív feladatok befejezése után.<br/><br/>Például ha egy tevékenység futott 6 percig tart, és az élettartam értéke 5 perc, a fürt marad, a figyelő életben 5 perc, a 6 percnél feldolgozásának a tevékenység futtatása után. Ha egy másik tevékenységfuttatási 6-perc időkeretet, dolgoz fel ugyanabban a fürtben.<br/><br/>Igény szerinti HDInsight fürtök létrehozásával egy (igénybe vehet) drága művelet, ezt a beállítást, mint egy adat-előállító teljesítményének javításával újból felhasználja az igény szerinti HDInsight-fürtök által szükséges Igen használja.<br/><br/>A TimeToLive tulajdonság értékét 0-ra állítja be, ha törölni a fürtöt, amint a tevékenység futtatása befejeződött. Mivel a magas értéket ad meg, ha a fürt felfüggesztheti üresjárati feleslegesen magas költségeket eredményez. Ezért fontos, hogy beállította-e a megfelelő értéket a igényei szerint.<br/><br/>A timetolive tulajdonság értékének megfelelően van beállítva, ha több folyamatok megoszthatja az igény szerinti HDInsight-fürt példányának. | Igen      |
| Verzió                      | A HDInsight-fürt verziószáma. Az alapértelmezett értéke 3.1 Windows-fürt és a Linux-fürt 3.2-es verzióját. | Nem       |
| linkedServiceName            | Az Azure tárolás társított szolgáltatásának történő tárolására és feldolgozására adatok az igény szerinti fürt által használható. A HDInsight-fürt létrehozása az Azure Storage-fiók ugyanabban a régióban.<p>Jelenleg nem hozható létre, amely egy Azure Data Lake Store használ a tárolási igény szerinti HDInsight-fürtöt. Ha szeretné tárolni az eredményadatok a HDInsight-feldolgozás alatt álló egy Azure Data Lake Store-ból, a másolási tevékenység segítségével az adatok másolása az Azure Blob Storage-ból az Azure Data Lake Store. </p> | Igen      |
| additionalLinkedServiceNames | Adja meg a további tárfiókok a HDInsight a társított szolgáltatás, hogy a Data Factory szolgáltatásnak is regisztrálja őket az Ön nevében. Ezekre a tárfiókokra a HDInsight-fürthöz, és a linkedServiceName által megadott tárfiók ugyanabban a régióban létrehozott ugyanabban a régióban kell lennie. | Nem       |
| osType                       | Az operációs rendszer típusát. Két érték engedélyezett: (alapértelmezett) Windows és Linux | Nem       |
| hcatalogLinkedServiceName    | A név az Azure SQL társított szolgáltatásnak, mutasson az HCatalog-adatbázisra. Az igény szerinti HDInsight-fürt létrehozása az Azure SQL database segítségével a metaadattárhoz. | Nem       |

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON – példa

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

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
A méret, head, az adatok és a zookeeper csomópontok tulajdonságok a következők adhatók meg: 

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Meghatározza az átjárócsomópont méretét. Az alapértelmezett érték: Standard, D3. Tekintse meg a **csomópont méretét megadó** című szakaszban talál információt. | Nem       |
| dataNodeSize      | Az adatcsomóponton méretét határozza meg. Az alapértelmezett érték: Standard, D3. | Nem       |
| zookeeperNodeSize | Az itt üzemeltetőjének csomópont méretét adja meg. Az alapértelmezett érték: Standard, D3. | Nem       |

#### <a name="specifying-node-sizes"></a>Csomópont méret megadása
Tekintse meg a [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md) meg kell adnia az előző szakaszban említett tulajdonságok a karakterlánc-értékek a cikkben találhat. Az értékeket meg kell felelniük a a **parancsmagok & API-k** a cikk hivatkozik. Ahogy látja, a cikkben, az adatok (alapértelmezett) nagy méretű fürtcsomópont 7 GB memória, amely nem lehet adott esetben elég jó. 

Ha szeretne létrehozni, D4 méretű átjárócsomópontokkal és feldolgozó csomópontokat, adja meg **standard szintű, D4** headNodeSize és dataNodeSize tulajdonság értéke. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ezeket a tulajdonságokat a hibás érték megadása esetén is megjelenhet a következő **hiba:** nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. A fürt állapota: „hiba”. Üzenet: "PreClusterCreationValidationFailure". Ha ez a hibaüzenet jelenik meg, győződjön meg arról, hogy használja a **PARANCSMAG & API-k** nevét a táblázatból a [virtuálisgép-méretek](../../virtual-machines/linux/sizes.md) cikk.  

> [!NOTE]
> Azure Data Factory jelenleg nem támogatja a HDInsight-fürtök az Azure Data Lake Store elsődleges tárolóként. Azure Storage használják elsődleges tár a HDInsight-fürtök. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Kapcsolja a saját számítási környezet
Az ilyen típusú konfigurációs a felhasználók regisztrálhatják egy már meglévő informatikai környezete a Data Factory kapcsolt szolgáltatásként. A felhasználó a számítógépes környezet kezeli, és a Data Factory szolgáltatásnak a tevékenységek végrehajtásához használja.

Ez a fajta konfiguráció a következő számítási környezetek esetén támogatott:

* Az Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Az Azure SQL-adatbázis, az Azure SQL DW, SQL Server

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
| type              | A type tulajdonságot kell megadni **HDInsight**. | Igen      |
| clusterUri        | Az URI-je a HDInsight-fürthöz.        | Igen      |
| felhasználónév          | Adja meg a felhasználó egy meglévő HDInsight-fürtre való kapcsolódáshoz használt nevét. | Igen      |
| jelszó          | Adja meg a felhasználói fiók jelszavát.   | Igen      |
| linkedServiceName | Az Azure blob storage a HDInsight-fürt által használt az Azure Storage társított szolgáltatás neve. <p>Jelenleg nem adhat meg egy Azure Data Lake Store társított szolgáltatás ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Store, előfordulhat, hogy éri az az Azure Data Lake Store Hive/Pig-parancsfájlok. </p> | Igen      |

## <a name="azure-batch-linked-service"></a>Az Azure Batch társított szolgáltatás
Egy adat-előállító hozhat létre egy csatolt Azure Batch szolgáltatás regisztrálása virtuális gépek (VM) kötegelt készletét. .NET-egyéni tevékenységek Azure Batch vagy Azure HDInsight segítségével is futtathatja.

Lásd az alábbi témakörök, ha most ismerkedik az Azure Batch szolgáltatás:

* [Azure Batch alapjai](../../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintését.
* [Új AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) parancsmaggal hozhat létre Azure Batch-fiók (vagy) [Azure-portálon](../../batch/batch-account-create-portal.md) az Azure portál használata az Azure Batch-fiók létrehozásához. Lásd: [PowerShell használatával kezelheti az Azure Batch-fiók](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) témakör részletes útmutatást a parancsmag segítségével.
* [Új AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) parancsmaggal hozhat létre Azure Batch-készlet.

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

Hozzáfűzendő "**.\< régió neve\>**"nevére, a batch-fiók esetében a **accountName** tulajdonság. Példa:

```json
"accountName": "mybatchaccount.eastus"
```

Egy másik lehetőség egy adja meg a batchUri végpont a következő mintában látható módon:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Szükséges |
| ----------------- | ---------------------------------------- | -------- |
| type              | A type tulajdonságot kell megadni **AzureBatch**. | Igen      |
| Fióknév       | Az Azure Batch-fiók neve.         | Igen      |
| accessKey         | Az Azure Batch-fiók elérési kulcsának.  | Igen      |
| poolName          | A virtuálisgép-készlet neve.    | Igen      |
| linkedServiceName | Neve az Azure Storage társított szolgáltatásnak a kapcsolódó Azure-köteg szolgáltatással kapcsolatos. A társított szolgáltatás átmeneti fájlok kell futnia a tevékenység és a tevékenység végrehajtási naplók tárolására szolgál. | Igen      |

## <a name="azure-machine-learning-linked-service"></a>A társított szolgáltatásnak Azure gépi tanulás
A Machine Learning kötegelt scoring-végpontja számára egy adat-előállító regisztrálni Azure Machine Learning társított szolgáltatás létrehozása.

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
| Típus       | A type tulajdonságot kell megadni: **AzureML**. | Igen      |
| mlEndpoint | A kötegelt pontozás URL-CÍMÉT.                   | Igen      |
| apiKey     | A közzétett munkaterület-modell API.     | Igen      |

## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics társított szolgáltatás
Létrehozhat egy **Azure Data Lake Analytics** társított szolgáltatás az Azure Data Lake Analytics csatolásához számítási egy az Azure data factory szolgáltatást. A Data Lake Analytics U-SQL-tevékenység a feldolgozási szolgáltatásnak hivatkozik. 

A következő táblázat ismerteti a JSON-definícióból használt általános tulajdonságok. További választhat egyszerű szolgáltatásnév és felhasználói hitelesítő adatok hitelesítése.

| Tulajdonság                 | Leírás                              | Szükséges                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **típusa**                 | A type tulajdonságot kell megadni: **AzureDataLakeAnalytics**. | Igen                                      |
| **Fióknév**          | Az Azure Data Lake Analytics-fiók neve.  | Igen                                      |
| **datalakeanalyticsuri paraméter** | Az Azure Data Lake Analytics URI.           | Nem                                       |
| **előfizetés-azonosító**       | Az Azure előfizetés-azonosító                    | Nem (Ha nincs megadva, a data factory-előfizetése szerepel). |
| **erőforráscsoport-név**    | Azure erőforráscsoport-név                | Nem (Ha nincs megadva, az adat-előállító erőforráscsoport szerepel). |

### <a name="service-principal-authentication-recommended"></a>Szolgáltatás egyszerű hitelesítés (ajánlott)
Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és hozzáférést, a Data Lake Store-bA. Részletes útmutató: [szolgáltatások közötti hitelesítési](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:
* Alkalmazásazonosító
* Alkalmazás kulcs 
* Bérlőazonosító

Szolgáltatás egyszerű hitelesítés használatára a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Szükséges |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Adja meg az alkalmazás ügyfél-azonosítót.     | Igen      |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsot.           | Igen      |
| **Bérlői**              | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen      |

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

### <a name="user-credential-authentication"></a>Felhasználói hitelesítő adatok hitelesítése
Alternatív megoldásként használható felhasználói hitelesítő Data Lake Analytics megadását követően az alábbi tulajdonságokat:

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| **engedélyezési** | Kattintson a **engedélyezés** a Data Factory Editor gombra, és adja meg a hitelesítő adatok, amelyek az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ehhez a tulajdonsághoz. | Igen      |
| **munkamenet-azonosító**     | OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan jön létre, a Data Factory Editor használatakor. | Igen      |

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
Az engedélyezési kód használatával előállított a **engedélyezés** gomb némi várakozás után lejár. Lásd az alábbi táblázatban a különböző típusú felhasználói fiókokat a lejárati idejét. Jelenhetnek meg a következő hiba jelenik meg, ha a hitelesítési **-token érvényessége lejár**: hitelesítőadat-műveleti hiba: invalid_grant - AADSTS70002: Hiba történt a hitelesítő adatok ellenőrzése. AADSTS70008: A megadott hozzáférés biztosítása lejárt vagy visszavonták. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelációazonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 időbélyegző: 2015-12-15 21:09:31Z

| Felhasználó típusa                                | Után lejár                            |
| :--------------------------------------- | :--------------------------------------- |
| Felhasználói fiókok Azure Active Directory által nem kezelt (@hotmail.com, @live.comstb.) | 12 óra                                 |
| Felhasználói fiókok felügyelete által Azure Active Directory (AAD) | Futtassa a 14 nap után utolsó újrapróbálása. <br/><br/>90 nap, ha a szelet OAuth-alapú társított szolgáltatás fut legalább 14 naponta. |

Ez a hiba elkerüléséhez/hárítsa, ismét engedélyezheti a használatával a **engedélyezés** gombra kattint, ha a **jogkivonat lejár** és telepítse újra a társított szolgáltatás. Értékek is létrehozhat **sessionId** és **engedélyezési** programozott módon, az alábbiak szerint kód tulajdonságok:

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

Lásd: [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), és [AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témakörök a Data Factory osztályok, a kódban használt vonatkozó további információért. Adjon hozzá egy hivatkozást,: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll WindowsFormsWebAuthenticationDialog osztálynál. 

## <a name="azure-sql-linked-service"></a>Az Azure SQL társított szolgáltatásnak
Hozzon létre egy Azure SQL társított szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. Lásd: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#linked-service-properties) szóló cikkben olvashat a szolgáltatásnak.

## <a name="azure-sql-data-warehouse-linked-service"></a>Az Azure SQL Data Warehouse-társított szolgáltatás
Hozzon létre egy Azure SQL Data Warehouse társított szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. Lásd: [Azure SQL Data Warehouse-összekötő](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) szóló cikkben olvashat a szolgáltatásnak.

## <a name="sql-server-linked-service"></a>SQL Server társított szolgáltatás
Hozzon létre csatolt SQL Server szolgáltatást, és együtt használja a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) meghívni a Data Factory-folyamat a tárolt eljárást. Lásd: [SQL Server-összekötő](data-factory-sqlserver-connector.md#linked-service-properties) szóló cikkben olvashat a szolgáltatásnak.

