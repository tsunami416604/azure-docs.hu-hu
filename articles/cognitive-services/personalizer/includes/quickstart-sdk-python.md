---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: fe800280a7a652b5d9a397a21a5b0a66b40af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122921"
---
[Referenciadokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [(pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [minták](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>A rövid útmutató használata


A rövid útmutató számos lépésből áll:

* Az Azure Portalon hozzon létre egy personalizer erőforrást
* Az Azure Portalon a Personalizer erőforrás, a **konfiguráció lapon** módosítsa a modell frissítési gyakoriságát egy nagyon rövid időközre
* Kódszerkesztőben hozzon létre egy kódfájlt, és szerkesztse a kódfájlt
* A parancssorba vagy a terminálba telepítse az SDK-t a parancssorból
* A parancssorban vagy a terminálon futtassa a kódfájlt

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>A Python-kódtár telepítése a Personalizer számára

Telepítse a Personalizer python-alapú ügyfélkönyvtárat a következő paranccsal:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objektummodell

A Personalizer ügyfél egy [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) objektum, amely hitelesíti magát az Azure-ban a Microsoft.Rest.ServiceClientCredentials használatával, amely tartalmazza a kulcsot.

A tartalom egyetlen legjobb elemének kéréséhez hozzon létre egy [RankRequest-et,](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)majd adja át az ügyfélnek. Rang módszer. A Rank metódus RankResponse függvényt ad vissza.

Ha jutalompontszámot szeretne küldeni a Personalizer számára, állítsa be az eseményazonosítót és a jutalompontszámot (értéket), amelyet az EventOperations osztály [Jutalom](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) metódusába kell küldenie.

A jutalom meghatározása ebben a rövid útmutatóban triviális. A termelési rendszerben annak meghatározása, hogy milyen hatással van a [jutalompontszám,](../concept-rewards.md) és mennyi lehet egy összetett folyamat, hogy ön dönthet úgy, hogy idővel megváltozik. Ez az egyik elsődleges tervezési döntéseket a Personalizer architektúra.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan teheti meg a következőket a Python-hoz való Personalizer ügyfélkódtárban:

* [Személyre szabó ügyfél létrehozása](#create-a-personalizer-client)
* [Rang API](#request-the-best-action)
* [Jutalom API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Új python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást `sample.py`az előnyben részesített szerkesztőben vagy ide nevű.

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárából nyissa meg a **sample.py** fájlt a kívánt szerkesztőben vagy IDE-ben. Adja hozzá a következőket:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabászati erőforrásadatok hozzáadása

Hozzon létre változókat az erőforrás Azure-kulcsához és végpontjából a nevű `PERSONALIZER_RESOURCE_KEY` és `PERSONALIZER_RESOURCE_ENDPOINT`a környezeti változókból lekért végponthoz. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia és újra kell töltenie a változó eléréséhez. A módszerek a rövid útmutató későbbi részében jönnek létre.

Az erőforrás neve a végpont URL-címének része: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabó ügyfél létrehozása

Ezután hozzon létre egy módszert a Personalizer ügyfél visszaadására. A metódus paramétere `PERSONALIZER_RESOURCE_ENDPOINT` a, az ApiKey pedig a `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>A tartalomválasztási lehetőségek műveletként való ábrázolása

A műveletek azt a tartalomválasztási lehetőséget jelölik, amelyből azt szeretné, hogy a Personalizer válassza ki a legjobb tartalomelemet. Adja hozzá a következő módszereket a Program osztályhoz a műveletek készletének és jellemzőiknek ábrázolására.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A Personalizer tanulási hurok a [Rang](#request-the-best-action) és [jutalom](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban minden rangsorolási hívás, a tartalom személyre szabása, amelyet jutalomhívás követ, hogy elmondja a Personalizer-nak, hogy milyen jól teljesített a szolgáltatás.

A következő kód egy cikluson keresztül halad, amikor megkérdezi a felhasználótól a preferenciáikat a parancssorban, elküldi ezt az információt a Personalizer-nek, hogy válassza ki a legjobb műveletet, bemutatva a választást az ügyfélnek, hogy válasszon a lista közül, majd jutalmat küld jön a A személyre szabó jelzi, hogy milyen jól teljesített a szolgáltatás a kiválasztásban.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

A kódfájl futtatása előtt adja hozzá a következő módszereket, amelyek [a tartalommal kapcsolatos választási lehetőségeket kapják:](#get-content-choices-represented-as-actions)

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>A legjobb művelet kérése


A Rangsorolás kérés teljesítéséhez a program megkéri a `currentContent` felhasználó preferenciáit, hogy hozzanak létre egy tartalomválasztási lehetőséget. A folyamat létrehozhat olyan tartalmat, amelyet kizár a műveletekből, és a néven `excludeActions`jelenik meg. A Rang kérés nek szüksége van a műveletek és azok jellemzői, currentContext funkciók, excludeActions, és egy egyedi eseményazonosító, a válasz fogadásához.

Ez a rövid útmutató egyszerű környezetben funkciók a napszak és a felhasználó élelmiszer-preferencia. A termelési rendszerekben [evaluating](../concept-feature-evaluation.md) [az intézkedések és funkciók](../concepts-features.md) meghatározása és értékelése nem triviális kérdés lehet.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése


A jutalompontszám beküldéséhez a program lekéri a felhasználó kiválasztását a parancssorból, numerikus értéket rendel a kijelöléshez, majd elküldi az egyedi eseményazonosítót és a jutalompontszámot numerikus értékként a Jutalom API-hoz.

Ez a rövid útmutató egy egyszerű számot rendel jutalompontszámként, nullát vagy 1-et. A termelési rendszerekben annak meghatározása, hogy mikor és mit kell küldeni a [Jutalom](../concept-rewards.md) hívás lehet egy nem triviális kérdés, attól függően, hogy az Ön egyedi igényeinek.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a python az alkalmazás könyvtárából.

```console
python sample.py
```

![A rövid útmutató program feltesz néhány kérdést a felhasználói beállítások , más néven szolgáltatások összegyűjtéséhez, majd biztosítja a legjobb műveletet.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)