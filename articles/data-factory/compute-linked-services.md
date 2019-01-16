---
title: Számítási környezetek Azure Data Factory által támogatott |} A Microsoft Docs
description: Tudnivalók a számítási környezetek, amelyek segítségével az Azure Data Factory-folyamatok (például Azure HDInsight) adatok átalakítás vagy folyamat.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: douglasl
ms.openlocfilehash: 5e620b03f5588369fc73a62f2019d857766596fd
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321942"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Számítási környezetek Azure Data Factory által támogatott
Ez a cikk ismerteti a különböző számítási környezetekben használható folyamat vagy átalakítási adatok. Emellett biztosítja linking ezeket a társított szolgáltatások konfigurálásakor a Data Factory által támogatott különböző konfigurációt (igény szerinti és használata a saját) adatait számítási környezeteket az Azure data factoryt.

Az alábbi táblázat a Data Factory és a rajtuk futó tevékenységek által támogatott számítási környezetek listáját tartalmazza. 

| Számítási környezet                                          | tevékenységek                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Igény szerinti HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [a saját HDInsight-fürt](#azure-hdinsight-linked-service) | [Hive-](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streamelési](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Egyéni](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning-tevékenységek: Kötegelt végrehajtás és erőforrás frissítése](transform-data-using-machine-learning.md) |
| [Az Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Az Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [az SQL Server](#sql-server-linked-service) | [Tárolt eljárás](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-hdinsight-compute-environment"></a>Igény szerinti HDInsight számítási környezet
Az ilyen típusú konfigurációt a számítási környezet teljes körűen felügyelt, az Azure Data Factory szolgáltatás által. Automatikusan létrejön a Data Factory szolgáltatás előtt egy feladat küldött adatok feldolgozásához, és eltávolítja a feladat elvégzésekor. Létrehoz egy társított szolgáltatást az igény szerinti számítási környezet, konfigurálja és feladat-végrehajtási, a kezelő és a műveletek rendszerindítása részletes beállításait szabályozhatja.

> [!NOTE]
> Az igény szerinti konfigurációs jelenleg csak az Azure HDInsight-fürtök esetén támogatott. Az Azure Databricks emellett támogatja az igény szerinti feladatok feladat-fürtöket használó, tekintse meg [az Azure databricks társított szolgáltatás](#azure-databricks-linked-service) további részletekért.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Az Azure HDInsight igény szerinti társított szolgáltatás
Az Azure Data Factory szolgáltatás automatikusan létrehozhat egy igény szerinti HDInsight-fürtön dolgozza fel az adatokat. A fürt a tárfiókjával (linkedServiceName tulajdonságot a JSON-) a fürthöz társított ugyanabban a régióban jön létre. A tárfiók egy általános célú standard szintű Azure storage-fiókot kell lennie. 

Vegye figyelembe a következőket **fontos** pontokra vonatkozó igény szerinti HDInsight társított szolgáltatást:

* Az igény szerinti HDInsight-fürt jön létre az Azure-előfizetéshez. A fürt az Azure Portalon megtekintheti, ha a fürt működik és fut áll. 
* A naplók egy igény szerinti HDInsight-fürtön futó feladatok a HDInsight-fürthöz társított tárfiókba kerülnek. A clusterUserName, clusterPassword, clusterSshUserName, a társított szolgáltatás definíciójában meghatározott clusterSshPassword segítségével jelentkezzen be a fürthöz, a részletes hibakeresés a fürt élettartama során. 
* Csak a az időpontot, amikor a HDInsight-fürt kell felfelé és a futó feladatok számítunk fel.
* Használhat egy **parancsfájlművelet** együtt az Azure HDInsight igény szerinti társított szolgáltatás.  

> [!IMPORTANT]
> Vesz **20 perc** legalább egy igény szerinti Azure HDInsight-fürt kiépítéséhez.

### <a name="example"></a>Példa
A következő JSON egy Linux-alapú igény szerinti HDInsight társított szolgáltatás határozza meg. A Data Factory szolgáltatás automatikusan létrehoz egy **Linux-alapú** HDInsight-fürt számára a szükséges tevékenység. 

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
> Ahogy több tevékenység fut, az Azure blob storage több tároló jelenik. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](http://storageexplorer.com/).
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Szükséges |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A type tulajdonságot kell megadni **HDInsightOnDemand**. | Igen      |
| clusterSize                  | A fürt feldolgozó-és adatcsomópontok száma. A HDInsight-fürt 2 fő csomóponttal, ez a tulajdonság adja meg a munkavégző csomópontok számával együtt jön létre. A csomópontok mérete 4 mag, így egy 4 feldolgozó csomópontot tartalmazó fürtben 24 mag szükséges rendelkező Standard_D3 vannak (4\*4 = 16 mag, a feldolgozó csomópontokat, valamint 2\*4 = 8 mag fő csomópontok esetében). Lásd: [fürtök beállítása a HDInsight a Hadoop, Spark, Kafka és további](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) részleteiről. | Igen      |
| linkedServiceName            | Az Azure Storage társított szolgáltatás, tárolására és az adatok feldolgozása az igény szerinti fürt használni. A HDInsight-fürt létrehozása az Azure Storage-fiók ugyanabban a régióban. Az Azure HDInsightban korlátozott azon magok száma, amelyek az egyes támogatott Azure-régiókban felhasználhatók. Győződjön meg arról, hogy a magkvótája elegendő Azure-régióban, hogy a szükséges fürt méretének nullánál kielégítése érdekében. Részletekért tekintse meg a [a Hadoop, Spark, Kafka és több HDInsight-fürtök beállítása](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Jelenleg nem hozható létre egy igény szerinti HDInsight-fürtöt, amely egy Azure Data Lake Store a tárolót használja. A feldolgozás alatt egy Azure Data Lake Store HDInsight eredmény adatokat tárolni szeretné, ha a másolási tevékenység használatával másolja az adatokat az Azure Blob Storage-ból az Azure Data Lake Store. </p> | Igen      |
| clusterResourceGroup         | A HDInsight-fürtöt az erőforráscsoportban jön létre. | Igen      |
| az élettartam                   | A megengedett üresjárati idő az igény szerinti HDInsight-fürt számára. Itt adhatja meg, mennyi ideig az igény szerinti HDInsight-fürt aktív marad egy tevékenység fut, ha nincsenek a fürt más aktív feladatok befejezése után. A minimális megengedett érték érték 5 perc (00: 05:00).<br/><br/>Például ha egy tevékenység futtatása 6 percig tart, és az élettartam értéke 5 perc, a fürt marad, a figyelő életben 5 perc, a 6 percnek feldolgozása a tevékenység futtatása után. Ha egy másik tevékenység-végrehajtásonként 6 – perc időkeretet, dolgoz fel ugyanazon a fürtön.<br/><br/>Egy igény szerinti HDInsight-fürt létrehozása egy (eltarthat egy ideig) drága művelet, így használja ezt a beállítást a szükséges adat-előállító egy igény szerinti HDInsight-fürt újrafelhasználásával teljesítményét.<br/><br/>Ha timetolive az érték 0, a fürt törlődik, amint az a tevékenység futása befejeződik. Mivel a Ha a magas érték, a fürt számára, hogy jelentkezzen be az egyes elhárítása üresjárati felfüggesztheti a célú, de eredményezheti magas költségeknek. Ezért fontos, hogy beállította-e a megfelelő értéket a saját igényei szerint.<br/><br/>A timetolive tulajdonság értéke megfelelően van beállítva, ha több folyamatot oszthatnak meg az igény szerinti HDInsight-fürt példányának. | Igen      |
| clusterType                  | A HDInsight-fürt létrehozása típusa. Megengedett értékek: "hadoop" és "spark". Ha nincs megadva, alapértelmezett érték: hadoop. Vállalati biztonsági csomag engedélyezett fürt nem hozható létre igény szerinti, használja inkább egy [meglévő fürt / használata a saját számítási](#azure-hdinsight-linked-service). | Nem       |
| version                      | A HDInsight-fürt verzióját. Ha nincs megadva, a jelenlegi HDInsight meghatározott alapértelmezett verziót használ. | Nem       |
| hostSubscriptionId           | A HDInsight-fürt létrehozásához használt Azure-előfizetés azonosítója. Ha nincs megadva, az előfizetés-azonosító az Azure bejelentkezési kontextus használ. | Nem       |
| clusterNamePrefix           | A HDI-fürt nevét, az időbélyegző-előtagot automatikusan hozzá lesznek fűzve a fürt nevének végén| Nem       |
| sparkVersion                 | Ha a fürt típusa "Spark" spark verziója | Nem       |
| additionalLinkedServiceNames | Itt adhatja meg, további tárfiókok esetében a HDInsight társított szolgáltatás, így a Data Factory szolgáltatás segítségével regisztrálja őket az Ön nevében. A storage-fiókok a HDInsight-fürt, és a linkedServiceName által megadott tárfiók ugyanabban a régióban létrehozott ugyanabban a régióban kell lennie. | Nem       |
| osType                       | Operációs rendszer típusát. Engedélyezett értékek a következők: A Linux és Windows (a HDInsight 3.3-as csak). Alapértelmezés szerint Linux. | Nem       |
| hcatalogLinkedServiceName    | A név az Azure SQL társított szolgáltatás, amely a HCatalog adatbázis mutasson. Az igény szerinti HDInsight-fürtöt az Azure SQL database használatával, mint a metaadattár jön létre. | Nem       |
| connectVia                   | Az Integration Runtime használható tart elküldeni a tevékenységek a HDInsight társított szolgáltatást. Igény szerinti HDInsight társított szolgáltatás, az Azure integrációs modul csak támogatja. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem       |
| clusterUserName                   | A felhasználónév, aki a fürtöt. | Nem       |
| clusterPassword                   | A jelszó, aki a fürtöt biztonságos karakterlánc típusú. | Nem       |
| clusterSshUserName         | Ssh felhasználónév távolról csatlakozhat a fürtcsomópont (a Linux rendszeren). | Nem       |
| clusterSshPassword         | A jelszót az ssh biztonságos karakterlánc típusú távolról csatlakozzon a fürtcsomópont (a Linux rendszeren). | Nem       |
| scriptActions | Adja meg a parancsfájl [HDInsight-fürt testreszabások](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) igény szerinti fürtök létrehozása során. <br />Jelenleg az Azure Data Factory felhasználói felülete a szerkesztőeszköz támogatja a késleltetve csak 1 parancsfájlművelet, de ezt a korlátozást a JSON-keresztül kap (adja meg több parancsprogram-művelet a JSON-fájlban). | Nem |


> [!IMPORTANT]
> HDInsight Hadoop fürt több verzió telepítését követően támogatja. Minden verzió választási lehetőséget egy adott verzióját a Hortonworks Data Platform (HDP) telepítési és összetevők, hogy a terjesztés belüli lemezképcsomagban hoz létre. A HDInsight-verziók támogatott listája tartja a legújabb Hadoop-ökoszisztéma összetevők és javításokat frissítése folyamatban. Ellenőrizze, hogy a legfrissebb információk mindig hivatkozik [HDInsight támogatott verziója és az operációs rendszer típusa](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) annak biztosítása érdekében a HDInsight támogatott verzióját használja. 
>
> [!IMPORTANT]
> Jelenleg a HDInsight társított szolgáltatás nem támogatja a HBase, interaktív lekérdezés (Hive LLAP), a Storm. 
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

Az igény szerinti HDInsight társított szolgáltatás egy egyszerű szolgáltatásnév hitelesítése az Ön nevében HDInsight-fürtök létrehozásához szükséges. Használja az egyszerű szolgáltatásnév hitelesítése, egy alkalmazás entitás regisztrálása az Azure Active Directory (Azure AD), és adja meg azt a **közreműködői** szerepkört az előfizetés vagy az erőforráscsoport, amelyben a HDInsight-fürt létrehozása. Részletes lépéseiért lásd: [egy Azure Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

- Alkalmazásazonosító
- Alkalmazáskulcs 
- Bérlőazonosító

Egyszerű szolgáltatásnév hitelesítése használja a következő tulajdonságok megadásával:

| Tulajdonság                | Leírás                              | Szükséges |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Adja meg az alkalmazás ügyfél-azonosítót.     | Igen      |
| **servicePrincipalKey** | Adja meg az alkalmazáskulcsot.           | Igen      |
| **bérlő**              | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen      |

### <a name="advanced-properties"></a>Speciális tulajdonságok

Az igény szerinti HDInsight-fürt részletes konfigurációját a következő tulajdonságokat is megadhatja.

| Tulajdonság               | Leírás                              | Szükséges |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Megadja a HDInsight-fürt létrehozása az alapvető konfigurációs paramétereket (ahogy a core-site.xml). | Nem       |
| hBaseConfiguration     | Megadja a HDInsight-fürt HBase-konfigurációs paramétereket (a hbase-site.xml). | Nem       |
| hdfsConfiguration      | Megadja a HDInsight-fürt HDFS-konfigurációs paramétereket (hdfs-site.xml). | Nem       |
| hiveConfiguration      | Megadja a HDInsight-fürtben hive-konfigurációs paramétereket (hive-site.xml). | Nem       |
| mapReduceConfiguration | Megadja a HDInsight-fürt MapReduce-konfigurációs paramétereket (mapred-site.xml). | Nem       |
| oozieConfiguration     | Megadja a HDInsight-fürt az Oozie-konfigurációs paramétereket (az oozie-site.xml). | Nem       |
| stormConfiguration     | Megadja a HDInsight-fürt Storm-konfigurációs paramétereket (a storm-site.xml). | Nem       |
| yarnConfiguration      | Megadja a HDInsight-fürt Yarn-konfigurációs paramétereket (yarn-site.xml). | Nem       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Példa – speciális tulajdonságokkal rendelkező igény szerinti HDInsight-fürt konfigurálása

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

### <a name="node-sizes"></a>Csomópontméret
A fő-, adatok, és a következő tulajdonságokkal zookeeper-csomópontok méretét is megadhatja: 

| Tulajdonság          | Leírás                              | Szükséges |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Meghatározza a fő csomópont méretét. Az alapértelmezett érték a következő: Standard_D3. Tekintse meg a **csomópontméretűek megadása** című szakasz részletezi. | Nem       |
| dataNodeSize      | Az adatcsomóponton a méretét határozza meg. Az alapértelmezett érték a következő: Standard_D3. | Nem       |
| zookeeperNodeSize | Megadja a állatkert üzemeltetőjének csomópont méretét. Az alapértelmezett érték a következő: Standard_D3. | Nem       |

#### <a name="specifying-node-sizes"></a>Adja meg a csomópontok méretei
Tekintse meg a [virtuális gépek méretei](../virtual-machines/linux/sizes.md) karakterlánc-értékeket kell megadnia a tulajdonságokat, az előző szakaszban ismertetett ismertető cikket. Az értékeket kell felelnek meg a **parancsmagok és API-k** a cikkben. Ahogy az a cikkben látható, az adatok (alapértelmezett) nagy méretű fürtcsomópont 7 GB memória, amely nem lehet a forgatókönyv esetén használatos. 

Ha szeretne létrehozni, D4 méretű fő csomópontból és a feldolgozó csomópontokat, adja meg **Standard_D4** headNodeSize és a dataNodeSize tulajdonságok értékeként. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ezek a tulajdonságok nem megfelelő értéket ad meg, ha jelenhet meg a következő **hiba:** Nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. Fürt állapota: "Error". Üzenet: 'PreClusterCreationValidationFailure'. Ha ezt a hibaüzenetet kapja, győződjön meg arról, hogy használja a **PARANCSMAG és az API-k** nevét a táblázatból a [virtuális gépek méretei](../virtual-machines/linux/sizes.md) cikk.        

## <a name="bring-your-own-compute-environment"></a>A saját számítási környezet használata
Ezt a konfigurációtípust, a felhasználók regisztrálhatnak egy már meglévő számítási környezettel a Data Factory társított szolgáltatásként. A felhasználó a számítási környezet kezeli, és a Data Factory szolgáltatás használ a tevékenységek végrehajtásához.

Ezt a konfigurációtípust támogatott a következő számítási környezetekben:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Az Azure HDInsight társított szolgáltatás
Létrehozhat egy Azure HDInsight társított szolgáltatás regisztrálni a saját HDInsight-fürt a Data Factory.

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
| Tulajdonság          | Leírás                                                  | Szükséges |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | A type tulajdonságot kell megadni **HDInsight**.            | Igen      |
| clusterUri        | A HDInsight-fürt URI azonosítója.                            | Igen      |
| felhasználónév          | Adja meg a felhasználó egy meglévő HDInsight-fürthöz való kapcsolódáshoz használt nevét. | Igen      |
| jelszó          | Adja meg a felhasználói fiókhoz tartozó jelszót.                       | Igen      |
| linkedServiceName | Az Azure blob storage a HDInsight-fürt által használt az Azure Storage társított szolgáltatás neve. <p>Jelenleg nem adhat meg egy Azure Data Lake Store-beli társított szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Store, a Hive és Pig-parancsfájlok előfordulhat, hogy fér hozzá a az Azure Data Lake Store az adatokhoz. </p> | Igen      |
| isEspEnabled      | Adja meg "*igaz*" Ha a HDInsight-fürt [vállalati biztonsági csomag](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction) engedélyezve van. Alapértelmezett érték a "*hamis*". | Nem       |
| connectVia        | Az Integration Runtime használható tart elküldeni a tevékenységeket a társított szolgáltatáshoz. Azure integrációs modul vagy a helyi Integration Runtime is használhatja. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. <br />Vállalati biztonsági csomag (ESP) HDInsight-fürt használata engedélyezett egy saját üzemeltetésű integrációs modult, amely rendelkezik a fürthöz egy üzemel, vagy a ESP HDInsight-fürt ugyanazon a virtuális hálózaton belül kell telepíteni. | Nem       |

> [!IMPORTANT]
> HDInsight Hadoop fürt több verzió telepítését követően támogatja. Minden verzió választási lehetőséget egy adott verzióját a Hortonworks Data Platform (HDP) telepítési és összetevők, hogy a terjesztés belüli lemezképcsomagban hoz létre. A HDInsight-verziók támogatott listája tartja a legújabb Hadoop-ökoszisztéma összetevők és javításokat frissítése folyamatban. Ellenőrizze, hogy a legfrissebb információk mindig hivatkozik [HDInsight támogatott verziója és az operációs rendszer típusa](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) annak biztosítása érdekében a HDInsight támogatott verzióját használja. 
>
> [!IMPORTANT]
> Jelenleg a HDInsight társított szolgáltatás nem támogatja a HBase, interaktív lekérdezés (Hive LLAP), a Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Az Azure Batch-beli társított szolgáltatás

Regisztrálja a Batch-készlet, a virtuális gépek (VM) egy társított Azure Batch szolgáltatás létrehozhat egy adat-előállítóhoz. Egyéni tevékenység az Azure Batch segítségével is futtathatja.

Lásd az alábbi témakörök, ha most ismerkedik az Azure Batch szolgáltatás:

* [Az Azure Batch alapjai](../batch/batch-technical-overview.md) az Azure Batch szolgáltatás áttekintése.
* [Új AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) parancsmaggal hozzon létre egy Azure Batch-fiók (vagy) [az Azure portal](../batch/batch-account-create-portal.md) létrehozása az Azure Batch-fiókot az Azure portal használatával. Lásd: [PowerShell használata kezelheti az Azure Batch-fiók](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) témakör nyújt részletes tájékoztatást a parancsmag használatával.
* [Új-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) parancsmaggal hozzon létre egy Azure Batch-készletben.

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
| accountName       | Az Azure Batch-fiók nevére.         | Igen      |
| accessKey         | Az Azure Batch-fiók hozzáférési kulcsa.  | Igen      |
| batchUri          | URL-cím az Azure Batch-fiókhoz, formátumban: https://*batchaccountname.region*. batch.azure.com. | Igen      |
| poolName          | A virtuálisgép-készlet neve.    | Igen      |
| linkedServiceName | Neve az Azure Storage társított szolgáltatást, a társított Azure Batch szolgáltatáshoz társított. Ezt a társított szolgáltatást a tevékenység futtatásához szükséges fájlok átmeneti szolgál. | Igen      |
| connectVia        | Az Integration Runtime használható tart elküldeni a tevékenységeket a társított szolgáltatáshoz. Azure integrációs modul vagy a helyi Integration Runtime is használhatja. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem       |

## <a name="azure-machine-learning-linked-service"></a>Az Azure Machine Learning-beli társított szolgáltatás
Létrehoz egy társított Azure Machine Learning szolgáltatást a Machine Learning kötegelt pontozási végpontjához adat-előállítóval történő regisztrálásához.

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
| Típus                   | A type tulajdonságot kell beállítani: **AzureML**. | Igen                                      |
| mlEndpoint             | A kötegelt pontozás URL-CÍMÉT.                   | Igen                                      |
| apiKey                 | A közzétett munkaterület-modell API-t.     | Igen                                      |
| updateResourceEndpoint | Frissítés erőforrás URL-CÍMÉT a prediktív webszolgáltatás frissítheti a betanított modell fájlt az Azure ML Web Service-végpont | Nem                                       |
| servicePrincipalId     | Adja meg az alkalmazás ügyfél-azonosítót.     | Ha meg van adva updateResourceEndpoint szükséges |
| servicePrincipalKey    | Adja meg az alkalmazáskulcsot.           | Ha meg van adva updateResourceEndpoint szükséges |
| bérlő                 | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Ha meg van adva updateResourceEndpoint szükséges |
| connectVia             | Az Integration Runtime használható tart elküldeni a tevékenységeket a társított szolgáltatáshoz. Azure integrációs modul vagy a helyi Integration Runtime is használhatja. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Az Azure Data Lake Analytics hivatkozott szolgáltatást
Létrehoz egy **Azure Data Lake Analytics** társított szolgáltatást, az Azure Data Lake Analytics számítási szolgáltatás az Azure data factoryt. A Data Lake Analytics U-SQL-tevékenység, a folyamat a társított szolgáltatásra vonatkozik. 

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
| type                 | A type tulajdonságot kell beállítani: **AzureDataLakeAnalytics**. | Igen                                      |
| accountName          | Azure Data Lake Analytics Account Name.  | Igen                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI.           | Nem                                       |
| subscriptionId       | Azure-előfizetés azonosítója                    | Nem                                       |
| resourceGroupName    | Azure-erőforráscsoport neve                | Nem                                       |
| servicePrincipalId   | Adja meg az alkalmazás ügyfél-azonosítót.     | Igen                                      |
| servicePrincipalKey  | Adja meg az alkalmazáskulcsot.           | Igen                                      |
| bérlő               | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen                                      |
| connectVia           | Az Integration Runtime használható tart elküldeni a tevékenységeket a társított szolgáltatáshoz. Azure integrációs modul vagy a helyi Integration Runtime is használhatja. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem                                       |



## <a name="azure-databricks-linked-service"></a>Az Azure Databricks társított szolgáltatás
Létrehozhat **Azure Databricks társított szolgáltatás** Databricks-munkaterület, amely futtatása a Databricks workloads(notebooks) használatával fogja regisztrálni.

### <a name="example---using-new-job-cluster-in-databricks"></a>Példa – új feladatfürt Databricks használatával

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Példa – meglévő interaktív fürt segítségével a Databricksben

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

| Tulajdonság             | Leírás                              | Szükséges                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| név                 | A társított szolgáltatás neve               | Igen   |
| type                 | A type tulajdonságot kell beállítani: **Az Azure Databricks**. | Igen                                      |
| tartomány               | Adja meg az Azure-régió, ennek megfelelően a Databricks-munkaterület az a régió alapján. Például: https://eastus.azuredatabricks.net | Igen                                 |
| hozzáférési tokent          | Hozzáférési jogkivonat hitelesíti az Azure databricks a Data Factory szükség. Hozzáférési jogkivonat hozható létre a databricks-munkaterület szükséges. Keresse meg a hozzáférési jogkivonat található lépéseket részletes [Itt](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Igen                                       |
| existingClusterId    | A fürt összes feladat futtatásához a meglévő fürtök azonosítója. Egy már létrehozott interaktív fürt kell lennie. Szükség lehet manuálisan indítsa újra a fürtöt, ha azt nem válaszol. Databricks javasolja a futó feladatok nagyobb megbízhatóság érdekében új fürtökön. A fürt azonosító található egy interaktív fürt a Databricks-munkaterület ->-fürtök interaktív fürt neve > -> Konfiguráció -> címkéket. [További részletek](https://docs.databricks.com/user-guide/clusters/tags.html) | Nem 
| newClusterVersion    | A Spark-verziót a fürt. A databricks-feladat fürtjében hoz létre. | Nem  |
| newClusterNumOfWorker| Ehhez a fürthöz kell munkavégző csomópontok számát. A fürt rendelkezik egy Spark-illesztő és num_workers végrehajtóval num_workers + 1 Spark csomópontok összesen. Egy karakterlánc formátumú Int32, "1" azt jelenti, hogy numOfWorker, 1 vagy "1:10" azt jelenti, perc, 1 és 10 max, az automatikus méretezés.  | Nem                |
| newClusterNodeType   | Ez a mező kódol, keresztül egyetlen értéket, a Spark-csomópontok a fürt számára elérhető erőforrások. Ha például a Spark is üzembe helyezhető és memória- vagy számítási számításigényes feladatokra optimalizált csomópontok a mező kitöltése kötelező az új fürt                | Nem               |
| newClusterSparkConf  | nem kötelező, a felhasználó által megadott Spark konfigurációs kulcs-érték párok halmaza. Felhasználók is átadható egy karakterlánc további JVM-beállítások az illesztőprogramot, és a végrehajtóval spark.driver.extraJavaOptions és spark.executor.extraJavaOptions jelölik. | Nem  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás
Hozzon létre egy Azure SQL társított szolgáltatás, és együtt használja, a [tárolt eljárási tevékenység](transform-data-using-stored-procedure.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. Lásd: [Azure SQL-összekötő](connector-azure-sql-database.md#linked-service-properties) részleteivel kapcsolatos ezt a társított szolgáltatást.

## <a name="azure-sql-data-warehouse-linked-service"></a>Az Azure SQL Data Warehouse-beli társított szolgáltatás
Hozzon létre egy Azure SQL Data Warehouse társított szolgáltatást, és együtt használja, a [tárolt eljárási tevékenység](transform-data-using-stored-procedure.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. Lásd: [Azure SQL Data Warehouse-összekötő](connector-azure-sql-data-warehouse.md#linked-service-properties) részleteivel kapcsolatos ezt a társított szolgáltatást.

## <a name="sql-server-linked-service"></a>Az SQL Server-alapú társított szolgáltatás
Az SQL Server-alapú társított szolgáltatás létrehozása, és együtt használja, a [tárolt eljárási tevékenység](transform-data-using-stored-procedure.md) egy tárolt eljárást a Data Factory-folyamatok meghívásához. Lásd: [SQL Server-összekötő](connector-sql-server.md#linked-service-properties) részleteivel kapcsolatos ezt a társított szolgáltatást.

## <a name="next-steps"></a>További lépések
Az Azure Data Factory által támogatott Adatátalakítási tevékenységek listáját lásd: [adatátalakítás](transform-data.md).
