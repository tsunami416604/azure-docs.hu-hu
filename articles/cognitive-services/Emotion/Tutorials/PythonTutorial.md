---
title: 'Oktatóanyag: Érzelemfelismerés képeken szereplő arcokon – Emotion API, Python'
titlesuffix: Azure Cognitive Services
description: Egy Jupyter-notebook segítségével megtudhatja, hogyan használhatja az Emotion API-t a Pythonnal. Az eredményeket népszerű kódtárak használatával jelenítheti meg.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 31e346cd9a3f43f8181ebee4474ae6c9ee2cc6fc
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237857"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Oktatóanyag: Az Emotion API használata egy Jupyter-notebookkal és a Pythonnal.

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
