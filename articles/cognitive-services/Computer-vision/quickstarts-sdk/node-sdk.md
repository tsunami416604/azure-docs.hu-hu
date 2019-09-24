---
title: 'Gyors útmutató: A Node. js-hez készült ügyféloldali kódtár Computer Vision | Microsoft Docs'
description: Ismerkedés a Computer Vision ügyféloldali kódtár a Node. js-hez...
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/18/2019
ms.author: pafarley
ms.openlocfilehash: bcce9c53e2618f9501dde63905fb834bce7985ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204505"
---
# <a name="quickstart-computer-vision-client-library-for-nodejs"></a>Gyors útmutató: A Node. js-hez készült ügyféloldali kódtár Computer Vision

Ismerkedés a Node. js-hez készült Computer Vision ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. 

A Node. js-hez készült Computer Vision ügyféloldali kódtár a következőhöz használható:

* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

[A dokumentációs](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [-csomagjához (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-computervision) | tartozó[minták](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org/) jelenlegi verziója

## <a name="setting-up"></a>Beállítás

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást Computer Vision a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes hét napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

Miután megszerezte a kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `COMPUTER_VISION_SUBSCRIPTION_KEY` kulcs `COMPUTER_VISION_ENDPOINT` és a végpont URL-címéhez, valamint a nevét.
 
### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy `package.json` Node-alkalmazás fájlhoz való létrehozásához. 

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és `azure-cognitiveservices-computervision` a NPM csomagokat:

```console
npm install azure-cognitiveservices-computervision ms-rest-azure
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

### <a name="prepare-the-nodejs-script"></a>A Node. js-szkript előkészítése

Hozzon létre egy új fájlt, az *index. js*fájlt, és nyissa meg egy szövegszerkesztőben. Adja hozzá a következő importálási utasításokat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Ezután Definiáljon egy függvényt `computerVision` , és deklaráljon egy aszinkron sorozatot az elsődleges függvény és a visszahívási függvénnyel. Adja hozzá a rövid útmutató kódját az elsődleges függvényhez, és hívja `computerVision` meg a szkript alján.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Computer Vision Node. js SDK főbb funkcióit kezelik.

|Name (Név)|Leírás|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Ez az enumerálás a képelemzés különböző típusait határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a **VisualFeatureTypes** -értékek készletét. |

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Node. js-hez készült Computer Vision ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) objektumot a kulccsal és a végponttal, és használja egy [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) objektum létrehozásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Rendszerkép elemzése

Az ebben a szakaszban található kód elemzi a távoli képeket a különböző vizualizációs funkciók kinyeréséhez. Ezeket a műveleteket az **analyzeImage** metódusának részeként is elvégezheti, vagy meghívhatja őket az egyes metódusok használatával. A részletekért tekintse meg a [dokumentációt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) .

> [!NOTE]
> Elemezheti a helyi rendszerképet is. A helyi rendszerképeket érintő forgatókönyvek a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) találhatók.

### <a name="get-image-description"></a>Rendszerkép lekérése – Leírás

A következő kód beolvassa a rendszerképhez létrehozott feliratok listáját. További részletekért tekintse meg a [képek leírása](../concept-describing-images.md) című témakört.

Először határozza meg az elemezni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Ezután adja hozzá a következő kódot a rendszerkép leírásának beszerzéséhez, és nyomtassa ki a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Rendszerkép beolvasása kategória

A következő kód lekéri a rendszerkép észlelt kategóriáját. További részletekért tekintse meg a [képek kategorizálása](../concept-categorizing-images.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

A segítő függvény `formatCategories`megadása:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Képcímkék beolvasása

A következő kód beolvassa az észlelt címkék készletét a képen. További részletekért tekintse meg a [tartalom címkéit](../concept-tagging-images.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

A segítő függvény `formatTags`megadása:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a rendszerképben szereplő általános objektumokat, és kiírja azokat a konzolra. További részletekért lásd az [objektum észlelése](../concept-object-detection.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

A segítő függvény `formatRectObjects`megadása:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a rendszerképben, és kinyomtatja őket a konzolra. További részletekért lásd a [márka észlelését](../concept-brand-detection.md) ismertető témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód az észlelt arcokat adja vissza a képen a téglalap koordinátáival, majd a Face attribútumok elemet. További részletekért tekintse meg az [Arcfelismerés](../concept-detecting-faces.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

A segítő függvény `formatRectFaces`megadása:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-or-racy-content"></a>Felnőtt vagy zamatos tartalom észlelése

A következő kód kinyomtatja a képen a felnőtt vagy a zamatos tartalom észlelt jelenlétét. További részletekért tekintse meg a [felnőtt és a zamatos tartalmat](../concept-detecting-adult-content.md) .

Adja meg a használni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)] 

Ezután adja hozzá a következő kódot a felnőtt tartalom észleléséhez, és nyomtassa ki az eredményeket a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)] 

### <a name="get-image-color-scheme"></a>Rendszerképek színsémájának beolvasása

A következő kód az észlelt színattribútumokat nyomtatja ki a képen, például a domináns színeket és a kiejtés színét. További részletekért lásd: [Színsémák](../concept-detecting-color-schemes.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Adja meg a segítő `printColorScheme` függvényt a színséma adatainak a konzolra való kinyomtatásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Tartományhoz tartozó tartalom beolvasása

A Computer Vision speciális modellt használhat a képek további elemzéséhez. További részletekért tekintse meg a [tartományra vonatkozó tartalmat](../concept-detecting-domain-content.md) . 

Először határozza meg az elemezni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

A következő kód az észlelt tereptárgyak adatait elemzi a képen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Adja meg a segítő `formatRectDomain` függvényt az észlelt tereptárgyak hely adatainak elemzéséhez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>A rendszerkép típusának beolvasása

A következő kód a képtípussal&mdash;kapcsolatos információkat jeleníti meg, legyen szó ClipArt vagy vonalas rajzolásról.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

A segítő függvény `describeType`megadása:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

A Computer Vision a képen látható szöveget olvashatja, és átalakíthatja a karakteres adatfolyamba.

> [!NOTE]
> Helyi rendszerképből is beolvashat szöveget. A helyi rendszerképeket érintő forgatókönyvek a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) találhatók.

### <a name="set-up-test-images"></a>Tesztelési lemezképek beállítása

Mentse el a képek URL-címét, amelyből szöveget szeretne kinyerni.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Az felismerő API meghívása

Adja hozzá az alábbi kódot, amely meghívja a `recognizeText` függvényt az adott lemezképekhez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Adja meg `recognizeText` a függvényt. Ez meghívja a **recognizeText** metódust az ügyfél objektumon, amely egy műveleti azonosítót ad vissza, és egy aszinkron folyamatot indít el a rendszerkép tartalmának olvasásához. Ezután a műveleti azonosító használatával vizsgálja meg a műveletet egy másodperces időközönként, amíg vissza nem adja az eredményeket. Ezután a kinyert eredményeket adja vissza.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Ezután adja meg a segítő függvényt `printRecText`, amely egy felismerési művelet eredményét kiírja a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást `node` a gyors üzembe helyezési fájlban található paranccsal.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Computer Vision API-hivatkozás (node. js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Mi a Computer Vision API?](../Home.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)található.