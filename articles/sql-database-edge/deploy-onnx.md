---
title: Az ONNX telepítése és előrejelzései az SQL Database Edge előzetes verzióban
description: Ismerje meg, hogyan taníthatja be a modellt, konvertálhatja AZT ONNX-re, telepítheti azt az Azure SQL Database Edge Preview-ba, majd futtathatja a natív PREDICT-t a feltöltött ONNX-modell használatával.
keywords: sql adatbázis peremének telepítése
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: aff9346595d3b8985d3558658af32d05f88c0554
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365444"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-in-sql-database-edge-preview"></a>AZ ONNX-modell telepítésével és előrejelzésével az SQL Database Edge előzetes verziójában

Ebben a rövid útmutatóban megtudhatja, hogyan taníthatja be a modellt, konvertálhatja AZT ONNX-re, hogyan telepítheti az Azure SQL Database Edge Preview-ba, majd futtathatja a natív PREDICT-t a feltöltött ONNX-modell használatával. További információ: [Machine learning and AI with ONNX in SQL Database Edge Preview](onnx-overview.md).

Ez a rövid útmutató a **scikit-learn-en** alapul, és a [Boston Housing adatkészletet](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)használja.

## <a name="before-you-begin"></a>Előkészületek

* Ha még nem telepítette az Azure SQL Database Edge modult, kövesse az [SQL Database Edge előzetes verziójának üzembe helyezésének](deploy-portal.md)lépéseit az Azure Portalon.

* Telepítse [az Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download)alkalmazást.

* Nyissa meg az Azure Data Stu-t, és kövesse az alábbi lépéseket a rövid útmutatóhoz szükséges csomagok telepítéséhez:

    1. Nyissa meg a Python 3 kernelhez csatlakoztatott [új jegyzetfüzetet.](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks) 
    1. Kattintson **a Csomagok kezelése elemre,** és az Új **hozzáadása**csoportban keresse meg a **scikit-learn (Scikit-learn) (Scikit-learn)**(A csomagok kezelése) területen keresse meg a scikit-learn (Scikit-learn) menüt. 
    1. Telepítse a **setuptools**, **numpy**, **onnxmltools**, **onnxruntime**, **skl2onnx**, **pyodbc**és **sqlalchemy** csomagokat is.
    
* Az alábbi parancsfájlok egyes részeihez írja be azt az Azure Data Studio-jegyzetfüzet egyik cellájába, és futtassa a cellát.

## <a name="train-a-pipeline"></a>Csővezeték betanítása

Az adatkészlet felosztása a szolgáltatások segítségével előre jelzi a ház mediánértékét.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])

# x contains all predictors (features)
x = df.drop(['MEDV'], axis = 1)

# y is what we are trying to predict - the median value
y = df.iloc[:,-1]

# Split the data frame into features and target
x_train = df.drop(['MEDV'], axis = 1)
y_train = df.iloc[:,-1]

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Kimenet**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Hozzon létre egy folyamatot a LinearRegression modell betanításához. Más regressziós modelleket is használhat.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Ellenőrizze a modell pontosságát, majd számítsa ki az R2 pontszámot és az átlagos négyzethibát.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Kimenet**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>A modell konvertálása ONNX-re

Konvertálja az adattípusokat a támogatott SQL-adattípusokra. Erre az átalakításra más adatkeretek esetében is szükség lesz.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

A `skl2onnx`használatával konvertálja a LinearRegression modellt ONNX formátumra, és mentse helyileg.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>Tesztelje az ONNX modellt

Miután a modellt ONNX formátumra konvertálta, pontszerezze a modellt, hogy a teljesítmény alig vagy egyáltalán ne mutasson csökkenést.

> [!NOTE]
> Az ONNX Runtime a páros helyett úszókat használ, így kis eltérések lehetségesek.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Kimenet**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>Az ONNX modell beszúrása

A modell tárolják az Azure `models` SQL Database `onnx`Edge, egy táblában egy adatbázisban. A kapcsolati karakterláncban adja meg a **kiszolgáló címét,** **felhasználónevét**és **jelszavát.**

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Az adatok betöltése

Töltse be az adatokat az Azure SQL Database Edge.Load the data into Azure SQL Database Edge.

Először hozzon létre két táblát, **funkciókat** és **célkészleteket**a bostoni házadatkészlet részhalmazainak tárolására.

* **A szolgáltatások** tartalmazza az összes olyan adatot, amelyet a cél, a medián érték előrejelzésére használnak. 
* **A cél** az adatkészlet egyes rekordjának mediánértékét tartalmazza. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Végül, `sqlalchemy` használja, `x_train` hogy `y_train` helyezze be a `features` és `target`pandák dataframes a táblákba, és , illetve. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

Most megtekintheti az adatokat az adatbázisban.

## <a name="run-predict-using-the-onnx-model"></a>A PREDICT futtatása az ONNX modell használatával

A modell az Azure SQL Database Edge,futtassa natív PREDICT az adatok on a feltöltött ONNX-modell használatával.

> [!NOTE]
> Módosítsa a jegyzetfüzetkernelt SQL-re a fennmaradó cella futtatásához.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [onnx].[dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input) WITH (variable1 FLOAT) AS p
```

## <a name="next-steps"></a>Következő lépések

* [Machine Learning és AI ONNX-szel az SQL Database Edge-ben](onnx-overview.md)
