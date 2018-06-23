---
title: Érzelemfelismerési API Python oktatóanyag |} Microsoft Docs
description: Jupyter notebook használatával megtudhatja, hogyan kognitív szolgáltatások Érzelemfelismerési API-t használó Python. Az eredmények megjelenítése népszerű könyvtárak használatával.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347550"
---
# <a name="emotion-api-using-python-tutorial"></a>Érzelemfelismerési API Python-oktatóprogram

> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Hogy megkönnyítse a Érzelemfelismerési API használatába, az alábbi hivatkozásokon elérhető Jupyter notebook bemutatja, hogyan történő API a Python és hogyan kell a népszerű könyvtárak használatával eredményeinek képi megjelenítése. 

[A Githubon notebook csatolása](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>A Jupyter Notebook használatával

A notebook interaktív használatához szüksége lesz klónozza, és futtassa azt Jupyter. Annak megismerése, hogyan lásson interaktív Jupyter notebookok, kövesse http://jupyter.readthedocs.org/en/latest/install.html. 

A notebook használatához szüksége lesz egy előfizetés kulcs a Érzelemfelismerési API-t. Látogasson el a [előfizetés lapján](https://azure.microsoft.com/try/cognitive-services/) regisztrálni. A "Bejelentkezés" oldalon a Microsoft-fiók használatával jelentkezzen be, és meg fogja tudni szolgáltatásra, és szabad kulcsok beolvasása. A regisztrációs folyamat befejezése után illessze be a kulcsot a változók szakaszban alább látható. Működik az elsődleges vagy másodlagos kulcsát.

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
