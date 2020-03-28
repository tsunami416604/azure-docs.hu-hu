---
title: Gépi tanulási kísérlet kódjának átalakítása éles kóddá
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan konvertálhatja a gépi tanulási kísérleti kódot éles kódtá az MLOpsPython kódsablon használatával.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477136"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Oktatóanyag: Ml kísérleti kód átalakítása éles kódra

Egy gépi tanulási projekt kísérletezést igényel, ahol a hipotéziseket olyan agilis eszközökkel tesztelik, mint a Jupyter Notebook valós adatkészletek használatával. Miután a modell készen áll az éles használatra, a modellkódot egy termelésikód-tárházba kell helyezni. Bizonyos esetekben a modellkódot python-parancsfájlokká kell konvertálni, hogy a termelésikód-tárházban legyenek elhelyezve. Ez az oktatóanyag ismerteti a kísérletezési kód Python-parancsfájlokba történő exportálásának ajánlott megközelítését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Nem alapvető fontosságú kód tisztítása
> * Refactor Jupyter Notebook kód függvények
> * Python-parancsfájlok létrehozása kapcsolódó feladatokhoz
> * Egységtesztek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- Hozza létre az [MLOpsPython sablont,](https://github.com/microsoft/MLOpsPython/generate) és használja a és `experimentation/Diabetes Ridge Regression Training.ipynb` `experimentation/Diabetes Ridge Regression Scoring.ipynb` a notebookokat. Ezek a jegyzetfüzetek példaként szolgálnak a kísérletezésből az éles környezetbe való konvertáláshoz. Ezeket a jegyzetfüzeteket [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)a.
- Telepítse nbconvert. Csak a telepítési utasításokat kövesse __az Nbconvert telepítése__ a [telepítési](https://nbconvert.readthedocs.io/en/latest/install.html) oldalon című szakaszban.

## <a name="remove-all-nonessential-code"></a>Az összes nem alapvető fontosságú kód eltávolítása

A kísérletezés során írt egyes kódok csak feltáró célokra szolgálnak. Ezért a kísérleti kód éles kóddá történő átalakításának első lépése a nem alapvető fontosságú kód eltávolítása. A nem alapvető fontosságú kód eltávolítása a kód karbantartását is lehetővé teszi. Ebben a szakaszban eltávolíthatja a `experimentation/Diabetes Ridge Regression Training.ipynb` kódot a jegyzetfüzetből. Az alakot `X` és `y` a cellahívást `features.describe` kinyomtató utasítások csak az adatok feltárására szolgálnak, és eltávolíthatók. A nem létfontosságú kód `experimentation/Diabetes Ridge Regression Training.ipynb` eltávolítása után a következő kódnak kell kinéznie jelölés nélkül:

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

## <a name="refactor-code-into-functions"></a>Újrafaktoroló kód függvényekké

Másodszor, a Jupyter-kódot függvényekké kell alakítani. A kód függvényekbe való újrabontása megkönnyíti az egységtesztelést, és karbantarthatóbbá teszi a kódot. Ebben a részben a következőket kell figyelembe venni:

- A Diabetes Ridge regressziós képzés notebook(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- A Diabetes Ridge regresszió pontozási notebook(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge regressziós képzés notebook funkciók

A `experimentation/Diabetes Ridge Regression Training.ipynb`alkalmazásban hajtsa végre a következő lépéseket:

1. Hozzon létre `train_model`egy függvényt, `data` `alpha` amely a paramétereket veszi, és egy modellt ad vissza.
1. Másolja a kódot a "Train Model on Training Set" és a `train_model` "Validate Model on Validation Set" fejlécalá.

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

A `train_model` függvény létrehozása után cserélje le a "Train Model on Training Set" és a "Validate Model on Validation Set" fejlécek alatti kódot a következő utasításra:

```python
reg = train_model(data, alpha)
```

Az előző utasítás `train_model` meghívja `data` `alpha` a függvény t és paramétereket, és adja vissza a modellt.

A `experimentation/Diabetes Ridge Regression Training.ipynb`alkalmazásban hajtsa végre a következő lépéseket:

1. Hozzon létre `main`egy új függvényt, amely nem vesz igénybe paramétereket, és nem ad vissza semmit.
1. Másolja a kódot a "Adatok betöltése", "Adatok felosztása betanítási és `main` érvényesítési készletekre" és a "Modell mentése" fejléc alá a függvénybe.
1. Másolja az újonnan `train_model` létrehozott `main` hívást a függvénybe.

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

A `main` függvény létrehozása után cserélje le az összes kódot az "Adatok betöltése", az "Adatok felosztása betanítási `train_model` és érvényesítési készletekre" és a "Modell mentése" címszó alatt, valamint az újonnan létrehozott hívást a következő utasítással:

```python
main()
```

A refactoring `experimentation/Diabetes Ridge Regression Training.ipynb` után a következő kódnak kell kinéznie a jelnélkül:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refactor Diabetes Ridge regresszió pontozási notebook funkciók

A `experimentation/Diabetes Ridge Regression Scoring.ipynb`alkalmazásban hajtsa végre a következő lépéseket:

1. Hozzon létre `init`egy új függvényt, amely nem vesz igénybe paramétereket, és nem ad vissza semmit.
1. Másolja a kódot a "Modell betöltése" fejléc alá a `init` függvénybe.

A `init` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

A `init` funkció létrehozása után cserélje le a "Modell betöltése" alatt `init` lévő összes kódot egyetlen hívásra az alábbiak szerint:

```python
init()
```

A `experimentation/Diabetes Ridge Regression Scoring.ipynb`alkalmazásban hajtsa végre a következő lépéseket:

1. Hozzon létre `run`egy új `raw_data` `request_headers` függvényt, amely paraméterekként és paraméterekként az eredmények szótárát adja vissza az alábbiak szerint:

    ```python
    {"result": result.tolist()}
    ```

1. Másolja a kódot az "Adatok előkészítése" és a `run` "Pontszámadatok" fejléc alá a függvénybe.

    A `run` függvénynek a következő kódhoz hasonlóan kell kinéznie (Ne `request_headers`felejtse el eltávolítani `run` a változókat beállító állításokat, `raw_data` és a , amelyet később fog használni, amikor a függvényt hívják):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

A `run` függvény létrehozása után cserélje le az "Adatok előkészítése" és a "Pontszámadatok" fejlécben lévő összes kódot a következő kódra:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Az előző kód `raw_data` beállítja `run` a `raw_data` változókat, és `request_header`a függvényt a és `request_header`a lehetőséggel hívja meg, és kinyomtatja az előrejelzéseket.

A refactoring `experimentation/Diabetes Ridge Regression Scoring.ipynb` után a következő kódnak kell kinéznie a jelnélkül:

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

Harmadszor, a kapcsolódó funkciókat össze kell vonni a Python fájlokba, hogy jobban segítse a kód újrafelhasználását. Ebben a szakaszban python-fájlokat fog létrehozni a következő jegyzetfüzetekhez:

- A Diabetes Ridge regressziós képzés notebook(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- A Diabetes Ridge regresszió pontozási notebook(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Python-fájl létrehozása a Diabetes Ridge regressziós képzés jegyzetfüzetéhez

A jegyzetfüzetet végrehajtható parancsfájllá konvertálhatja a parancssorban a következő utasítás futtatásával, amely `experimentation/Diabetes Ridge Regression Training.ipynb`az nbconvert csomagot és az elérési utat használja:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Miután a jegyzetfüzetet `train.py`konvertálta, távolítsa el az összes megjegyzést. A `train.py` fájlnak a következő kódnak kell lennie:

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

Az `train.py` MLOpsPython `diabetes_regression/training` tárházban található könyvtárban található fájl támogatja a parancssori argumentumokat (nevezetesen `build_id`a `model_name`, és `alpha`a ) A dinamikus modellnevek és `train.py` `alpha` -értékek támogatásához a parancssori argumentumok támogatása hozzáadható a fájlhoz, de a kód sikeres végrehajtásához nem szükséges.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-fájl létrehozása a Diabetes Ridge regressziós pontozási jegyzetfüzethez

A jegyzetfüzetet egy végrehajtható parancsfájlba rejtette, ha a következő utasítást futtatja `experimentation/Diabetes Ridge Regression Scoring.ipynb`egy parancssorban, amely az nbconvert csomagot és az elérési utat használja:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Miután a jegyzetfüzetet `score.py`konvertálta, távolítsa el az összes megjegyzést. A `score.py` fájlnak a következő kódnak kell lennie:

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

A `train_model` függvény módosításra van szükség, hogy példányosítani egy globális változó modell, hogy látható legyen az egész parancsfájlt. Adja hozzá a következő utasítást a `init` függvény elejére:

```python
global model
```

Az előző utasítás hozzáadása `init` után a függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Egységtesztek létrehozása minden Python-fájlhoz

Negyedszer, egységteszteket kell létrehozni minden Python-fájlhoz, ami robusztusabbá és könnyebben karbantarthatóvá teszi a kódot. Ebben a szakaszban `train.py`egységtesztet fog létrehozni a függvények egyikéhez.

`train.py`két függvényt `train_model` `main`tartalmaz: és . Minden függvénynek szüksége van egy egységtesztre, de csak `train_model` egy egységtesztet hozunk létre a függvényhez az oktatóanyag Pytest keretrendszerhasználatával. Pytest nem az egyetlen Python egység tesztelési keretrendszer, de ez az egyik leggyakrabban használt. További információkért látogasson el [Pytest](https://pytest.org).

Az egységvizsgálat általában három fő műveletet tartalmaz:

- Objektum rendezése - szükséges objektumok létrehozása és beállítása
- Cselekvés egy objektumra
- A várt érték érvényesítése

Az `alpha` érték `train_model` átadása `data` gyakran megszokott feltétele. A várt eredmény `Ridge.train` az, hogy a és `Ridge.predict` a függvényeket meg kell hívni. Mivel a gépi tanulási képzési módszerek gyakran `Ridge.train` nem gyorsan futnak, a hívás mocked. Mivel a visszatérési `Ridge.train` értéke egy kigúnyolt tárgy, `Ridge.predict`mi is gúnyolódni . A gúnyos `train_model` és a `data` Pytest `alpha` keretrendszer által `Ridge.train` várt `Ridge.predict` eredményés függvények várható eredményével és funkcióival végzett érték ellenőrzésére szolgáló egységvizsgálatnak a következő kódhoz hasonlóan kell kinéznie:

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

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Saját modell használata mlOpsPython kódsablonnal

Ha követte az útmutatóban leírt lépéseket, parancsfájlok készletével fog rendelkezni, amelyek korrelálnak az MLOpsPython-tárházban elérhető train/score/test parancsfájlokkal.  A fent említett struktúra szerint a következő lépések végigvezetik a fájlok saját gépi tanulási projektjéhez szükséges lépéseken:

1. Kövesse az MLOpsPython [első lépései](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) című útmutatóját
2. A projekt kiindulópontjának létrehozásához kövesse az MLOpsPython [rendszerindítási utasításokat](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md)
3. A képzési kód cseréje
4. A pontszámkód cseréje
5. Az értékelési kód frissítése

### <a name="follow-the-getting-started-guide"></a>Kövesse az Első lépések útmutatót
Az [Első lépések](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) útmutatót követve szükség van a támogató infrastruktúra és a folyamatok végrehajtásához MLOpsPython.

### <a name="follow-the-bootstrap-instructions"></a>Kövesse a Bootstrap utasításokat

A [Bootstrap az MLOpsPython tárház](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) útmutató segít gyorsan előkészíti a tárház a projekt.

**Megjegyzés:** Mivel a rendszerindítási parancsfájl átnevezi a diabetes_regression mappát az Ön által választott `[project name]` projektnévre, a projektre úgy hivatkozunk, mint amikor elérési utakról van szó.

### <a name="replace-training-code"></a>Képzési kód cseréje

A modell betanításához használt kód cseréje, valamint a megfelelő egységtesztek eltávolítása vagy cseréje szükséges ahhoz, hogy a megoldás a saját kódjával működjön. Kövesse az alábbi lépéseket kifejezetten:

1. Csere `[project name]/training/train.py`. Ez a parancsfájl helyileg vagy az Azure ML-számításra bevonata a modellt.
1. Távolítsa el vagy cserélje ki a képzési egység tesztjeit, amelyek`[project name]/training/test_train.py`

### <a name="replace-score-code"></a>Pontszámkód cseréje

Ahhoz, hogy a modell valós idejű következtetési képességeket biztosítson, a pontszámkódot le kell cserélni. Az MLOpsPython-sablon a pontszámkódot használja a modell üzembe helyezéséhez az ACI, az AKS vagy a webalkalmazások valós idejű pontozásához. Ha továbbra is gólt szeretne `[project name]/scoring/score.py`szerezni, cserélje ki a .

### <a name="update-evaluation-code"></a>Értékelési kód frissítése

Az MLOpsPython sablon a evaluate_model parancsfájl t használja az újonnan betanított modell és az aktuális termelési modell teljesítményének összehasonlításához a Mean Squared Error alapján. Ha az újonnan betanított modell teljesítménye jobb, mint az aktuális termelési modell, majd a folyamatok folytatódnak. Ellenkező esetben a folyamatok megszakadnak. A kiértékelés megtartásához cserélje `mse` `[project name]/evaluate/evaluate_model.py` le az összes példányt a kívánt metrikával.

A kiértékelés megszüntetéséhez állítsa a `RUN_EVALUATION` DevOps-folyamat `.pipelines/[project name]-variables-template.yml` változót a értékre. `false`

## <a name="next-steps"></a>További lépések

Most, hogy már tisztában van azzal, hogyan konvertálhat kísérletből éles kódra, az alábbi hivatkozások segítségével megtudhatja, hogyan figyelheti a kísérletfuttatásokat és a webszolgáltatásként telepített modelleket:

> [!div class="nextstepaction"]
> [Az Azure ML-kísérletek futásának és metrikáinak](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [figyelése és az ML webszolgáltatás végpontjaiból származó adatok gyűjtése figyelése](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
