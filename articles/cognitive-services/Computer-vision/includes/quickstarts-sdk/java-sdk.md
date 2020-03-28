---
title: 'Rövid útmutató: Computer Vision ügyfélkönyvtár Java-hoz'
description: Ebben a rövid útmutatóban ismerkedje meg a Computer Vision java-ügyfélkódtárral.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.author: pafarley
ms.openlocfilehash: dbe986145a223f1958f1945abfa189de90952f4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272812"
---
<a name="HOLTop"></a>

[Referenciadokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [-műtermék (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [minták](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle build eszköz](https://gradle.org/install/)vagy egy másik függőségi menedzser.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-computer-vision-azure-resource"></a>Computer Vision Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a Computer Vision az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Tekintse meg az erőforrást az [Azure Portalon.](https://portal.azure.com/)

Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `COMPUTER_VISION_SUBSCRIPTION_KEY` kulcs- és szolgáltatásvégpont-karakterlánchoz, amelyet elnevezett, illetve `COMPUTER_VISION_ENDPOINT`a.

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárából. Ez a parancs alapvető buildfájlokat hoz létre a Gradle számára, beleértve a *build.gradle.kts fájlt,* amely et futásidőben az alkalmazás létrehozásához és konfigurálásához használják.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

Keresse *meg a build.gradle.kts fájlt,* és nyissa meg a kívánt IDE-vel vagy szövegszerkesztővel. Ezután másolja a következő buildkonfiguráció. Ez a konfiguráció Java-alkalmazásként definiálja a projektet, amelynek belépési pontja a **ComputerVisionQuickstarts**osztály. Importálja a Computer Vision könyvtárat.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

A munkakönyvtárból futtassa a következő parancsot a projekt forrásmappájának létrehozásához:

```console
mkdir -p src/main/java
```

Nyissa meg az új mappát, és hozzon létre egy *ComputerVisionQuickstarts.java*nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy `import` IDE-ben, és adja hozzá a következő állításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Ezután adjon hozzá egy osztálydefiníciót a **ComputerVisionQuickstarts eszközhez.**

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Ez a rövid útmutató a Gradle-függőségkezelőt használja. Az ügyfélkönyvtárat és a többi függőségi kezelő adatait a [Maven központi adattárban](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)találja.

A projekt *build.gradle.kts* fájljában adja meg függőségként a Computer Vision ügyfélkönyvtárat.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és interfészek kezelik a Computer Vision Java SDK néhány főbb jellemzőjét.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Ez az osztály szükséges az összes Computer Vision funkcióhoz. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Ez az osztály az ügyfélobjektumból származik, és közvetlenül kezeli az összes képműveletet, például a képelemzést, a szövegfelismerést és a miniatűrök létrehozását.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Ez a felsorítás határozza meg a különböző típusú képelemzés, hogy lehet tenni egy szabványos Elemzés művelet. A VisualFeatureTypes értékek et az igényektől függően adhatja meg. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Computer Vision java ügyfélkódtárban:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Kép elemzése](#analyze-an-image)
* [Nyomtatott és kézzel írt szöveg elolvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy létrehozott egy környezeti `COMPUTER_VISION_SUBSCRIPTION_KEY` [változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a Computer Vision kulcshoz, amelynek neve .

A következő kód `main` hozzáad egy metódust az osztályhoz, és változókat hoz létre az erőforrás Azure-végpontjának és kulcsának. Meg kell adnia a saját végpont-karakterláncát, amelyet az Azure Portal **Áttekintés** szakaszának ellenőrzésével találhat meg. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Ezután adja hozzá a következő kódot egy [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) objektum létrehozásához, és adja át más metódus(ok)nak, amelyet később definiál.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

## <a name="analyze-an-image"></a>Rendszerkép elemzése

A következő kód egy `AnalyzeLocalImage`metódust határoz meg, amely az ügyfélobjektummal elemzi a helyi lemezképet, és kinyomtatja az eredményeket. A metódus szöveges leírást, kategorizálást, címkék listáját, észlelt arcokat, felnőtt tartalomjelzőket, főszíneket és képtípust ad vissza.

### <a name="set-up-test-image"></a>Tesztkép beállítása

Először hozzon létre egy **erőforrást /** mappát a projekt **src / main /** mappájában, és adjon hozzá egy képet, amelyet elemezni szeretne. Ezután adja hozzá a következő metódusdefiníciót a **ComputerVisionQuickstarts** osztályhoz. Ha szükséges, módosítsa a `pathToLocalImage` képfájlértékének megfelelőértéket. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> A távoli képeket az URL-cím használatával is elemezheti. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) a távoli lemezképeket érintő forgatókönyvek.

### <a name="specify-visual-features"></a>Vizuális szolgáltatások megadása

Ezután adja meg, hogy mely vizuális funkciókat szeretné kivonni az elemzéssorán. A teljes listát a [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) enum című témakörben láthatja.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Elemzés
Ez a módszer részletes eredményeket nyomtat a konzolra a képelemzés minden hatóköréhez. Azt javasoljuk, hogy vegye körül ezt a metódust hívás egy try/catch blokk. Az **analysisImageInStream** metódus egy **Olyan ImageAnalysis** objektumot ad vissza, amely az összes kinyert információt tartalmazza.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

A következő szakaszok bemutatják, hogyan elemezze részletesen ezeket az információkat.

### <a name="get-image-description"></a>Kép leírásának beszerezése

A következő kód lekéri a képhez létrehozott feliratok listáját. További információt a Képek leírása című témakörben [talál.](../../concept-describing-images.md)

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Képkategória beszereznie

A következő kód leteszi a kép észlelt kategóriáját. További információ: [Képek kategorizálása](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Képcímkék beszerezése

A következő kód leveszi az észlelt címkék készletét a képen. További információt a Tartalomcímkék című [témakörben](../../concept-tagging-images.md)talál.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Arcfelismerés

A következő kód a képen lévő észlelt arcokat adja vissza a téglalap koordinátáival, és kiválasztja az arcjellemzőket. További információ: [Face detection](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Felnőtt, pikáns vagy véres tartalom észlelése

A következő kód kinyomtatja a felnőtt tartalom észlelt jelenlétét a képen. További információ: [Felnőtt, pikáns, véres tartalom](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Képszínséma beszerezése

A következő kód kinyomtatja a kép észlelt színjellemzőit, például a domináns színeket és a kiemelőszínt. További információt a [Színsémák](../../concept-detecting-color-schemes.md)című témakörben talál.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Tartományspecifikus tartalom beszereznie

Computer Vision segítségével speciális modell további elemzést a képeken. További információt a Tartományspecifikus tartalom című [témakörben talál.](../../concept-detecting-domain-content.md) 

A következő kód elemzi a képen észlelt hírességek adatait.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

A következő kód elemzi a lemezkép észlelt tájékozódási pontjainak adatait.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>A képtípus beszereznie

A következő kód a kép&mdash;típusára vonatkozó információkat nyomtatja ki, függetlenül attól, hogy ClipArt-elemről vagy vonalrajzról van-e szó.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg elolvasása

A Computer Vision képes olvasni a kép látható szövegét, és karakterfolyammá alakítani.

> [!NOTE]
> A távoli kép szövegét az URL-cím használatával is elolvashatja. Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) a távoli lemezképeket érintő forgatókönyvek.

### <a name="call-the-recognize-api"></a>Hívja meg az Recognize API-t

Először a következő kódot használja az adott kép **recognizePrintedTextInStream** metódusának hívásához. Amikor hozzáadja ezt a kódot a projekthez, `localTextImagePath` le kell cserélnie az értéket a helyi lemezkép elérési útvonalára. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Nyomtatási eredmények felismerése

A következő kódblokk feldolgozza a visszaadott szöveget, és elemzi azt, hogy kinyomtassa az egyes sorok első szavát. Ezzel a kóddal gyorsan megismerheti az **OcrResult** példány szerkezetét.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Végül zárja be a try/catch blokkot és a metódusdefiníciót.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást a következő kkel hozhatja létre:

```console
gradle build
```

Futtassa az `gradle run` alkalmazást a következő paranccsal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Computer Vision Java függvénytárat alapfeladatok elvégzésére. Ezután tekintse meg a referenciadokumentációt, ha többet szeretne megtudni a könyvtárról.

> [!div class="nextstepaction"]
>[Computer Vision referencia (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Mi a Computer Vision?](../../Home.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)