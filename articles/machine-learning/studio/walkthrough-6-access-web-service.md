---
title: '6. lépés: A Machine Learning webszolgáltatás eléréséhez |} A Microsoft Docs'
description: 'A fejlesztés egy prediktív megoldás bemutatója 6. lépés: egy aktív Azure Machine Learning Web service eléréséhez.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: abb94165d08a9f4e45924d71ed11a088d662339e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822157"
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Az útmutató 6. lépése: Hozzáférés az Azure Machine Learning webszolgáltatáshoz

Ez a forgatókönyv utolsó lépésében a [az Azure Machine Learning prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. **Hozzáférés a webszolgáltatáshoz**

- - -
Ez az útmutató az előző lépésben üzembe helyeztünk egy webszolgáltatás, amelyet a kredit kockázati előrejelzési modellt használ. Most, a felhasználók adatokat küldeni és fogadni az eredményeket. 

A Web service egy Azure-alapú webes szolgáltatás, amely képes fogadni, és REST API-k használatával a két módszer egyikével adatokat adja vissza:  

* **Kérés/válasz** – a felhasználó egy vagy több sornyi adatot követel küld a szolgáltatás egy HTTP-protokoll használatával, és a szolgáltatás válasza az eredményeket egy vagy több csoportjára.
* **Kötegelt végrehajtás** – a felhasználó tárolja egy vagy több adatsor kredit az Azure-ban blob-, és ezután elküldi a szolgáltatásnak a blob helyére. A szolgáltatás az adatok a sorok pontszámmodell a bemeneti BLOB tárolja az eredményeket egy másik blob és URL-címét, hogy a tároló adja vissza.  

Klasszikus webszolgáltatás eléréséhez a leggyorsabb és legegyszerűbb módja van a [Azure ML kérés-válasz szolgáltatás webalkalmazás](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) vagy [Azure ML kötegelt végrehajtási szolgáltatás webalkalmazás-sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

A webalkalmazás-sablonokat hozhat létre egyéni webalkalmazásokat, hogy ismeri a webszolgáltatás bemeneti adatokat és milyen adja vissza. Ehhez szüksége, adja meg a webszolgáltatás és az adatokhoz való hozzáférés, és a sablon végzi.

A webalkalmazás-sablonokat, további tájékoztatást lásd: [egy Azure Machine Learning Web service a webalkalmazás-sablonok felhasználása](consume-web-service-with-web-app-template.md).

Hozzáférés a webszolgáltatáshoz, R, a megadott alapszintű kód segítségével egyéni alkalmazásokat is fejleszthet C#, és a Python programozási nyelveket.

A részleteket annak [használata az Azure Machine Learning Web service](consume-web-services.md).

