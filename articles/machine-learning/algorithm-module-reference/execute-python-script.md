---
title: 'Python-parancsfájl végrehajtása: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Python-parancsfájl-modult az Azure Machine Learningben a Python-kód futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684728"
---
# <a name="execute-python-script-module"></a>Python-parancsfájl-modul végrehajtása

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal futtatja a Python-kódot. A Python architektúrájáról és tervezési elveiről az alábbi cikkben olvashat [bővebben.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

A Python nal olyan feladatokat hajthat végre, amelyeket jelenleg nem támogatnak a meglévő modulok, például:

+ Adatok megjelenítése a használatával`matplotlib`
+ Python-kódtárak használata adatkészletek és modellek számbavételére a munkaterületen
+ [Adatok importálása](./import-data.md) modul által nem támogatott forrásokból származó adatok olvasása, betöltése és kezelése
+ Futtassa saját mélytanulási kódját 


Az Azure Machine Learning a Python Anaconda disztribúcióját használja, amely számos gyakori segédprogramot tartalmaz az adatfeldolgozáshoz. Az Anaconda verzióját automatikusan frissítjük. A jelenlegi verzió:
 -  Anaconda 4.5+ disztribúció Python 3.6-hoz 

Az előre telepített csomagok a következők:
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    az úraz úrközös==1.1.25
-    azure-core==1.3.0
-    azúr-graphrbac==0.61.1
-    azure-identity==1.3.0
-    azure-mgmt-authorization==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azure-storage-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-internal==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetria==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    kattintás==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    kriptográfia==2,8
-    cycler==0,10,0
-    kapor==0.3.1.1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    lombik==1.0.3
-    flausepy==3.0.1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-folytatás-média==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2,9
-    kiegyensúlyozatlan-tanulni==0,4,3
-    izodát==0.6.0
-    itsdangerous==1.1.0
-    jeepney==0.4.3
-    jinja2==2.11.1
-    jmespath==0,9,5
-    joblib==0.14.0
-    json-fakitermelés-py==0,2
-    jsonpickle==1,3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    több-itertools==6.0.0
-    msal-hosszabbítók==0.1.3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1,18,2
-    oauthlib==3.1.0
-    pandák==0,25,3
-    pathspec==0,7,0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0,16,0
-    pyasn1-modulok==0,2,8
-    pyasn1==0,4,8
-    pycparser==2,20
-    pycryptodomex==3.7.3
-    pyjwt==1.7.1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pyrsistent==0,16,0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    kérés-oauthlib==1.3.0
-    requests==2.23.0
-    rsa==4,0
-    ruamel.yaml==0.15.89
-    s3transfer==0.3.3
-    scikit-learn==0.22.2
-    scipy==1.4.1
-    secretstorage==3.1.2
-    setuptools==46.1.1.post20200323
-    hat==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0.57.0
-    werkzeug==0.16.1
-    kerék==0,34,2

 Az előre telepített listán nem szereplő egyéb csomagok, például *a scikit-misc*telepítéséhez adja hozzá a következő kódot a parancsfájlhoz: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Fájlok feltöltése
A **Python-parancsfájl végrehajtása** támogatja a fájlok feltöltését az [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-)használatával.

A következő példa bemutatja, hogyan tölthet fel egy képfájlt a **Python-parancsfájl végrehajtása** modulban:

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

A folyamat futtatása után megtekintheti a kép előnézetét a modul jobb oldali paneljén

> [!div class="mx-imgBorder"]
> ![Feltöltött kép](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>A Python-parancsfájl végrehajtása parancskonfigurálása

A **Python-parancsfájl végrehajtása** modul tartalmaz minta Python-kódot, amely kiindulási pontként használható. A **Python-parancsfájl végrehajtása** modul konfigurálásához adja meg a bemenetek és a Python-kód végrehajtásához a **Python script** szövegmezőben futtatható egy készletét.

1. Adja hozzá a **Python-parancsfájl végrehajtása** modult a folyamathoz.

2. Adja hozzá és csatlakoztassa a **Dataset1-en** a bevitelhez használni kívánt adatkészleteket a tervezőtől. Hivatkozzon erre az adatkészletre a Python-parancsfájlban **DataFrame1**néven.

    Az adatkészlet használata nem kötelező, ha python használatával szeretne adatokat generálni, vagy python-kód használatával szeretné importálni az adatokat közvetlenül a modulba.

    Ez a modul támogatja egy második adatkészlet hozzáadását a **Dataset2-en.** Hivatkozzon a Python-parancsfájl második adatkészletére DataFrame2 néven.

    Az Azure Machine Learningben tárolt adatkészletek automatikusan **pandadata.frames-é** alakulnak át, ha ezzel a modullal töltik be.

    ![Python-bemeneti térkép végrehajtása](media/module/python-module.png)

4. Új Python-csomagok vagy -kódok hozzáadásához adja hozzá az ezeket az egyéni erőforrásokat tartalmazó tömörített fájlt a **Parancsfájl csomagban.** A **Parancsfájl-köteg** bemenetének fájltípusú adatkészletként a munkaterületre feltöltött tömörített fájlnak kell lennie. Az adatkészletet feltöltheti az **Adatkészletek** eszközlapra, és az adatkészletmodult a tervezőszerzői lap bal oldali modulfájának **Saját adatkészletek** listájából húzhatja és elhúzhatja. 

    A feltöltött tömörítési archívumban található bármely fájl használható a folyamat végrehajtása során. Ha az archívum tartalmaz egy könyvtárstruktúrát, a struktúra megmarad, de elő kell készítenie egy **src** nevű könyvtárat az elérési útra.

5. A **Python-parancsfájl** szövegmezőjébe írja be vagy illessze be az érvényes Python-parancsfájlt.

    > [!NOTE]
    > Kérjük, legyen nagyon óvatos a parancsfájl írásakor, és győződjön meg arról, hogy nincs szintaktikai hiba, például nem deklarált objektum vagy nem importált modul használata. Fordítson különös figyelmet az előre telepített modulok listájára is. A listán nem szereplő modulok importálásához telepítse a megfelelő csomagokat a parancsfájlba,
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    A **Python-parancsfájl** szövegmezője előre ki van töltve néhány megjegyzésben szereplő utasítással, valamint az adathozzáféréshez és a kimenethez szükséges mintakóddal. Ezt a kódot kell szerkesztenie vagy cserélnie. Ügyeljen arra, hogy kövesse a Python-konvenciókat a behúzásról és a házról.

    + A parancsfájlnak tartalmaznia `azureml_main` kell egy, a modul belépési pontjaként megnevezett függvényt.
    + A belépési pont függvénynek két `Param<dataframe1>` `Param<dataframe2>`bemeneti argumentuma lehet: és akkor is, ha ezek az argumentumok nincsenek a parancsfájlban használva.
    + A harmadik bemeneti porthoz csatlakoztatott tömörített fájlok kivannak `.\Script Bundle`csomagolva és a könyvtárban tárolódnak, amely szintén hozzáadódik a Pythonhoz. `sys.path` 

    Ezért ha a zip `mymodule.py`fájl tartalmazza, importálja a használatával. `import mymodule`

    + Két adatkészlet et lehet visszaadni a tervezőnek, `pandas.DataFrame`amelynek típusnak kell lennie. A Python-kódban létrehozhat más kimeneteket is, és közvetlenül az Azure storage-ba írhatja őket.

6. Küldje el a folyamatot, vagy válassza ki a modult, és kattintson a **Futtatás lehetőségre,** ha csak a Python-parancsfájlt szeretné futtatni.

    Az összes adat és kód betöltődik egy virtuális gépbe, és a megadott Python-környezetben fut.

## <a name="results"></a>Results (Eredmények)

A beágyazott Python-kód által végrehajtott számítások eredményeit pandákként kell megadni. DataFrame, amely automatikusan konvertálja az Azure Machine Learning adatkészlet formátumban, így az eredményeket a folyamat más modulokkal.

A modul két adatkészletet ad vissza:  
  
+ **Results Dataset 1**, amelyet az első visszaadott panda dataframe definiált Python-parancsfájlban

+ **2. eredményadatkészlet**, amelyet a második visszaadott panda dataframe határoz meg Python-parancsfájlban


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 