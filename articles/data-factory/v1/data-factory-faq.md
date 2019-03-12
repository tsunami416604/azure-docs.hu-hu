---
title: Az Azure Data Factory – gyakori kérdések
description: Gyakori kérdések az Azure Data Factory szolgáltatásról.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4c50ba76cf1e9b8103bd006c9f97c18a3e92d0c2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550279"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Az Azure Data Factory – gyakori kérdések
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [– gyakori kérdések – Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
A Data Factory szolgáltatás egy felhőalapú adatintegrációs szolgáltatás, amely **automatizálja az adatáthelyezési és -átalakítási adatok**. A gyári, amely berendezések és nyersanyagokat késztermékekké történő átalakítására, mint a Data Factory nyers adatokat gyűjthet, és átalakítja a nyersanyagokat használatra kész adatokat meglévő szolgáltatásokat hangolja össze.

Adat-előállító mind a helyszíni és felhőalapú adattárak, valamint számítási szolgáltatások, például Azure HDInsight és az Azure Data Lake Analytics használata feldolgozhatók és átalakíthatók az adatok közötti adatvezérelt munkafolyamatok létrehozását teszi lehetővé. Miután létrehozott egy folyamatot, amely a szükséges műveletet hajt végre, ütemezheti rendszeres időközönként futtatását (óránként, naponta, hetente stb.).   

További információkért lásd: [áttekintés és alapfogalmak](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Hol találom az Azure Data Factory díjszabása?
Lásd: [Data Factory szolgáltatás díjszabása oldalon] [ adf-pricing-details] díjszabási információkat az Azure Data Factory számára.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hogyan kezdhetem az Azure Data Factory?
* Azure Data Factory áttekintése, lásd: [az Azure Data Factory bemutatását](data-factory-introduction.md).
* Hogyan oktatóanyagot **adatok másolása vagy áthelyezése** másolási tevékenység használatával, lásd: [adatok másolása az Azure Blob Storage-ból az Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Hogyan oktatóanyagot **adatátalakítás** HDInsight Hive-tevékenységgel. Lásd: [adatok feldolgozása a Hive-szkriptet futtat a Hadoop-fürt](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Mi a Data Factory-régiók rendelkezésre állása?
A Data Factory érhető **USA nyugati régiójában** és **Észak-Európa**. Az adat-előállítók által használt számítási és tárolási szolgáltatások más régiókban is lehetnek. Lásd: [támogatott régiók](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Mik azok a korlátok a data factoryk/folyamatok/tevékenységek/adatkészletek száma?
Lásd: **Azure Data Factory korlátai** szakaszában a [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md#data-factory-limits) cikk.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Mi a szerzői műveletek/fejlesztői felületet biztosít az Azure Data Factory szolgáltatásban?
Szerző/létrehozhat adat-előállítók az alábbi eszközök/SDK-k egyikével:

* **Az Azure portal** a Data Factory paneleket az Azure Portalon hozhat létre data factoryk ad társított szolgáltatások gazdag felhasználói felületet kínálnak. A **Data Factory Editor**, amely tartalmaz-e a portál lehetővé teszi, hogy könnyedén hozhatja létre a társított szolgáltatások, táblák, adatkészletek és folyamatok JSON-definíciók összetevőkért megadásával. Lásd: [adatok első folyamatát az Azure portal használatával](data-factory-build-your-first-pipeline-using-editor.md) a portál /-szerkesztő használatával hozhat létre és telepíthet egy adat-előállítót egy példát.
* **A Visual Studio** Visual Studio használatával hozzon létre egy Azure data factoryt. Lásd: [hozhat létre az első folyamatot a Visual Studio használatával](data-factory-build-your-first-pipeline-using-vs.md) részleteiről.
* **Az Azure PowerShell** lásd [létrehozása és a figyelő Azure PowerShell-lel az Azure Data Factory](data-factory-build-your-first-pipeline-using-powershell.md) oktatóanyag/forgatókönyv egy PowerShell-lel adat-előállító létrehozásához. Lásd: [Data Factory parancsmagjainak leírása] [ adf-powershell-reference] a Data Factory-parancsmagok átfogó dokumentáció tartalmát az MSDN könyvtárában.
* **.NET-osztálytár** Data Factory .NET SDK-val programozott módon hozhat létre data factoryt. Lásd: [létrehozása, figyelése és kezelése a .NET SDK-t az adat-előállítók](data-factory-create-data-factories-programmatically.md) használható a .NET SDK használatával egy adat-előállító létrehozásához. Lásd: [Data Factory osztálytár-referenciát] [ msdn-class-library-reference] egy átfogó Data Factory .NET SDK dokumentációját.
* **REST API-val** is használhatja az Azure Data Factory szolgáltatás által elérhetővé tett REST API létrehozása és üzembe helyezése az adat-előállítók. Lásd: [Data Factory REST API-referencia] [ msdn-rest-api-reference] átfogó dokumentáció, amely a Data Factory REST API számára.
* **Az Azure Resource Manager-sablon** lásd [oktatóanyag: Az első Azure data factory használatával az Azure Resource Manager-sablon létrehozása](data-factory-build-your-first-pipeline-using-arm.md) fo részleteit.

### <a name="can-i-rename-a-data-factory"></a>Átnevezheti a data factory?
Nem. Egyéb Azure-erőforrások, például egy Azure data factory neve nem módosítható.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Áthelyezhető egy adat-előállítót egy Azure-előfizetésből egy másikba?
Igen. Használja a **áthelyezése** gombra a data factory panel az alábbi ábrán látható módon:

![Helyezze át a data factory](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Mik azok a Data Factory által támogatott számítási környezetek?
Az alábbi táblázat a Data Factory és a rajtuk futó tevékenységek által támogatott számítási környezetek listáját tartalmazza.

| Számítási környezet | tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) vagy [a saját HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streamelési](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning-tevékenységek: Kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Az Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Az Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [az SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Tárolt eljárás](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Azure Data Factory összehasonlítása az SQL Server Integration Services (SSIS) 
Tekintse meg a [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) bemutatót az MVP-k (legnagyobb értékű szakemberek számára) közül: Reza Rad. Az útmutató legutóbbi módosításait a Data Factory néhány előfordulhat, hogy nem jelenik meg a bemutatót. Folyamatosan adjuk hozzá további képességek az Azure Data Factory. Folyamatosan adjuk hozzá további képességek az Azure Data Factory. Azt fogja funkcióját beépítse ezeket a frissítéseket a Microsoft integrációs adattechnológiáira összehasonlítása valamikor év.   

## <a name="activities---faq"></a>Tevékenységek – gyakori kérdések
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Mik azok a tevékenységek is használhatja a Data Factory-folyamatok különböző típusú?
* [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) adatok áthelyezéséhez.
* [Adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) feldolgozza vagy átalakítja az adatokat.

### <a name="when-does-an-activity-run"></a>Amikor fut egy tevékenység?
A **rendelkezésre állási** konfigurációs beállítás, a kimeneti adatok tábla meghatározza, hogy a tevékenység futtatásakor. Ha a bemeneti adatkészletek meg van adva, a tevékenység ellenőrzi, hogy teljesülnek-e a bemeneti adatok függőségeket (azt jelenti, **készen** állapot) előtt el fog indulni.

## <a name="copy-activity---faq"></a>Másolási tevékenység – gyakori kérdések
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Ennyi az egész jobban szeretné, hogy több tevékenységet a folyamat vagy egy külön folyamat minden egyes tevékenységhez?
A folyamatok menteniük szeretné a kapcsolódó tevékenységeket. Az adatkészletek, csatlakoztassa őket a folyamat kívül bármely más tevékenységre nem használnak fel, ha a tevékenységek megmaradhat egy folyamatot. Ezzel a módszerrel nem kell lánc folyamat aktív időszakokban, hogy azok egymással. Emellett az adatok integritásának megőrzése, belső, a folyamat a táblák jobban megőrződik az adatcsatorna frissítésekor. Folyamat frissítése lényegében leállítja a folyamaton belül minden tevékenység, eltávolítja azokat a, és újra létrehozza őket. A terv készítése, is lehet a folyamatot a folyamat egy JSON-fájlt a kapcsolódó tevékenységek Data áttekinthetőbb.

### <a name="what-are-the-supported-data-stores"></a>Mik azok a támogatott adattárak?
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="what-are-the-supported-file-formats"></a>Mik azok a támogatott fájlformátumokról?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Hol történik a másolási művelet?
Lásd: [globálisan elérhető adatáthelyezés](data-factory-data-movement-activities.md#global) című szakasz részletezi. Röviden Ha egy helyszíni adattárolóban van szó, a másolási művelet történik az adatkezelési átjárót a helyszíni környezetben. És ha az adatok áthelyezése két felhőalapú adattárak között, a másolási művelet azonos földrajzi helyen található a fogadó hely legközelebb eső régióban történik.

## <a name="hdinsight-activity---faq"></a>HDInsight-tevékenység – gyakori kérdések
### <a name="what-regions-are-supported-by-hdinsight"></a>Mely régiók támogatottak HDInsight?
A földrajzi elérhetőség című a következő cikket: vagy [HDInsight díjszabása][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Egy igény szerinti HDInsight-fürt által használt régióját?
Az igény szerinti HDInsight-fürt létrejön ugyanabban a régióban, ahol a használható a fürt a megadott tároló létezik.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Hogyan társítsa a HDInsight-fürthöz további tárfiókok?
Ha a saját HDInsight-fürt (BYOC - Bring Your saját fürt) használ, az alábbi témakörök:

* [Egy HDInsight-fürtöt használ, alternatív Storage-fiókok és Metaadattárak][hdinsight-alternate-storage]
* [További Tárfiókok használata a HDInsight Hive-val][hdinsight-alternate-storage-2]

Ha a Data Factory szolgáltatás által létrehozott egy igény szerinti-fürtöt használ, adja meg a további tárfiókok esetében a HDInsight társított szolgáltatás, így a Data Factory szolgáltatás segítségével regisztrálja őket az Ön nevében. Használja az igény szerinti társított szolgáltatás JSON-definíciót, **additionalLinkedServiceNames** tulajdonság alternatív tárfiókok megadására, a következő JSON-kódrészletben látható módon:

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
A fenti példában a otherLinkedServiceName1 és otherLinkedServiceName2 társított szolgáltatások, amelyeknek a definícióit tartalmazza a HDInsight-fürt másodlagos tárfiókok eléréséhez szükséges hitelesítő adatokat képviselik.

## <a name="slices---faq"></a>A szeletek – gyakori kérdések
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Miért vannak saját nem kész állapotú bemeneti szelet?
Gyakori hiba a nem beállítás **külső** tulajdonságot **igaz** a bemeneti adatkészlet külső, az adat-előállító (nem az adat-előállító elő), a bemeneti adatok esetén.

A következő példában, csak be kell **külső** igaz értékre az **dataset1**.  

**DataFactory1** 1 folyamat: dataset1 -> activity1 -> dataset2 -> runbook az activity2 dataset3 folyamat 2 ->: dataset3 -> az activity3 dataset4 ->

Ha egy másik adat-előállító egy olyan folyamattal, amely a dataset4 (az 1. a data factory 2. folyamat elő), megjelölése dataset4 külső adatkészlettel, mert az adatkészlet különböző adat-előállító (DataFactory1, nem DataFactory2) által előállított.  

**DataFactory2**    
1. folyamat: dataset4 -> activity4 dataset5 ->

Ha az external tulajdonság megfelelően van beállítva, győződjön meg arról, hogy létezik-e a bemeneti adatokat a bemeneti adatkészlet definíciójában megadott helyen.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Hogyan futtathat egy szeletet, mint ha a szelet naponta előállítása éjfél egy másik időpontban?
Használja a **eltolás** tulajdonság, amelyeknél szeretné a szelet előállított azon időpont megadásához. Lásd: [adatkészlet rendelkezésre](data-factory-create-datasets.md#dataset-availability) ezt a tulajdonságot részleteit a következő szakaszban. Íme egy gyors példát:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Napi szeletek kezdőpont **reggel 6 óra** helyett az alapértelmezés szerinti éjfél.     

### <a name="how-can-i-rerun-a-slice"></a>Hogyan futtathatja a szelet?
A szelet futtathatja a következő módszerek valamelyikével:

* Monitor and Manage Appot használatával futtassa egy tevékenységablakot vagy a szeletet. Lásd: [kijelölt tevékenység windows újrafuttatása](data-factory-monitor-manage-app.md#perform-batch-actions) útmutatást.   
* Kattintson a **futtatása** a parancssorban a a **ADATSZELET** panelen a szelet az Azure Portalon.
* Futtatás **Set-AzDataFactorySliceStatus** parancsmagot és az állapot beállítása **Várakozás** a szelet.   

    ```PowerShell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Lásd: [Set-AzDataFactorySliceStatus] [ set-azure-datafactory-slice-status] a parancsmag részleteit.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Mennyi volt időt vesz igénybe egy szelet feldolgozása?
A Monitor & Manage alkalmazással Activity Window Explorer használatával tudja, hogy mennyi ideig tartott adatszelet feldolgozni. Lásd: [Activity Window Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) részleteiről.

Az Azure Portalon a következőket is teheti:  

1. Kattintson a **adatkészletek** csempét a **adat-előállító** a data Factory panel.
2. Kattintson az adott adatkészlet a **adatkészletek** panelen.
3. Válassza ki az Önt érdeklő a szelet a **legutóbbi szeletek** listájában a **tábla** panelen.
4. Kattintson arra a tevékenységre, futtassa a **Tevékenységfuttatások** listájában a **ADATSZELET** panelen.
5. Kattintson a **tulajdonságok** csempét a **tevékenység Futtatás részletei** panelen.
6. Megtekintheti a **időtartama** mezőt a egy értéke. Ez az érték a szelet feldolgozásának ideje.   

### <a name="how-to-stop-a-running-slice"></a>Hogyan lehet leállítani a futó szelet?
Ha szeretné megszüntetni a folyamat futtatásának, [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) parancsmagot. Jelenleg az felfüggesztése a folyamat nem állítja le a folyamatban lévő szelet végrehajtások. Miután végzett a folyamatban lévő végrehajtások, nincsenek felesleges szelet van mértékének.

Ha valóban szeretné azonnal leállítja az összes végrehajtás, az egyetlen módszer a folyamat törléséhez, és újra létre kell hoznia lesz. Ha törli a folyamatot, nem kell a táblákat és a folyamat által használt összekapcsolt szolgáltatások törlése.

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
