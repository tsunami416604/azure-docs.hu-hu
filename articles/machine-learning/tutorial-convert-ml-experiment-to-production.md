---
title: Machine learning-kísérlet kódjának átalakítása éles kódra
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthatja át a gépi tanulás kísérleti kódját a termelési kódra a MLOpsPython-kód sablonnal.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 7f5e24261fd5d006004a51186e22f6bfe1b8ab32
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589181"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Oktatóanyag: ML kísérleti kód átalakítása üzemi kódra

A Machine learning-projektek kísérletezést igényelnek, ahol a hipotézisek olyan agilis eszközökkel vannak tesztelve, mint a valós adathalmazok használatával Jupyter Notebook. Miután a modell készen áll az éles üzemre, a modell kódját egy üzemi programkódba kell helyezni. Bizonyos esetekben a modell kódját át kell alakítani Python-szkriptekre az üzemi programkódba való adattárházba való helyezéshez. Ez az oktatóanyag a kísérletezési kódok Python-szkriptekre való exportálásának ajánlott módszerét ismerteti.  

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Nem nélkülözhetetlen kód tisztítása
> * Refactor Jupyter Notebook kódot a functions szolgáltatásba
> * Python-parancsfájlok létrehozása kapcsolódó feladatokhoz
> * Egység tesztek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- A [MLOpsPython-sablon](https://github.com/microsoft/MLOpsPython/generate) előállítása és a `experimentation/Diabetes Ridge Regression Training.ipynb` és `experimentation/Diabetes Ridge Regression Scoring.ipynb` jegyzetfüzetek használata. Ezek a jegyzetfüzetek példaként szolgálnak a kísérletezésről a termelésre való áttérésre.
- Telepítse a nbconvert. [A telepítés lapon csak](https://nbconvert.readthedocs.io/en/latest/install.html) a telepítési utasításokat kövesse a __nbconvert telepítése__ című részben.

## <a name="remove-all-nonessential-code"></a>Az összes nem nélkülözhetetlen kód eltávolítása

A kísérletezés során írt kódok némelyike csak tájékozódási célokra szolgál. Ezért a kísérleti kód éles kódban való átalakításának első lépése a nem nélkülözhetetlen kód eltávolítása. A nem nélkülözhetetlen kód eltávolításával a kód még karbantartható lesz. Ebben a szakaszban a `experimentation/Diabetes Ridge Regression Training.ipynb` jegyzetfüzetből eltávolítja a kódot. A `X` és `y` alakját, valamint a `features.describe` cellát jelölő utasítások csak az adatfeltáráshoz használhatók, és eltávolíthatók. A nem nélkülözhetetlen kód eltávolítását követően `experimentation/Diabetes Ridge Regression Training.ipynb` a következő kódhoz hasonlóan kell kinéznie, Markdown nélkül:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Újrabontási kód a függvényekbe

Másodszor, a Jupyter-kódot át kell alakítani a függvényekbe. A kód into functions-re való újrabontása megkönnyíti az egységek tesztelését, és a kód karbantartását is lehetővé teszi. Ebben a szakaszban a következőt fogja újraválasztani:
- A diabetes Ridge regressziós betanítása notebook (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- A diabetes Ridge regressziós pontozási notebookja (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaction diabetes Ridge regressziós betanítási jegyzetfüzet a functions szolgáltatásba
A `experimentation/Diabetes Ridge Regression Training.ipynb`on hajtsa végre a következő lépéseket:

1. Hozzon létre egy `train_model`nevű függvényt, amely végrehajtja a paramétereket `data` és `alpha`, és visszaadja a modellt. 
1. Másolja a kódot a "Train Model on Training set" és a "validate Model on validate set" (modell ellenőrzése az érvényesítési csoporton) elemre a `train_model` függvénybe.

A `train_model` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Miután létrehozta a `train_model` függvényt, cserélje le a kódot a "Train Model on Training set" és a "validate Model on validate set" kifejezésre a következő utasítással:

```python
reg = train_model(data, alpha)
```

Az előző utasítás meghívja a `train_model` függvényt a `data` és `alpha` paraméterek átadásával, és visszaadja a modellt.

A `experimentation/Diabetes Ridge Regression Training.ipynb`on hajtsa végre a következő lépéseket:

1. Hozzon létre egy `main`nevű új függvényt, amely nem tartalmaz paramétereket, és nem ad vissza semmit.
1. Másolja a kódot a fejlécek alatt a "betöltési adat", az "adat felosztása a betanítási és érvényesítési készletekbe" és a "modell mentése" kifejezést a `main` függvénybe.
1. Másolja `train_model` az újonnan létrehozott hívást a `main` függvénybe.

A `main` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Az `main` függvény létrehozása után cserélje le az összes kódot az "adatok betöltése", az "adatok felosztása a betanítási és érvényesítési készletekbe" és a "modell mentése" lehetőségre, valamint az újonnan létrehozott hívást a `train_model`ra a következő utasítással:

```python
main()
```

Az újrabontás után a `experimentation/Diabetes Ridge Regression Training.ipynb` a következő kódhoz hasonlóan kell kinéznie a Markdown nélkül:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaction diabetes Ridge regressziós pontozási jegyzetfüzet into functions
A `experimentation/Diabetes Ridge Regression Scoring.ipynb`on hajtsa végre a következő lépéseket:

1. Hozzon létre egy `init`nevű új függvényt, amely nem tartalmaz paramétereket, és nem ad vissza semmit.
1. Másolja a kódot a "betöltési modell" fejléc alá a `init` függvénybe.

A `init` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Az `init` függvény létrehozása után cserélje le az összes kódot a "modell betöltése" fejléc alá egyetlen hívással, hogy `init` a következőképpen:

```python
init()
```

A `experimentation/Diabetes Ridge Regression Scoring.ipynb`on hajtsa végre a következő lépéseket:

1. Hozzon létre egy új, `run`nevű függvényt, amely `raw_data` és `request_headers` paramétereket fogad el, és a következő módon adja vissza az eredményeket:

    ```python
    {"result": result.tolist()}
    ```

1. Másolja az "adatelőkészítés" és az "adat pontszáma" fejléc alatti kódot a `run` függvénybe.

    A `run` függvénynek a következő kódhoz hasonlóan kell kinéznie (ne felejtse el eltávolítani a változókat a `raw_data` és a `request_headers`változóknál, amelyeket később a `run` függvény hívásakor fog használni):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Miután létrehozta az `run` függvényt, cserélje le az "adatelőkészítés" és az "adat pontszáma" fejléc alatti kódot az alábbi kódra:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
Az előző kód beállítja a változókat `raw_data` és `request_header`, meghívja az `run` függvényt `raw_data` és `request_header`, és kinyomtatja az előrejelzéseket.

Az újrabontás után a `experimentation/Diabetes Ridge Regression Scoring.ipynb` a következő kódhoz hasonlóan kell kinéznie a Markdown nélkül:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Kapcsolódó függvények kombinálása Python-fájlokban
A harmadik, kapcsolódó függvényeket a Python-fájlokba kell egyesíteni a kód újrafelhasználásának jobb elősegítése érdekében. Ebben a szakaszban Python-fájlokat fog létrehozni a következő jegyzetfüzetekhez:
- A diabetes Ridge regressziós betanítása notebook (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- A diabetes Ridge regressziós pontozási notebookja (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Python-fájl létrehozása a diabetes Ridge regressziós betanítási jegyzetfüzethez
Alakítsa át a jegyzetfüzetet egy végrehajtható parancsfájlba úgy, hogy futtatja a következő utasítást egy parancssorban, amely a nbconvert csomagot és a `experimentation/Diabetes Ridge Regression Training.ipynb`elérési útját használja:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Miután a jegyzetfüzetet átalakította `train.py`re, távolítsa el az összes megjegyzést. A `train.py` fájlnak a következő kódhoz hasonlóan kell kinéznie:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

A MLOpsPython-tárház `diabetes_regression/training` könyvtárában található `train.py` fájl támogatja a parancssori argumentumokat (azaz `build_id`, `model_name`és `alpha`). Parancssori argumentumok támogatása a `train.py`-fájlhoz a dinamikus modellek nevének és `alpha` értékének támogatásához, de a kód sikeres végrehajtásához nem szükséges.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-fájl létrehozása a diabetes Ridge regressziós pontozási jegyzetfüzethez
A jegyzetfüzetet egy végrehajtható parancsfájlba úgy állítsa be, hogy a következő utasítást futtatja egy parancssorban, amely a nbconvert csomagot és a `experimentation/Diabetes Ridge Regression Scoring.ipynb`elérési útját használja:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Miután a jegyzetfüzetet átalakította `score.py`re, távolítsa el az összes megjegyzést. A `score.py` fájlnak a következő kódhoz hasonlóan kell kinéznie:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

A `train_model` függvénynek módosítást kell létrehoznia egy globális változó modell létrehozásához, hogy az a parancsfájlban látható legyen. Adja hozzá a következő utasítást a `init` függvény elején:

```python
global model
```

Az előző utasítás hozzáadása után a `init` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Egységbeli tesztek létrehozása minden Python-fájlhoz
Negyedszer minden Python-fájlhoz létre kell hozni az Unit-teszteket, ami megbízhatóbb és könnyebben karbantartható kódot tesz lehetővé. Ebben a szakaszban a `train.py`egyik funkciójának egy egységének tesztelését fogja létrehozni.

`train.py` két függvényt tartalmaz: `train_model` és `main`. Minden függvénynek szüksége van egy egység tesztre, de csak egyetlen egység tesztet hozunk létre a `train_model` függvényhez az oktatóanyag Pytest keretrendszere használatával.  A Pytest nem az egyetlen Python-egység tesztelési keretrendszere, de az egyik leggyakrabban használt. További információért látogasson el a [Pytest](https://pytest.org)webhelyre.

Az egységek tesztelése általában három fő műveletet tartalmaz:
- Objektum elrendezése – szükséges objektumok létrehozása és beállítása
- Objektum megvonása
- A várt érték érvényesítése

A `train_model` általános feltétele `data` és `alpha` érték átadása. A várt eredmény az `Ridge.train` és `Ridge.predict` függvények hívása. Mivel a gépi tanulási tanítási módszerek gyakran nem gyorsan futnak, a `Ridge.train` meghívása lesz kigúnyolva. Mivel a `Ridge.train` visszatérési értéke egy kigúnyolt objektum, `Ridge.predict`is kigúnyoljuk. A `train_model` teszteléséhez `data` és `alpha` érték elküldésének tesztelése a `Ridge.train` és `Ridge.predict` függvények elvárt eredményével, és a Pytest-keretrendszernek a következő kódhoz hasonlóan kell kinéznie:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Saját modell használata a MLOpsPython-kód sablonnal
Ha követte az útmutató lépéseit, a MLOpsPython-tárházban elérhető, a vonat/pontszám/tesztelési szkriptekhez kapcsolódó parancsfájlok is rendelkezésre állnak.  A fent említett struktúra alapján a következő lépések végigvezetik a saját gépi tanulási projekthez szükséges fájlok használatának lépésein:  

1.  Kövesse a Első lépések útmutatót
2.  A betanítási kód cseréje
3.  A pontszám kód cseréje
4.  A kiértékelési kód frissítése

### <a name="follow-the-getting-started-guide"></a>Kövesse a Első lépések útmutatót
Az első lépéseket ismertető útmutatóban a MLOpsPython végrehajtásához szükséges támogató infrastruktúrát és folyamatokat kell végrehajtania.  Azt javasoljuk, hogy a MLOpsPython-kód a-ként való üzembe helyezése előtt a saját kód megtartásával győződjön meg arról, hogy a szerkezet és a folyamat megfelelően működik.  Az is hasznos, hogy megismerkedjen az adattár kódjának struktúrájával.

### <a name="replace-training-code"></a>Betanítási kód cseréje
A modell betanításához használt kód cseréje, valamint a megfelelő egység tesztek eltávolítása vagy cseréje szükséges ahhoz, hogy a megoldás működjön a saját kódjával.  Kövesse az alábbi lépéseket kifejezetten:

1. Cserélje le a `diabetes_regression\training\train.py`. Ez a szkript helyileg vagy az Azure ML számítási feladatokon is betanítja a modellt.
1. A `tests/unit/code_test.py`ban található képzési egységhez tartozó tesztek eltávolítása vagy cseréje

### <a name="replace-score-code"></a>Pontszám kód cseréje
Ahhoz, hogy a modell valós idejű következtetési képességeket nyújtson, le kell cserélni a pontszám kódját. A MLOpsPython sablon a pontszám kód használatával helyezi üzembe a modellt, hogy valós idejű pontozást végezzen az ACI, az AK vagy a Web Apps szolgáltatásban.  Ha meg szeretné tartani a pontozást, cserélje le `diabetes_regression/scoring/score.py`.

### <a name="update-evaluation-code"></a>Értékelési kód frissítése
A MLOpsPython sablon a evaluate_model szkripttel hasonlítja össze az újonnan betanított modell teljesítményét, valamint az aktuális üzemi modellt az átlagos négyzetes hiba alapján. Ha az újonnan betanított modell teljesítménye jobb, mint az aktuális üzemi modell, akkor a folyamatok folytatódnak. Ellenkező esetben a rendszer leállítja a folyamatokat. Az értékelés megtartásához cserélje le a `mse` összes példányát `diabetes_regression/evaluate/evaluate_model.py` a kívánt metrikára. 

A kiértékelés megszerzéséhez állítsa a DevOps pipeline változót `RUN_EVALUATION` `.pipelines\diabetes_regression-variables` a `false`re.

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette, hogyan alakíthat át kísérletből termelési kódra, az alábbi hivatkozások segítségével megtudhatja, hogyan figyelheti a kísérlet futtatását és a webszolgáltatásként telepített modelleket:

> [!div class="nextstepaction"]
> Az [Azure ml-kísérletek futtatásának és metrikáinak monitorozása](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [a ml webszolgáltatás-végpontokról származó adatok figyelése és gyűjtése](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
