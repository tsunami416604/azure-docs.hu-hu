---
title: "Adatok előkészítése az Írisz osztályozása oktatóanyaghoz az Azure Machine Learning-szolgáltatásokban (előzetes verzió) | Microsoft Docs"
description: "Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljeskörűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez az 1. rész, amely az adatok előkészítését ismerteti."
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 12cba3d4acf0e6018cea6e76df9208bcf380d976
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>Oktatóanyag: Írisz osztályozása, 1. rész – Az adatok előkészítése

Az Azure Machine Learning szolgáltatás (előzetes verzió) az adatszakértők általi használatra szánt, az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldás.

Ez az oktatóanyag **egy háromrészes sorozat első része**. Az oktatóanyag az Azure Machine Learning-szolgáltatások (előzetes verzió) alapjait és a következőket mutatja be:

> [!div class="checklist"]
> * Projekt létrehozása az Azure Machine Learning Workbenchben
> * Adat-előkészítési csomag létrehozása
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

Az oktatóanyag a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/Iris_flower_data_set) használja. 

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:
- Egy Azure Machine Learning kísérletezési fiókra
- Egy telepített Azure Machine Learning Workbenchre

Ezen előfeltételek hiányában kövesse a [telepítés és indítás rövid útmutatójában](quickstart-installation.md) ismertetett lépéseket a fiókok beállításához és az Azure Machine Learning Workbench alkalmazás telepítéséhez. 

## <a name="create-a-new-project-in-workbench"></a>Új projekt létrehozása a Workbenchben

Ha követte a [telepítés és indítás rövid útmutatójában](quickstart-installation.md) ismertetett lépéseket, már létrehozta ezt a projektet, és továbbléphet a következő szakaszra.

1. Nyissa meg az Azure Machine Learning Workbench alkalmazást, és szükség esetén jelentkezzen be. 
   
   + Windowsban használja a **Machine Learning Workbench** asztali parancsikonját. 
   + MacOS rendszeren válassza a Launchpaden az **Azure ML Workbench** elemet.

1. Válassza a **PROJEKTEK** ablaktáblán a plusz jelet (+), majd az **Új projekt** lehetőséget.  

   ![Új munkaterület](media/tutorial-classifying-iris/new_ws.png)

1. Töltse ki az űrlap mezőit, és válassza a **Létrehozás** gombot, hogy új projektet hozzon létre a Workbenchben.

   Mező|Oktatóanyaghoz ajánlott érték|Leírás
   ---|---|---
   Projektnév | myIris |Adjon meg egy egyedi nevet a fiók azonosításához. Használhatja a saját nevét, vagy egy részleg vagy projekt nevét is. Olyasmit adjon meg, amivel a legjobban azonosítható a kísérlet. A név 2–32 karakter hosszúságú lehet. A név csak alfanumerikus és kötőjel (-) karaktert tartalmazhat. 
   Projektkönyvtár | c:\Temp\ | Adja meg a könyvtárat, amelyben a projekt létrejött.
   Projekt leírása | _hagyja üresen_ | A projekt leírására szolgáló mező, amelyet nem kötelező kitölteni.
   Visualstudio.com |_hagyja üresen_ | Nem kötelező kitölteni. Egy projektet egy Git-adattárhoz társíthat a Visual Studio Team Servicesben a forráskezelés és az együttműködés megkönnyítése érdekében. [További tudnivalókat erről a lehetőségről itt talál](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Munkaterület | IrisGarden (ha van) | Válasszon egy olyan munkaterületet, amelyet a kísérletezési fiókhoz hozott létre az Azure Portalon. <br/>Ha követte a rövid útmutatót, rendelkeznie kell egy IrisGarden nevű munkaterülettel. Ha nincs ilyen munkaterülete, válassza azt, amelyet a kísérletezési fiók létrehozásakor hozott létre, vagy válasszon egy tetszőleges munkaterületet.
   Projektsablon | Írisz osztályozása | A sablonok olyan szkripteket és adatokat tartalmaznak, amelyek elősegítik a termék különböző funkcióinak megismerését. Ez a sablon azokat a szkripteket és adatokat tartalmazza, amelyekre szüksége van ehhez a rövid bemutatóhoz, illetve a dokumentációs webhelyen található egyéb oktatóanyagokhoz. 

   ![Új projekt](media/tutorial-classifying-iris/new_project.png)
 
 Létrejön egy új projekt, és megnyílik az irányítópultja. Ezen a ponton áttekintheti a projekt kezdőlapját, adatforrásait, jegyzetfüzeteit és forráskódfájljait. 

   ![Projekt megnyitása](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>Adat-előkészítési csomag létrehozása

Következő lépésként felderítheti az adatokat, és megkezdheti az előkészítésüket az Azure Machine Learning Workbenchben. A Workbenchben végzett minden átalakítás JSON-formátumban tárolódik egy helyi adatelőkészítési csomagban (*.dprep fájlban). Ez az adatelőkészítési csomag a Workbench adatelőkészítési munkáinak elsődleges tárolója.

Az adatelőkészítési csomag később átadható egy futtatókörnyezetnek, például a local-C#/CoreCLR-nek, a Scala/Sparknak vagy a Scala/HDI-nek. 

1. Válassza ki a mappaikont a fájlnézet, majd az **iris.csv** fájlt a fájl megnyitásához.  

   A fájl egy 5 oszlopból és 50 sorból álló táblázatot tartalmaz. Négy oszlop számokat tartalmaz. Az ötödik egy karakterláncokat tartalmazó céloszlop. Egyik oszlopnak sincs fejlécneve.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Ne helyezzen adatfájlokat a projektmappába, különösen akkor, ha nagy fájlokról van szó. Kis méretéből kifolyólag a sablon az **iris.csv** adatfájlt használja bemutatási célból. További információkat a [nagyméretű adatfájlok írásával és olvasásával](how-to-read-write-files.md) kapcsolatos cikkben találhat.

2. Az **Adatnézetben** válassza a pluszjelet (**+**) egy új adatforrás hozzáadásához. Megnyílik az **Adatforrás hozzáadása** lap. 

   ![Az Adatnézet az Azure Machine Learning Workbenchben](media/tutorial-classifying-iris/data_view.png)

3. Válassza a **Szövegfájlok (*.csv, .json, .txt., …)** lehetőséget, és kattintson a **Tovább** gombra.
   ![Adatforrás az Azure Machine Learning Workbenchben](media/tutorial-classifying-iris/data-source.png)
   

4. Keresse meg az **iris.csv** fájlt, majd kattintson a **Tovább** gombra.  

   >[!IMPORTANT]
   >Győződjön meg arról, hogy az **iris.csv** fájlt választja ki a gyakorlathoz az aktuális projektmappából. Máskülönben a későbbi lépések sikertelenek lehetnek.
 
   ![Válassza ki az iris elemet](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. Ne módosítsa az alapértelmezett értékeket, és kattintson a **Befejezés** gombra.

6. Létrejön egy új fájl **iris-1.dsource** néven. A fájl neve egyedi, és tartalmazza a „-1” karakterláncot, mert a projektben már van egy nem számozott, **iris.dsource** nevű fájl.  

   Megnyílik a fájl, és megjelennek az adatok. A rendszer az **1.** és **5. oszlop** között automatikusan hozzáadja az oszlopfejlécet az adatkészlethez. Görgessen le a táblázat aljára. Itt az adatkészlet utolsó sora üres. Ez azért van, mert a CSV-fájl tartalmaz egy extra sortörést.

   ![Iris adatnézet](media/tutorial-classifying-iris/iris_data_view.png)

1. Válassza a **Mérőszámok** gombot. Hisztogramok jönnek létre és jelennek meg a képernyőn.

   Az **Adat** gombra kattintva válthat vissza az adatnézetbe. 
   
   ![Iris adatnézet](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. Tekintse át a hisztogramokat. A rendszer minden oszlophoz részletes statisztikákat számított ki. 

   ![Iris adatnézet](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Az **Előkészítés** gombra kattintva kezdheti el az adatelőkészítési csomag létrehozását. Megnyílik az **Előkészítés** párbeszédpanel. 

   A mintaprojekt alapértelmezés szerint tartalmaz egy **iris.dprep** nevű adatelőkészítési fájlt. 

   ![Iris adatnézet](media/tutorial-classifying-iris/prepare.png)

1. A legördülő menüben az **+ Új adatelőkészítési csomag** elem kiválasztásával hozzon létre egy új adatelőkészítési csomagot.

   ![Iris adatnézet](media/tutorial-classifying-iris/prepare_new.png)

1. Adjon meg új értéket a csomag neveként (**iris-1**), majd válassza az **OK** gombot.

   Létrejön egy új adat-előkészítési csomag **iris-1.dprep** néven, és megnyílik az adatelőkészítés-szerkesztőben.

   ![Iris adatnézet](media/tutorial-classifying-iris/prepare_iris-1.png)

   Most végezzünk el néhány alapszintű adatelőkészítési műveletet. 

1. Ehhez jelölje ki egyenként az oszlopfejléceket, és tegye szerkeszthetővé a fejlécszöveget. Ezután nevezze át az oszlopokat a következőképpen: 

   Adja meg sorrendben a **Csészelevél hossza**, **Csészelevél szélessége**, **Szirom hosszúsága**, **Szirom szélessége** és **Fajok** nevet a megfelelő oszlopoknak.

   ![Oszlopok átnevezése](media/tutorial-classifying-iris/rename_column.png)

1. Az egyes értékek megszámlálása:
   1. Jelölje ki a **Fajok** oszlopot.
   1. Kattintson a jobb gombbal a kiválasztásához. 
   1. Válassza az **Értékek száma** elemet a legördülő menüből. 

   Megnyílik a **Vizsgálók** panel az adatok alatt. Megjelenik egy négysávos hisztogram. A céloszlop három különféle értékkel rendelkezik: **Iris_virginica**, **Iris_versicolor**, illetve **Iris-setosa**, és van még egy **(null)** érték.

   ![Az Értékek számának kiválasztása](media/tutorial-classifying-iris/value_count.png)

   ![Értékek száma hisztogram](media/tutorial-classifying-iris/filter_out.png)

1. A null értékek kiszűréséhez válassza a (null) sávot, majd a mínuszjelet (**-**). 

   Ezután a (null) sor szürkévé válik, ami jelzi, hogy ki lett szűrve. 

   ![Null értékek kiszűrése](media/tutorial-classifying-iris/filter_out2.png)

1. Figyelje meg a **LÉPÉSEK** panelen részletezett egyes adatelőkészítési lépéseket. Amikor átnevezi az oszlopokat és kiszűri a nullértékű sorokat, a rendszer minden műveletet adat-előkészítési lépésként rögzít. Az egyes lépések szerkesztésével módosíthatja a beállításokat, illetve átrendezheti vagy eltávolíthatja a lépéseket.

   ![Lépések](media/tutorial-classifying-iris/steps.png)

1. Zárja be az adatelőkészítés-szerkesztőt. Válassza az x ikont a diagram ikonnal ellátott **iris-1** fülön a lap bezárásához. A rendszer automatikusan menti a munkáját az **Adat-előkészítések** fejléc alatt látható **iris-1.dprep** fájlban.

   ![Bezárás](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Python-/PySpark-kód létrehozása az adatelőkészítési csomagok meghívásához

 Az adatelőkészítési csomagok kimenetét közvetlenül megvizsgálhatja a Pythonban vagy egy Jupyter notebookban. A csomagok több futtatókörnyezetben is végrehajthatók, beleértve a helyi Pythont, a Sparkot (a Dockerrel együtt) és a HDInsight-környezetet. 

1. Keresse meg az Adat-előkészítések fül alatt látható **iris-1.dprep** fájlt.

1. Kattintson a jobb gombbal az **iris-1.dprep** fájlra, majd válassza az **Adathozzáférési kódfájl létrehozása** elemet a helyi menüből. 

   ![Kód létrehozása](media/tutorial-classifying-iris/generate_code.png)

   Megnyílik egy új, **iris-1.py** nevű fájl az alábbi kódsorokkal az adatelőkészítési csomagként létrehozott logika meghívásához:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   Attól függően, hogy a kód milyen környezetben fut, a `df` az adatkerettípust jelöli. 
   + Python-futtatókörnyezetben a rendszer [pandas Dataframe](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et használ.
   + Spark-környezetben a [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) van használatban. 
   
   Az adatok Azure Machine Learning Workbenchben való előkészítésének részleteit [az adat-előkészítés első lépéseit](data-prep-getting-started.md) ismertető útmutatóban találja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban azt sajátította el, hogyan használhatja az Azure Machine Learning Workbenchet a következőkhöz:
> [!div class="checklist"]
> * Új projekt létrehozása
> * Adat-előkészítési csomag létrehozása
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

Továbbléphet az oktatóanyag-sorozat következő részére, amely az Azure Machine Learning-modellek létrehozását ismerteti:
> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek létrehozása](tutorial-classifying-iris-part-2.md)
