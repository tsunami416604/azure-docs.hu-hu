---
title: ML modell üzembe helyezése az Azure SQL Edge-ben a ONNX használatával
description: A három részből álló Azure SQL Edge oktatóanyag harmadik része, amely az Iron Ore-szennyeződések előrejelzését ismerteti, a ONNX Machine learning-modelleket futtathatja az SQL Edge-ben.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f38a973611cb1ab18eead4ec51e6be91ada2cc40
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318641"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ML modell üzembe helyezése az Azure SQL Edge-ben a ONNX használatával 

A három részből álló, az Azure SQL Edge-beli Iron Ore-szennyeződések előrejelzésére szolgáló oktatóanyag harmadik részében a következőkre lesz szüksége:

1. Az Azure SQL Edge-példányban SQL Databasehoz való kapcsolódáshoz használja a Azure Data Studio.
2. Az Iron Ore-szennyeződések előrejelzése az ONNX az Azure SQL Edge-ben.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Kapcsolódás a SQL Databasehoz az Azure SQl Edge-példányban

1. Nyissa meg az Azure Data Studiót.

2. Az **üdvözlő** lapon kezdjen el egy új kapcsolattal a következő részletekkel:

   |_Mező_|_Érték_|
   |-------|-------|
   |Kapcsolat típusa| Microsoft SQL Server|
   |Kiszolgáló|A bemutatóhoz létrehozott virtuális gépen említett nyilvános IP-cím|
   |Felhasználónév|sa|
   |Jelszó|Az Azure SQL Edge-példány létrehozásakor használt erős jelszó|
   |Adatbázis|Alapértelmezett|
   |Kiszolgálócsoport|Alapértelmezett|
   |Név (nem kötelező)|Adjon meg egy opcionális nevet|

3. Kattintson a **kapcsolat** gombra.

4. A **fájl** szakaszban nyisson meg egy új jegyzetfüzetet, vagy használja a billentyűparancsot Alt + Windows + N. 

5. Állítsa a rendszermagot a Python 3 értékre.

## <a name="predict-iron-ore-impurities-with-onnx"></a>Iron Ore-szennyeződések előrejelzése a ONNX

Adja meg a következő Python-kódot a Azure Data Studio jegyzetfüzetben, és futtassa.

1. Először telepítse és importálja a szükséges csomagokat.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. Adja meg az Azure AutoML-munkaterületet és a AutoML-kísérlet konfigurációját a regressziós kísérlethez.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. Importálja az adatkészletet egy Panda-keretbe. A modell betanításához használja a betanítási adatkészletek minőségi előrejelzését a Kaggle-ből származó [adatbányászati folyamatban](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) . Töltse le az adatfájlt, és mentse helyileg a fejlesztői gépen. Ezeknek az információknak a segítségével megjósolhatja, hogy mekkora a szennyeződés az érc-koncentrátumban.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. Elemezheti az adatelemzést, hogy azonosítsa a torzítást. A folyamat során tekintse meg az adatkeretben található egyes oszlopok eloszlását és az elferdítés információit.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. Vizsgálja meg és javítsa ki az adattorzítás szintjét.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. Az előrejelzési funkcióval más funkciók korrelációját is megvizsgálhatja. Ha a korreláció nem magas, távolítsa el ezeket a funkciókat.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. Indítsa el a AzureML kísérletet a legjobb algoritmus megkereséséhez és betanításához. Ebben az esetben az összes regressziós algoritmust teszteli, és a normalizált legfelső szintű, négyzetes hibával (NRMSE) rendelkező elsődleges metrikával dolgozik. További információt az [Azure ml-kísérletek elsődleges metrikája](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric)című témakörben talál. A következő kód az ML kísérlet helyi futtatását indítja el.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. Töltse be a modellt az Azure SQL Edge-adatbázisban a helyi pontozáshoz.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. Végül az Azure SQL Edge-modell használatával a betanított modellel elvégezheti az előrejelzéseket.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. A Python használatával hozzon létre egy diagramot a prediktív szilícium-százalékos arányban az Iron-hírcsatornán, a DateTime-on és a szilícium-hírcsatornán.

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>További lépések

További információ a ONNX-modellek Azure SQL Edge-ben történő használatáról: [Machine learning és AI with ONNX in SQL Edge (előzetes verzió)](onnx-overview.md).
