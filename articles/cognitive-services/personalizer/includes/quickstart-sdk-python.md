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
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: d35f11125809167c4834b33f913d9369a6402ac0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188865"
---
[A dokumentációs](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [-csomagjához (PyPI)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | tartozó[minták](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>A rövid útmutató használata


A rövid útmutató használatának számos lépése van:

* A Azure Portal hozzon létre egy személyre szabott erőforrást
* A Azure Portal a személyre szabott erőforráshoz a **konfiguráció** lapon módosítsa a modell frissítésének gyakoriságát nagyon rövid időtartamra.
* Egy Kódszerkesztő alkalmazásban hozzon létre egy kódot tartalmazó fájlt, és szerkessze a kódot.
* A parancssorban vagy a terminálban telepítse az SDK-t a parancssorból.
* A parancssorban vagy a terminálban futtassa a fájl kódját.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>A Python-könyvtár telepítése személyre szabáshoz

Telepítse a Pythonhoz készült személyre szabott ügyféloldali kódtárat a következő paranccsal:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objektummodell

A személyre szabott ügyfél egy [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

Ha szeretné megkérdezni a tartalom egyetlen legjobb elemét, hozzon létre egy [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python), majd továbbítsa azt az ügyfélnek. Rangsor metódusa. A Rank metódus egy RankResponse ad vissza.

A jutalom pontszámának személyre szabásához állítsa be az esemény AZONOSÍTÓját és a jutalom pontszámát (érték), amelyet a EventOperations osztály [jutalmazási](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) metódusának küld.

A jutalom meghatározása ebben a rövid útmutatóban triviális. Éles rendszerekben annak meghatározása, hogy milyen hatással van a [jutalom pontszáma](../concept-rewards.md) , és hogy mekkora mértékben lehet egy összetett folyamat, dönthet úgy, hogy idővel változhat. Ennek az egyik elsődleges tervezési döntésnek kell lennie a személyre szabott architektúrában.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Python személyre szabott ügyféloldali kódtár használatával:

* [Személyre szabott ügyfél létrehozása](#create-a-personalizer-client)
* [Rangsor API](#request-the-best-action)
* [Jutalom API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást az előnyben részesített `sample.py`szerkesztőben vagy a nevű ide-ben.

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a **sample.py** fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következőket:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabott erőforrás-információk hozzáadása

Hozzon létre változókat az erőforráshoz tartozó Azure-kulcshoz és végponthoz a `PERSONALIZER_RESOURCE_KEY` ( `PERSONALIZER_RESOURCE_ENDPOINT`z) és a (z) nevű környezeti változók alapján. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusokat később a rövid útmutatóban fogja létrehozni.

Az erőforrás neve a végpont URL-címének része `https://<your-resource-name>.api.cognitive.microsoft.com/`:.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabott ügyfél létrehozása

Ezután hozzon létre egy metódust, amely egy személyre szabott ügyfelet ad vissza. A metódus paramétere a `PERSONALIZER_RESOURCE_ENDPOINT` és a ApiKey. `PERSONALIZER_RESOURCE_KEY`

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Tevékenységekként jelölt tartalmak beolvasása

A műveletek azokat a tartalmi beállításokat jelentik, amelyeknek a személyre szabásával ki kell választania a legjobb tartalmi elemet. Adja hozzá a következő metódusokat a program osztályhoz a műveletek és a hozzájuk tartozó funkciók megjelenítéséhez.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A személyre szabott tanulási hurok a [Range](#request-the-best-action) és a [jutalmazási](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban a tartalom személyre szabásához a rangsorban megjelenő minden egyes hívást egy jutalmazási hívás követ, amely alapján eldöntheti, hogy milyen jól teljesíti a szolgáltatást.

A következő kód hurkokat mutat be a felhasználónak a parancssorban való megadására, az információknak a személyre szabására való kiválasztásához, az ügyfélnek a listából való kiválasztásához, majd a személyre szabási jelzés elküldéséhez, hogy a szolgáltatás milyen jól van kiválasztva.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Adja hozzá a következő metódusokat, amelyek [megkapják a tartalom választási lehetőségeit](#get-content-choices-represented-as-actions)a kódlap futtatása előtt:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>A legjobb művelet kérése


A rangsorolási kérelem teljesítéséhez a program megkéri a felhasználó beállításait, hogy hozzon létre egy `currentContent` tartalom-választási lehetőséget. A folyamat létrehozhat olyan tartalmat, amely kizárható a műveletekből, `excludeActions`a következő módon:. A válasz fogadásához a Rank kérelemnek szüksége van a műveletekre és azok szolgáltatásaira, a LicenseManager CurrentContext szolgáltatásaira, a excludeActions és egy egyedi esemény-AZONOSÍTÓra.

Ez a rövid útmutató a napszak és a felhasználói élelmiszer-beállítások egyszerű kontextusát tartalmazza. Az éles rendszerekben a [műveletek és szolgáltatások](../concepts-features.md) meghatározása és [értékelése](../concept-feature-evaluation.md) nem triviális kérdés lehet.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése


Ahhoz, hogy a jutalom pontszáma a jutalom iránti kérelemben legyen elküldve, a program beolvassa a felhasználó kijelölését a parancssorból, hozzárendel egy numerikus értéket a kijelöléshez, majd elküldi az egyedi eseményazonosító és a jutalom pontszámát a jutalmazási API-nak megfelelő numerikus értékként.

Ez a rövid útmutató egy egyszerű számot rendel hozzá a jutalom pontszámához, vagy nulla vagy 1 értéket. Az éles rendszerekben az adott igényektől függően nem triviális kérdés lehet annak meghatározása, hogy mikor és mit kell elküldeni a [jutalmazási](../concept-rewards.md) hívásnak.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a Pythonból az alkalmazás könyvtárából.

```console
python sample.py
```

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)