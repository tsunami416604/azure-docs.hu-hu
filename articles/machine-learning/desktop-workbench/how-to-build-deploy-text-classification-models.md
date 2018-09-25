---
title: Fejleszthet és telepíthet egy szöveges osztályozási modell a Text Analytics az Azure Machine Learning-csomag használatával.
description: Ismerje meg, hogyan hozhat létre, betanítását, tesztelése és üzembe helyezése egy szöveges osztályozási modell segítségével az Azure Machine Learning csomag szövegelemzési.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 97d988332a2c5234cb260cef29f195f0fecfee45
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994938"
---
# <a name="build-and-deploy-text-classification-models-with-azure-machine-learning"></a>Létrehozása és üzembe helyezése az Azure Machine Learning szövegbesorolási modellek

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Ebben a cikkben megismerheti betanítására és szöveges osztályozási modell üzembe helyezése **Azure Machine Learning csomag szövegelemzési** (AMLPTA). A szöveg besorolási célja, hogy egy adott szöveg hozzárendelése egy vagy több előre definiált osztályok vagy kategóriák. Ez a szöveg, például lehet egy dokumentum, hír, keresési lekérdezés, e-mailt, Twitter-üzenetet, a támogatási jegyeket.

Szöveg besorolású, mint például a széles körű alkalmazások is vannak: 
+ Osztályozóját újság cikkek és hírek átviteli tartalmával kapcsolatos témakörök
+ Hierarchikus kategóriákba weblapok rendszerezéséhez 
+ Levélszemét e-mail szűrése
+ Hangulatelemzés
+ A keresési lekérdezések felhasználói szándékot előrejelzése
+ Útválasztási támogatási jegyek
+ Ügyfeleink visszajelzései elemzése 

A szöveg besorolási modell létrehozásához és üzembe helyezéséhez munkafolyamat AMLPTA modell a következőképpen történik:

1. Az adatok betöltése
2. A modell betanítása
3. A alkalmazni az osztályozó által igénybe vett 
4. Modellek teljesítményének elemzése
5. A folyamat mentése
6. A folyamat tesztelése
8. A modellt üzembe helyezzük webszolgáltatásként

Tekintse át a [csomag dokumentációja](https://aka.ms/aml-packages/text) minden egyes modul és osztály a részletes vonatkozó.

Ebben a cikkben szereplő mintakódban használ, a scikit-ismerje meg a folyamatot.

## <a name="prerequisites"></a>Előfeltételek 

1. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

1. A következő fiókok és az alkalmazás kell hozni és telepítve:
   - Az Azure Machine Learning-Kísérletezési fiók 
   - Az Azure Machine Learning Modellkezelés-fiók
   - Egy telepített Azure Machine Learning Workbenchre

   Ha három vannak létrehozott vagy még nincs telepítve, kövesse a [Azure Machine Learning gyors üzembe helyezés és a Workbench telepítési](../desktop-workbench/quickstart-installation.md) cikk. 

1. Az Azure Machine Learning csomag szövegelemzési telepítve kell lennie. Ismerje meg, hogyan [telepíti ezt a csomagot Itt](https://aka.ms/aml-packages/text).


## <a name="sample-data-and-jupyter-notebook"></a>Mintaadatok és a Jupyter notebook

### <a name="get-the-jupyter-notebook"></a>A Jupyter notebook beszerzése

Próbálja ki. Töltse le a notebookot, és futtathatja saját magának.

> [!div class="nextstepaction"]
> [A Jupyter notebook beszerzése](https://aka.ms/aml-packages/text/notebooks/text_classification_sentiment_data)

### <a name="download-and-explore-the-sample-data"></a>Töltse le, és Fedezze fel a mintaadatokat
Az alábbi példában a [20 hírcsoportok adatkészlet](http://qwone.com/~jason/20Newsgroups/) , a scikit keresztül érhető el – ismerje meg a könyvtár létrehozása az Azure Machine Learning-csomag egy szövegosztályozás Text Analytics bemutatása. 

A 20 hírcsoportok adatkészlet körülbelül 18,000 hírcsoportok bejegyzések rendelkezik 20 különböző témakörök két részekre osztva: egy a képzés és a másik egy teljesítményének értékeléséhez. Minden üzenet utáni dátumot a felosztása tanítási és tesztelési alapul-e előtt vagy után az adott dátumhoz.

```python
# Import Packages 
# Use Azure Machine Learning history magic to control history collection
# History is off by default, options are "on", "off", or "show"
#%azureml history on
%matplotlib inline
# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
import os

logger = get_azureml_logger()

# Log cell runs into run history
logger.log('Cell','Set up run')
# from tatk.utils import load_newsgroups_data, data_dir, dictionaries_dir, models_dir
import pip
pip.main(["show", "azureml-tatk"])
```

### <a name="set-the-location-of-the-data"></a>Az adatok helyének beállítása
Állítsa be a hely, ahol az adatokat a dir parancs paraméter az adatok már letöltötte. Használhatja a saját adataival, a bemeneti adatkészlet *.tsv fájlformátumot kell lennie.

```python
import os
import pandas as pd

#set the working directory where to save the training data files
resources_dir = os.path.join(os.path.expanduser("~"), "tatk", "resources")
data_dir = os.path.join(os.path.expanduser("~"), "tatk", "data")

from sklearn.datasets import fetch_20newsgroups
twenty_train = fetch_20newsgroups(data_home=data_dir, subset='train')
X_train, y_train = twenty_train.data, twenty_train.target
df_train = pd.DataFrame({"text":X_train, "label":y_train})

twenty_test = fetch_20newsgroups(data_home=data_dir, subset='test')
X_test, y_test = twenty_test.data, twenty_test.target   
df_test = pd.DataFrame({"text":X_test, "label":y_test})
    
# Training Dataset Location
#training_file_path = <specify-your-own-training-data-file-path-here>
# df_train = pd.read_csv(training_file_path,
#                        sep = '\t',                        
#                        header = 0, names= <specify-your-column-name-list-here>)
df_train.head()
print("df_train.shape= {}".format(df_train.shape))

# Test Dataset Location
#test_file_path = <specify-your-own-test-data-file-path-here>
# df_test = pd.read_csv(test_file_path,
#                       sep = '\t',                        
#                       header = 0, names= <specify-your-column-name-list-here>)

print("df_test.shape= {}".format(df_test.shape))
df_test.head()
```
    df_train.shape= (11314, 2)
    df_test.shape= (7532, 2)
 
 Felirat- és áll az adatok
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>label</th>
      <th>szöveg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Forrás: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL b...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Forrás: Rick Miller &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Forrás: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Forrás: bakken@cs.arizona.edu (Dave Bakken) \nSub...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Forrás: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
    </tr>
  </tbody>
</table>
</div>

A kategóriák és a nevük közötti becslései beolvasása.

```python
int_to_categories = pd.DataFrame({'category':range(20), 'category_name': list(twenty_train.target_names)})
int_to_categories 
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>category_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>ALT.atheism</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>comp.Graphics</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>comp.os.MS-windows.misc</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>comp.sys.IBM.PC.Hardware</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>comp.sys.MAC.Hardware</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>comp.Windows.x</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Misc.forsale</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>rec.Autos</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>rec.motorcycles</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>rec.sport.Baseball</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>rec.sport.hockey</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>Sci.crypt</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>Sci.Electronics</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>Sci.med</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>Sci.Space</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>SOC.religion.Christian</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>Talk.politics.guns</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>Talk.politics.mideast</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>Talk.politics.Misc</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>Talk.religion.Misc</td>
    </tr>
  </tbody>
</table>
</div>

Most a osztály gyakoriság előzetes feltárás diagram Hisztogram létrehozása az képzés, és adatkészletek teszteléséhez. 

```python
import numpy as np
import math
from matplotlib import pyplot as plt

data = df_train["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('training data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()

data = df_test["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('test data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()
```

A Jupypter notebook futtatásakor grafikon jelennek meg az előző kódblokkot futtatása után.


## <a name="train-the-model"></a>A modell betanítása

### <a name="specify-scikit-learn-algorithm-and-define-the-text-classifier"></a>Adja meg a scikit-ismerje meg az algoritmus, és adja meg a szövegosztályozás

Ennek a lépésnek része a scikit képzés – ismerje meg a szöveg osztályozási modell egy-vagy Rest használatával logisztikai regressziós tanulási algoritmus.

A tapasztalatainkat teljes listájáért tekintse meg a [Scikit tanulók](http://scikit-learn.org/stable/supervised_learning) dokumentációját.

```python
from sklearn.linear_model import LogisticRegression
import tatk
from tatk.pipelines.text_classification.text_classifier import TextClassifier

log_reg_learner =  LogisticRegression(penalty='l2', dual=False, tol=0.0001, 
                            C=1.0, fit_intercept=True, intercept_scaling=1, 
                            class_weight=None, random_state=None, 
                            solver='lbfgs', max_iter=100, multi_class='ovr',
                            verbose=1, warm_start=False, n_jobs=3) 

#train the model a text column "tweets"
text_classifier = TextClassifier(estimator=log_reg_learner, 
                                text_cols = ["text"], 
                                label_cols = ["label"], 
#                                 numeric_cols = None,
#                                 cat_cols = None, 
                                extract_word_ngrams=True, extract_char_ngrams=True)

```

    TextClassifier::create_pipeline ==> start
    :: number of jobs for the pipeline : 12
    0   text_nltk_preprocessor
    1   text_word_ngrams
    2   text_char_ngrams
    3   assembler
    4   learner
    TextClassifier::create_pipeline ==> end
    
### <a name="fit-the-model"></a>A modell igazítása

A csomag alapértelmezett paramétereinek használata. Alapértelmezés szerint a szövegosztályozás kinyeri:
+ A Word unigrams és bigrams
+ 4 g karakter

```python
text_classifier.fit(df_train)        
```
   
    TextClassifier::fit ==> start
    schema: col=label:I4:0 col=text:TX:1 header+
    NltkPreprocessor::tatk_fit_transform ==> start
    NltkPreprocessor::tatk_fit_transform ==> end     Time taken: 0.08 mins
    NGramsVectorizer::tatk_fit_transform ==> startNGramsVectorizer::tatk_fit_transform ==> start
    
                vocabulary size=216393
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.41 mins
                vocabulary size=67230
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.49 mins
    VectorAssembler::transform ==> start, num of input records=11314
    (11314, 216393)
    (11314, 67230)
    all_features::
    (11314, 283623)
    Time taken: 0.06 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_fit ==> start
    
    [Parallel(n_jobs=3)]: Done  20 out of  20 | elapsed:  2.4min finished
    
    LogisticRegression::tatk_fit ==> end     Time taken: 2.4 mins
    Time taken: 3.04 mins
    TextClassifier::fit ==> end

    TextClassifier(add_index_col=False, callable_proprocessors_list=None,
            cat_cols=None, char_hashing_original=False, col_prefix='tmp_00_',
            decompose_n_grams=False, detect_phrases=False,
            dictionary_categories=None, dictionary_file_path=None,
            embedding_file_path=None, embedding_file_path_fastText=None,
            estimator=None, estimator_vectorizers_list=None,
            extract_char_ngrams=True, extract_word_ngrams=True,
            label_cols=['label'], numeric_cols=None,
            pos_tagger_vectorizer=False,
            preprocessor_dictionary_file_path=None, regex_replcaement='',
            replace_regex_pattern=None, scale_numeric_cols=False,
            text_callable_list=None, text_cols=['text'], text_regex_list=None,
            weight_col=None)


Során képzés, szöveg és a felirat oszlopban kell rendelkeznie. Míg csak a szöveges oszlop előrejelzéseket van szükség. 

### <a name="examine-and-set-the-parameters-of-the-different-pipeline-steps"></a>Vizsgálja meg, és állítsa be a paraméterek különböző folyamat lépéseit
    
Általában paramétereinek beállítása, mielőtt Ön egy modellt a következőképpen illeszkedik. 

***Text_word_ngrams mellett látható példa*** 

A következő kódot a minták bemutatják, hogyan folyamat és a modell alapértelmezett paraméterek használatával a modell betanításához. 

Mely paraméterek "text_word_ngrams" megjelenítéséhez használja [get_step_param_names_by_name](https://docs.microsoft.com/python/api/tatk.core.base_text_model.basetextmodel). Ez a függvény visszaad a paraméterek, például kisbetű, input_col, output_col és így tovább. 

```python
text_classifier.get_step_param_names_by_name("text_word_ngrams")
```

    ['min_df',
     'strip_accents',
     'max_df',
     'decode_error',
     'max_features',
     'binary',
     'input',
     'vocabulary',
     'analyzer',
     'token_pattern',
     'encoding',
     'use_idf',
     'save_overwrite',
     'output_col',
     'stop_words',
     'sublinear_tf',
     'input_col',
     'lowercase',
     'ngram_range',
     'preprocessor',
     'tokenizer',
     'hashing',
     'dtype',
     'norm',
     'smooth_idf',
     'n_hashing_features']

Ezután ellenőrizze a "text_char_ngrams" paraméter értékét:

```python
text_classifier.get_step_params_by_name("text_char_ngrams")        
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (4, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

Ha szükséges, módosíthatja az alapértelmezett paramétereket.  A következő kódra módosíthatja a kinyert karakter n-gramokat (4,4) a (3,4) való kinyeréséhez karakter tri – g és a 4 g tartományán:

```python
text_classifier.set_step_params_by_name("text_char_ngrams", ngram_range =(3,4)) 
text_classifier.get_step_params_by_name("text_char_ngrams")
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (3, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

### <a name="export-the-parameters-to-a-file"></a>A paraméterek exportálja egy fájlba
Szükség esetén átdolgozott paraméterekkel rendelkező lépés különbözőek a modell újrafuttatásával modellek teljesítményének optimalizálásához:

```python
import os
params_file_path = os.path.join(data_dir, "params.tsv")
text_classifier.export_params(params_file_path)
```

## <a name="apply-the-classifier"></a>A alkalmazni az osztályozó által igénybe vett

A tesztelési adatkészletnél osztály előrejelzéseket létrehozni a betanított szövegosztályozás telepíteni:

```python
 df_test = text_classifier.predict(df_test)
```

    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.15 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.37 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.46 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>label</th>
      <th>szöveg</th>
      <th>valószínűségek</th>
      <th>Előrejelzés</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>Forrás: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL b...</td>
      <td>[0.0165036341329, 0.0548664746458, 0.020549685...</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5</td>
      <td>Forrás: Rick Miller &lt; rick@ee.uwm.edu &gt;\nSubject:...</td>
      <td>[0.025145498995, 0.125877400021, 0.03947047877...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>Forrás: mathew &lt; mathew@mantis.co.uk &gt;\nSubject: R...</td>
      <td>[0.67566338235, 0.0150749738583, 0.00992439163...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>Forrás: bakken@cs.arizona.edu (Dave Bakken) \nSub...</td>
      <td>[0.146063943868, 0.00232465192179, 0.002442807...</td>
      <td>18</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>Forrás: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
      <td>[0.670712265297, 0.017332269703, 0.01062429663...</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="evaluate-model-performance"></a>Modellek teljesítményének elemzése
A [kiértékelése modul](https://docs.microsoft.com/python/api/tatk.evaluation) kiértékeli a betanított szövegosztályozás a tesztelési adathalmazon pontosságát. A kiértékelés függvényt hoz létre a keveredési mátrix, és a makró-F1 pontszám biztosít.

```python
 text_classifier.evaluate(df_test)          
```

    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> end
    

A félreértések nélkül normalizálási mátrix Vizualizáció ábrázolásához.


```python
evaluator.plot_confusion_matrix(normalize=False,
                                title='Confusion matrix, without normalization', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

Normalizálási nélkül a keveredési mátrix
    
A notebook futtatásakor megjelenik-e a keveredési mátrix



A normalizált keveredési mátrix Vizualizáció ábrázolásához.


```python
evaluator.plot_confusion_matrix(normalize=True,
                                title='Normalized Confusion matrix', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

    Normalized confusion matrix
   
A notebook futtatásakor megjelenik-e a keveredési mátrix

## <a name="save-the-pipeline"></a>A folyamat mentése
A besorolási folyamatok mentse a zip-fájlban. Mentheti a word-ngrams és karakter n-gramokat szöveges fájlok formájában.

```python
import os
working_dir = os.path.join(data_dir, 'outputs')  
if not os.path.exists(working_dir):
    os.makedirs(working_dir)

# you can save the trained model as a folder or a zip file
model_file = os.path.join(working_dir, 'sk_model.zip')    
text_classifier.save(model_file)
# %azureml upload outputs/models/sk_model.zip

```
    BaseTextModel::save ==> start
    TatkPipeline::save ==> start
    Time taken: 0.28 mins
    TatkPipeline::save ==> end
    Time taken: 0.38 mins
    BaseTextModel::save ==> end
    
```python
# for debugging, you can save the word n-grams vocabulary to a text file
word_vocab_file_path = os.path.join(working_dir, 'word_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_word_ngrams").save_vocabulary(word_vocab_file_path) 
# %azureml upload outputs/dictionaries/word_ngrams_vocabulary.pkl

# for debugging, you can save the character n-grams vocabulary to a text file
char_vocab_file_path = os.path.join(working_dir, 'char_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_char_ngrams").save_vocabulary(char_vocab_file_path) 
# %azureml upload outputs/dictionaries/char_ngrams_vocabulary.pkl
```

    save_vocabulary ==> start
    saving 216393 n-grams ...
    Time taken: 0.01 mins
    save_vocabulary ==> end
    save_vocabulary ==> start
    saving 67230 n-grams ...
    Time taken: 0.0 mins
    save_vocabulary ==> end
 
## <a name="load-the-pipeline"></a>A folyamat betöltése
A besorolási folyamatok és a word-ngrams és karakter n-gramokat következtetési a betöltése:

```python
# in order to deploy the trained model, you have to load the zip file of the classifier pipeline
loaded_text_classifier = TextClassifier.load(model_file)

from tatk.feature_extraction import NGramsVectorizer
word_ngram_vocab = NGramsVectorizer.load_vocabulary(word_vocab_file_path)
char_ngram_vocab = NGramsVectorizer.load_vocabulary(char_vocab_file_path)
```
    BaseTextModel::load ==> start
    TatkPipeline::load ==> start
    Time taken: 0.14 mins
    TatkPipeline::load ==> end
    Time taken: 0.15 mins
    BaseTextModel::load ==> end
    loading 216393 n-grams ...
    loading 67230 n-grams ...
    

## <a name="test-the-pipeline"></a>A folyamat tesztelése

Tesztadatkészlet kiértékelése, alkalmazza a betöltött szöveg osztályozási folyamat:

```python
predictions = loaded_text_classifier.predict(df_test)
loaded_evaluator = loaded_text_classifier.evaluate(predictions)
loaded_evaluator.get_metrics('macro_f1')
```
    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.14 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.36 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.45 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> en
    
    0.82727029243808903

## <a name="operationalization-deploy-and-consume"></a>Operacionalizálás: üzembe helyezése és felhasználása

Ebben a szakaszban, a folyamat üzembe helyezéséhez szöveg besorolást, egy Azure Machine Learning web service a [Azure Machine Learning parancssori](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Ezt követően a tanítási és pontozási webszolgáltatás értékben.

**Jelentkezzen be az Azure-előfizetéshez az Azure CLI-vel**

Használatával egy [Azure](https://azure.microsoft.com/) fiók és a egy érvényes előfizetést, jelentkezzen be a következő CLI-paranccsal:
<br>`az login`

+ Váltson egy másik Azure-előfizetéshez, használja a parancsot:
<br>`az account set --subscription [your subscription name]`

+ Az aktuális modellkezelési fiók megtekintéséhez használja a parancsot:
  <br>`az ml account modelmanagement show`

**Hozzon létre, és állítsa be a telepítési környezet**

Csak egyszer beállítani az üzembehelyezési környezetet kell. Ha Ön még nem rendelkezik, állítsa be az üzembe helyezési környezet most [ezek az utasítások](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

1. Ellenőrizze, hogy a környezet az Azure Machine Learning modellkezelési fiók és erőforráscsoport ugyanabban a régióban találhatók.

1. Töltse le a telepítési konfigurációs fájlt a Blob storage-ból, és mentse helyileg:

   ```python
   # Download the deployment config file from Blob storage `url` and save it locally under `file_name`:
   deployment_config_file_url = 'https://aztatksa.blob.core.windows.net/dailyrelease/tatk_deploy_config.yaml'
   deployment_config_file_path=os.path.join(resources_dir, 'tatk_deploy_config.yaml')
   import urllib.request
   urllib.request.urlretrieve(deployment_config_file_url, deployment_config_file_path)
   ```

1. Frissítse a telepítési konfigurációs fájlt letöltötte az erőforrások megfelelően:

   ```python
   web_service_name = 'please type your web service name'
   working_directory= os.path.join(resources_dir, 'deployment') 

   web_service = text_classifier.deploy(web_service_name= web_service_name, 
                          config_file_path=deployment_config_file_path,
                          working_directory= working_directory)  
   ```

1. Tekintettel arra, hogy a betanított modell üzembe helyezése sikeresen meghívni a pontozási webszolgáltatás új adatkészlet:

   ```python
   print("Service URL: {}".format(web_service._service_url))
   print("Service Key: {}".format(web_service._api_key))
   ```

1. A web service betöltése a név bármikor:

   ```python
   from tatk.operationalization.csi.csi_web_service import CsiWebService
   url = "<please type the service URL here>"
   key = "<please type the service Key here>"
   web_service = CsiWebService(url, key)
   ```

1. Tesztelje a web service együtt származik a 20 newsgrpoups adatkészlet két e-mailek törzsét:

   ```python
   # Example input data for scoring
   import json
   dict1 ={}
   dict1["recordId"] = "a1" 
   dict1["data"]= {}
   dict1["data"]["text"] = """
   I'd be interested in a copy of this code if you run across it.
   (Mail to the author bounced)
    > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,
    1993 /
    > I fooled around with this problem a few years ago, and implemented a
    > simple method that ran on a PC.
    > was very simple - about 40 or 50 lines of code.
    . . .
    > Somewhere I still have it
    > and could dig it out if there was interest.
   """
   
   dict2 ={}
   dict2["recordId"] = "b2"
   dict2["data"] ={}
   dict2["data"]["text"] = """
   >>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,
   >>take their discussions to alt.drugs? Their discussions will receive greatest
   >>contribution and readership there. The people interested in strictly
   >>"smart drugs" (i.e. Nootropics) should post to this group. The two groups
   >>(alt.drugs & alt.psychoactives) have been used interchangably lately.
   >>I do think that alt.psychoactives is a deceiving name. alt.psychoactives
   >>is supposedly the "smart drug" newsgroup according to newsgroup lists on
   >>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &
   >>nutrients)? I have noticed some posts in sci.med.nutrition regarding
   >>"smart nutrients." We may lower that groups burden as well.
   >   

   I was wondering if a group called 'sci.pharmacology' would be relevent.
   This would be used for a more formal discussion about pharmacological
   issues (pharmacodynamics, neuropharmacology, etc.)      

   Just an informal proposal (I don't know anything about the net.politics
   for adding a newsgroup, etc.)

   """

   dict_list =[dict1, dict2]
   data ={}
   data["values"] = dict_list
   input_data_json_str = json.dumps(data)
   print (input_data_json_str)
   prediction = web_service.score(input_data_json_str)
   prediction
   ```

   ```
   {"values": [{"recordId": "a1", "data": {"text": "\nI'd be interested in a copy of this code if you run across it.\n(Mail to the author bounced)\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\n 1993 /\n > I fooled around with this problem a few years ago, and implemented a\n > simple method that ran on a PC.\n > was very simple - about 40 or 50 lines of code.\n . . .\n > Somewhere I still have it\n > and could dig it out if there was interest.\n"}}, {"recordId": "b2", "data": {"text": "\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\n>>take their discussions to alt.drugs? Their discussions will receive greatest\n>>contribution and readership there. The people interested in strictly\n>>\"smart drugs\" (i.e. Nootropics) should post to this group. The two groups\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\n>>is supposedly the \"smart drug\" newsgroup according to newsgroup lists on\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\n>>\"smart nutrients.\" We may lower that groups burden as well.\n>\n\nI was wondering if a group called 'sci.pharmacology' would be relevent.\nThis would be used for a more formal discussion about pharmacological\nissues (pharmacodynamics, neuropharmacology, etc.)\n\nJust an informal proposal (I don't know anything about the net.politics\nfor adding a newsgroup, etc.)\n\n"}}]}
   F1 2018-05-02 00:10:58,272 INFO Web service scored. 
    
   '{"values": [{"recordId": "b2", "data": {"text": "\\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\\n>>take their discussions to alt.drugs? Their discussions will receive greatest\\n>>contribution and readership there. The people interested in strictly\\n>>\\"smart drugs\\" (i.e. Nootropics) should post to this group. The two groups\\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\\n>>is supposedly the \\"smart drug\\" newsgroup according to newsgroup lists on\\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\\n>>\\"smart nutrients.\\" We may lower that groups burden as well.\\n>\\n\\nI was wondering if a group called \'sci.pharmacology\' would be relevent.\\nThis would be used for a more formal discussion about pharmacological\\nissues (pharmacodynamics, neuropharmacology, etc.)\\n\\nJust an informal proposal (I don\'t know anything about the net.politics\\nfor adding a newsgroup, etc.)\\n\\n", "class": 13}}, {"recordId": "a1", "data": {"text": "\\nI\'d be interested in a copy of this code if you run across it.\\n(Mail to the author bounced)\\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\\n 1993 /\\n > I fooled around with this problem a few years ago, and implemented a\\n > simple method that ran on a PC.\\n > was very simple - about 40 or 50 lines of code.\\n . . .\\n > Somewhere I still have it\\n > and could dig it out if there was interest.\\n", "class": 1}}]}'
   ```


## <a name="next-steps"></a>További lépések

További információ az Azure Machine Learning-csomagot a Text Analytics ezekben a cikkekben:

+ Olvassa el a [csomag áttekintése](https://aka.ms/aml-packages/text).

+ Fedezze fel a [referenciadokumentációt](https://aka.ms/aml-packages/text) a csomag számára.

+ Ismerje meg [egyéb Python-csomagokat az Azure Machine Learning](reference-python-package-overview.md).