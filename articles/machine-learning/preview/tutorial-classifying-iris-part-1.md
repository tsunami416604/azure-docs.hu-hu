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
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>Írisz osztályozása, 1. rész: Adatok előkészítése
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldás.

Ez az oktatóanyag egy háromrészes sorozat első része. Az oktatóanyag az Azure Machine Learning-szolgáltatások (előzetes verzió) alapjait mutatja be. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Projekt létrehozása az Azure Machine Learning Workbench alkalmazásban
> * Adat-előkészítési csomag létrehozása
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

Az oktatóanyag az egyszerűség kedvéért a jól ismert [Iris flower adatkészletet](https://en.wikipedia.org/wiki/Iris_flower_data_set) használja. A képernyőképek Windows-specifikusak, de a macOS rendszeren tapasztalható felhasználói élmény közel azonos.

## <a name="launch-azure-machine-learning-workbench"></a>Az Azure Machine Learning Workbench elindítása
Kövesse a [telepítési és létrehozási rövid útmutató](quickstart-installation.md) lépéseit az Azure Machine Learning Workbench alkalmazás telepítéséhez. Az alkalmazás a parancssori felületet (CLI) is tartalmazza. Indítsa el az Azure Machine Learning Workbench alkalmazást, és szükség esetén jelentkezzen be.

## <a name="create-a-new-project"></a>Új projekt létrehozása
1. A **PROJEKTEK** panelen kattintson a **+** ikonra egy **Új projekt** létrehozásához.

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
1. Nyissa meg az `iris.csv` fájlt a **Fájlnézetben**. A fájl egy 5 oszlopból és 150 sorból álló egyszerű táblázat. Négy oszlop számokat tartalmaz, a céloszlop pedig karakterláncokat. Oszlopfejlécek nem szerepelnek benne.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >Megjegyzés: nem ajánlott adatfájlokat helyezni a projektmappába, különösen akkor, ha nagy fájlokról van szó. Ez a sablon kis mérete miatt, bemutató célokból tartalmazza az `iris.csv` fájlt. További információkat a [nagyméretű adatfájlok írásával és olvasásával](how-to-read-write-files.md) kapcsolatos cikkben találhat.

2. Az **Adatnézetben** kattintson a **+** ikonra egy új adatforrás hozzáadásához. Elindul az **Adatforrás hozzáadása** varázsló. 

   ![adatnézet](media/tutorial-classifying-iris/data_view.png)

3. Válassza ki a **Fájl(ok)/Mappa** lehetőséget, majd válassza ki a(z) `iris.csv` helyi fájlt. Fogadja el az alapértelmezett beállításokat az összes megjelenő képernyőn, majd kattintson a **Befejezés** gombra. 

   ![iris kiválasztása](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Győződjön meg arról, hogy az `iris.csv` fájlt választja ki a gyakorlathoz az aktuális projektmappából, különben a későbbi lépések sikertelenek lehetnek. 

4. Létrejön egy új fájl `iris-1.dsource` néven. A fájl neve egyedi, és tartalmazza a kötőjelet és az egyes számot, mert a projektben már van egy nem számozott, `iris.dsource` nevű fájl.  Megnyílik a fájl, és megjelennek az adatok. A rendszer automatikusan hozzáadja a `Column1`–`Column5` oszlopfejlécet az adatkészlethez. Görgessen le a táblázat aljára. Az adatkészlet utolsó sora üres. Ez azért van, mert a CSV-fájl tartalmaz egy extra sortörést.

   ![iris adatnézet](media/tutorial-classifying-iris/iris_data_view.png)

5. Ezután kattintson a **Mérőszámok** gombra. Tekintse át a hisztogramokat és az egyes oszlopokhoz kiszámított részletes statisztikákat. Az **Adatok** gombra kattintva ismét megjelenítheti az adatokat. 

   ![iris adatnézet](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Kattintson a **Mérőszámok** mellett található **Előkészítés** gombra (vagy az **Adatok** gombra mérőszám nézetben). Megjelenik az **Előkészítés** párbeszédpanel. A mintaprojekt már tartalmaz `iris.dprep` nevű fájlt, így alapértelmezés szerint kérni fogja, hogy hozzon létre egy új adatfolyamot a meglévő **iris.dprep** adat-előkészítési csomagban. Állítsa át a legördülő lista értékét a **+Új adat-előkészítési csomag** lehetőségre, adja meg az „iris-1” új értéket, majd kattintson az **OK** gombra.

   ![iris adatnézet](media/tutorial-classifying-iris/new_dprep.png)

Létrejön egy új adat-előkészítési csomag `iris-1.dprep` néven, és megnyílik az adatelőkészítés-szerkesztőben.

Most végezzünk el néhány egyszerű adatelőkészítési műveletet. Nevezze át az oszlopokat. Ehhez kattintson egyenként az oszlopfejlécekre, és tegye szerkeszthetővé a fejlécszöveget. Az öt oszlopnak adja a következő neveket sorrendben: `Sepal Length`, `Sepal Width`, `Petal Length`, `Petal Width` és `Species`.

![oszlopok átnevezése](media/tutorial-classifying-iris/rename_column.png)

Jelölje ki a `Species` oszlopot, majd kattintson rá a jobb gombbal. Válassza az **Értékek száma** lehetőséget. 

![értékek száma](media/tutorial-classifying-iris/value_count.png)

Ez a művelet létrehoz egy négysávos hisztogramot. A céloszlop három különféle értékkel rendelkezik: `Iris_virginica`, `Iris_versicolor` és `Iris-setosa`. Emellett található egy `(null)` értékű sor is. A sor eltávolításához jelölje ki a null értéket jelölő sávot, majd kattintson a **-** szűrő gombra. 

![értékek száma](media/tutorial-classifying-iris/filter_out.png)

Amikor átnevezi az oszlopokat és kiszűri a nullértékű sort, az elvégzett műveletek rögzítve lesznek a **LÉPÉSEK** panelen adatelőkészítési lépésekként. Ezeket szerkesztheti (módosíthatja a beállításaikat), átrendezheti, vagy akár el is távolíthatja.

![lépések](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

Zárja be az adatelőkészítés-szerkesztőt. (Ne aggódjon, a rendszer automatikus mentést végez.) Kattintson a jobb gombbal az **iris-1.dprep** fájlra a helyi menü megjelenítéséhez, majd válassza ki az **Adathozzáférési kódfájl létrehozása** lehetőséget. 

![kód létrehozása](media/tutorial-classifying-iris/generate_code.png)

Létrejön az **iris-1.py** fájl, amely előre fel van töltve a következő két kódsorral (valamint néhány megjegyzéssel):

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
Ez a kódrészlet meghívja az adat-előkészítési csomagként létrehozott logikát. A kód futtatási környezetétől függően a `df` [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) is lehet, ha Python-futtatókörnyezetben hajtják végre, vagy [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html), ha Spark-környezetben hajtják végre. Az adatok Azure Machine Learning Workbenchben való előkészítésével kapcsolatos további információkért tekintse meg [az adatelőkészítés első lépéseit](data-prep-getting-started.md) ismertető útmutatót.

## <a name="next-steps"></a>Következő lépések
A háromrészes oktatóanyag-sorozat jelen, első részében a következőkre használta az Azure Machine Learning Workbench alkalmazást:
> [!div class="checklist"]
> * Új projekt létrehozása 
> * Adat-előkészítési csomag létrehozása
> * Python-/PySpark-kód létrehozása az adat-előkészítési csomagok meghívásához

Továbbléphet a sorozat következő részére, amely a Machine Learning-modellek létrehozását ismerteti.
> [!div class="nextstepaction"]
> [Modell létrehozása](tutorial-classifying-iris-part-2.md)

