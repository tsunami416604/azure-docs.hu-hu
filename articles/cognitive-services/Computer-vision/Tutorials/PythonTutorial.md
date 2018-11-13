---
title: 'Oktatóanyag: Computer Vision API Python'
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Computer Vision API-t és a Pythont Jupyter-notebookokkal. Az eredményeket népszerű címtárakkal jelenítheti meg.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 11/6/2018
ms.author: kefre
ms.openlocfilehash: 16054d19c1ce6f211ebd3e2f0bbc4d152a255dda
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276967"
---
# <a name="tutorial-computer-vision-api-python"></a>Oktatóanyag: Computer Vision API Python

Ebben az oktatóanyagban megtudhatja, hogyan használható a Computer Vision API a Pythonban, és hogyan vizualizálhatók az eredmények népszerű kódtárak használatával. Az oktatóanyag futtatásához a Jupytert fogja használni. Ha többet szeretne megtudni az interaktív Jupyter-notebookok használatáról, tekintse meg a [Jupyter dokumentációját](http://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Előfeltételek

- [A Python 2.7-es vagy újabb, illetve 3.5-ös vagy újabb verziója](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) eszköz
- [Jupyter Notebook](http://jupyter.org/install) telepítve

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