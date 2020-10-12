---
title: 'Webszolgáltatás bemenete/kimenete: modul leírása'
description: Tudnivalók a Azure Machine Learning Designer webszolgáltatási moduljairól
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: a62f8aee0bd0a0d2b7009a48e9d5f00ea3c5155f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90883214"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Webszolgáltatás bemeneti és webszolgáltatási kimeneti moduljai

Ez a cikk a Azure Machine Learning Designer webszolgáltatás bemeneti és webszolgáltatási kimeneti moduljait ismerteti.

A webszolgáltatás bemeneti modulja csak a **DataFrameDirectory**típusú bemeneti porthoz tud csatlakozni. A webszolgáltatás kimeneti modulja csak **DataFrameDirectory**típusú kimeneti portról csatlakoztatható. A modul fájában található két modult a **webszolgáltatás** kategóriájában találja. 

A webszolgáltatás bemeneti modulja azt jelzi, hogy a felhasználói adatok hol jutnak be a folyamatba. A webszolgáltatás kimeneti modulja azt jelzi, hogy a rendszer hol adja vissza a felhasználói adatokat egy valós idejű következtetési folyamat során.

## <a name="how-to-use-web-service-input-and-output"></a>A webszolgáltatás bemenetének és kimenetének használata

Amikor [valós idejű következtetési folyamatot hoz létre](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline) a betanítási folyamatból, a webszolgáltatás bemeneti és webszolgáltatás-kimeneti moduljai automatikusan hozzáadódnak a folyamathoz, ahol a felhasználói adatok belépnek a folyamatba, és az adatok visszaadása történik. 

> [!NOTE]
> A valós idejű következtetések automatikus generálása egy szabály-alapú, legalkalmasabb folyamat. Nincs garancia a pontosságra. 

Manuálisan is hozzáadhatja vagy eltávolíthatja a webszolgáltatás bemeneti és webszolgáltatás-kimeneti modulját, hogy megfeleljen az igényeinek. Győződjön meg arról, hogy a valós idejű következtetési folyamatnak legalább egy webszolgáltatás-bemeneti modulja és egy webszolgáltatás-kimeneti modulja van. Ha több webszolgáltatás-bemeneti vagy webszolgáltatás-kimeneti modulja van, ügyeljen arra, hogy egyedi névvel rendelkezzenek. A nevet megadhatja a modul jobb oldali paneljén.

A valós idejű következtetéseket manuálisan is létrehozhatja a webszolgáltatás bemeneti és webszolgáltatás-kimeneti moduljainak a nem elküldött folyamathoz való hozzáadásával.

> [!NOTE]
> A folyamat típusát a rendszer az első elküldésekor határozza meg. A beküldés előtt ügyeljen arra, hogy a webszolgáltatás bemeneti és webszolgáltatási kimeneti moduljait is hozzáadja.

Az alábbi példa bemutatja, hogyan hozhat létre manuálisan valós idejű következtetési folyamatot a Python parancsfájl végrehajtása modulból. 

![Példa](media/module/web-service-input-output-example.png)
   
A folyamat elküldése és a Futtatás sikeres befejezése után üzembe helyezheti a valós idejű végpontot.
   
> [!NOTE]
>  Az előző példában az **adatok manuális megadása** a webszolgáltatás bemeneti adatsémáját adja meg, és a valós idejű végpont üzembe helyezéséhez szükséges. Általában mindig csatlakoztatnia kell egy modult vagy adatkészletet ahhoz a porthoz, ahol a **webszolgáltatás bemenete** csatlakozik az Adatséma biztosításához.
   
## <a name="next-steps"></a>Következő lépések
További információ [a valós idejű végpont üzembe helyezéséről](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .