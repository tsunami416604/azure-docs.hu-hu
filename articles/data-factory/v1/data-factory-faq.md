---
title: Azure Data Factory – gyakori kérdések
description: Gyakori kérdések az Azure Data Factory.Frequently questions about Azure Data Factory.
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
ms.openlocfilehash: 1b042c0279e458cf83bd5c81147f4b1f25d7687d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645140"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – gyakori kérdések
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [gyakran ismételt kérdéseket - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amely **automatizálja az adatok mozgását és átalakítását.** Csakúgy, mint egy gyár, amely a nyersanyagok at a nyersanyagok at, és átalakítja azokat késztermékekké, a Data Factory vezényli a meglévő szolgáltatásokat, amelyek nyers adatokat gyűjtenek, és használatra kész információkká alakítják át.

A Data Factory lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre az adatok áthelyezéséhez a helyszíni és a felhőbeli adattárak között, valamint az adatok feldolgozásához/átalakításához olyan számítási szolgáltatások használatával, mint az Azure HDInsight és az Azure Data Lake Analytics. Miután létrehozott egy folyamatot, amely végrehajtja a szükséges műveletet, ütemezheti, hogy rendszeresen fusson (óránkénti, napi, heti stb.).   

További információt [& A legfontosabb fogalmak áttekintése című témakörben talál.](data-factory-introduction.md)

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Hol találhatom meg az Azure Data Factory díjszabási adatait?
Az Azure Data Factory díjszabási részletei oldalon találja a Data [Factory díjszabási][adf-pricing-details] részleteit.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hogyan kezdhetem el az Azure Data Factory t?
* Az Azure Data Factory áttekintését az [Azure Data Factory bemutatása](data-factory-introduction.md)című témakörben találja.
* Az **adatok másolása/áthelyezése** a Másolási tevékenység használatával című témakörben az [Adatok másolása az Azure Blob Storage-ból az Azure SQL Database szolgáltatásba](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)című témakörben található.
* Az adatok HDInsight Hive-tevékenység használatával **történő átalakításáról** szóló oktatóanyag. Lásd: [Adatok feldolgozása Hive-parancsfájl futtatásával a Hadoop-fürtön](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Mi a Data Factory régióelérhetősége?
Data Factory elérhető **az Egyesült Államok nyugat-és** **észak-európai**. Az adatgyárak által használt számítási és tárolási szolgáltatások más régiókban is lehetnek. Lásd: [Támogatott régiók](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Milyen korlátok vonatkoznak az adatgyárak/folyamatok/tevékenységek/adatkészletek számára?
Tekintse meg az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) című cikk **Azure Data Factory Limits** című szakaszát.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Mi a szerzői/fejlesztői élmény az Azure Data Factory szolgáltatással?
Adatgyárakat az alábbi eszközök/SDK-k egyikével hozhat létre/hozhat létre:

* **Vizuális stúdió** A Visual Studio segítségével azure-adatfeldolgozót hozhat létre. A részletekért [olvassa el az első adatfolyamat létrehozása a Visual Studio használatával.](data-factory-build-your-first-pipeline-using-vs.md)
* **Azure PowerShell** [Az Azure Data Factory létrehozása és figyelése az Azure PowerShell használatával](data-factory-build-your-first-pipeline-using-powershell.md) című témakörben egy adat-előállító létrehozásához a PowerShell használatával című témakörben található. Az Adatfeldolgozó parancsmagok átfogó dokumentációját az MSDN-tár [adatfeldolgozó parancsmagjának][adf-powershell-reference] referenciatartalma című témakörben találja.
* **.NET osztálytár** A Data Factory .NET SDK használatával programozott módon hozhat létre adatfeldolgozókat. Lásd: [Adatgyárak létrehozása, figyelése és kezelése a .NET SDK használatával](data-factory-create-data-factories-programmatically.md) az adat-előállító létrehozásának forgatókönyvéhez .NET SDK használatával. A Data Factory .NET SDK átfogó dokumentációját a [Data Factory osztálykönyvtárának útmutatójában találja.][msdn-class-library-reference]
* **REST API** Az Azure Data Factory szolgáltatás által elérhetővé tett REST API-t is használhatja adatgyárak létrehozásához és üzembe helyezéséhez. A [Data Factory REST API-útmutató][msdn-rest-api-reference] a Data Factory REST API átfogó dokumentációját tartalmazza.
* **Azure Resource Manager-sablon** Tekintse meg [az oktatóanyagot: Az Azure Resource Manager-sablon részleteivel készítse el az első Azure-adatgyárat.](data-factory-build-your-first-pipeline-using-arm.md)

### <a name="can-i-rename-a-data-factory"></a>Átnevezhetek egy adat-előállítót?
Nem. Más Azure-erőforrásokhoz hasonlóan az Azure-adatfeldolgozó neve sem módosítható.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Áthelyezhetek egy adatgyárat egyik Azure-előfizetésből a másikba?
Igen. Használja az adatgyári panel **Áthelyezés** gombját az alábbi ábrán látható módon:

![Adatgyár áthelyezése](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Melyek a Data Factory által támogatott számítási környezetek?
Az alábbi táblázat a Data Factory által támogatott számítási környezetek és a rajtuk futtatható tevékenységek listáját tartalmazza.

| Számítási környezet | tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning-tevékenységek: kötegelt végrehajtás és az erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), SQL [Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Tárolt eljárás](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hogyan viszonyul az Azure Data Factory az SQL Server Integration Services (SSIS) szolgáltatáshoz? 
Tekintse meg az [Azure Data Factory vs. SSIS-bemutatót](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) az egyik MVP-nktől (legértékesebb szakemberek): Reza Rad. Előfordulhat, hogy a Data Factory néhány legutóbbi változása nem szerepel a diapakliban. Folyamatosan további képességekkel bővül az Azure Data Factory. Folyamatosan további képességekkel bővül az Azure Data Factory. Ezeket a frissítéseket az év folyamán valamikor a Microsoft adatintegrációs technológiáinak összehasonlításába építjük be.   

## <a name="activities---faq"></a>Tevékenységek - GYAKORI KÉRDÉSEK
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Milyen típusú tevékenységeket használhat a Data Factory-folyamatokban?
* [Adatmozgatási tevékenységek](data-factory-data-movement-activities.md) az adatok áthelyezéséhez.
* [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) az adatok feldolgozásához/átalakításához.

### <a name="when-does-an-activity-run"></a>Mikor fut egy tevékenység?
A kimeneti adattábla **rendelkezésre állási** konfigurációs beállítása határozza meg, hogy mikor fut a tevékenység. Ha bemeneti adatkészletek vannak megadva, a tevékenység ellenőrzi, hogy az összes bemeneti adatfüggőség teljesül-e (azaz **Kész** állapot) a futás megkezdése előtt.

## <a name="copy-activity---faq"></a>Tevékenység másolása – gyakori kérdések
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Jobb, ha egy több tevékenységet tartalmazó folyamat, vagy egy külön folyamat minden tevékenységhez?
A folyamatoknak kötegelniük kell a kapcsolódó tevékenységeket. Ha az azokat összekötő adatkészleteket nem használja fel más tevékenység a folyamaton kívül, a tevékenységeket egy folyamatban tarthatja. Így nem kell a folyamat aktív időszakokláncolnia, hogy azok egymáshoz igazodjanak. Emellett a folyamat belső tábláiban lévő adatintegritás jobban megmarad a folyamat frissítésekor. A folyamatfrissítés lényegében leállítja a folyamaton belüli összes tevékenységet, eltávolítja őket, és újra létrehozza őket. A szerzői szempontból előfordulhat, hogy a kapcsolódó tevékenységeken belüli adatfolyam is könnyebb a folyamat egy JSON-fájljában.

### <a name="what-are-the-supported-data-stores"></a>Melyek a támogatott adattárak?
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="what-are-the-supported-file-formats"></a>Melyek a támogatott fájlformátumok?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Hol történik a másolási művelet?
A részleteket lásd [a Globálisan elérhető adatmozgatási](data-factory-data-movement-activities.md#global) szakaszban. Röviden, ha egy helyszíni adattár is részt vesz, a másolási műveletet az adatkezelési átjáró hajtja végre a helyszíni környezetben. És ha az adatáthelyezés két felhőtároló között van, a másolási művelet a fogadó helyéhez legközelebb eső régióban történik ugyanazon a földrajzi helyen.

## <a name="hdinsight-activity---faq"></a>HDInsight-tevékenység – gyakori kérdések
### <a name="what-regions-are-supported-by-hdinsight"></a>Mely régiókat támogatja a HDInsight?
Tekintse meg a Földrajzi elérhetőség szakaszt a következő cikkben: vagy [a HDInsight díjszabási részletek című témakört.][hdinsight-supported-regions]

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Melyik régiót használja egy igény szerinti HDInsight-fürt?
Az igény szerinti HDInsight-fürt ugyanabban a régióban jön létre, ahol a fürttel való használathoz megadott tároló létezik.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>További tárfiókok társítása a HDInsight-fürthöz?
Ha saját HDInsight-fürtöt (BYOC – Saját fürtet hoz) használ, olvassa el az alábbi témaköröket:

* [HDInsight-fürt használata alternatív tárfiókokkal és metaáruházakkal][hdinsight-alternate-storage]
* [További tárfiókok használata HDInsight Hive-vel][hdinsight-alternate-storage-2]

Ha a Data Factory szolgáltatás által létrehozott igény szerinti fürtöt használ, adjon meg további tárfiókokat a HDInsight-csatolt szolgáltatáshoz, hogy a Data Factory szolgáltatás regisztrálhassa őket az Ön nevében. Az igény szerinti összekapcsolt szolgáltatás JSON-definíciójában **az továbbiLinkedServiceNames** tulajdonsággal adjon meg alternatív tárfiókokat a következő JSON-kódrészletben látható módon:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
A fenti példában az otherLinkedServiceName1 és az otherLinkedServiceName2 olyan csatolt szolgáltatásokat jelöl, amelyek definíciói olyan hitelesítő adatokat tartalmaznak, amelyeknek a HDInsight-fürtnek szüksége van az alternatív tárfiókok eléréséhez.

## <a name="slices---faq"></a>Szeletek – gyakori kérdések
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Miért nincs kész állapotban a bemeneti szeletek?
Gyakori hiba, hogy a **külső** tulajdonságot nem állítja **igaz** értékre a bemeneti adatkészleten, ha a bemeneti adatok az adat-előállítón kívül vannak (nem az adat-előállító által.

A következő példában csak az **adatkészleten1** **kell beállítani** a true értéket.  

**DataFactory1** 1. folyamat: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 2: dataset3-> activity3 -> dataset4

Ha egy másik adat-előállító, amely a dataset4 (által előállított folyamat 2 adatgyár 1), jelölje adatkészlet4 külső adatkészletként, mert az adatkészlet által létrehozott egy másik adat-előállító (DataFactory1, nem DataFactory2).  

**DataFactory2**    
1. folyamat: 4 >tevékenység4 >adatkészlet5

Ha a külső tulajdonság megfelelően van beállítva, ellenőrizze, hogy a bemeneti adatok léteznek-e a bemeneti adatkészlet-definícióban megadott helyen.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hogyan kell futtatni egy szeletet egy másik időpontban, mint éjfélkor, amikor a szelet et naponta állítják elő?
Az **eltolástulajdonsággal** megadhatja, hogy mikor rakjon létre a szeletet. A tulajdonsággal kapcsolatos részletekaz [Adatkészlet elérhetősége](data-factory-create-datasets.md#dataset-availability) című szakaszban találhatók. Íme egy gyors példa:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
A napi szeletek az alapértelmezett éjfél helyett **reggel 6** órakor kezdődnek.     

### <a name="how-can-i-rerun-a-slice"></a>Hogyan futtathatok újra egy szeletet?
A szeleteket az alábbi módokon futtathatja újra:

* A Figyel és az Alkalmazás kezelése segítségével újra futtathatok egy tevékenységablakot vagy szeletet. Az utasításokat a [Kijelölt tevékenységablakok újrafuttatása.](data-factory-monitor-manage-app.md#perform-batch-actions)   
* Kattintson a **Futtatás** gombra a **DATA SLICE** panelen a szelet az Azure Portalon.
* Futtassa **a Set-AzDataFactorySliceStatus** parancsmast úgy, hogy az Állapot beállítása **Várakozás** a szeletre.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  A parancsmagról a [Set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] című témakörben talál részleteket.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Mennyi ideig tartott feldolgozni egy szeletet?
A Tevékenységablak-kezelő vel a Figyelő & az Alkalmazás kezelése alkalmazásban megtudhatja, hogy mennyi ideig tartott egy adatszelet feldolgozása. A részleteket lásd a [Tevékenységablak-kezelőben.](data-factory-monitor-manage-app.md#activity-window-explorer)

Az Azure Portalon a következőket is megteheti:  

1. Kattintson **az adatkészletek** csempére a **DATA FACTORY** panelen az adat-előállítóhoz.
2. Kattintson az **adatkészletek** panelen található adott adatkészletre.
3. Jelölje ki a kívánt szeletet a **TABLE** panel **Legutóbbi szeletek** listájából.
4. Kattintson a tevékenység futtatása a **DATA SLICE** panel **tevékenységfuttatások** listájából.
5. Kattintson **a TULAJDONSÁGOK** csempére a **TEVÉKENYSÉG FUTTATÁSRÉSZLETEI** panelen.
6. Az **IDŐTARTAM** mezőnek értékkel kell látnia. Ez az érték a szelet feldolgozásához szükséges idő.   

### <a name="how-to-stop-a-running-slice"></a>Hogyan lehet megállítani egy futó szeletet?
Ha le kell állítania a folyamat futtatását, [használhatja a Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) parancsmag használatát. Jelenleg a folyamat felfüggesztése nem állítja le a folyamatban lévő szelet-végrehajtásokat. A folyamatban lévő végrehajtások befejezése után nem kap további szeletet.

Ha valóban azonnal le szeretné állítani az összes kivégzést, az egyetlen módja az lenne, hogy törölje a folyamatot, és hozza létre újra. Ha úgy dönt, hogy törli a folyamatot, nem kell törölnie a folyamat által használt táblákat és csatolt szolgáltatásokat.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
