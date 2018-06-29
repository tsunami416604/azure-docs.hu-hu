---
title: Az Azure Data Factory - gyakran ismételt kérdések
description: Azure Data Factory vonatkozó gyakran ismételt kérdések.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 1db10a1578133021b976231a66aee6b550d71faf
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046754"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Az Azure Data Factory - gyakran ismételt kérdések
> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [gyakran feltett kérdés - adat-előállító](../frequently-asked-questions.md).

## <a name="general-questions"></a>Általános kérdések
### <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
Adat-előállító, a felhőalapú szolgáltatáshoz Adatintegráció **automatizálja az adatátviteli és az adatok átalakítása**. Olyan adat-előállítóval, amely a nyers és alakíthatják termékek berendezések fut, mint például a Data Factory koordinálja a meglévő szolgáltatások, amely a nyers adatokat gyűjthet, és irányítópulttá, használatra kész adatokat.

Adat-előállító hozhat létre munkafolyamatokat az adatvezérelt áthelyezni az adatokat is a helyszíni és felhőalapú adattároló, valamint számítási szolgáltatásokkal, például az Azure HDInsight és az Azure Data Lake Analytics folyamat vagy átalakítási adatok között. Miután létrehozott egy folyamatot, amely hajt végre a műveletet, amelyekre szüksége van, ütemezhető futtatását időnként (óránként, naponta, hetente stb.).   

További információkért lásd: [– áttekintés és alapfogalmak](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Hol található az Azure Data Factory díjszabása?
Lásd: [Data Factory díjszabás lap] [ adf-pricing-details] az az Azure Data Factory árképzési részletekért.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Hogyan kezdjem el az Azure Data Factoryvel?
* Azure Data Factory áttekintését lásd: [Bevezetés az Azure Data Factory](data-factory-introduction.md).
* Az egy útmutatót a **másolására/áthelyezésére adatok** használata a másolási tevékenység, lásd: [adatok másolása az Azure Blob Storage az Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Az egy útmutatót a **adatok** HDInsight Hive tevékenységgel. Lásd: [feldolgozni az adatokat a Hadoop-fürt Hive parancsfájl futtatásával](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Mi az az adat-Előállítóban régiónkénti elérhetőség?
Adat-előállító érhető el **Velünk nyugati** és **Észak-Európa**. Az adat-előállítók által használt számítási és tárolási szolgáltatások más régiókban lehet. Lásd: [támogató régiók](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Az adatok előállítók/folyamatok/tevékenységek/adatkészletek számú korlátai
Lásd: **Azure Data Factory korlátok** szakasza a [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../../azure-subscription-service-limits.md#data-factory-limits) cikk.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Mi az Azure Data Factory szolgáltatással a szerzői/fejlesztők számára?
Szerző/létrehozhat adat-előállítók a következő eszközök/SDK-k egyikének használatával:

* **Azure-portálon** a Data Factory paneleket az Azure portálon gazdag felhasználói felület adatok előállítók ad társított szolgáltatások létrehozásához adjon meg. A **Data Factory Editor**, amely része is a portál lehetővé teszi a könnyen hozzanak létre az összekapcsolt szolgáltatások, táblák, adathalmazok és adatcsatornák ezen összetevők JSON definícióit megadásával. Lásd: [felépítheti első adatok folyamatát az Azure portál használatával](data-factory-build-your-first-pipeline-using-editor.md) a portál szerkesztő használatával hozhat létre és telepíthet egy adat-előállító példát.
* **A Visual Studio** Visual Studio segítségével az Azure data factory létrehozása. Lásd: [felépítheti első adatok folyamatát Visual Studio használatával](data-factory-build-your-first-pipeline-using-vs.md) részleteiről.
* **Az Azure PowerShell** lásd [létrehozása és a figyelő az Azure PowerShell Azure Data Factory](data-factory-build-your-first-pipeline-using-powershell.md) oktatóanyag/forgatókönyv egy PowerShell-lel adat-előállító létrehozásához. Lásd: [Data Factory parancsmag-referencia] [ adf-powershell-reference] tartalom az MSDN Library a Data Factory-parancsmagok átfogó dokumentációját.
* **.NET class Library** programozott módon létrehozhat az adat-előállítók Data Factory .NET SDK használatával. Lásd: [létrehozása, figyelheti és kezelheti a .NET SDK használatával adat-előállítók](data-factory-create-data-factories-programmatically.md) részletes útmutatás a .NET SDK használatával adat-előállító létrehozása. Lásd: [Data Factory osztály kódtár – dokumentáció] [ msdn-class-library-reference] egy átfogó Data Factory .NET SDK-dokumentáció.
* **REST API** létrehozása és telepítése az adat-előállítók jelennek meg, ha az Azure Data Factory szolgáltatásnak REST API-t is használhatja. Lásd: [Data Factory REST API-referencia] [ msdn-rest-api-reference] egy átfogó Data Factory REST API-dokumentáció.
* **Az Azure Resource Manager-sablon** lásd: [oktatóanyag: az első az Azure data factory Azure Resource Manager-sablonnal Build](data-factory-build-your-first-pipeline-using-arm.md) fő részleteit.

### <a name="can-i-rename-a-data-factory"></a>Átnevezheti egy adat-előállító?
Nem. Egyéb Azure-erőforrások, például egy Azure data factory neve nem módosítható.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Áthelyezhető egy adat-előállító egy Azure-előfizetésből másik?
Igen. Használja a **áthelyezése** gombra a data factory panelen a következő ábrán látható módon:

![Adat-előállító áthelyezése](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Mik a Data Factory által támogatott számítási környezetek?
A következő táblázat felsorolja a Data Factory és az ezeken futó tevékenységek által támogatott számítási környezetek.

| Számítási környezet | tevékenységek |
| --- | --- |
| [Igény szerinti HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streamelési](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning-tevékenységek: kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Az Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Az Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Tárolt eljárás](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Hogyan hasonlítsa össze a Azure Data Factory az SQL Server Integration Services (SSIS)? 
Tekintse meg a [Azure Data Factory vs. SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) bemutató az MVP (legtöbb értékelni szakemberek) egyikéből: Reza Rad. A Data Factory végrehajtott legutóbbi módosításoknak némelyike nem jelennek meg a diák pakli. Azt az Azure Data Factory folyamatosan ad hozzá további képességeket. Azt az Azure Data Factory folyamatosan ad hozzá további képességeket. Azt fogja beépítse ezeket a frissítéseket a Microsoft integrációs technológiái összehasonlítása valamikor az év.   

## <a name="activities---faq"></a>Tevékenységek – gyakori kérdések
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Mik azok a különféle típusú tevékenységek a Data Factory-folyamathoz használható?
* [Adatok mozgása tevékenységek](data-factory-data-movement-activities.md) áthelyezni az adatokat.
* [Adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) folyamat vagy átalakítási adatok számára.

### <a name="when-does-an-activity-run"></a>Amikor fut egy tevékenység?
A **rendelkezésre állási** a kimeneti adatok tábla konfigurációs beállítás határozza meg, a tevékenység futása közben. Nincs megadva bemeneti adatkészletek, ha a tevékenység ellenőrzi, hogy a bemeneti adatok függőségeket teljesülnek-e (Ez azt jelenti, hogy **készen áll a** állapot) futtató megkezdése előtt.

## <a name="copy-activity---faq"></a>A másolási tevékenység – gyakori kérdések
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Az azt jobban több tevékenységek folyamat vagy egy különálló folyamat minden egyes tevékenységhez?
Folyamatok elvileg szeretné a kapcsolódó tevékenységeket. Az adatkészleteket, amelyek csatlakoztassa őket a feldolgozási sor kívül bármely más tevékenység nem használnak fel, ha egy folyamat megtarthatja a tevékenységeket. Ezzel a módszerrel Ön nem kellene lánc adatcsatorna aktív időszakokat, hogy azok egymással. Is az adatok a táblák belső, hogy az adatcsatornához jobban integritása az adatcsatorna frissítésekor. Összefüggő frissítési lényegében leállítja a feldolgozási sor összes tevékenységét, eltávolítja azokat, és újra létrehozza azokat. A terv készítése, is láthatja, adatáramlásra belül a kapcsolódó tevékenységeket az adatcsatorna egy JSON-fájlban.

### <a name="what-are-the-supported-data-stores"></a>Mik azok a támogatott adatokat tároló?
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.

### <a name="what-are-the-supported-file-formats"></a>Mik azok a támogatott fájlformátumokról?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Hol történik a másolási művelet?
Lásd: [világszerte elérhető adatmozgás](data-factory-data-movement-activities.md#global) című szakaszban talál információt. Rövid Ha egy helyszíni adattároló van szó, a másolási művelet végzi az adatkezelési átjáró a helyszíni környezetben. És az adatmozgás között két felhőalapú tárolók esetén a másolási művelet történik az ugyanazon földrajzi a fogadó hely legközelebb eső régiót.

## <a name="hdinsight-activity---faq"></a>HDInsight tevékenység – gyakori kérdések
### <a name="what-regions-are-supported-by-hdinsight"></a>Milyen régiók HDInsight által támogatott?
A cikknek a földrajzi elérhetőséggel című rész a következő cikkben: vagy [HDInsight Díjszabásának részleteit][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Milyen régió igény szerinti HDInsight-fürtök által használt?
Az igény szerinti HDInsight-fürt létrehozása ugyanabban a régióban, ahol a tárolási, a fürtben használni kívánt megadott van.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Hogyan rendelje hozzá a további tárfiókok HDInsight-fürtjét?
Ha a saját HDInsight-fürt (BYOC - Bring Your saját fürt) használ, a következő témakörökben talál:

* [HDInsight-fürtök használata alternatív Storage-fiókok és a Metaadattárakat][hdinsight-alternate-storage]
* [További Tárfiókok használata a HDInsight Hive][hdinsight-alternate-storage-2]

Ha az igény szerinti fürt a Data Factory szolgáltatásnak által létrehozott használ, adja meg, további tárfiókok a HDInsight a társított szolgáltatás, hogy a Data Factory szolgáltatásnak is regisztrálja őket az Ön nevében. Az igény szerinti társított szolgáltatás JSON-definícióból, használjon **additionalLinkedServiceNames** tulajdonság adja meg az alternatív tárolócsoport fiókokat, ahogy az az alábbi JSON-részlet:

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
A fenti példában otherLinkedServiceName1 és otherLinkedServiceName2 képviselő társított szolgáltatások, amelynek meghatározásokat tartalmazza, amely a HDInsight-fürt másodlagos tárfiókok hozzáférésre van szüksége a hitelesítő adatokat.

## <a name="slices---faq"></a>A szeletek – gyakori kérdések
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Miért van a bemeneti szeletek nem üzemkész állapotban?
Gyakori hiba nem állít **külső** tulajdonságot **igaz** az bemeneti adatkészlettel külső az adat-előállítóban (nem elő a data factory) a bemeneti adatok esetén.

A következő példában csak be kell **külső** igaz a **dataset1**.  

**DataFactory1** csővezeték-1: dataset1 -> activity1 -> dataset2 activity2 -> -> dataset3 csővezeték 2: dataset3 -> activity3 dataset4 ->

Ha egy másik, egy folyamatot, amely dataset4 (hozott létre adatcsatorna adat-előállítóban 1 2) az adat-előállító, megjelölése dataset4 egy külső adathalmaz, mert az adatkészlet elő egy másik adat-előállítóban (DataFactory1, nem DataFactory2).  

**DataFactory2**    
Feldolgozási sor 1: dataset4 -> activity4 dataset5 ->

Ha a külső tulajdonság nincs megfelelően beállítva, győződjön meg arról, hogy a bemeneti adatok létezik-e a bemeneti adatkészlet-definícióban meghatározott helyre.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Ha a szelet alatt hozzák naponta éjfél mint egy másik időpontban szelet futtatásának módját?
Használja a **eltolás** meg a időtartamát, amellyel az előállított szelet tulajdonságot. Lásd: [adatkészlet rendelkezésre állási](data-factory-create-datasets.md#dataset-availability) szakasz ezt a tulajdonságot vonatkozó további információért. Íme egy gyors példát:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Napi szeletek kezdjék **reggel 6 óra** helyett az alapértelmezett éjfél.     

### <a name="how-can-i-rerun-a-slice"></a>Hogyan lehet futtassa újból a szeletet?
A szelet újra futtathatja a következő módszerek valamelyikével:

* A figyelő és App kezelése segítségével futtassa egy tevékenységek ablakát vagy a szelet. Lásd: [kijelölt tevékenység windows újrafuttatása](data-factory-monitor-manage-app.md#perform-batch-actions) utasításokat.   
* Kattintson a **futtatása** a parancssáv a **ADATSZELET** panel az Azure-portálon szeletek.
* Futtatás **Set-AzureRmDataFactorySliceStatus** állapotú parancsmag beállítása **Várakozás** a szeletre vonatkozó.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Lásd: [Set-AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] a parancsmag vonatkozó további információért.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Mennyi volt vesz igénybe a szelet feldolgozása?
-Kezelővel tevékenység ablak figyelő & App kezelése tudni, hogy mennyi ideig tartott a adatszelet feldolgozni. Lásd: [tevékenység ablak Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) részleteiről.

Az Azure-portálon a következőket is teheti:  

1. Kattintson a **adatkészletek** csempét a **adat-előállító** a data factory paneljét.
2. Kattintson az adott adatkészletet a **adatkészletek** panelen.
3. Válassza ki a szelet, amely a megváltozása a **legutóbbi szeletek** listájából a **tábla** panelen.
4. Kattintson arra a tevékenységre, futtassa a **tevékenységek** listájából a **ADATSZELET** panelen.
5. Kattintson a **tulajdonságok** csempét a **tevékenység futtatása részletei** panelen.
6. Megjelenik a **időtartam** mezőt a értéket. Ez az érték az az idő a szelet feldolgozásának.   

### <a name="how-to-stop-a-running-slice"></a>Előfordulhat, hogy a futó szelet?
Ha szeretné megszüntetni a feldolgozási sor végrehajtása a, [Suspend-AzureRmDataFactoryPipeline](/powershell/module/azurerm.datafactories/suspend-azurermdatafactorypipeline) parancsmag. Jelenleg az felfüggesztése a folyamat nem állítja le a folyamatban lévő szelet végrehajtások. Miután végzett a folyamatban lévő végrehajtások, nincs külön szelet van felvételre.

Ha valóban szeretné azonnal állítsa le a végrehajtások, az egyetlen lehetőség lenne a feldolgozási sor törlése, és hozza létre újra. Ha törli a feldolgozási sor választja, nem kell törölnie a táblák és összekapcsolt szolgáltatások, a folyamat használja.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/azurerm.datafactories
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
