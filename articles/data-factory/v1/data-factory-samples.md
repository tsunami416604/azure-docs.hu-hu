---
title: "Az Azure Data Factory - minták"
description: "Részletesen bemutatja mintának, hogy küldje el az Azure Data Factory szolgáltatásban."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e72cdee5b164fcd4944f4f1f95ff22bb5ceba6a4
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="azure-data-factory---samples"></a>Az Azure Data Factory - minták
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használata [PowerShell-példák 2-es verzióját a Data factoryban](../samples-powershell.md) és [Kódminták az Azure-Kódminták katalógusában](https://azure.microsoft.com/en-us/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Minták a Githubon
A [GitHub Azure-DataFactory-tárház](https://github.com/azure/azure-datafactory) tartalmaz, amelyek segítenek több mintát gyorsan Azure Data Factory szolgáltatással felkészülési (vagy) módosíthatja a parancsfájlok és saját alkalmazás használatát. A Samples\JSON mappa tartalmazza JSON kódtöredékek szabhatják.

| Sample | Leírás |
|:--- |:--- |
| [ADF forgatókönyv](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Ez a minta egy végpontok közötti forgatókönyv Azure Data Factory használatával kapcsolja be adatokat a naplófájlokból insights naplófájlok feldolgozásának biztosít. <br/><br/>Ebben a bemutatóban a Data Factory-folyamathoz minta naplókat, folyamatok gyűjti az adatokat a naplók referenciaadatok a következőképpen színesíti, és átalakítja az adatokat nemrég elindult marketingkampányt hatékonyságát. |
| [JSON-minták](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Ez a minta szabhatják JSON példákat. |
| [HTTP letöltő minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Ez a minta showcases letöltése HTTP-végponttal Azure Blob Storage használatával egyéni .NET tevékenység adatainak. |
| [Kereszt-AppDomain pont nettó tevékenység minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Ez a minta lehetővé teszi, hogy egy egyéni .NET tevékenység, amely nem korlátozza az ADF indító (például windowsazure.Storage kifejezésre v4.3.0 Newtonsoft.Json v6.0.x, stb.) által használt szerelvény verziók készíthet. |
| [R-parancsfájl futtatása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Ez a minta magában foglalja a Data Factory egyéni tevékenység, amelynek segítségével RScript.exe meghívni. Ez a minta csak a saját (nem igény) HDInsight-fürt, amely már a R telepítve van rajta működik. |
| [A Spark HDInsight Hadoop-fürt egy feladat meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Ez a példa bemutatja, hogyan használhatja a MapReduce művelethez meghívni egy Spark program. A spark program csak másol adatokat egy Azure Blob-tároló egy másikra. |
| [Twitter elemzése Azure Machine Learning kötegelt pontozási tevékenység használatával](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Ez a minta bemutatja, hogyan AzureMLBatchScoringActivity használatával meghívni az Azure Machine Learning modell, amely végrehajtja a twitter véleményeket elemzés, pontozási, előrejelzés stb. |
| [Az egyéni tevékenység Twitter-elemzés](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Ez a példa bemutatja egy egyéni .NET tevékenység használata meghívni az Azure Machine Learning modell, amely végrehajtja a twitter véleményeket elemzés, pontozási, előrejelzés stb. |
| [Az Azure Machine Learning paraméteres folyamatok](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |A minta egy végpont C#-kódban N folyamatok pontozási és egy másik régióban paraméter, ahol régiók listáját, amelyen megtalálható ez a minta parameters.txt fájlból jön az átképezési telepítéséhez nyújt. |
| [Hivatkozás az adatfrissítési Azure Stream Analytics-feladatok](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Ez a példa bemutatja, hogyan használható Azure Data Factory és az Azure Stream Analytics együtt a lekérdezések futtatása referenciaadatokkal, és a frissítés a referenciaadatoknál ütemezés beállításához. |
| [A helyszíni Hortonworks hadooppal hibrid folyamat](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |A minta egy helyszíni Hadoop-fürt egy számítási célként adat-előállítóban futó feladatok, ugyanúgy, mint például egy HDInsight Hadoop-fürt a felhő alapú más számítási célok felvenni használ. |
| [JSON-átalakítás eszköz](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Ez az eszköz lehetővé teszi JSONs konvertálása verzió előtt 2015 07-01. dátumú előnézeti legújabb vagy 2015-07-01. dátumú előnézeti (alapértelmezett). |
| [U-SQL minta bemeneti fájl](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Ezt a fájlt egy U-SQL-tevékenység által használt mintafájl. |
| [A blob fájl törlése](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Ez a minta egy C# fájlt törli a fájlokat a forrás Azure Blob helyére, a fájlok másolása után az ADF egyéni .net tevékenység részeként használható bővíthető.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
A Data Factory a Githubon található a következő Azure Resource Manager-sablonok.

| Sablon | Leírás |
| --- | --- |
| [Az Azure SQL Database az Azure Blob Storage másolása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Ez a sablon telepítéséhez hoz létre egy Azure data factory egy folyamatot, amely másol adatokat az adott Azure blob storage az Azure SQL Database adatbázishoz |
| [Az Azure Blob Storage Salesforce másolása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Ez a sablon telepítése egy Azure data factory hoz létre egy folyamatot, amely a megadott Salesforce-fiókban adatainak másolása az Azure blob storage. |
| [Adatok átalakítása Azure HDInsight-fürtök a Hive parancsfájl futtatásával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Ez a sablon telepítése egy Azure data factory hoz létre egy folyamatot, amely átalakítja az adatokat az Azure HDInsight Hadoop-fürt a minta Hive-parancsfájl futtatásával. |

## <a name="samples-in-azure-portal"></a>Az Azure portál – minták
Használhatja a **folyamatok minta** csempe a data factory minta folyamatok és a kapcsolódó entitásokra (adatkészletek és összekapcsolt szolgáltatások) telepítése a data factory a kezdőlapon.

1. Egy adat-előállító létrehozása, vagy nyissa meg valamelyik adat-előállítót. Lásd: [adatok másolása az Blob-tároló az SQL-adatbázis használata a Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy adat-előállító létrehozásának lépései.
2. Az a **adat-előállító** az adat-előállítóban paneljén kattintson a **folyamatok minta** csempére.

    ![A minta folyamatok csempe](./media/data-factory-samples/SamplePipelinesTile.png)
3. Az a **folyamatok minta** panelen kattintson a **minta** , amely számára telepíteni kívánja.

    ![Minta folyamatok panel](./media/data-factory-samples/SampleTile.png)
4. Adja meg a minta konfigurációs beállításait. Például az Azure storage-fiók nevét és a fiók kulcs Azure SQL-kiszolgáló neve, adatbázis, felhasználói Azonosítóját, és a jelszó, stb.

    ![A minta panel](./media/data-factory-samples/SampleBlade.png)
5. Miután elkészült, a konfigurációs beállítások megadásával, kattintson **létrehozása** létrehozása/a minta folyamatok és telepítéséhez kapcsolódó szolgáltatások, illetve táblákat a folyamatok által használt.
6. A minta csempe kattintott a korábbi központi telepítés állapotát megtekintheti a **folyamatok minta** panelen.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik a **KözpontiTelepítés sikerült** a csempére a minta, zárja be az üzenetet a **folyamatok minta** panelen.  
8. A **adat-előállító** panelen látja, hogy társított szolgáltatások, adathalmazok és adatcsatornák bekerülnek a data factory.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>A Visual Studio minták
### <a name="prerequisites"></a>Előfeltételek
A számítógépre a következőket kell telepíteni:

* Visual Studio 2013 vagy Visual Studio 2015
* Töltse le az Azure SDK-t a Visual Studio 2013-hoz vagy a Visual Studio 2015-höz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** vagy a **VS 2015** elemre a **.NET** szakaszban.
* Töltse le a legújabb Azure Data Factory beépülő modult a Visual Studióhoz: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) vagy [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Ha a Visual Studio 2013-at használja, a beépülő modult a következőképpen is frissítheti: A menüben kattintson a **Tools** -> **Extensions and Updates** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools for Visual Studio** ->  **Update** (Eszközök > Bővítmények és frissítések > Online > Visual Studio-gyűjtemény > Microsoft Azure Data Factory-eszközök a Visual Studióhoz > Frissítések) elemre.

### <a name="use-data-factory-templates"></a>Használja a Data Factory sablonok
1. Kattintson a **fájl** a menüben mutasson a **új**, és kattintson a **projekt**.
2. Az a **új projekt** párbeszédpanelen tegye a következőket:

   1. Válassza ki **DataFactory** alatt **sablonok**.
   2. Válassza ki **Data Factory sablonok** a jobb oldali ablaktáblán.
   3. Adjon meg egy **neve** a projekthez.
   4. Válassza ki a **hely** a projekthez.
   5. Kattintson az **OK** gombra.

      ![A New project (Új projekt) párbeszédpanel](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Az a **Data Factory sablonok** párbeszédpanelen jelölje ki a minta sablont a **használati eset sablonok** szakaszt, és kattintson a **tovább**. A következő lépések végigvezetik használatával a **ügyfél profilkészítési** sablont. Lépések hasonlóak a más minták.

    ![Data Factory sablonok párbeszédpanel](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Az a **Data Factory konfigurációs** párbeszédpanel, kattintson a **következő** a a **Data Factory alapjai** lap.
5. Az a **konfigurálása adat-előállító** lapon esetén tegye a következőket:
   1. Válassza ki **hozzon létre új adat-előállító**. Igény szerint kiválaszthatja **használja a meglévő adat-előállító**.
   2. Adjon meg egy **neve** esetében az adat-előállítóban.
   3. Válassza ki a **Azure-előfizetés** a kívánja létrehozni az adat-előállítóban.
   4. Válassza ki a **erőforráscsoport** esetében az adat-előállítóban.
   5. Válassza ki a **USA nyugati régiója**, **USA keleti régiója**, vagy **Észak-Európa** a a **régió**.
   6. Kattintson a **Tovább** gombra.
6. Az a **adattárolókhoz konfigurálása** csoportjában adja meg egy létező **Azure SQL adatbázis** és **Azure storage-fiók** (vagy) adatbázis tárolási létrehozása, és kattintson a Tovább gombra.
7. Az a **számítási konfigurálása** lapon válassza ki az alapértelmezett beállításokat, és kattintson a **következő**.
8. Az a **összegzés** lapon tekintse át az összes beállítást, és kattintson a **következő**.
9. Az a **központi telepítési állapot** lapon Várjon, amíg a telepítés befejeződött, és kattintson a **Befejezés**.
10. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra.
11. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
12. A következő párbeszédpanelnek kell megjelennie:

    ![Publish (Közzététel) párbeszédpanel](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. A **Data Factory konfigurálási** lapján hajtsa végre a következő lépéseket:

    1. Ellenőrizze, hogy **használja a meglévő adat-előállító** lehetőséget.
    2. Válassza ki a **adat-előállító** kellett válassza ki a sablon használatakor.
    3. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz. (Ha a **Tovább** gomb le van tiltva, nyomja le a **TAB** billentyűt a Name (Név) mezőből való kilépéshez.)
14. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.     
15. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.
16. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra.

Lásd: [az első adat-előállítóban (Visual Studio) Build](data-factory-build-your-first-pipeline-using-vs.md) Visual Studio használatával a Data Factory entitásokat hozhatnak létre és közzé őket az Azure-bA.          
