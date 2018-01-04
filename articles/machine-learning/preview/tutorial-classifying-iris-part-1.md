---
title: "Adatok előkészítése az Írisz osztályozása oktatóanyaghoz az Azure Machine Learning-szolgáltatásokban (előzetes verzió) | Microsoft Docs"
description: "Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljeskörűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez az 1. rész, amely az adatok előkészítését ismerteti."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 09/28/2017
ms.openlocfilehash: f417154c2c2a27b356cefb94739838bd2136e756
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="classify-iris-part-1-prepare-the-data"></a>Írisz osztályozása, 1. rész: Az adatok előkészítése
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldást kínálnak.

Ez az oktatóanyag egy háromrészes sorozat első része. Az oktatóanyag az Azure Machine Learning-szolgáltatások (előzetes verzió) alapjait mutatja be. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> * Projekt létrehozása az Azure Machine Learning Workbench alkalmazásban.
> * Adat-előkészítési csomag létrehozása.
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához.

Az oktatóanyag a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/Iris_flower_data_set) használja. A képernyőképek Windows-specifikusak, de a macOS rendszeren szinte azonos a felhasználói élmény.

## <a name="prerequisites"></a>Előfeltételek
- Azure Machine Learning-kísérletezési fiók létrehozása.
- Az Azure Machine Learning Workbench telepítése.

Az Azure Machine Learning Workbench alkalmazás telepítéséhez követheti a [telepítési és létrehozási rövid útmutató](quickstart-installation.md) cikk utasításait. Ez a telepítési csomag tartalmazza az Azure többplatformos parancssori eszközét (Azure CLI) is.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Új projekt létrehozása az Azure Machine Learning Workbenchben
1. Nyissa meg az Azure Machine Learning Workbench alkalmazást, és szükség esetén jelentkezzen be. A **PROJEKTEK** panelen válassza a pluszjelet (**+**) egy **Új projekt** létrehozásához.

   ![Új munkaterület](media/tutorial-classifying-iris/new_ws.png)

2. Töltse ki az **Új projekt létrehozása** űrlapot: 

   ![Új projekt](media/tutorial-classifying-iris/new_project.png)

   - A **Projekt neve** mezőben adja meg a projekt nevét. Használja például a **myIris** értéket.
   - Válassza ki a **projektkönyvtárat**, amelyben a projekt létrejön. Használja például a `C:\Temp\` értéket. 
   - Töltse ki a **Projekt leírása** mezőt. Ez nem kötelező. 
   - A **Git-adattár** mezőt szintén nem kötelező kitölteni. Megadhat egy meglévő üres Git-adattárat (olyan adattárat, amelynek nincs „master” ága) a Visual Studio Team Servicesen. Ha már létező Git-adattárat használ, később engedélyezheti a barangolási és megosztási forgatókönyveket. További információkat a [Git-adattár használatát](using-git-ml-project.md) ismertető témakörben talál. 
   - Válasszon ki egy **munkaterületet**, például ez az oktatóanyag az **IrisGarden** nevűt használja. 
   - Válassza ki az **Írisz osztályozása** sablont a projektsablonlistából. 

3. Válassza a **Létrehozás** gombot. Létrejön és megnyílik a projekt.

## <a name="create-a-data-preparation-package"></a>Adat-előkészítési csomag létrehozása
1. Nyissa meg az **iris.csv** fájlt a **Fájlnézetben**. A fájl egy 5 oszlopból és 150 sorból álló táblázat. Négy oszlop számokat tartalmaz, a céloszlop pedig karakterláncokat. Oszlopfejlécek nem szerepelnek benne.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Ne helyezzen adatfájlokat a projektmappába, különösen akkor, ha nagy fájlokról van szó. Ez a sablon a kis méretű **iris.csv** fájlt tartalmazza bemutató célokra. További információkat a [nagyméretű adatfájlok írásával és olvasásával](how-to-read-write-files.md) kapcsolatos cikkben találhat.

2. Az **Adatnézetben** válassza a pluszjelet (**+**) egy új adatforrás hozzáadásához. Megnyílik az **Adatforrás hozzáadása** lap. 

   ![Adatnézet](media/tutorial-classifying-iris/data_view.png)

3. Nem módosítsa az alapértelmezett értékeket, majd válassza a **Tovább** gombot.  
 
   ![Válassza ki az iris elemet](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Győződjön meg arról, hogy az **iris.csv** fájlt választja ki a gyakorlathoz az aktuális projektmappából. Máskülönben a későbbi lépések sikertelenek lehetnek.
   
4. Válassza ki a fájlt, majd kattintson a **Befejezés** gombra.

4. Létrejön egy új fájl **iris-1.dsource** néven. A fájl neve egyedi, és tartalmazza a kötőjelet és az 1 számot, mert a projektben már van egy nem számozott, **iris.dsource** nevű fájl.  

   Megnyílik a fájl, és megjelennek az adatok. A rendszer az **1.** és **5. oszlop** között automatikusan hozzáadja az oszlopfejlécet az adatkészlethez. Görgessen le a táblázat aljára. Itt az adatkészlet utolsó sora üres. Ez azért van, mert a CSV-fájl tartalmaz egy extra sortörést.

   ![Iris adatnézet](media/tutorial-classifying-iris/iris_data_view.png)

5. Válassza a **Mérőszámok** gombot. Tekintse át a hisztogramokat. A rendszer minden oszlophoz részletes statisztikákat számított ki. Az **Adatok** gomb kiválasztásával ismét megjelenítheti az adatokat. 

   ![Iris adatnézet](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Válassza az **Előkészítés** gombot. Megnyílik az **Előkészítés** párbeszédpanel. 

   A mintaprojekt tartalmaz egy **iris.dprep** nevű fájlt. Ez alapértelmezés szerint kéri, hogy hozzon létre egy új adatfolyamot a meglévő **iris.dprep** adat-előkészítési csomagban. 

   A legördülő menüben válassza az **+ Új adat-előkészítési csomag** elemet, adjon meg új értéket a csomag neveként (**iris-1**), majd válassza az **OK** lehetőséget.

   ![Iris adatnézet](media/tutorial-classifying-iris/new_dprep.png)

   Létrejön egy új adat-előkészítési csomag **iris-1.dprep** néven, és megnyílik az adatelőkészítés-szerkesztőben.

7. Most végezzünk el néhány alapszintű adatelőkészítési műveletet. Nevezze át az oszlopokat. Ehhez jelölje ki egyenként az oszlopfejléceket, és tegye szerkeszthetővé a fejlécszöveget. 

   Adja meg a **Csészelevél hossza**, **Csészelevél szélessége**, **Szirom hosszúsága**, **Szirom szélessége** és **Fajok** nevet a megfelelő oszlopoknak.

   ![Oszlopok átnevezése](media/tutorial-classifying-iris/rename_column.png)

8. A külön értékek kiszámításához jelölje ki a **Fajok** oszlopot, majd kattintson rá a jobb gombbal a kiválasztáshoz. Válassza az **Értékek száma** elemet a legördülő menüből. 

   ![Az Értékek számának kiválasztása](media/tutorial-classifying-iris/value_count.png)

   Ez a művelet megnyitja a **Vizsgálók** panelt, és megjelenít egy négysávos hisztogramot. A céloszlop három különféle értékkel rendelkezik: **Iris_virginica**, **Iris_versicolor**, illetve **Iris-setosa**, és van még egy **(null)** érték.

9. A nullértékek kiszűréséhez válassza ki a nullértéket jelölő sávot a diagramon. Egy **(null)** értékű sor van. A sor eltávolításához válassza a mínuszjelet (**-**).

   ![Értékek száma hisztogram](media/tutorial-classifying-iris/filter_out.png)

10. Figyelje meg a **LÉPÉSEK** panelen részletezett egyes lépéseket. Amikor átnevezi az oszlopokat és kiszűri a nullértékű sorokat, a rendszer minden műveletet adat-előkészítési lépésként rögzít. Az egyes lépések szerkesztésével módosíthatja a beállításokat, illetve átrendezheti vagy eltávolíthatja a lépéseket.

   ![Lépések](media/tutorial-classifying-iris/steps.png)

11. Zárja be az adatelőkészítés-szerkesztőt. Válassza a **Bezárás** (x) elemet a diagram ikonnal ellátott **iris-1** lapon. A rendszer automatikusan menti a munkáját az **Adat-előkészítések** fejléc alatt látható **iris-1.dprep** fájlban.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

1. Kattintson a jobb gombbal az **iris-1.dprep** fájlra a helyi menü megjelenítéséhez, majd válassza az **Adathozzáférési kódfájl létrehozása** elemet. 

   ![Kód létrehozása](media/tutorial-classifying-iris/generate_code.png)

2. Megnyílik egy új, **iris-1.py** nevű fájl az alábbi kódsorokkal:

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

   Ez a kódrészlet meghívja az adat-előkészítési csomagként létrehozott logikát. Attól függően, hogy milyen környezetben fut ez a kód, a `df` különböző adatkerettípusokat jelölhet. A rendszer [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et használ, ha a kódot Python-futtatókörnyezetben, illetve [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html)-et, ha Spark-környezetben hajtják végre. 

   Az adatok Azure Machine Learning Workbenchben való előkészítésével kapcsolatos további információkért tekintse meg [az adat-előkészítés első lépéseit](data-prep-getting-started.md) ismertető útmutatót.

## <a name="next-steps"></a>További lépések
A háromrészes oktatóanyag-sorozat jelen, első részében a következőkre használta az Azure Machine Learning Workbenchet:
> [!div class="checklist"]
> * Új projekt létrehozása. 
> * Adat-előkészítési csomag létrehozása.
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához.

Továbbléphet a sorozat következő részére, amely az Azure Machine Learning-modellek létrehozását ismerteti:
> [!div class="nextstepaction"]
> [Modell létrehozása](tutorial-classifying-iris-part-2.md)
