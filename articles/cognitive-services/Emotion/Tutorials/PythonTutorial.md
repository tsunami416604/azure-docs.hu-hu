---
title: 'Oktatóanyag: Érzelemfelismerés a egy oldallal a kép – Emotion API, Python'
titlesuffix: Azure Cognitive Services
description: Egy Jupyter-notebook segítségével megtudhatja, hogyan használhatja az Emotion API-t a Pythonnal. Az eredményeket népszerű kódtárak használatával jelenítheti meg.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 9e03e5d534fa741def2010a21120edc3d7169964
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237467"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Oktatóanyag: Az Emotion API használata egy Jupyter notebookot, és a Python.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként általánosan elérhető. 

Az Emotion API-val való ismerkedés megkönnyítése érdekében az alább hivatkozott Jupyter-notebook bemutatja, hogyan használható az API a Pythonban, és hogyan jelenítheti meg az eredményeket népszerű kódtárak használatával.

[Hivatkozás a GitHub-on található notebookra](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>A Jupyter-notebook használata

Az interaktív használathoz klónoznia kell a notebookot, és futtatnia kell a Jupyterben. Az interaktív Jupyter-notebookok használatát a http://jupyter.readthedocs.org/en/latest/install.html címen található utasításokat követve sajátíthatja el.

A notebook használatához szüksége lesz egy előfizetői azonosítóra az Emotion API-hoz. A regisztrációhoz nyissa meg az [Előfizetés lapot](https://azure.microsoft.com/try/cognitive-services/). A „Bejelentkezés” lapon jelentkezzen be Microsoft-fiókjával. Ezt követően előfizethet, és beszerezheti az ingyenes kulcsokat. A regisztrációs folyamat befejezése után illessze be a kapott kulcsot az alább látható „variables” (változók) szakaszba. Az elsődleges és a másodlagos kulcs közül csak az egyik használható.

```
Python Example

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
