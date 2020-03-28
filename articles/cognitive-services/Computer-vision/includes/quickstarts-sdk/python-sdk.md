---
title: 'Rövid útmutató: Computer Vision ügyfélkódtár pythonhoz'
description: Ezzel a rövid útmutatóval ismerkedje meg a Computer Vision python-ügyféltárral.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5f552dfc320459d23e538e65e5f34c085f5b5ad9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136006"
---
<a name="HOLTop"></a>

[Referenciadokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [minták](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítása

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a Computer Vision az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Az erőforrás megtekintése az [Azure Portalon](https://portal.azure.com/)

Miután lekért egy kulcsot a próba-előfizetés vagy erőforrás, [hozzon létre környezeti változók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) at a kulcs és a végpont URL-cím, nevű, `COMPUTER_VISION_SUBSCRIPTION_KEY` illetve. `COMPUTER_VISION_ENDPOINT`
 
### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre&mdash;például egy új Python-parancsfájlt*quickstart-file.py.* Ezután nyissa meg a kívánt szerkesztőben vagy IDE-ben, és importálja a következő könyvtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Ezután hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Az ügyfélkönyvtár a következő kkel telepíthető:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Computer Vision Python SDK néhány főbb funkcióját kezelik.

|Név|Leírás|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Ez az osztály közvetlenül kezeli az összes képműveletet, például a képelemzést, a szövegfelismerést és a miniatűrök létrehozását.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Ez az osztály szükséges az összes Computer Vision funkcióhoz. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket. Megvalósítja **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Ez a felsorítás határozza meg a különböző típusú képelemzés, hogy lehet tenni egy szabványos Elemzés művelet. A **VisualFeatureTypes** értékek et az igényektől függően adhatja meg. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Computer Vision ügyfélkódtár a Python:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg elolvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy létrehozott egy környezeti `COMPUTER_VISION_SUBSCRIPTION_KEY` [változót](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a Computer Vision kulcshoz, amelynek neve .

Ügyfél példányosítsa meg a végpontot és a kulcsot. Hozzon létre egy [CognitiveServicesCredentials objektumot](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) a kulccsal, és használja azt a végponttal egy [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) objektum létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Rendszerkép elemzése

Az elemezni kívánt kép URL-címére mutató hivatkozás mentése.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Kép leírásának beszerezése

A következő kód lekéri a képhez létrehozott feliratok listáját. További részletek a [Képek leírása](../../concept-describing-images.md) című témakörben talál.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Képkategória beszereznie

A következő kód leteszi a kép észlelt kategóriáját. További részletekért lásd: [Képek kategorizálása.](../../concept-categorizing-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Képcímkék beszerezése

A következő kód leveszi az észlelt címkék készletét a képen. További részletekért lásd a [Tartalomcímkéket.](../../concept-tagging-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő kód észleli a képen lévő gyakori objektumokat, és kinyomtatja azokat a konzolra. További részleteket az [Objektumészlelés](../../concept-object-detection.md) ben talál.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a képen, és kinyomtatja azokat a konzolra. További részletek a [Márkaészlelés](../../concept-brand-detection.md) ben.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód a képen lévő észlelt arcokat adja vissza a téglalap koordinátáival, és kiválasztja az arcjellemzőket. További részleteket az [Arcfelismerés](../../concept-detecting-faces.md) ben talál.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, pikáns vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További [részletek: Felnőtt, pikáns, véres tartalom.](../../concept-detecting-adult-content.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Képszínséma beszerezése

A következő kód kinyomtatja a kép észlelt színjellemzőit, például a domináns színeket és a kiemelőszínt. További részleteket a [Színsémák](../../concept-detecting-color-schemes.md) ban talál.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Tartományspecifikus tartalom beszereznie

Computer Vision segítségével speciális modell további elemzést a képeken. További részletekért lásd [a Tartományspecifikus tartalmat.](../../concept-detecting-domain-content.md) 

A következő kód elemzi a képen észlelt hírességek adatait.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

A következő kód elemzi a lemezkép észlelt tájékozódási pontjainak adatait.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>A képtípus beszereznie

A következő kód a kép&mdash;típusára vonatkozó információkat nyomtatja ki, függetlenül attól, hogy ClipArt-elemről vagy vonalrajzról van-e szó.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg elolvasása

A Computer Vision képes olvasni a kép látható szövegét, és karakterfolyammá alakítani. Ezt két részből áll.

### <a name="call-the-read-api"></a>Az olvasási API hívása

Először használja a következő kódot az adott kép **batch_read_file** metódusának hívásához. Ez egy műveletazonosítót ad vissza, és aszinkron folyamatot indít el a lemezkép tartalmának olvasásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután kapja vissza a műveletazonosítót a **batch_read_file** hívásból, és ezzel kérdezze le a szolgáltatást a művelet eredményéhez. A következő kód egy másodperces időközönként ellenőrzi a műveletet, amíg az eredmények et vissza nem adja. Ezután kinyomtatja a kinyert szöveges adatokat a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `python` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Computer Vision függvénytárat a Pythonhoz alapfeladatok elvégzéséhez. Ezután tekintse meg a referenciadokumentációt, ha többet szeretne megtudni a könyvtárról.


> [!div class="nextstepaction"]
>[Computer Vision API referencia (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Mi a Computer Vision?](../../Home.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)
