---
title: Gépi tanulási kísérlet kódjának átalakítása éles kóddá
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan konvertálhatja a gépi tanulási kísérleti kódot éles kódtá az MLOpsPython kódsablon használatával.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: e3c9b16ae3d2b06ec19ecd29d15762a065c0c1ae
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521441"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Oktatóanyag: Ml kísérleti kód átalakítása éles kódra

Egy gépi tanulási projekt kísérletezést igényel, ahol a hipotéziseket olyan agilis eszközökkel tesztelik, mint a Jupyter Notebook valós adatkészletek használatával. Miután a modell készen áll az éles használatra, a modellkódot egy termelésikód-tárházba kell helyezni. Bizonyos esetekben a modellkódot python-parancsfájlokká kell konvertálni, hogy a termelésikód-tárházban legyenek elhelyezve. Ez az oktatóanyag ismerteti a kísérletezési kód Python-parancsfájlokba történő exportálásának ajánlott megközelítését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Nem alapvető fontosságú kód tisztítása
> * Refactor Jupyter Notebook kód függvények
> * Python-parancsfájlok létrehozása kapcsolódó feladatokhoz
> * Egységtesztek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- Hozza létre az [MLOpsPython sablont,](https://github.com/microsoft/MLOpsPython/generate) és használja a és `experimentation/Diabetes Ridge Regression Training.ipynb` `experimentation/Diabetes Ridge Regression Scoring.ipynb` a notebookokat. Ezek a jegyzetfüzetek példaként szolgálnak a kísérletezésből az éles környezetbe való konvertáláshoz. Ezeket a jegyzetfüzeteket [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)a.
- Telepítse a(z) `nbconvert` rendszert. Csak a telepítési utasításokat kövesse __az Nbconvert telepítése__ a [telepítési](https://nbconvert.readthedocs.io/en/latest/install.html) oldalon című szakaszban.

## <a name="remove-all-nonessential-code"></a>Az összes nem alapvető fontosságú kód eltávolítása

A kísérletezés során írt egyes kódok csak feltáró célokra szolgálnak. Ezért a kísérleti kód éles kóddá történő átalakításának első lépése a nem alapvető fontosságú kód eltávolítása. A nem alapvető fontosságú kód eltávolítása a kód karbantartását is lehetővé teszi. Ebben a szakaszban eltávolíthatja a `experimentation/Diabetes Ridge Regression Training.ipynb` kódot a jegyzetfüzetből. Az alakot `X` és `y` a cellahívást `features.describe` kinyomtató utasítások csak az adatok feltárására szolgálnak, és eltávolíthatók. A nem létfontosságú kód `experimentation/Diabetes Ridge Regression Training.ipynb` eltávolítása után a következő kódnak kell kinéznie jelölés nélkül:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Újrafaktoroló kód függvényekké

Másodszor, a Jupyter-kódot függvényekké kell alakítani. A kód függvényekbe való újrabontása megkönnyíti az egységtesztelést, és karbantarthatóbbá teszi a kódot. Ebben a részben a következőket kell figyelembe venni:

- A Diabetes Ridge regressziós képzés notebook(`experimentation/Diabetes Ridge Regression Training.ipynb`)
- A Diabetes Ridge regresszió pontozási notebook(`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refactor Diabetes Ridge regressziós képzés notebook funkciók

A `experimentation/Diabetes Ridge Regression Training.ipynb`alkalmazásban hajtsa végre a következő lépéseket:

1. Hozzon létre `split_data` egy függvényt, amelynek célja az adatkeret teszt- és betanítási adatokra való felosztása. A függvénynek paraméterként `df` kell tekintenie az adatkeretet, `train` és `test`vissza kell adnia egy szótárat, amely tartalmazza a kulcsokat és a .

    Helyezze át a kódot az *Adatok felosztása betanítási és érvényesítési készletekfejléc* alá a `split_data` függvénybe, és módosítsa az `data` objektum visszaadásához.

1. Hozzon létre `train_model`egy függvényt, `data` `args` amely a paramétereket veszi, és egy betanított modellt ad vissza.

    Helyezze át a kódot a *betanítási modell betanítási modell* címalatt a `train_model` függvénybe, és módosítsa az `reg_model` objektum visszaadásához. Távolítsa `args` el a szótárt, az `args` értékek a paraméterből származnak.

1. Hozzon létre `get_model_metrics`egy függvényt, amely paramétereket `reg_model` vesz fel, és `data`kiértékeli a modellt, majd visszaadja a betanított modell metrikáinak szótárát.

    Helyezze át a kódot az *Érvényesítési modell érvényesítési modell* fejléce alá, `get_model_metrics` és módosítsa az `metrics` objektum visszaadásához.

A három funkció nak a következőnek kell lennie:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Még `experimentation/Diabetes Ridge Regression Training.ipynb`mindig a , hajtsa végre a következő lépéseket:

1. Hozzon létre `main`egy új függvényt, amely nem vesz igénybe paramétereket, és nem ad vissza semmit.
1. Helyezze át a kódot az "Adatok `main` betöltése" fejléc alatt a függvénybe.
1. Az újonnan írt függvények meghívásainak hozzáadása a `main` függvényhez:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Helyezze át a kódot a "Modell `main` mentése" fejléc alatt a függvénybe.

A `main` függvénynek a következő kódhoz hasonlóan kell kinéznie:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Ebben a szakaszban nem lehet olyan kód a jegyzetfüzetben, amely nem egy függvényben van, kivéve az importálási kimutatásokat az első cellában.

Adjon hozzá egy `main` utasítást, amely meghívja a függvényt.

```python
main()
```

A refactoring `experimentation/Diabetes Ridge Regression Training.ipynb` után a következő kódnak kell kinéznie a jelnélkül:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

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

A jegyzetfüzetet végrehajtható parancsfájllá alakíthatja a következő utasítás futtatásával `nbconvert` a parancssorban, amely a csomagot és az elérési utat `experimentation/Diabetes Ridge Regression Training.ipynb`használja:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Miután a jegyzetfüzetet `train.py`konvertálta, távolítsa el a nem kívánt megjegyzéseket. Cserélje le `main()` a fájl végén lévő hívást a következő kódhoz hasonló feltételes meghívásra:

```python
if __name__ == '__main__':
    main()
```

A `train.py` fájlnak a következő kódnak kell lennie:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py`most már meglehet hívni egy `python train.py`terminálfutással.
A függvények más fájlokból `train.py` is meghívhatók.

Az `train_aml.py` MLOpsPython-tárházban található `diabetes_regression/training` könyvtárban található fájl meghívja az Azure Machine Learning-kísérlet futtatása környezetében `train.py` definiált függvényeket. A funkciók is lehet hívni az egység vizsgálatok, amely a későbbiekben ebben az útmutatóban.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Python-fájl létrehozása a Diabetes Ridge regressziós pontozási jegyzetfüzethez

A jegyzetfüzetet egy végrehajtható parancsfájlba rejtette, ha a következő `nbconvert` utasítást futtatja `experimentation/Diabetes Ridge Regression Scoring.ipynb`egy parancssorban, amely a csomagot és a következő elérési útját használja:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Miután a jegyzetfüzetet `score.py`konvertálta, távolítsa el a nem kívánt megjegyzéseket. A `score.py` fájlnak a következő kódnak kell lennie:

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

A `model` változónak globálisnak kell lennie, hogy az végiglátható legyen a parancsfájlban. Adja hozzá a következő utasítást a `init` függvény elejére:

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

Negyedszer hozzon létre egységteszteket a Python-függvényekhez. Az egységtesztek védik a kódot a funkcionális regressziók ellen, és megkönnyítik a karbantartást. Ebben a szakaszban `train.py`egységteszteket fog létrehozni a függvényekhez.

`train.py`több függvényt tartalmaz, de csak egy egységtesztet hozunk létre a `train_model` függvényhez az oktatóanyag Pytest keretrendszerének használatával. Pytest nem az egyetlen Python egység tesztelési keretrendszer, de ez az egyik leggyakrabban használt. További információkért látogasson el [Pytest](https://pytest.org).

Az egységvizsgálat általában három fő műveletet tartalmaz:

- Objektum rendezése - szükséges objektumok létrehozása és beállítása
- Cselekvés egy objektumra
- A várt érték érvényesítése

Az egységteszt `train_model` néhány kódolt adattal és argumentummal hív `train_model` meg, és ellenőrzi, hogy az eredményül kapott betanított modell használatával az adott betanított modell használatával előrejelzést készítsen, és összehasonlítja az előrejelzést egy várt értékkel.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>További lépések

Most, hogy már tisztában van azzal, hogyan konvertálható kísérletből éles kóddá, további információkért és a következő lépésekért tekintse meg az alábbi hivatkozásokat:

+ [MLOpsPython:](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md)Ci/CD-folyamat létrehozása saját modell betanításához, kiértékeléséhez és üzembe helyezéséhez az Azure Pipelines és az Azure Machine Learning használatával
+ [Az Azure ML-kísérletek futásának és metrikáinak figyelése](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Az ml-es webszolgáltatás végpontjaiból származó adatok figyelése és gyűjtése](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
