---
title: 'Gyors útmutató: Arcok észlelése, a képet az Azure REST API és a Python használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid, használatával az Azure Face REST API a Pythonnal arcok észlelése a képet.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 6e6f5b61f3e5e99d08e377bd5a78398a05fb4321
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577890"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>Gyors útmutató: Arcok észlelése, a képet, a Face REST API és a Python használatával

Ebben a rövid, használatával az Azure Face REST API a Pythonnal emberi arcok észlelése a képet. A parancsfájl az arcok körül keretek készíti és szélt a kép nem és életkor információkat.

![Egy férfiról és egy nőről, arcokat és korának, nemét jelenik meg a kép körül téglalap rendelkező](../media/labelled-faces-python.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 


## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.

## <a name="run-the-jupyter-notebook"></a>Futtassa a Jupyter notebook

Ez a rövid útmutató elérhető mint Jupyter notebook a [MyBinder](https://mybinder.org)-en. Indítsa el a Binder, válassza az alábbi gombra. Ezután kövesse a Notebookban lévő utasításokat.

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>További lépések

Ezután megkezdheti a Face API referenciadokumentációt tartalmaz további információt a támogatott forgatókönyveket.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
