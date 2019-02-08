---
title: 'Oktatóanyag: Hajtsa végre a lemezkép műveletek – Python'
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Computer Vision API-t és a Pythont Jupyter-notebookokkal. Az eredményeket népszerű címtárakkal jelenítheti meg.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: afb937f1be780c35ef44023acfedeb38a6b6a877
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875070"
---
# <a name="tutorial-computer-vision-api-python"></a>Oktatóanyag: Számítógép Vision API Python

Ebben az oktatóanyagban megtudhatja, hogyan használható a Computer Vision API a Pythonban, és hogyan vizualizálhatók az eredmények népszerű kódtárak használatával. Az oktatóanyag futtatásához a Jupytert fogja használni. Ha többet szeretne megtudni az interaktív Jupyter-notebookok használatáról, tekintse meg a [Jupyter dokumentációját](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Előfeltételek

- [A Python 2.7-es vagy újabb, illetve 3.5-ös vagy újabb verziója](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) eszköz
- [Jupyter Notebook](https://jupyter.org/install) telepítve

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Az oktatóanyag notebookjának megnyitása a Jupyterben 

1. Nyissa meg a [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python) GitHub-adattárat. 
2. Kattintson a zöld gombra az adattár klónozásához vagy letöltéséhez. 
3. Nyisson meg egy parancssort, és lépjen a **Cognitive-Vision-Python\Jupyter Notebook** mappába.
1. A `pip install requests opencv-python numpy matplotlib` parancs parancssorból történő futtatásával győződjön meg arról, hogy minden szükséges kódtárral rendelkezik.
1. Indítsa el a Jupytert a `jupyter notebook` parancs parancssorból történő futtatásával.
1. A Jupyter ablakában kattintson a _Computer Vision API Example.ipynb_ elemre az oktatóanyag notebookjának megnyitásához.

## <a name="run-the-tutorial"></a>Az oktatóanyag futtatása

Ehhez a notebookhoz egy Computer Vision API-előfizetési kulcsra lesz szüksége. A regisztrációhoz nyissa meg az [Előfizetés lapot](https://azure.microsoft.com/try/cognitive-services/). A **Bejelentkezés** lapon jelentkezzen be a Microsoft-fiókjával. Ezután előfizethet, és beszerezheti az ingyenes kulcsokat. A feliratkozási folyamat befejezése után illessze be a kulcsot a notebook `Variables` tartalmazó szakaszába (az alább látható módon). Az elsődleges és a másodlagos kulcs egyaránt használható. Ügyeljen arra, hogy a kulcsot tegye idézőjelek közé, hogy a rendszer sztringként kezelje.

Fontos meggyőződnie arról is, hogy a `_region` mező egyezik az előfizetésének megfelelő régióval.

```python
# Variables
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

Az oktatóanyag futtatásakor az URL-cím megadásával vagy a helyi tárolóból is hozzáadhat elemezni kívánt képeket. A szkript megjelenít a képeket és az elemzési adatokat a notebookban.
