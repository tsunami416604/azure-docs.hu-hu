---
title: 'Python-szkript végrehajtása: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan futtathatja a Python-kód futtatásához a Azure Machine Learning-szolgáltatásban a Python-parancsfájl végrehajtása modult.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: ac68239c12fb284dd3cb5179b5719f3d36acbd34
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693796"
---
# <a name="execute-python-script-module"></a>Python parancsfájl-modul végrehajtása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ez a modul a Python-kód futtatására használható. A Python architektúrával és tervezési alapelveivel kapcsolatos további információkért tekintse meg [a következő cikket.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

A Python használatával olyan feladatokat hajthat végre, amelyeket jelenleg nem támogat a meglévő modulok, például a következők:

+ Az adatmegjelenítés `matplotlib` használatával
+ A Python-kódtárak használata a munkaterületen található adatkészletek és modellek számbavételéhez
+ Az adatok [importálása](./import-data.md) modul által nem támogatott forrásokból származó adatok olvasása, betöltése és módosítása
+ Saját mély tanulási kód futtatása 


A Azure Machine Learning a Python anaconda-eloszlását használja, amely számos gyakori segédprogramot tartalmaz az adatfeldolgozáshoz. Az anaconda verzióját automatikusan frissíteni fogjuk. A jelenlegi verzió:
 -  Anaconda 4.5 + Distribution for Python 3,6 

Az előre telepített csomagok a következők:
-  asn1crypto = = 0.24.0
- attrs = = 19.1.0
- Azure – Common = = 1.1.18
- Azure-Storage – blob = = 1.5.0
- Azure-Storage – Common = = 1.4.0
- 2019.3.9 = =
- cffi = = 1.12.2
- chardet = = 3.0.4
- titkosítás = = 2.6.1
- disztribúció = = 1.4.0
- IDNA = = 2.8
- jsonschema = = 3.0.1
- lightgbm = = 2.2.3
- több – itertools = = 6.0.0
- NumPy = = 1.16.2
- Pandák = = 0.24.2
- Párna = = 6.0.0
- pip = = 19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent = = 0.14.11
- Python-dateutil = = 2.8.0
- pytz = = 2018.9
- kérelmek = = 2.21.0
- scikit – Learn = = 0.20.3
- SciPy = = 1.2.1
- setuptools = = 40.8.0
- hat = = 1.12.0
- Torch = = 1.0.1. Post2
- torchvision = = 0.2.2. post3
- urllib3 = = 1.24.1
- Wheel = = 0.33.1 

 Ha az előre telepített listában nem szereplő egyéb csomagokat szeretne telepíteni, például *scikit-misc*, adja hozzá a következő kódot a szkripthez: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>A használat módja

A **Python-szkript végrehajtása** modul olyan minta Python-kódot tartalmaz, amelyet kiindulási pontként használhat. A **Python parancsfájl-végrehajtási** moduljának konfigurálásához adja meg az Inputs és a Python-kód futtatására szolgáló bemeneteket és a Python- **szkriptek** szövegmezőjét.

1. Adja hozzá a **Python-szkript végrehajtása** modult a folyamathoz.

2. Vegyen fel és kapcsolódjon a **DataSet1 elemet** a bemenethez használni kívánt felületről származó adatkészletekhez. Hivatkozzon erre az adatkészletre a Python-szkriptben **DataFrame1**néven.

    Az adatkészletek használata nem kötelező, ha a Python használatával szeretne adatokat előállítani, vagy Python-kód használatával importálja az adatokat közvetlenül a modulba.

    Ez a modul a **Dataset2**-on található második adatkészlet hozzáadását támogatja. Hivatkozzon a Python-szkript második adatkészletére DataFrame2.

    Az Azure Machine Learningban tárolt adatkészletek automatikusan a **pandák** adatba lesznek konvertálva. a rendszer az ezzel a modullal tölti be a kereteket.

    ![Python bemeneti térképének végrehajtása](media/module/python-module.png)

4. Új Python-csomagok vagy-kódok felvételéhez adja hozzá az egyéni erőforrásokat tartalmazó tömörített fájlt a **parancsfájl-csomagban**. A **parancsfájlhoz** való bemenetnek a munkaterületre már feltöltött tömörített fájlnak kell lennie. 

    A feltöltött tömörített archívumban található összes fájl használható a folyamat végrehajtása során. Ha az Archívum tartalmaz egy címtár-struktúrát, a rendszer megőrzi a struktúrát, de egy **src** nevű könyvtárat kell megadnia az elérési útra.

5. A **Python-szkript** szövegmezőbe írja be vagy illessze be az érvényes Python-szkriptet.

    A **Python-szkript** szövegmezője előre ki van töltve a megjegyzésekben található utasításokkal, és az adathozzáféréshez és a kimenethez tartozó mintakód. **Szerkesztenie vagy cserélnie kell ezt a kódot.** Ügyeljen arra, hogy kövesse a behúzással és a burkolattal kapcsolatos Python-konvenciókat.

    + A szkriptnek tartalmaznia kell egy `azureml_main` nevű függvényt, amely a modul belépési pontja.
    + A belépési pont függvény legfeljebb két bemeneti argumentumot tartalmazhat: `Param<dataframe1>` és `Param<dataframe2>`
    + A harmadik bemeneti porthoz csatlakoztatott tömörített fájlok kibontása és tárolása a könyvtárban történik, `.\Script Bundle`, amely a Python-`sys.path` is hozzá van adva. 

    Ezért ha a zip-fájl `mymodule.py` tartalmaz, importálja azt `import mymodule` használatával.

    + Két adatkészletet lehet visszaadni az illesztőfelületnek, amelynek egy `pandas.DataFrame` típusú sorozatot kell tartalmaznia. A Python-kódban más kimeneteket is létrehozhat, amelyeket közvetlenül az Azure Storage-ba írhat.

6. Futtassa a folyamatot, vagy válassza ki a modult, és kattintson a **kijelölt futtatása** lehetőségre, hogy csak a Python-szkriptet futtassa.

    Az összes adattal és kóddal betöltődik egy virtuális gépre, és a megadott Python-környezet használatával fut.

## <a name="results"></a>Eredmények

A beágyazott Python-kód által végrehajtott számítások eredményét pandákként kell megadni. A DataFrame, amelyet a rendszer automatikusan a Azure Machine Learning adatkészlet formátumára konvertál, így az eredményeket a folyamat más moduljaival is használhatja.

A modul két adatkészletet ad vissza:  
  
+ **Eredmények adatkészlet 1**, a Python-szkriptben az első visszaadott pandák dataframe által definiált

+ A **2. eredmény adatkészlete**, amelyet a második visszaadott Panda Dataframe a Python-parancsfájlban definiált


## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 