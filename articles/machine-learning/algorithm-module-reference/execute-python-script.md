---
title: 'Python-szkript végrehajtása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Python-kód futtatásához a Azure Machine Learning Python-szkriptek modulját.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684728"
---
# <a name="execute-python-script-module"></a>Python parancsfájl-modul végrehajtása

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ez a modul a Python-kód futtatására használható. A Python architektúrával és tervezési alapelveivel kapcsolatos további információkért tekintse meg [a következő cikket](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

A Python használatával olyan feladatokat hajthat végre, amelyeket jelenleg nem támogat a meglévő modulok, például a következők:

+ Adatmegjelenítés az használatával`matplotlib`
+ A Python-kódtárak használata a munkaterületen található adatkészletek és modellek számbavételéhez
+ Az adatok [importálása](./import-data.md) modul által nem támogatott forrásokból származó adatok olvasása, betöltése és módosítása
+ Saját mély tanulási kód futtatása 


A Azure Machine Learning a Python anaconda-eloszlását használja, amely számos gyakori segédprogramot tartalmaz az adatfeldolgozáshoz. Az anaconda verzióját automatikusan frissíteni fogjuk. A jelenlegi verzió:
 -  Anaconda 4.5 + Distribution for Python 3,6 

Az előre telepített csomagok a következők:
-    adal = = 1.2.2
-    applicationinsights = = 0.11.9
-    attrs = = 19.3.0
-    Azure – Common = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-mgmt-Authorization = = 0.60.0
-    Azure-mgmt-containerregistry = = 2.8.0
-    Azure-mgmt-kulcstartó = = 2.2.0
-    Azure-mgmt-Resource = = 8.0.1
-    Azure-mgmt-Storage = = 8.0.0
-    Azure-Storage – blob = = 1.5.0
-    Azure-Storage – Common = = 1.4.2
-    azureml-Core = = 1.1.5.5
-    azureml-adatelőkészítés-Native = = 14.1.0
-    azureml-adatelőkészítés = = 1.3.5
-    azureml – alapértékek = = 1.1.5.1
-    azureml-Designer-klasszikus-modulok = = 0.0.118
-    azureml-Designer-Core = = 0.0.31
-    azureml-Designer-Internal = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml – folyamat – mag = = 1.1.5
-    azureml-telemetria = = 1.1.5.3
-    backports. tempfile = = 1.0
-    backports. weakref = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    2019.11.28 = =
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    Kattintson = = 7.1.1
-    cloudpickle = = 1.3.0
-    configparser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    titkosítás = = 2.8
-    feldolgozó = = 0.10.0
-    Kapros = = 0.3.1.1
-    disztribúció = = 1.4.0
-    Docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    lombik = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-Core = = 1.16.0
-    Google-Auth = = 1.12.0
-    Google – Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-folytatható – média = = 0.5.0
-    googleapis-Common-proto = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    kiegyensúlyozatlan – Learn = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-naplózás-a-ről = = 0.2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    liac-arff = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    több – itertools = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    NumPy = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandák = = 0.25.3
-    pathspec = = 0.7.0
-    pip = = 20.0.2
-    portalocker = = 1.6.0
-    protopuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1 – modulok = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    kérelmek – oauthlib = = 1.3.0
-    kérelmek = = 2.23.0
-    RSA = = 4.0
-    ruamel. YAML = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit – Learn = = 0.22.2
-    SciPy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    hat = = 1.14.0
-    intelligens – nyitott = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket – Client = = 0.57.0
-    Werkzeug = = 0.16.1
-    Wheel = = 0.34.2

 Ha az előre telepített listában nem szereplő egyéb csomagokat szeretne telepíteni, például *scikit-misc*, adja hozzá a következő kódot a szkripthez: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Fájlok feltöltése
A **Python-szkript végrehajtása** támogatja a fájlok feltöltését [Azure Machine learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)használatával.

Az alábbi példa bemutatja, hogyan tölthet fel egy képfájlt a **Python parancsfájl végrehajtása** modulban:

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

A folyamat futásának befejezése után a rendszerképet a modul jobb oldali paneljén tekintheti meg

> [!div class="mx-imgBorder"]
> ![Feltöltött – rendszerkép](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>A Python-szkript végrehajtásának konfigurálása

A **Python-szkript végrehajtása** modul olyan minta Python-kódot tartalmaz, amelyet kiindulási pontként használhat. A **Python parancsfájl-végrehajtási** moduljának konfigurálásához adja meg az Inputs és a Python-kód futtatására szolgáló bemeneteket és a Python- **szkriptek** szövegmezőjét.

1. Adja hozzá a **Python-szkript végrehajtása** modult a folyamathoz.

2. Adja hozzá a (z) elemet a **DataSet1 elemet** bármely olyan adatkészlethez, amelyet a bemenethez szeretne használni. Hivatkozzon erre az adatkészletre a Python-szkriptben **DataFrame1**néven.

    Az adatkészletek használata nem kötelező, ha a Python használatával szeretne adatokat előállítani, vagy Python-kód használatával importálja az adatokat közvetlenül a modulba.

    Ez a modul a **Dataset2**-on található második adatkészlet hozzáadását támogatja. Hivatkozzon a Python-szkript második adatkészletére DataFrame2.

    Az Azure Machine Learningban tárolt adatkészletek automatikusan a **pandák** adatba lesznek konvertálva. a rendszer az ezzel a modullal tölti be a kereteket.

    ![Python bemeneti térképének végrehajtása](media/module/python-module.png)

4. Új Python-csomagok vagy-kódok felvételéhez adja hozzá az egyéni erőforrásokat tartalmazó tömörített fájlt a **parancsfájl-csomagban**. A **parancsfájlba** való bevitelnek a munkaterületre fájltípus-adatkészletként feltöltött tömörített fájlnak kell lennie. Feltöltheti az adatkészletet az **adatkészletek** eszköz lapján, és az adatkészletek modulját áthelyezheti **a bal** oldali modul konzolfáján a tervező szerzői műveletek lapján. 

    A feltöltött tömörített archívumban található összes fájl használható a folyamat végrehajtása során. Ha az Archívum tartalmaz egy címtár-struktúrát, a rendszer megőrzi a struktúrát, de egy **src** nevű könyvtárat kell megadnia az elérési útra.

5. A **Python-szkript** szövegmezőbe írja be vagy illessze be az érvényes Python-szkriptet.

    > [!NOTE]
    > Kérjük, legyen nagyon körültekintő a parancsfájl írásakor, és győződjön meg róla, hogy nincs szintaktikai hiba, például egy nem deklarált objektum vagy egy nem importált modul használata. Emellett az előre telepített modulok listájára is külön figyelmet kell fordítani. A nem felsorolt modulok importálásához telepítse a megfelelő csomagokat a parancsfájlba, például:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    A **Python-szkript** szövegmezője előre ki van töltve a megjegyzésekben található utasításokkal, és az adathozzáféréshez és a kimenethez tartozó mintakód. Szerkesztenie vagy cserélnie kell ezt a kódot. Ügyeljen arra, hogy kövesse a behúzással és a burkolattal kapcsolatos Python-konvenciókat.

    + A szkriptnek tartalmaznia kell egy nevű `azureml_main` függvényt, amely a modul belépési pontja.
    + A belépési pont függvénynek két bemeneti argumentummal `Param<dataframe1>` kell `Param<dataframe2>`rendelkeznie: és, még akkor is, ha ezek az argumentumok nem használatosak a parancsfájlban.
    + A harmadik bemeneti porthoz csatlakoztatott tömörített fájlok kibontása és tárolása a könyvtárban `.\Script Bundle`történik, amely a Pythonhoz `sys.path`is hozzá van adva. 

    Ezért ha a zip-fájlja `mymodule.py`tartalmaz, importálja `import mymodule`azt a használatával.

    + Két adatkészletet lehet visszaadni a tervezőnek, amelynek típusú `pandas.DataFrame`sorozatot kell megadni. A Python-kódban más kimeneteket is létrehozhat, amelyeket közvetlenül az Azure Storage-ba írhat.

6. Küldje el a folyamatot, vagy válassza ki a modult, és kattintson a **kijelölt futtatása** lehetőségre, hogy csak a Python-szkriptet futtassa.

    Az összes adattal és kóddal betöltődik egy virtuális gépre, és a megadott Python-környezet használatával fut.

## <a name="results"></a>Results (Eredmények)

A beágyazott Python-kód által végrehajtott számítások eredményét pandákként kell megadni. A DataFrame, amelyet a rendszer automatikusan a Azure Machine Learning adatkészlet formátumára konvertál, így az eredményeket a folyamat más moduljaival is használhatja.

A modul két adatkészletet ad vissza:  
  
+ **Eredmények adatkészlet 1**, a Python-szkriptben az első visszaadott pandák dataframe által definiált

+ A **2. eredmény adatkészlete**, amelyet a második visszaadott Panda Dataframe a Python-parancsfájlban definiált


## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 