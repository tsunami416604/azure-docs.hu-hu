---
title: 'Hajtsa végre a Python-szkriptet: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a Python-szkript végrehajtására modul az Azure Machine Learning szolgáltatásban Python-kód futtatása.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029130"
---
# <a name="execute-python-script-module"></a>Python-szkriptet a modul végrehajtása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával Python-kód futtatása. Python architektúra és tervezés elveinek kapcsolatos további információkért lásd: [a következő cikket.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

A Python jelenleg nem támogatottak a meglévő modulok például feladatokat hajthat végre:

+ Adatok megjelenítése `matplotlib`
+ Python-kódtárakat használó számba veheti az adatkészleteket és modellek a munkaterületen
+ Olvasó, betöltés és által nem támogatott forrásokból származó adatok módosítása a [adatok importálása](./import-data.md) modul
+ A saját deep learning-kód futtatása 


Az Azure Machine Learning Python, adatfeldolgozási számos gyakori segédeszközei tartalmazó Anaconda elosztása használ. Automatikusan frissítjük Anaconda-verzió. Jelenlegi verzió a következő:
 -  Anaconda 4.5+ distribution for Python 3.6 

Az előre telepített csomagok a következők:
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi == 2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- titkosítás == 2.6.1
- disztribúció 1.4.0-s ==
- idna==2.8
- jsonschema==3.0.1
- lightgbm == 2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow==6.0.0
- pip==19.0.3
- pyarrow == 0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pyrsistent==0.14.11
- Python-dateutil == 2.8.0
- pytz==2018.9
- requests==2.21.0
- scikit-további == 0.20.3
- scipy 1.2.1-es ==
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 További csomagok telepítése az előre telepített listán nem szereplő például *scikit-vegyes*, adja hozzá a következő kódot a szkriptet: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>A használat módja

A **Python-szkript végrehajtására** modul tartalmazza a Python-mintakód kiindulási pontként használható. Konfigurálhatja a **Python-szkript végrehajtására** bemenetei között, és hajtsa végre a Python-kód tárházát biztosítja, a modul a **Python-szkriptet** szövegmező.

1. Adja hozzá a **Python-szkript végrehajtására** modult a kísérletvászonra.

2. Adja hozzá, és csatlakozzon a **Dataset1** a felhasználói felületről, amely a bemeneti használni kívánt adatkészletekkel. Ennek az adatkészletnek, a Python-szkript hivatkozhat **DataFrame1**.

    Adatkészlet használata nem kötelező, ha szeretné készítése a Python használata az adatok, vagy a Python-kód használatával importálja az adatokat közvetlenül a modult.

    Ez a modul egy második adatkészlet hozzáadását támogatja a **Dataset2**. A második adatkészlet DataFrame2, a Python-szkript-hivatkozás.

    Az Azure Machine Learning tárolt adatkészleteinek automatikusan konvertálva **pandas** data.frames, ez a modul a betöltésekor.

    ![Hajtsa végre a Python bemeneti térkép](media/module/python-module.png)

4. Új Python-csomagokat vagy kód történő felvételéhez adja hozzá a ezeket az egyéni erőforrásokat tartalmazó tömörített fájl **parancsfájl csomag**. A bemeneti **parancsfájl csomag** kell egy tömörített fájlt már feltölteni a munkaterülethez. 

    A feltöltött ZIP-archívumot szereplő bármely fájl kísérlet-végrehajtás során használható. Ha az archívum egy könyvtárstruktúrát tartalmazza, a szerkezet megmaradnak, de be kell illesztenie nevű címtárban **src** az elérési úthoz.

5. Az a **Python-szkriptet** szövegbeviteli mezőben írja be vagy illessze be az érvényes Python-szkriptet.

    A **Python-szkriptet** szövegmező előre megadott, a megjegyzéseket, és a adathozzáféréssel és -kimenet mintakód néhány utasításait. **Szerkeszteni kell, vagy cserélje le ezt a kódot.** Mindenképpen behúzás és a kis-és Python konvencióinak.

    + A parancsfájl tartalmaznia kell egy nevű függvény `azureml_main` a modulhoz tartozó belépési pontként.
    + A belépési pont függvényben legfeljebb két bemeneti argumentummal tartalmazhat: `Param<dataframe1>` és `Param<dataframe2>`
    + A harmadik bemeneti porthoz csatlakozik a tömörített fájlok kicsomagolt és a címtárban tárolt `.\Script Bundle`, amely is adnak hozzá a Python `sys.path`. 

    Ezért ha a zip-fájlt tartalmaz `mymodule.py`, importálja a `import mymodule`.

    + Két adatkészletet adhatók vissza a felületre, amelynek típusa sorozatát kell `pandas.DataFrame`. Más kimeneti adatok létrehozása a Python-kódban, és közvetlenül az Azure-tárolóba írja azokat.

6. Futtassa a kísérletet, vagy jelölje ki a modult, és kattintson a **kijelölt futtatása** csak a Python-szkript futtatásához.

    Az összes adat és kód tölti be a virtuális gép, és futtassa a megadott Python-környezetet használ.

## <a name="results"></a>Results (Eredmények)

A beágyazott Python-kód által végrehajtott minden számítások eredményét egy pandas meg kell adni. Adathalmaz, amelyeket más modulok a kísérletben is használhatja az eredményeket, hogy automatikusan az Azure Machine Learning adatkészlet formátumra alakítja át.

A modul adja vissza a két adatkészletet:  
  
+ **Adatkészlet 1 eredmények**az első visszaadott pandas dataframe a Python-szkript által definiált

+ **Adatkészlet 2 eredmény**, a második visszaadott pandas dataframe a Python-szkript által definiált


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 