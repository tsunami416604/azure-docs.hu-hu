---
title: 'Rövid útmutató: Computer Vision ügyfélkönyvtár go'
titleSuffix: Azure Cognitive Services
description: Első lépések a Computer Vision ügyféltár go ezzel a rövid útmutató.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: d8f40ab57ee2569b2cb5bf62f391919476b8ab17
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136013"
---
<a name="HOLTop"></a>

[Referenciadokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Csomag](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója

## <a name="setting-up"></a>Beállítása

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a Computer Vision az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Tekintse meg az erőforrást az [Azure Portalon.](https://portal.azure.com/)

Miután lekért egy kulcsot a próba-előfizetés vagy erőforrás, [hozzon létre környezeti változók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) at a kulcs és a végpont URL-cím, nevű, `COMPUTER_VISION_SUBSCRIPTION_KEY` illetve. `COMPUTER_VISION_ENDPOINT`

### <a name="create-a-go-project-directory"></a>Go projektkönyvtár létrehozása

Egy konzolablakban (cmd, PowerShell, Terminál, Bash) hozzon létre `my-app`egy új munkaterületet a Go projekthez, amelynek neve , és keresse meg azt.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

A munkaterület három mappát tartalmaz:

* **src** - Ez a könyvtár forráskódot és csomagokat tartalmaz. A paranccsal `go get` telepített csomagok ebbe a könyvtárba kerülnek.
* **pkg** - Ez a könyvtár tartalmazza a lefordított Go csomag objektumokat. Ezek a fájlok `.a` mind rendelkeznek kiterjesztéssel.
* **bin** - Ez a könyvtár a futtatáskor `go install`létrehozott bináris végrehajtható fájlokat fogja tartalmazni.

> [!TIP]
> A Go-munkaterület szerkezetéről a [Go nyelvi dokumentációjában](https://golang.org/doc/code.html#Workspaces)olvashat bővebben. Ez az útmutató `$GOPATH` a `$GOROOT`beállítással és a beállításával kapcsolatos információkat tartalmazza.

### <a name="install-the-client-library-for-go"></a>Az ügyféltár telepítése az Ugráshoz

Ezután telepítse az ügyféltárat az Ugráshoz:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

vagy ha dep-t használ, a társzalag-futtatáson belül:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Go-alkalmazás létrehozása

Ezután hozzon létre egy fájlt `sample-app.go`a **src** könyvtárban, amelynek neve:

```bash
cd src
touch sample-app.go
```

Nyissa `sample-app.go` meg a kívánt IDE vagy szövegszerkesztőben. Ezután adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Is, deklarálni egy környezetben a gyökere a parancsfájl. A legtöbb Computer Vision függvényhívás végrehajtásához szüksége lesz erre az objektumra:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Ezután elkezdi hozzáadni a kódot a különböző Computer Vision műveletek elvégzéséhez.

## <a name="object-model"></a>Objektummodell

A következő osztályok és interfészek kezelik a Computer Vision Go SDK néhány főbb jellemzőjét.

|Név|Leírás|
|---|---|
| [Alapügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Ez az osztály szükséges a Computer Vision összes funkciójához, például a képelemzéshez és a szövegolvasáshoz. Az előfizetési adatokkal azonnal hozzágyűjtheti azt, és a legtöbb képművelethez használhatja őket.|
|[ImageAnalysis (Képelemzés)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Ez a típus egy **AnalyzeImage** függvényhívás eredményeit tartalmazza. Az egyes kategóriaspecifikus függvényekhez hasonló típusok tartoznak.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Ez a típus egy kötegelt olvasási művelet eredményeit tartalmazza. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Ez a típus határozza meg a szabványos Elemzési műveletben elvégezhető képelemzés különböző típusait. A VisualFeatureTypes értékek et az igényektől függően adhatja meg. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan hajtheti el a következő feladatokat a Computer Vision go ügyfélkódtárban:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg elolvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a lépés feltételezi, hogy [környezeti változókat hozott létre](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT` Computer Vision kulcshoz és végponthoz, amelyeket elnevezett és illetve.

Hozzon `main` létre egy függvényt, és adja hozzá a következő kódot, hogy példányosítani egy ügyfél a végpont és a kulcs.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód az ügyfélobjektum segítségével elemzi a távoli lemezképet, és kinyomtatja az eredményeket a konzolra. Szöveges leírást, kategorizálást, címkék listáját, észlelt objektumokat, észlelt márkákat, észlelt arcokat, felnőtteknek szóló tartalomjelzőket, fő színeket és képtípust kaphat.

### <a name="set-up-test-image"></a>Tesztkép beállítása

Először mentse az elemezni kívánt kép URL-címére mutató hivatkozást. Tedd ezt `main` a funkciódba.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> A helyi lemezképet is elemezheti. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) a helyi lemezképeket érintő forgatókönyvek.

### <a name="specify-visual-features"></a>Vizuális szolgáltatások megadása

A következő függvény hívások kivonat különböző vizuális funkciók a mintaképből. Ezeket a függvényeket a következő szakaszokban határozhatja meg.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Kép leírásának beszerezése

A következő függvény lekéri a képhez létrehozott feliratok listáját. A képek leírásáról a [Képek leírása című](../../concept-describing-images.md)témakörben talál további információt.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Képkategória beszereznie

A következő függvény a kép észlelt kategóriáját kapja. További információ: [Képek kategorizálása](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Képcímkék beszerezése

A következő függvény leállítja az észlelt címkéket a képen. További információt a Tartalomcímkék című [témakörben](../../concept-tagging-images.md)talál.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Objektumok észlelése

A következő függvény észleli a kép gyakori objektumait, és kinyomtatja azokat a konzolra. További információt az [Objektumészlelés](../../concept-object-detection.md)című témakörben talál.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Márkák észlelése

A következő kód észleli a vállalati márkákat és emblémákat a képen, és kinyomtatja azokat a konzolra. További információ: [Márkaészlelés](../../concept-brand-detection.md).

Először deklaráljon egy `main` hivatkozást egy új képre a függvényen belül.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

A következő kód határozza meg a márkaészlelési funkciót.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Arcfelismerés

A következő függvény a képen észlelt arcokat adja vissza a téglalap koordinátáival és bizonyos arcattribútumaival. További információ: [Face detection](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, pikáns vagy véres tartalom észlelése

A következő függvény kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További információ: [Felnőtt, pikáns, véres tartalom](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Képszínséma beszerezése

A következő függvény a kép észlelt színjellemzőit nyomtatja ki, például a domináns színeket és a kiemelőszínt. További információt a [Színsémák](../../concept-detecting-color-schemes.md)című témakörben talál.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Tartományspecifikus tartalom beszereznie

Computer Vision segítségével speciális modellek további elemzést a képeken. További információt a Tartományspecifikus tartalom című [témakörben talál.](../../concept-detecting-domain-content.md) 

A következő kód elemzi a képen észlelt hírességek adatait.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

A következő kód elemzi a lemezkép észlelt tájékozódási pontjainak adatait.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>A képtípus beszereznie

A következő függvény a kép&mdash;típusára vonatkozó információkat nyomtatja ki, legyen az ClipArt-elem vagy vonalrajz.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg elolvasása

A Computer Vision képes olvasni a kép látható szövegét, és karakterfolyammá alakítani. Ebben a szakaszban a kód `RecognizeTextReadAPIRemoteImage`egy függvényt határoz meg, amely az ügyfélobjektumot használja a képnyomtatott vagy kézzel írt szövegének észlelésére és kibontására.

Adja hozzá a mintakép-referencia- és függvényhívást a `main` függvényben.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Helyi képből is kinyerheti a szöveget. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) a helyi lemezképeket érintő forgatókönyvek.

### <a name="call-the-read-api"></a>Az olvasási API hívása

Adja meg a szöveg `RecognizeTextReadAPIRemoteImage`olvasásának új függvényét. Adja hozzá az alábbi kódot, amely meghívja a **BatchReadFile** metódust az adott képhez. Ez a módszer egy műveletazonosítót ad vissza, és aszinkron folyamatot indít el a lemezkép tartalmának olvasásához.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután olvassa be a **BatchReadFile** hívásból visszaadott műveletazonosítót, és használja azt a **GetReadOperationResult** metódussal a szolgáltatás működési eredmények lekérdezéséhez. A következő kód egy másodperces időközönként ellenőrzi a műveletet, amíg az eredmények et vissza nem adja. Ezután kinyomtatja a kinyert szöveges adatokat a konzolra.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Olvasási eredmények megjelenítése

Adja hozzá a következő kódot a beolvasott szöveges adatok elemzéséhez és megjelenítéséhez, és fejezze be a függvénydefiníciót.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `go run` alkalmazáskönyvtárból a paranccsal.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Computer Vision API referencia (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Mi a Computer Vision?](../../Home.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)
