---
title: Azure Data Factory - Minták
description: Az Azure Data Factory szolgáltatással szállított minták részletei.
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
ms.openlocfilehash: 63db1810cbdd2133bc0577530325351997b31f30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139265"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory - Minták
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [PowerShell-mintákat a Data Factoryban,](../samples-powershell.md) és [kódmintákat az Azure Code Samples gyűjteményben.](https://azure.microsoft.com/resources/samples/?service=data-factory)


## <a name="samples-on-github"></a>Példák a GitHubon
A [GitHub Azure-DataFactory tárház](https://github.com/azure/azure-datafactory) számos mintát tartalmaz, amelyek segítségével gyorsan felgyorsíthatja az Azure Data Factory szolgáltatás (vagy) módosíthatja a parancsfájlokat, és saját alkalmazásban használja. A Samples\JSON mappa json-kódrészleteket tartalmaz a gyakori esetekhez.

| Sample | Leírás |
|:--- |:--- |
| [ADF-forgatókönyv](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Ez a minta egy teljes körű forgatókönyvet biztosít a naplófájlok Azure Data Factory használatával történő feldolgozásához, hogy a naplófájlokból származó adatokat elemzési adatokká alakítsa. <br/><br/>Ebben a forgatókönyvben a Data Factory folyamat gyűjti a mintanaplókat, feldolgozza és gazdagítja az adatokat a naplók referenciaadatokkal, és átalakítja az adatokat, hogy kiértékelje a nemrégiben elindított marketingkampány hatékonyságát. |
| [JSON-minták](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Ez a minta json példákat a gyakori forgatókönyvek. |
| [Http Adatletöltő minta](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Ez a minta bemutatja az adatok letöltését egy HTTP-végpontról az Azure Blob Storage egyéni .NET tevékenység használatával. |
| [Alkalmazástartomány-tartományon belüli tevékenység– minta](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Ez a minta lehetővé teszi egy egyéni .NET-tevékenység létrehozására, amely nem korlátozza az ADF-indító által használt összeállítási verziókat (például WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x stb.). |
| [R parancsfájl futtatása](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Ez a minta tartalmazza a Data Factory egyéni tevékenységet, amely az RScript.exe meghívására használható. Ez a minta csak a saját (nem igény szerinti) HDInsight-fürttel működik, amelyen már telepítve van r. |
| [Spark-feladatok meghívása a HDInsight Hadoop-fürtön](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |Ez a minta bemutatja, hogyan használhatja a MapReduce tevékenységet egy Spark-program meghívásához. A spark-program csak másolja az adatokat az egyik Azure Blob-tárolóból a másikba. |
| [Twitter-elemzés az Azure Machine Learning batch scoring tevékenységhasználatával](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Ez a minta bemutatja, hogyan azureMLBatchScoringActivity egy Azure Machine Learning-modell, amely végrehajtja a Twitter hangulatelemzés, pontozás, előrejelzés stb. |
| [Twitter elemzés egyéni tevékenység használatával](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Ez a minta bemutatja, hogyan lehet egy egyéni .NET tevékenység használatával meghívni egy Azure Machine Learning-modellt, amely végrehajtja a Twitter hangulatelemzését, pontozását, előrejelzését stb. |
| [Paraméterezett folyamatok az Azure Machine Learninghez](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |A minta egy végpontok között C# kódot biztosít az N-folyamatok központi telepítéséhez a pontozáshoz és az újraképzéshez, amelyek mindegyike egy másik régióparaméterrel rendelkezik, ahol a régiók listája egy parameters.txt fájlból származik, amely ebben a mintában szerepel. |
| [Referenciaadat-frissítés az Azure Stream Analytics-feladatokhoz](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Ez a minta bemutatja, hogyan használhatja az Azure Data Factory és az Azure Stream Analytics együtt a lekérdezések futtatásához a referenciaadatokat, és állítsa be a frissítés referenciaadatok ütemezés szerint. |
| [Hibrid csővezeték a Hortonworks Hadoop helyszíni](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |A minta egy helyszíni Hadoop-fürtöt használ számítási célként a Data Factory-ban futó feladatokhoz, ahogy más számítási célokat is hozzáadna, például egy HDInsight-alapú Hadoop-fürtet a felhőben. |
| [JSON konverziós eszköz](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Ez az eszköz lehetővé teszi, hogy konvertálni JSONs verzió előtt 2015-07-01-preview a legújabb vagy 2015-07-01-preview (alapértelmezett). |
| [U-SQL mintabemeneti fájl](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Ez a fájl egy U-SQL tevékenység által használt mintafájl. |
| [Blobfájl törlése](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Ez a minta egy C# fájlt mutat be, amely az ADF egyéni .net tevékenység részeként használható fájlok törléséhez a forrásbeli Azure Blob helyéről a fájlok másolása után.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
A következő Azure Resource Manager-sablonok at Data Factory a GitHubon.

| Sablon | Leírás |
| --- | --- |
| [Másolás az Azure Blob Storage-ból az Azure SQL-adatbázisba](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |A sablon üzembe helyezése létrehoz egy Azure-adatfeldolgozó üzemet egy folyamattal, amely adatokat másol a megadott Azure blobstorage-ból az Azure SQL-adatbázisba |
| [Másolás a Salesforce-ról az Azure Blob Storage-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |A sablon üzembe helyezése létrehoz egy Azure-adatfeldolgozó egy folyamat, amely átmásolja az adatokat a megadott Salesforce-fiókból az Azure blob storage. |
| [Adatok átalakítása A Hive-parancsfájl Azure HDInsight-fürtön való futtatásával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |A sablon üzembe helyezése létrehoz egy Azure-adatfeldolgozó egy folyamat, amely átalakítja az adatokat egy Azure HDInsight Hadoop-fürtön futtatva a minta Hive-parancsfájl futtatásával. |

## <a name="samples-in-azure-portal"></a>Minták az Azure Portalon
A **mintafolyamatok** csempéje az adat-előállító kezdőlapján mintafolyamatokat és a hozzájuk tartozó entitásokat (adatkészleteket és csatolt szolgáltatásokat) telepíthet az adat-előállítóba.

1. Hozzon létre egy adat-előállító, vagy nyisson meg egy meglévő adatgyárat. Az adat-előállító létrehozásához lásd: [Adatok másolása a Blob Storage-ból sql-adatbázisba a Data Factory használatával](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben.
2. Az adat-előállító **DATA FACTORY** panelen kattintson a **Mintafolyamatok** csempére.

    ![Mintafolyamatok csempéje](./media/data-factory-samples/SamplePipelinesTile.png)
3. A **Mintafolyamatok** panelen kattintson az üzembe helyezni kívánt **mintára.**

    ![Mintafolyamatok panelje](./media/data-factory-samples/SampleTile.png)
4. Adja meg a minta konfigurációs beállításait. Például az Azure storage-fiók neve és a fiókkulcs, az Azure SQL-kiszolgáló neve, adatbázis, felhasználói azonosító és jelszó stb.

    ![Mintapen](./media/data-factory-samples/SampleBlade.png)
5. Miután végzett a konfigurációs beállítások megadásával, kattintson a **Létrehozás** gombra a mintafolyamatok és a folyamatok által használt csatolt szolgáltatások/táblák létrehozásához/üzembe helyezéséhez.
6. A **mintafolyamatok** panelen korábban kattintott mintacsempén látható a központi telepítés állapota.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik a **központi telepítés sikeres** üzenetet a csempe a minta, zárja be a **minta folyamatok** panel.  
8. A **DATA FACTORY** panelen láthatja, hogy a csatolt szolgáltatások, adatkészletek és folyamatok hozzáadódnak az adat-előállítóhoz.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Minták a Visual Studióban
### <a name="prerequisites"></a>Előfeltételek
A számítógépre a következőket kell telepíteni:

* Visual Studio 2013 vagy Visual Studio 2015
* Töltse le az Azure SDK-t a Visual Studio 2013-hoz vagy a Visual Studio 2015-höz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** vagy a **VS 2015** elemre a **.NET** szakaszban.
* Töltse le a legújabb Azure Data Factory beépülő modult a Visual Studióhoz: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) vagy [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Visual Studio 2013 használata esetén a bővítményt az alábbi lépésekkel is frissítheti: A menüben kattintson az **Eszközök** -> **bővítmények és frissítések** -> **online** -> visual studio**galériára** -> **Microsoft Azure Data Factory Tools for Visual Studio** -> **Update parancsra.**

### <a name="use-data-factory-templates"></a>Adatgyári sablonok használata
1. Kattintson a menü **Fájl parancsára,** mutasson az **Új**pontra, és kattintson a **Project**parancsra.
2. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

   1. Válassza a **DataFactory** elemet a **Sablonok csoportban.**
   2. Válassza a jobb oldali ablaktáblában a **Data Factory Templates** lehetőséget.
   3. Adjon meg a projekt **nevét**.
   4. Válassza ki a projekt **helyét.**
   5. Kattintson az **OK** gombra.

      ![A New project (Új projekt) párbeszédpanel](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. A **Data Factory Templates (Mezőtelepi sablonok)** párbeszédpanelen jelölje ki a mintasablont a **Használati eset sablonok** csoportban, majd kattintson a **Tovább**gombra. A következő lépések végigvezetik az **Ügyfélprofilkészítés sablon használatán.** A többi minta esetében hasonlóak a lépések.

    ![Adatfeldolgozó sablonok párbeszédpanel](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. A **Data Factory Configuration** párbeszédpanelen kattintson a **Tovább** gombra a Data **Factory Alapjai** lapon.
5. Az **Adatgyár konfigurálása** lapon tegye a következő lépéseket:
   1. Válassza **az Új adatgyár létrehozása**lehetőséget. A Meglévő **adat-előállító használata**lehetőséget is választhatja.
   2. Adja meg az adat-előállító **nevét.**
   3. Válassza ki azt az **Azure-előfizetést,** amelyben létre szeretné hozni az adatgyárat.
   4. Válassza ki az adat-előállító **erőforráscsoportját.**
   5. Válassza ki az **USA nyugati**régióját , az USA **keleti régióját**vagy **Észak-Európát** a **régióhoz.**
   6. Kattintson a **Tovább** gombra.
6. Az **Adatmegőrzések konfigurálása** lapon adjon meg egy meglévő **Azure SQL-adatbázist** és **Az Azure storage-fiókot** (vagy) hozzon létre adatbázist/tárhelyet, majd kattintson a Tovább gombra.
7. A **Számítás konfigurálása** lapon válassza az alapértelmezett beállításokat, majd kattintson a **Tovább**gombra.
8. Az **Összegzés** lapon tekintse át az összes beállítást, majd kattintson a **Tovább**gombra.
9. A **Telepítés állapota** lapon várja meg, amíg a telepítés befejeződik, majd kattintson a **Befejezés gombra.**
10. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra.
11. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
12. A következő párbeszédpanelnek kell megjelennie:

    ![Publish (Közzététel) párbeszédpanel](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. A **Data Factory konfigurálási** lapján hajtsa végre a következő lépéseket:

    1. Erősítse meg, hogy **a meglévő adatgyárhasználata** beállítás használata.
    2. Válassza ki azt az **adatgyárat,** amelyet a sablon használatakor kijelölt.
    3. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz. (Ha a **Tovább** gomb le van tiltva, nyomja le a **TAB** billentyűt a Name (Név) mezőből való kilépéshez.)
14. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.     
15. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.
16. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra.

[Az első adatgyár (Visual Studio) létrehozása](data-factory-build-your-first-pipeline-using-vs.md) című témakörben további információt talál arról, hogy a Visual Studio segítségével segítségével készíthet-e Data Factory-entitásokat, és közzéteszi őket az Azure-ban.          
