---
title: Az Azure Data Factory - minták
description: A minták azt szállításra részletesen ismerteti az Azure Data Factory szolgáltatással.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 03127dc777588f669ef07af52c8f73d986bfe0ea
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020812"
---
# <a name="azure-data-factory---samples"></a>Az Azure Data Factory - minták
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [PowerShell-minták az adat-előállító](../samples-powershell.md) és [Kódminták az Azure-Kódminták katalógusában](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Példák a GitHubon
A [GitHub Azure-DataFactory-tárház](https://github.com/azure/azure-datafactory) tartalmaz, amelyek segítségével több mintát gyorsan az Azure Data Factory szolgáltatás felkészülési (vagy) módosítsa a szkripteket, és használhatja saját alkalmazás. A Samples\JSON mappában található JSON-kódrészletek gyakran használt esetekben.

| Sample | Leírás |
|:--- |:--- |
| [ADF-forgatókönyv](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Ez a minta Azure Data Factory használatával történő információkká alakítsa adatait a naplófájlokat a naplófájlok feldolgozásának egy végpontok közötti forgatókönyv biztosít. <br/><br/>Ez az útmutató a Data Factory-folyamatot mintanaplók folyamatok gyűjti bővíti az adatokat a naplókból a referenciaadatok és alakítja át a közelmúltban indította el marketingkampány hatékonyságának kiértékelheti, hogy az adatok. |
| [JSON-minták](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Ezt a mintát gyakran használt esetekben JSON példákat. |
| [HTTP-adatok letöltési segédprogramja minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |A minta ezen adása letöltése az Azure Blob Storage .NET egyéni tevékenység használatával adatokat egy HTTP-végpontot. |
| [Adatbázisközi AppDomain Dot Net tevékenység minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Ez a minta lehetővé teszi, hozzon létre egy egyéni .NET-tevékenységet, amely nem korlátozza az ADF indítója (például WindowsAzure.Storage verze 4.3.0 Newtonsoft.Json v6.0.x, stb.) által használt szerelvény verziója. |
| [R-szkript futtatásához](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Ez a példa a Data Factory egyéni tevékenység RScript.exe meghívásához használt tartalmazza. Ez a minta csak (a saját nem igény szerinti) HDInsight-fürt, amely már rendelkezik az R telepítve van rajta működik. |
| [HDInsight Hadoop-fürtön a Spark-feladatok meghívása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Ez a példa bemutatja, hogyan MapReduce tevékenység használatával Spark-program meghívása. A spark-program csak adatokat másol egy Azure Blob-tárolóból egy másik. |
| [Twitter-elemzése Azure Machine Learning kötegelt pontozási tevékenység használatával](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Ez a példa bemutatja, hogyan AzureMLBatchScoringActivity elindításához egy Azure Machine Learning-modellhez, amely végrehajtja a twitter-vélemények elemzése, pontozási, előrejelzési stb. |
| [Az egyéni tevékenység Twitter elemzése](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Ez a példa bemutatja, hogyan .NET egyéni tevékenység elindításához egy Azure Machine Learning-modellhez, amely végrehajtja a twitter-vélemények elemzése, pontozási, előrejelzési stb. |
| [Az Azure Machine Learning paraméteres folyamatok](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |A példa azt mutatja egy teljes körű C# kód pontozó- és egy másik régióban paraméterrel egy parameters.txt fájlt, amely tartalmazza ezt a mintát forrását a régiók listáját az egyes átképezési N folyamatok üzembe helyezéséhez. |
| [Referencia az Azure Stream Analytics-feladatok adatok frissítése](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Ez a példa bemutatja, hogyan használata Azure Data Factory és az Azure Stream Analytics együttesen futtassa a lekérdezéseket a referenciaadatok, és a frissítés a referenciaadatoknál ütemezés beállításához. |
| [Hibrid folyamat helyszíni Hortonworks Hadoop-keretrendszerrel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |A minta egy számítási célnak egy helyi Hadoop-fürtöt használja, mint más számítási célnak jelentene, mint egy HDInsight-alapú Hadoop-fürtöt az adat-előállító feladatokat futtató. |
| [JSON-átalakítás eszköz](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Ez az eszköz lehetővé teszi JSONs próbaverzióról 2015-07-01-preview legújabb vagy 2015-07-01-preview (alapértelmezett) előtti verziót. |
| [U-SQL-minta bemeneti fájl](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Ezt a fájlt egy U-SQL-tevékenység által használt mintafájl. |
| [Blob, fájl törlése](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Ez a példa bemutatja egy C# fájlt, amely törli a fájlokat a forrás az Azure Blob helyére, a fájlok másolása után az ADF egyéni .net-tevékenység részeként használható.|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
Az alábbi Azure Resource Manager-sablonok a Data Factory a Githubon találja.

| Sablon | Leírás |
| --- | --- |
| [Az Azure Blob Storage-ból az Azure SQL Database másolása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Ez a sablon üzembe helyezése egy Azure data factoryt hoz egy folyamatot, amely a megadott Azure blob storage-ból másolnak adatokat az Azure SQL database |
| [Másolja a Salesforce-ból az Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Azure data factory helyezi üzembe a sablont hoz létre egy folyamatot, amely adatokat másol az Azure blob storage a megadott Salesforce-fiókban. |
| [Adatok átalakítása a Hive-szkriptet futtat egy Azure HDInsight-fürt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Azure data factory helyezi üzembe a sablont hoz létre egy folyamatot, amely egy Azure HDInsight Hadoop-fürtön a minta Hive-szkript futtatásával alakítja át az adatokat. |

## <a name="samples-in-azure-portal"></a>Minták az Azure Portalon
Használhatja a **folyamatok minta** csempe mintában a folyamatok és a kapcsolódó entitások (adatkészletek és társított szolgáltatások) telepítéséhez a saját data factoryját a data factory kezdőlapján.

1. Adat-előállító létrehozása, vagy nyisson meg egy meglévő data factoryt. Lásd: [adatok másolása Blob Storage-ból az SQL Database használatával a Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) az adat-előállító létrehozásához szükséges lépéseket.
2. Az a **adat-előállító** a data Factory panelen kattintson a **folyamatok minta** csempére.

    ![Minta folyamatok csempe](./media/data-factory-samples/SamplePipelinesTile.png)
3. Az a **folyamatok minta** panelen kattintson a **minta** , amely számára telepíteni kívánja.

    ![Minta folyamatok panel](./media/data-factory-samples/SampleTile.png)
4. Adja meg a minta konfigurációs beállításait. Például az Azure tárfiók nevét és kulcsát, az Azure SQL-kiszolgáló neve, adatbázis, felhasználói Azonosítóját, és a jelszó stb.

    ![Mintául szolgáló panelt](./media/data-factory-samples/SampleBlade.png)
5. Miután végzett a konfigurációs beállítások megadása gombra **létrehozás** létrehozása és üzembe helyezéséhez a mintában a folyamatok és a társított szolgáltatások/táblák az egyes folyamatok által használt.
6. Központi telepítés állapotát az minta csempéjén, amire kattintott korábban tekintse meg a **folyamatok minta** panelen.

    ![Üzembe helyezés állapota](./media/data-factory-samples/DeploymentStatus.png)
7. Amikor megjelenik a **üzembe helyezés sikeres** jelenik meg, zárja be a mintához a csempe a **folyamatok minta** panelen.  
8. A **adat-előállító** panelen láthatja, hogy a társított szolgáltatások, adatkészletek és folyamatok kerülnek az adat-előállítóhoz.  

    ![A Data Factory panel](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>A Visual Studio minták
### <a name="prerequisites"></a>Előfeltételek
A számítógépre a következőket kell telepíteni:

* Visual Studio 2013 vagy Visual Studio 2015
* Töltse le az Azure SDK-t a Visual Studio 2013-hoz vagy a Visual Studio 2015-höz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** vagy a **VS 2015** elemre a **.NET** szakaszban.
* Töltse le a legújabb Azure Data Factory beépülő modult a Visual Studióhoz: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) vagy [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Ha a Visual Studio 2013-at használ, is frissítheti a beépülő modul a következő lépések végrehajtásával: Kattintson a menü **eszközök** -> **bővítmények és frissítések** -> **Online** -> **Visual Studio-galéria**  ->  **Microsoft Azure Data Factory Tools for Visual Studio** -> **frissítés**.

### <a name="use-data-factory-templates"></a>Data Factory-sablonok használata
1. Kattintson a **fájl** menüben mutasson a **új**, és kattintson a **projekt**.
2. A **New Project** (Új projekt) párbeszédablakban hajtsa végre az alábbi lépéseket:

   1. Válassza ki **DataFactory** alatt **sablonok**.
   2. Válassza ki **Data Factory sablonok** a jobb oldali ablaktáblán.
   3. Adjon meg a projekt **nevét**.
   4. Válassza ki a **hely** a projekthez.
   5. Kattintson az **OK** gombra.

      ![A New project (Új projekt) párbeszédpanel](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Az a **Data Factory sablonok** párbeszédpanelen jelölje ki a minta-sablont a a **használatieset-sablonok** szakaszt, és kattintson a **tovább**. A következő lépések végigvezetik használatával a **ügyfél profilkészítés** sablont. Lépések hasonlóak a többi minták.

    ![Data Factory sablonok párbeszédpanel](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Az a **adat-előállító konfigurációja** párbeszédpanelen kattintson a **tovább** a a **Data Factory alapvető** lapot.
5. Az a **data factory konfigurálási** lapon, tegye a következőket:
   1. Válassza ki **új adat-előállító létrehozása**. Lehetőség kiválasztásával **használja a meglévő data factoryt**.
   2. Adjon meg egy **neve** a data Factory.
   3. Válassza ki a **Azure-előfizetés** a amely szeretné létrehozni az adat-előállító.
   4. Válassza ki a **erőforráscsoport** a data Factory.
   5. Válassza ki a **USA nyugati RÉGIÓJA**, **USA keleti Régiójában**, vagy **Észak-Európa** számára a **régió**.
   6. Kattintson a **tovább**.
6. Az a **adattárak konfigurálása** adja meg azokat a meglévő **Azure SQL database** és **Azure storage-fiók** (vagy) hozzon létre adatbázist/tárfiók, és kattintson a Tovább gombra.
7. Az a **számítás konfigurálása** lapon válassza ki az alapértelmezett beállításokat, és kattintson a **tovább**.
8. Az a **összefoglalás** lapon tekintse át a beállításokat, és kattintson a **tovább**.
9. Az a **központi telepítési állapot** lapon várja meg, amíg az üzembe helyezés befejeződött, és kattintson a **Befejezés**.
10. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra.
11. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
12. A következő párbeszédpanelnek kell megjelennie:

    ![Publish (Közzététel) párbeszédpanel](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. A **Data Factory konfigurálási** lapján hajtsa végre a következő lépéseket:

    1. Ellenőrizze, hogy **használja a meglévő data factoryt** lehetőséget.
    2. Válassza ki a **adat-előállító** kellett válassza ki a sablon használata esetén.
    3. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz. (Ha a **Tovább** gomb le van tiltva, nyomja le a **TAB** billentyűt a Name (Név) mezőből való kilépéshez.)
14. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.     
15. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.
16. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra.

Lásd: [az első data factory létrehozása (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) Visual Studio használatával a Data Factory-entitásokat hozzon létre, és ezek közzététele az Azure-bA.          
