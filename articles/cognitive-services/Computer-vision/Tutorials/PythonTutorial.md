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
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 59e88ecb253bc6da803ddf34a4a02229b99e259d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981711"
---
# <a name="tutorial-computer-vision-api-python"></a>Oktatóanyag: Computer Vision API Python

Ebben az oktatóanyagban megtudhatja, hogyan használhatja a Computer Vision API-t a Pythonban, valamint hogyan jelenítheti meg az eredményeket népszerű címtárakkal. Az oktatóanyag futtatásához használja a Jupytert. Ha többet szeretne megtudni az interaktív Jupyter-notebookok használatáról, tekintse meg a[Jupyter dokumentációját](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Az oktatóanyag notebokjának megnyitása a Jupyterben 

1. Keresse meg az [oktatóanyag notebookját a GitHubon](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Az oktatóanyag klónozásához vagy letöltéséhez kattintson a zöld gombra. 
3. Nyisson meg egy parancssort, és lépjen a _Cognitive-Vision-Python-master\Jupyter Notebook_ mappába. 
4. Futtassa a **jupyter notebook** parancsot a parancssorból. Ekkor elindul a Jupyter.
5. A Jupyter ablakában kattintson a _Computer Vision API Example.ipynb_ elemre az oktatóanyag notebookjának megnyitásához 

### <a name="running-the-tutorial"></a>Az oktatóanyag futtatása

Ehhez a notebookhoz egy Computer Vision API-előfizetési kulcsra lesz szüksége. A regisztrációhoz nyissa meg az [Előfizetés lapot](https://azure.microsoft.com/try/cognitive-services/). A bejelentkezési oldalon jelentkezzen be Microsoft-fiókjával, így előfizethet és lekérheti az ingyenes kulcsokat. A regisztrációs folyamat befejezését követően illessze be a kulcsot a notebook változókat tartalmazó szakaszába (ahogyan lent láthatja). Az elsődleges vagy a másodlagos kulcsnak működnie kell. Győződjön meg róla, hogy idézőjelek közé tegye a kulcsot, hogy a rendszer sztringként értelmezze.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
