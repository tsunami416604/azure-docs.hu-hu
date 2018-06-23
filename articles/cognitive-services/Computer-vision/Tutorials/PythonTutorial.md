---
title: Számítógép Látástechnológiai API Python oktatóanyag |} Microsoft Docs
description: Ismerje meg, hogy a számítógép Látástechnológiai API használata a Python Jupyter notebookok használatával a Microsoft kognitív szolgáltatások. A népszerű könyvtárak segítségével eredményeinek képi megjelenítése.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347535"
---
# <a name="computer-vision-api-python-tutorial"></a>Számítógép Látástechnológiai API Python oktatóanyag

Ez az oktatóanyag a Python számítógép Látástechnológiai API-t használó és a népszerű könyvtárak használatával eredményeinek képi megjelenítése. Az oktatóanyag futtatásához használt Jupyter. Ismerkedés az interaktív Jupyter notebookok beállításáról lásd: [Jupyter Documementation](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Az oktatóanyag Notebook nyithatók meg a Jupyter 

1. Keresse meg a [github oktatóanyag notebook](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Kattintson a klónozáshoz vagy töltse le az oktatóanyag zöld gombra. 
3. Nyisson meg egy parancssort, és a mappában _Cognitive-stratégiai – Python-master\Jupyter Notebook_. 
4. Futtassa a parancsot **jupyter notebook** a parancssorból. Jupyter elindítja.
5. A Jupyter ablakban kattintson a _számítógép Látástechnológiai API Example.ipynb_ az oktatóanyag jegyzetfüzet megnyitása 

### <a name="running-the-tutorial"></a>Az oktatóanyag futtatása

A notebook használatához szüksége lesz egy előfizetés kulcs a számítógép Látástechnológiai API-t. Látogasson el a [előfizetés lapján](https://azure.microsoft.com/try/cognitive-services/) regisztrálni. A "Bejelentkezés" oldalon a Microsoft-fiók használatával jelentkezzen be, és meg fogja tudni szolgáltatásra, és szabad kulcsok beolvasása. A regisztrációs folyamat befejezése után illessze be a kulcsot a változók szakaszban a notebook (az alábbiakban bemutatott). Működik az elsődleges vagy másodlagos kulcsát. Győződjön meg arról, hogy a kulcs tegye idézőjelek közé foglalt abba, hogy egy karakterláncot.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
