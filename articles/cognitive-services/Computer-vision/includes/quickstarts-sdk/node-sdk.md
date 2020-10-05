---
title: 'Gyors útmutató: Computer Vision ügyféloldali kódtár a Node.js'
description: Ismerkedjen meg az Computer Vision ügyféloldali függvénytárával Node.js ezzel a rövid útmutatóval
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: d0e94066c6e8ab60a52761898e49bdf11997a062
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91309819"
---
<a name="HOLTop"></a>

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Példák](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Node.js](https://nodejs.org/) aktuális verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* [Hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és a végpont URL-címéhez, illetve a nevet `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` .

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és a `@azure/cognitiveservices-computervision` NPM csomagokat:

```console
npm install @azure/cognitiveservices-computervision
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

### <a name="prepare-the-nodejs-script"></a>A Node.js parancsfájl előkészítése

Hozzon létre egy új fájlt, *index.js*, majd nyissa meg egy szövegszerkesztőben. Adja hozzá a következő importálási utasításokat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Ezután Definiáljon egy függvényt, `computerVision` és deklaráljon egy aszinkron sorozatot az elsődleges függvény és a visszahívási függvénnyel. Adja hozzá a rövid útmutató kódját az elsődleges függvényhez, és hívja meg a `computerVision` szkript alján.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Computer Vision Node.js SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Ez az enumerálás a képelemzés különböző típusait határozza meg, amelyeket szabványos elemzési műveletekben lehet elvégezni. Az igényeinek megfelelően adja meg a **VisualFeatureTypes** -értékek készletét. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Node.js Computer Vision ügyféloldali kódtár használatával:

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

A következő kód beolvassa a rendszerképhez létrehozott feliratok listáját. További részletekért tekintse meg a [képek leírása](../../concept-describing-images.md) című témakört.

Először határozza meg az elemezni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Ezután adja hozzá a következő kódot a rendszerkép leírásának beszerzéséhez, és nyomtassa ki a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Rendszerkép beolvasása kategória

A következő kód lekéri a rendszerkép észlelt kategóriáját. További részletekért tekintse meg a [képek kategorizálása](../../concept-categorizing-images.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

A segítő függvény megadása `formatCategories` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Képcímkék beolvasása

A következő kód beolvassa az észlelt címkék készletét a képen. További részletekért tekintse meg a [tartalom címkéit](../../concept-tagging-images.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

A segítő függvény megadása `formatTags` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a rendszerképben szereplő általános objektumokat, és kiírja azokat a konzolra. További részletekért lásd az [objektum észlelése](../../concept-object-detection.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Adja meg a segítő függvényt, `formatRectObjects` hogy a felső, bal, alsó és jobb koordinátákat adja vissza a szélességgel és a magassággal együtt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a rendszerképben, és kinyomtatja őket a konzolra. További részletekért lásd a [márka észlelését](../../concept-brand-detection.md) ismertető témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód az észlelt arcokat adja vissza a képen a téglalap koordinátáival, majd a Face attribútumok elemet. További részletekért tekintse meg az [Arcfelismerés](../../concept-detecting-faces.md) című témakört.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

A segítő függvény megadása `formatRectFaces` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, zamatos vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További részletekért tekintse meg a [felnőtt, a zamatos és a véres tartalmat](../../concept-detecting-adult-content.md) .

Adja meg a használni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Ezután adja hozzá a következő kódot a felnőtt tartalom észleléséhez, és nyomtassa ki az eredményeket a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Rendszerképek színsémájának beolvasása

A következő kód az észlelt színattribútumokat nyomtatja ki a képen, például a domináns színeket és a kiejtés színét. További részletekért lásd: [Színsémák](../../concept-detecting-color-schemes.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Adja meg a segítő függvényt a `printColorScheme` színséma adatainak a konzolra való kinyomtatásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Tartományhoz tartozó tartalom beolvasása

A Computer Vision speciális modellt használhat a képek további elemzéséhez. További részletekért tekintse meg a [tartományra vonatkozó tartalmat](../../concept-detecting-domain-content.md) .

Először határozza meg az elemezni kívánt rendszerkép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

A következő kód az észlelt tereptárgyak adatait elemzi a képen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Adja meg a segítő függvényt `formatRectDomain` az észlelt tereptárgyak hely adatainak elemzéséhez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>A rendszerkép típusának beolvasása

A következő kód a képtípussal kapcsolatos információkat jeleníti &mdash; meg, legyen szó ClipArt vagy vonalas rajzolásról.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

A segítő függvény megadása `describeType` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="extract-text-ocr-with-read"></a>Szöveg (OCR) kinyerése olvasással

A Computer Vision kinyerheti a képen látható szöveget, és átalakíthatja a karakteres adatfolyamba. Ez a példa az olvasási műveleteket használja.

> [!NOTE]
> Helyi rendszerképből is beolvashat szöveget. A helyi rendszerképeket érintő forgatókönyvek a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) találhatók.

### <a name="set-up-test-images"></a>Tesztelési lemezképek beállítása

Mentse annak a lemezképnek az URL-címét, amelyből szöveget szeretne kinyerni.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-read-api"></a>Az olvasási API meghívása

Adja hozzá az alábbi kódot, amely meghívja a `readTextFromURL` és a `readTextFromFile` függvényeket az adott lemezképekhez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Adja meg a `readTextFromURL` és a `readTextFromFile` függvényeket. Ezek meghívja a **READ** és a **readInStream** metódust az ügyfél objektumon, amely egy műveleti azonosítót ad vissza, és elindít egy aszinkron folyamatot a rendszerkép tartalmának olvasásához. Ezután a művelet AZONOSÍTÓját használják a művelet állapotának vizsgálatára, amíg az eredmények vissza nem állnak. Ezután visszaadják a kinyert eredményeket.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Ezután adja meg a segítő függvényt `printRecText` , amely kiírja az olvasási műveletek eredményeit a-konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Computer Vision API referenciája (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Mi a Computer Vision?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)található.
