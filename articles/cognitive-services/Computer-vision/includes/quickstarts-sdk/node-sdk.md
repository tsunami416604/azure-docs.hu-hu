---
title: 'Rövid útmutató: Computer Vision ügyfélkönyvtár a Node.js fájlhoz'
description: Első lépések a Computer Vision ügyfélkönyvtárnok számára ezzel a rövid útmutatóval
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 234e2402287fd76bdfd96cb6884c3fd20e805d06
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136020"
---
<a name="HOLTop"></a>

[Referenciadokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [minták](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org/) jelenlegi verziója

## <a name="setting-up"></a>Beállítása

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a Computer Vision az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Tekintse meg az erőforrást az [Azure Portalon.](https://portal.azure.com/)

Miután lekért egy kulcsot a próba-előfizetés vagy erőforrás, [hozzon létre környezeti változók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) at a kulcs és a végpont URL-cím, nevű `COMPUTER_VISION_SUBSCRIPTION_KEY` és `COMPUTER_VISION_ENDPOINT` volt.

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt.

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy `package.json` csomópontalkalmazás fájllal való létrehozásához.

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Telepítse `ms-rest-azure` a `@azure/cognitiveservices-computervision` és az NPM csomagokat:

```console
npm install @azure/cognitiveservices-computervision
```

Az alkalmazás `package.json` fájlja frissül a függőségekkel.

### <a name="prepare-the-nodejs-script"></a>A Node.js parancsfájl előkészítése

Hozzon létre egy új fájlt, *index.js*, és nyissa meg egy szövegszerkesztőben. Adja hozzá a következő importálási kimutatásokat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Ezután definiáljon `computerVision` egy függvényt, és deklarálja az elsődleges függvényt és visszahívási függvényt. A rövid útmutató kódot hozzá adja az `computerVision` elsődleges függvényhez, és a parancsfájl alján hívja meg.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Computer Vision Node.js SDK néhány főbb jellemzőjét.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Ez az osztály szükséges az összes Computer Vision funkcióhoz. Az előfizetési adatokkal azonnal hozzágyűjtheti azt, és a legtöbb képművelethez használhatja őket.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Ez a felsorítás határozza meg a különböző típusú képelemzés, hogy lehet tenni egy szabványos Elemzés művelet. A **VisualFeatureTypes** értékek et az igényektől függően adhatja meg. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Computer Vision ügyfélkódtárnode.js:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg elolvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Ügyfél példányosítsa meg a végpontot és a kulcsot. Hozzon létre egy [ApiKeyCredentials objektumot](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) a kulccsal és a végponttal, és használja [azt egy ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) objektum létrehozásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A jelen szakaszban található kód elemzi a távoli képeket a különböző vizuális funkciók kinyeréséhez. Ezeket a műveleteket az ügyfélobjektum **analyzeImage** metódusának részeként is elvégezheti, vagy meghívhatja őket egyedi módszerekkel. A részleteket lásd a [referenciadokumentációban.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

> [!NOTE]
> A helyi lemezképet is elemezheti. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) a helyi lemezképeket érintő forgatókönyvek.

### <a name="get-image-description"></a>Kép leírásának beszerezése

A következő kód lekéri a képhez létrehozott feliratok listáját. További részletek a [Képek leírása](../../concept-describing-images.md) című témakörben talál.

Először definiálja az elemző kép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Ezután adja hozzá a következő kódot a kép leírásához, és nyomtassa ki a konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Képkategória beszereznie

A következő kód leteszi a kép észlelt kategóriáját. További részletekért lásd: [Képek kategorizálása.](../../concept-categorizing-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

A segítő függvény `formatCategories`meghatározása :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Képcímkék beszerezése

A következő kód leveszi az észlelt címkék készletét a képen. További részletekért lásd a [Tartalomcímkéket.](../../concept-tagging-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

A segítő függvény `formatTags`meghatározása :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a képen lévő gyakori objektumokat, és kinyomtatja azokat a konzolra. További részleteket az [Objektumészlelés](../../concept-object-detection.md) ben talál.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

A segítő függvény `formatRectObjects`meghatározása :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a képen, és kinyomtatja azokat a konzolra. További részletek a [Márkaészlelés](../../concept-brand-detection.md) ben.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód a képen lévő észlelt arcokat adja vissza a téglalap koordinátáival, és kiválasztja az arcjellemzőket. További részleteket az [Arcfelismerés](../../concept-detecting-faces.md) ben talál.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

A segítő függvény `formatRectFaces`meghatározása :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, pikáns vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További [részletek: Felnőtt, pikáns, véres tartalom.](../../concept-detecting-adult-content.md)

Adja meg a használni használandó kép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Ezután adja hozzá a következő kódot a felnőtt tartalom észleléséhez, és nyomtassa ki az eredményeket a konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Képszínséma beszerezése

A következő kód kinyomtatja a kép észlelt színjellemzőit, például a domináns színeket és a kiemelőszínt. További részleteket a [Színsémák](../../concept-detecting-color-schemes.md) ban talál.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Adja meg a `printColorScheme` segítő funkciót a színséma részleteinek a konzolra történő nyomtatásához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Tartományspecifikus tartalom beszereznie

Computer Vision segítségével speciális modell további elemzést a képeken. További részletekért lásd [a Tartományspecifikus tartalmat.](../../concept-detecting-domain-content.md)

Először definiálja az elemző kép URL-címét:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

A következő kód elemzi a lemezkép észlelt tájékozódási pontjainak adatait.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Adja meg a `formatRectDomain` segítő függvényt az észlelt tájékozódási pontok helyadatainak elemzéséhez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>A képtípus beszereznie

A következő kód a kép&mdash;típusára vonatkozó információkat nyomtatja ki, függetlenül attól, hogy ClipArt-elemről vagy vonalrajzról van-e szó.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

A segítő függvény `describeType`meghatározása :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg elolvasása

A Computer Vision képes olvasni a kép látható szövegét, és karakterfolyammá alakítani.

> [!NOTE]
> A szöveget helyi képből is beolvashatja. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) a helyi lemezképeket érintő forgatókönyvek.

### <a name="set-up-test-images"></a>Tesztképek beállítása

Mentse azoknak a képeknek az URL-címét, amelyekből szöveget szeretne kinyerni.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Hívja meg az Recognize API-t

Adja hozzá az alábbi `recognizeText` kódot, amely meghívja a funkciót az adott képekhez.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Adja `recognizeText` meg a függvényt. Ez meghívja az **ügyfélobjektum recognizeText** metódusát, amely egy műveletazonosítót ad vissza, és aszinkron folyamatot indít el a lemezkép tartalmának olvasásához. Ezután a műveletazonosító segítségével egy másodperces időközönként ellenőrzi a műveletet, amíg az eredmények vissza nem adják. Ezután visszaadja a kivont eredményeket.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Ezután adja meg `printRecText`a segítő funkciót, amely egy Recognize művelet eredményeit nyomtatja ki a konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `node` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Computer Vision API referencia (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Mi a Computer Vision?](../../Home.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)
