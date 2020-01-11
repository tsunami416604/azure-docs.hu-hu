---
title: 'Gyors útmutató: személyre szabott ügyféloldali kódtár a Pythonhoz'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az első lépéseket a Python személyre szabott ügyféloldali kódtára egy tanulási hurok használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 01/09/2020
ms.author: diberry
ms.openlocfilehash: 0073b03cd06bcf5a6e0733ef1b72061e72c3afe2
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860299"
---
# <a name="quickstart-personalizer-client-library-for-python"></a>Gyors útmutató: személyre szabott ügyféloldali kódtár a Pythonhoz

Személyre szabott tartalom megjelenítése ebben a Python rövid útmutatóban a személyre szabási szolgáltatással.

Ismerkedjen meg a Python személyre szabott ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

 * A személyre szabási műveletek listájának rangsorolása.
 * A legjobb rangsorolt művelet sikerességét jelző jelentés jutalmazási pontszáma.

[Dokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer)| [csomag (PyPI)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [minták](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="using-this-quickstart"></a>A rövid útmutató használata


A rövid útmutató használatának számos lépése van:

* A Azure Portal hozzon létre egy személyre szabott erőforrást
* A Azure Portal a személyre szabott erőforráshoz a **konfiguráció** lapon módosítsa a modell frissítési gyakoriságát
* Egy Kódszerkesztő alkalmazásban hozzon létre egy kódot tartalmazó fájlt, és szerkessze a kódot.
* A parancssorban vagy a terminálban telepítse az SDK-t a parancssorból.
* A parancssorban vagy a terminálban futtassa a fájl kódját.


## <a name="create-a-personalizer-azure-resource"></a>Személyre szabott Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a személyre szabáshoz a [Azure Portal](https://portal.azure.com/) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával a helyi gépen. További részletekért tekintse meg, [hogyan hozhat létre Cognitive Services-erőforrást a Azure Portal használatával](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) . További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services) ingyenes 7 napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

A próbaverziós előfizetésből vagy erőforrásból származó kulcs lekérése után hozzon létre két [környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` az erőforrás-kulcshoz.
* `PERSONALIZER_ENDPOINT` az erőforrás-végponthoz.

A Azure Portal mind a kulcs, mind a végpont értéke elérhető a **gyors üzembe helyezési** lapon.


## <a name="install-the-python-library-for-personalizer"></a>A Python-könyvtár telepítése személyre szabáshoz

Telepítse a Pythonhoz készült személyre szabott ügyféloldali kódtárat a következő paranccsal:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="change-the-model-update-frequency"></a>A modell frissítési gyakoriságának módosítása

A Azure Portal a **konfiguráció** lapon a személyre szabott erőforrásban módosítsa a **modell frissítésének gyakoriságát** 10 másodpercre. Ez gyorsan betanítja a szolgáltatást, így láthatja, hogy az egyes iterációk legfelső szintű művelete hogyan változik.

![Modell frissítési gyakoriságának módosítása](./media/settings/configure-model-update-frequency-settings.png)

Ha a rendszer először létrehoz egy személyre szabott hurkot, nem áll rendelkezésre modell, mert nem áll rendelkezésre jutalom API-hívás a betanításhoz. A rangsorban megjelenő hívások az egyes elemek esetében azonos valószínűségeket adnak vissza. Az alkalmazásnak mindig a RewardActionId kimenetével kell rangsorolnia a tartalmat.

## <a name="object-model"></a>Objektummodell

A személyre szabott ügyfél egy PersonalizerClient objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

A tartalom rangjának megadásához hozzon létre egy RankRequest, majd továbbítsa azt az ügyfélnek. Rangsor metódusa. A Rank metódus egy RankResponse ad vissza, amely a rangsorolt tartalmat tartalmazza.

A személyre szabott jutalom elküldéséhez hozzon létre egy RewardRequest, majd továbbítsa azt az ügyfélnek. Jutalmazási módszer.

A jutalom meghatározása ebben a rövid útmutatóban triviális. Éles rendszerekben annak meghatározása, hogy milyen hatással van a [jutalom pontszáma](concept-rewards.md) , és hogy mekkora mértékben lehet egy összetett folyamat, dönthet úgy, hogy idővel változhat. Ennek az egyik elsődleges tervezési döntésnek kell lennie a személyre szabott architektúrában.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Python személyre szabott ügyféloldali kódtár használatával:

* [Személyre szabott ügyfél létrehozása](#create-a-personalizer-client)
* [Rangsor igénylése](#request-a-rank)
* [Jutalom küldése](#send-a-reward)

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást az előnyben részesített szerkesztőben vagy `sample.py`nevű IDE.

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a **sample.py** fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következőket:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Személyre szabott erőforrás-információk hozzáadása

Hozzon létre változókat az erőforráshoz tartozó Azure-kulcshoz és végponthoz a `PERSONALIZER_RESOURCE_KEY` és `PERSONALIZER_RESOURCE_ENDPOINT`nevű környezeti változókból. Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusokat később a rövid útmutatóban fogja létrehozni.

Az erőforrás neve a végpont URL-címének része: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Személyre szabott ügyfél létrehozása

Ezután hozzon létre egy metódust, amely egy személyre szabott ügyfelet ad vissza. A metódus paraméterének értéke a `PERSONALIZER_RESOURCE_ENDPOINT`, a ApiKey pedig a `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Tevékenységekként jelölt tartalmak beolvasása

A műveletek a személyre szabáshoz használni kívánt tartalmi beállításokat jelölik. Adja hozzá a következő metódusokat a felhasználó bemenetének lekéréséhez a parancssorból a nap időpontjában és az aktuális élelmiszer-preferencia alapján.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>A tanulási hurok létrehozása

A személyre szabott tanulási hurok a [Range](#request-a-rank) és a [jutalmazási](#send-a-reward) hívások ciklusa. Ebben a rövid útmutatóban a tartalom személyre szabásához a rangsorban megjelenő minden egyes hívást egy jutalmazási hívás követ, amely azt jelzi, hogy a szolgáltatás milyen jól rangsorolja a tartalmat.

A következő kód hurkokat mutat be a felhasználónak a parancssorban való megadására, az információknak a személyre szabására való átadására, a rangsorolt kijelölés kiválasztására az ügyfél számára, hogy kiválassza a listában, majd a személyre szabott jutalom küldését annak jelzése, hogy a szolgáltatás mennyire rangsorolta a kijelölést.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Tekintse meg alaposabban az alábbi részekben ismertetett rangot és jutalmazási hívásokat.

Adja hozzá a következő metódusokat, amelyek [megkapják a tartalom választási lehetőségeit](#get-content-choices-represented-as-actions)a kódlap futtatása előtt:

* get_user_preference
* get_user_timeofday
* get_actions

## <a name="request-a-rank"></a>Rangsor igénylése

A rangsorolási kérelem elvégzéséhez a program megkéri a felhasználó beállításait, hogy hozzon létre egy `currentContent` a tartalom megválasztásához. A folyamat olyan tartalmat hozhat létre, amely kizárható a rangsorból, `excludeActions`ként jelenik meg. A rangsorolási kérelemnek szüksége van a műveletekre, a LicenseManager CurrentContext, a excludeActions és az egyedi rangsorolt esemény-AZONOSÍTÓra (GUID) a rangsorolt válasz fogadásához.

Ez a rövid útmutató a napszak és a felhasználói élelmiszer-beállítások egyszerű kontextusát tartalmazza. Az éles rendszerekben a [műveletek és szolgáltatások](concepts-features.md) meghatározása és [értékelése](concept-feature-evaluation.md) nem triviális kérdés lehet.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Jutalom küldése

A jutalmazási kérelem elvégzéséhez a program beolvassa a felhasználó kijelölését a parancssorból, hozzárendel egy numerikus értéket az egyes kijelölésekhez, majd elküldi az egyedi rangú esemény AZONOSÍTÓját és a numerikus értéket a jutalmazási metódusnak.

Ez a rövid útmutató egy egyszerű számot rendel hozzá jutalomként, akár nulla, akár 1. Az éles rendszerekben az adott igényektől függően nem triviális kérdés lehet annak meghatározása, hogy mikor és mit kell elküldeni a [jutalmazási](concept-rewards.md) hívásnak.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>A program futtatása

Futtassa az alkalmazást a Pythonból az alkalmazás könyvtárából.

```console
python sample.py
```

![A gyors üzembe helyezési program néhány kérdést tesz fel a felhasználói preferenciák (más néven funkciók) összegyűjtésére, majd megadja a legfontosabb műveletet.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[A megszemélyesítő működése](how-personalizer-works.md)

* [Mi az a személyre szabott?](what-is-personalizer.md)
* [Hol használható a személyre szabás?](where-can-you-use-personalizer.md)
* [hibaelhárítással](troubleshooting.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)található.
