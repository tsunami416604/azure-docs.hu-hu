---
title: '6. lépés: A Machine Learning webszolgáltatás eléréséhez |} Microsoft Docs'
description: 'A prediktív megoldás bemutatóért Develop 6. lépés: egy aktív Azure Machine Learning Web service eléréséhez.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 82e068ca3656b28e5e8dad19a31d6e5ff9c6f8b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Az útmutató 6. lépése: Hozzáférés az Azure Machine Learning webszolgáltatáshoz

Ez a forgatókönyv utolsó lépését az [az Azure Machine Learning a prediktív elemzési megoldás fejlesztése](walkthrough-develop-predictive-solution.md)

1. [Machine Learning-munkaterület létrehozása](walkthrough-1-create-ml-workspace.md)
2. [Meglévő adatok feltöltése](walkthrough-2-upload-data.md)
3. [Új kísérlet létrehozása](walkthrough-3-create-new-experiment.md)
4. [A modellek betanítása és kiértékelése](walkthrough-4-train-and-evaluate-models.md)
5. [A webszolgáltatás üzembe helyezése](walkthrough-5-publish-web-service.md)
6. **Hozzáférés a webszolgáltatáshoz**

- - -
A forgatókönyv az előző lépésben helyeztünk üzembe egy webszolgáltatás, amelyet a jóváírás kockázat előrejelzési modellt használ. Most felhasználók képesek adatokat küldeni és fogadni az eredményeket. 

A webszolgáltatás egy Azure webes szolgáltatás, amely kaphat, és térjen vissza az adatokat, és REST API-k az alábbi két módszer egyikével:  

* **Kérelem/válasz** – a felhasználó küld egy vagy több sornyi adatot jóváírás a szolgáltatás egy HTTP protokoll használatával, és a szolgáltatás válaszol, az eredmények egy vagy több készletet.
* **Kötegelt végrehajtás** – a felhasználó tárolja egy vagy több sort jóváírás adatok az Azure blob-, és ezután elküldi a blob helyére a szolgáltatás. A szolgáltatás az adatok a sorokat a bemeneti BLOB pontszámaihoz, tárolja az eredményeket egy másik blob és tároló URL-CÍMÉT adja vissza.  

A leggyorsabb és legegyszerűbb módja a klasszikus webes szolgáltatás eléréséhez keresztül történik a [Azure ML kérés-válasz szolgáltatás webalkalmazás](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) vagy [Azure ML kötegelt végrehajtási szolgáltatás Web App sablon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Ezeket a webes alkalmazás sablonokat hozhat létre egy egyéni webalkalmazást, hogy ismeri a webszolgáltatás bemeneti adatokat, és mi ad vissza. Mindössze annyit kell tennie a webszolgáltatás és az adatok eléréséhez, és a sablon elvégzi a többit, azaz.

A webes alkalmazás sablonok használatával kapcsolatos további információkért lásd: [egy Azure Machine Learning Web service web app sablonnal felhasználásához](consume-web-service-with-web-app-template.md).

Is létrehozhat egy egyéni alkalmazást az előírt, R, C# és Python programozási nyelvek starter kód használatával webes szolgáltatás eléréséhez.

A teljes részletei [hogyan kell használni az Azure Machine Learning Web service](consume-web-services.md).

