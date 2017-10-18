---
title: "Adatok előkészítése az Írisz osztályozása oktatóanyaghoz a Machine Learning-szolgáltatásokban (előzetes verzió) | Microsoft Docs"
description: "Ez a részletes oktatóanyag bemutatja, hogyan használhatók ki teljes körűen az (előzetes verziójú) Azure Machine Learning-szolgáltatások. Ez az 1. rész, amely az adatok előkészítését ismerteti."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>Írisz osztályozása, 1. rész: Adatok előkészítése
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldás.

Ez az oktatóanyag egy háromrészes sorozat első része. Az oktatóanyag az Azure Machine Learning-szolgáltatások (előzetes verzió) alapjait mutatja be. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Projekt létrehozása az Azure Machine Learning Workbench alkalmazásban
> * Adat-előkészítési csomag létrehozása
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

Az oktatóanyag az egyszerűség kedvéért a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/Iris_flower_data_set) használja. A képernyőképek Windows-specifikusak, de a macOS rendszeren tapasztalható felhasználói élmény közel azonos.

## <a name="prerequisites"></a>Előfeltételek
- Azure Machine Learning-kísérletezési fiók létrehozása
- Az Azure Machine Learning Workbench telepítése

Követheti a [telepítési és létrehozási rövid útmutató](quickstart-installation.md) lépéseit az Azure Machine Learning Workbench alkalmazás telepítéséhez. Az alkalmazás a parancssori felületet (CLI) is tartalmazza.

## <a name="create-a-new-project-in-azure-ml-workbench"></a>Új projekt létrehozása az Azure ML Workbenchben
1. Indítsa el az Azure Machine Learning Workbench alkalmazást, és szükség esetén jelentkezzen be. A **PROJEKTEK** panelen kattintson a **+** ikonra egy **Új projekt** létrehozásához.

   ![új munkaterület](media/tutorial-classifying-iris/new_ws.png)

2. Töltse ki az **Új projekt létrehozása** űrlapot. 

   ![Új projekt](media/tutorial-classifying-iris/new_project.png)

   - A **Projekt neve** mezőben adja meg a projekt nevét. Használja például a **myIris** értéket.
   - Válassza ki a **projektkönyvtárat**, amelyben a projekt létrejön. Használja például a **C:\Temp** értéket. 
   - Töltse ki a **Projekt leírása** mezőt. 
   - A **Git-tárház** mezőt nem kötelező kitölteni. Megadhat egy meglévő üres Git-tárházat (olyan tárházat, amelynek nincs „master” ága) a VSTS-en. Ha így tesz, később engedélyezheti a barangolási és a megosztási forgatókönyveket. További információkért tekintse meg [a Git-tárház használatával](using-git-ml-project.md) kapcsolatos cikket. 
   - Válasszon ki egy **munkaterületet**, például ez az oktatóanyag a következőt használja: **IrisGarden**. 
   - Válassza ki az **Írisz osztályozása** sablont a projektsablonlistából. 

3. Kattintson a **Létrehozás** gombra a projekt létrehozásához. Létrejön és megnyílik a projekt.

## <a name="create-a-data-preparation-package"></a>Adat-előkészítési csomag létrehozása
1. Nyissa meg az **iris.csv** fájlt a **Fájlnézetben**. A fájl egy 5 oszlopból és 150 sorból álló egyszerű táblázat. Négy oszlop számokat tartalmaz, a céloszlop pedig karakterláncokat. Oszlopfejlécek nem szerepelnek benne.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Nem ajánlott adatfájlokat helyezni a projektmappába, különösen akkor, ha nagy fájlokról van szó. Ez a sablon kis mérete miatt, bemutató célokból az **iris.csv** fájlt tartalmazza. További információkat a [nagyméretű adatfájlok írásával és olvasásával](how-to-read-write-files.md) kapcsolatos cikkben találhat.

2. Az **Adatnézetben** kattintson a **+** ikonra egy új adatforrás hozzáadásához. Elindul az **Adatforrás hozzáadása** varázsló. 

   ![adatnézet](media/tutorial-classifying-iris/data_view.png)

3. Haladjon végig az adatelőkészítési varázsló lépésein. 
   - Az első képernyőn válassza a **Fájl(ok)/Mappa** lehetőséget, majd kattintson a **Tovább** gombra.
   - A második képernyőn válassza a helyi **iris.csv** fájlt (például: „C:\Temp\myIris\iris.csv”).
   - A harmadik, **Fájl részletei** képernyőn fogadja el az alapértelmezett értékeket.
   - A negyedik, **Adattípusok** képernyőn módosítsa az **ADATTÍPUS** értékét _Karakterláncról_ _Numerikusra_ az _1. oszloptól_ a _4. oszlopig_, mert ezekben az oszlopokban numerikus értékek szerepelnek. 
   - Az ötödik és hatodik képernyőn fogadja el az alapértelmezett értékeket.
   - Kattintson a **Befejezés** gombra.

   ![iris kiválasztása](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Győződjön meg arról, hogy az **iris.csv** fájlt választja ki a gyakorlathoz az aktuális projektmappából, különben a későbbi lépések sikertelenek lehetnek. 

4. Létrejön egy új fájl **iris-1.dsource** néven. A fájl neve egyedi, és tartalmazza a kötőjelet és az egyes számot, mert a projektben már van egy nem számozott, **iris.dsource** nevű fájl.  

   Megnyílik a fájl, és megjelennek az adatok. A rendszer az **1. oszlop**–**5. oszlop** között automatikusan hozzáadja az oszlopfejlécet az adatkészlethez. Görgessen le a táblázat aljára. Az adatkészlet utolsó sora üres. Ez azért van, mert a CSV-fájl tartalmaz egy extra sortörést.

   ![iris adatnézet](media/tutorial-classifying-iris/iris_data_view.png)

5. Ezután kattintson a **Mérőszámok** gombra. Tekintse át a hisztogramokat és az egyes oszlopokhoz kiszámított részletes statisztikákat. Az **Adatok** gombra kattintva ismét megjelenítheti az adatokat. 

   ![iris adatnézet](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Kattintson az **Előkészítés** gombra. Megjelenik az **Előkészítés** párbeszédpanel. 

   A mintaprojekt tartalmaz egy **iris.dprep** nevű fájlt, így alapértelmezés szerint kérni fogja, hogy hozzon létre egy új adatfolyamot a meglévő **iris.dprep** adatelőkészítési csomagban. 

   Állítsa át a legördülő lista értékét a **+Új adat-előkészítési csomag** lehetőségre, adja meg az „iris-1” új értéket, majd kattintson az **OK** gombra.

   ![Iris adatnézet](media/tutorial-classifying-iris/new_dprep.png)

   Létrejön egy új adatelőkészítési csomag **iris-1.dprep** néven, és megnyílik az adatelőkészítés-szerkesztőben.

7. Most végezzünk el néhány alapszintű adatelőkészítési műveletet. Nevezze át az oszlopokat. Ehhez kattintson egyenként az oszlopfejlécekre, és tegye szerkeszthetővé a fejlécszöveget. 

   Adja meg a **Csészelevél hossza**, **Csészelevél szélessége**, **Szirom hosszúsága**, **Szirom szélessége** és **Fajok** nevet a megfelelő oszlopoknak.

   ![Oszlopok átnevezése](media/tutorial-classifying-iris/rename_column.png)

8. A külön értékek számolásához jelölje ki a **Fajok** oszlopot, majd kattintson rá a jobb gombbal. Válassza az **Értékek száma** lehetőséget. 

   ![Kattintson az Értékek száma lehetőségre](media/tutorial-classifying-iris/value_count.png)

   Ez a művelet megnyitja a **Vizsgálók** panelt, és megjelenít egy négysávos hisztogramot. Figyelje meg, hogy a céloszlop három különféle értékkel rendelkezik: **Iris_virginica**, **Iris_versicolor**, illetve **Iris-setosa**, és van még egy **(null)** érték.

9. A null értékek kiszűréséhez válassza ki a null értéket jelölő sávot a diagramon. Egy **(null)** értékű sor van. A sor eltávolításához kattintson a **-** szűrőgombra.

   ![Értékek száma hisztogram](media/tutorial-classifying-iris/filter_out.png)

10. Figyelje meg a **LÉPÉSEK** panelen részletezett egyes lépéseket. Amikor átnevezi az oszlopokat és kiszűri a null értékű sort, a rendszer minden műveletet adatelőkészítési lépésként rögzít. Az egyes lépéseket szerkesztve módosíthatja a beállításokat, átrendezheti a lépéseket, és akár el is távolíthat egy lépést.

   ![Lépések](media/tutorial-classifying-iris/steps.png)

11. Most a diagram ikonnal ellátott **iris-1** nevű lap **X** ikonjára kattintva zárja be az adat-előkészítő szerkesztőjét. A munkáját a rendszer automatikusan menti az **Adat-előkészítések** fejléc alatt látható **iris-1.dprep** fájlban.

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

1. Kattintson a jobb gombbal az **iris-1.dprep** fájlra a helyi menü megjelenítéséhez, majd válassza ki az **Adathozzáférési kódfájl létrehozása** lehetőséget. 

   ![kód létrehozása](media/tutorial-classifying-iris/generate_code.png)

2. Megnyílik egy új, **iris-1.py** nevű fájl az alábbi kódsorokkal:

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   Ez a kódrészlet meghívja az adat-előkészítési csomagként létrehozott logikát. Attól függően, hogy milyen környezetben fut ez a kód, a `df` különböző adatkerettípusokat jelölhet. A rendszer [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et használ, ha a kódot Python-futtatókörnyezetben, illetve [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html)-et, ha Spark-környezetben hajtják végre. 

   Az adatok Azure Machine Learning Workbenchben való előkészítésével kapcsolatos további információkért tekintse meg [az adatelőkészítés első lépéseit](data-prep-getting-started.md) ismertető útmutatót.

## <a name="next-steps"></a>Következő lépések
A háromrészes oktatóanyag-sorozat jelen, első részében a következőkre használta az Azure Machine Learning Workbench alkalmazást:
> [!div class="checklist"]
> * Új projekt létrehozása 
> * Adat-előkészítési csomag létrehozása
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

Továbbléphet a sorozat következő részére, amely a Machine Learning-modellek létrehozását ismerteti.
> [!div class="nextstepaction"]
> [Modell létrehozása](tutorial-classifying-iris-part-2.md)
