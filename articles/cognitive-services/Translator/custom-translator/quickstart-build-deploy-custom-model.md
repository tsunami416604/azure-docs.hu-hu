---
title: 'Rövid útmutató: Egyéni modell létrehozása, üzembe helyezése és használata – Egyéni fordító'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban lépésről-lépésre folyamat létrehozása fordítási rendszer az egyéni fordító használatával.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: a22ae3b4b11a4eac7da147cf3ad896bbad435448
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370161"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Rövid útmutató: Egyéni modell létrehozása, üzembe helyezése és használata a fordításhoz

Ez a cikk lépésenkénti útmutatást nyújt a fordítási rendszer egyéni fordítóval történő létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

1. Az [egyéni fordítói](https://portal.customtranslator.azure.ai) portál használatához a bejelentkezéshez [microsoftos fiókra](https://signup.live.com) vagy [Azure AD-fiókra](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (az Azure-ban üzemeltetett szervezeti fiókra) van szükség.

2. A Translator Text API-előfizetés az Azure Portalon keresztül. A Translator Text API-előfizetési kulcsra lesz szüksége a munkaterülethez való társításhoz az egyéni fordítóban. Tekintse [meg, hogyan regisztrálhatok a Translator Text API-ra.](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup)

3. Ha a fentiek közül mindkettővel rendelkezik, jelentkezzen be az [egyéni fordítóportálra](https://portal.customtranslator.azure.ai) munkaterületek, projektek létrehozásához, fájlok feltöltéséhez és modellek létrehozásához/üzembe helyezéséhez.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Ha ön első alkalommal felhasználó, meg kell kérni, hogy fogadja el a Szolgáltatási feltételeket, hozzon létre egy munkaterületet, és társítsa a munkaterületet a Microsoft Translator Text API-előfizetéssel.

![](media/quickstart/terms-of-service.png)
![Munkaterület létrehozása Munkaterület](media/quickstart/create-workspace-1.png)
![létrehozása Munkaterület](media/quickstart/create-workspace-2.png)
![létrehozása](media/quickstart/create-workspace-3.png)
![Munkaterület létrehozása](media/quickstart/create-workspace-4.png)
![Munkaterület](media/quickstart/create-workspace-5.png)
![létrehozása Munkaterület létrehozása Munkaterület létrehozása Munkaterület létrehozása Munkaterület létrehozása Munkaterület létrehozása Munkaterület létrehozása Munkaterület létrehozása](media/quickstart/create-workspace-6.png)

Az Egyéni fordító portál későbbi felkeresésekor keresse meg a Beállítások lapot, ahol kezelheti munkaterületét, további munkaterületeket hozhat létre, társíthatja a Microsoft Translator Text API előfizetési kulcsát a munkaterületekkel, társtulajdonosokat vehet fel, és módosíthatja a előfizetési kulcs.

## <a name="create-a-project"></a>Projekt létrehozása

Az Egyéni fordító portál céllapján kattintson az Új projekt gombra. A párbeszédpanelen megadhatja a kívánt projektnevet, nyelvpárt és kategóriát, valamint más vonatkozó mezőket. Ezután mentse a projektet. További részletekért látogasson el a [Project létrehozása](how-to-create-project.md)című oldalra.

![Projekt létrehozása](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Dokumentumok feltöltése

Ezután töltse fel [a betanítási](training-and-model.md#training-document-type-for-custom-translator), [hangolási](training-and-model.md#tuning-document-type-for-custom-translator) és [tesztelési](training-and-model.md#testing-dataset-for-custom-translator) dokumentumkészleteket. A párhuzamos és a kombinált dokumentumokat [is](what-are-parallel-documents.md) feltöltheti. Azt is feltöltheti [szótár](what-is-dictionary.md).

A dokumentumokat a dokumentumok lapról vagy egy adott projekt oldaláról töltheti fel.

![Dokumentumok feltöltése](media/quickstart/ct-how-to-upload.png)

Dokumentumok feltöltésekor válassza ki a dokumentum típusát (betanítás, hangolás vagy tesztelés) és a nyelvpárt. Párhuzamos dokumentumok feltöltésekén további meg kell adnia egy dokumentumnevet. További részletekért látogasson el [a Dokumentum feltöltése .](how-to-upload-document.md)

## <a name="create-a-model"></a>Modell létrehozása

Az összes szükséges dokumentum feltöltésekén a következő lépés a modell létrehozása.

Válassza ki a létrehozott projektet. Ekkor megjelenik az összes feltöltött dokumentum, amely megosztja a nyelvi párját ezzel a projekttel. Jelölje ki a modellbe bevonni kívánt dokumentumokat. Kiválaszthatja [a betanítási,](training-and-model.md#training-document-type-for-custom-translator) [hangolási](training-and-model.md#tuning-document-type-for-custom-translator)és [tesztelési](training-and-model.md#testing-dataset-for-custom-translator) adatokat, vagy csak a betanítási adatokat, és hagyja, hogy az egyéni fordító automatikusan hangolási és tesztkészleteket hozzon létre a modellhez.

![Modell létrehozása](media/quickstart/ct-how-to-train.png)

Ha befejezte a kívánt dokumentumok kiválasztását, kattintson a Modell létrehozása gombra a modell létrehozásához és a betanítás megkezdéséhez. A Modellek lapon láthatja az edzés állapotát és az összes betanított modell részleteit.

További részletekért látogasson el [a Modell létrehozása című feled.](how-to-train-model.md)

## <a name="analyze-your-model"></a>A modell elemzése

Miután a képzés sikeresen befejeződött, vizsgálja meg az eredményeket. A BLEU pontszám egy mérőszám, amely a fordítás minőségét jelzi. Manuálisan is összehasonlíthatja az egyéni modellel készített fordításokat a tesztkészletben megadott fordításokkal, ha a "Teszt" fülre navigál, és a "Rendszereredmények" gombra kattint. A fordítások közül néhány manuális vizsgálata jó képet ad a rendszer által készített fordítás minőségéről. További részletekért látogasson el [a Rendszerteszt eredményei .](how-to-view-system-test-results.md)

## <a name="deploy-a-trained-model"></a>Betanított modell telepítése

Ha készen áll a betanított modell üzembe helyezésére, kattintson a "Telepítés" gombra. Projektenként egy üzembe helyezett modellt hozhat, és a központi telepítés állapotát az Állapot oszlopban tekintheti meg. További részletekért látogasson el [a Modell telepítés](how-to-view-system-test-results.md#deploy-a-model)

![Betanított modell telepítése](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Telepített modell használata

A telepített modellek a Microsoft Translator Text API V3 api-n keresztül érhetők el [a CategoryID megadásával.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) A Translator Text API-ról további információ az [API referencia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) weboldalán található.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan navigálhat az [egyéni fordító munkaterületen és kezelheti a projekteket.](workspace-and-project.md)
