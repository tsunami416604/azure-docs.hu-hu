---
title: 'Rövid útmutató: Bing Visual Search ügyféloldali kódtár a Javához | Microsoft Docs'
description: Töltsön fel egy rendszerképet a Bing Visual Search SDK-val, és szerezzen információt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 5a6d39fa6e10ad0ad102f9d25ffd252ec9e0fa8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379494"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Gyors útmutató: Bing Visual Search a Javához készült ügyféloldali kódtár

Ismerkedjen meg a Javához készült Bing Visual Search-ügyfél függvénytárával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. 

A Javához készült Bing Visual Search ügyféloldali kódtár a következőre használható:

* Töltsön fel egy képet egy vizuális keresési kérelem küldéséhez.
* Szerezze be a kép Insight-tokenjét és a vizuális keresési címkéket.

[Dokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | összetevő [(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőség-kezelő

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) `BING_SEARCH_V7_SUBSCRIPTION_KEY`nevű kulcshoz.

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve a *Build. Gradle. KTS* fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir -p src/main/java
```

Hozzon létre egy mappát ahhoz a rendszerképhez, amelyet fel szeretne tölteni az API-ba. Helyezze a rendszerképet az **erőforrások** mappába.

```console
mkdir -p src/main/resources
``` 

Navigáljon az új mappára, és hozzon létre egy *BingVisualSearchSample. Java*nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy IDE, és adja hozzá a következő `import` utasításokat:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Ezután hozzon létre egy új osztályt.

```java
public class BingVisualSearchSample {
}
```

Az alkalmazás `main` metódusában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt. Ezután hozzon létre egy `byte[]`t a feltölteni kívánt rendszerképhez. Hozzon létre egy `try` blokkot a később definiálni kívánt módszerekhez, majd töltse be a rendszerképet, és konvertálja bájtba `toByteArray()`használatával.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Ez a rövid útmutató a Gradle függőség-kezelőt használja. Megtalálhatja az ügyféloldali függvénytárat és az egyéb függőségi kezelők információit a [Maven központi adattárában](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

A projekt *Build. gradle. KTS* fájljában ügyeljen arra, hogy az ügyféloldali függvénytárat `implementation` utasításként adja meg. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Bing Visual Search ügyféloldali kódtár és a Java használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Vizuális keresési kérelem küldése](#send-a-visual-search-request)
* [A képelemzési jogkivonat és a vizuális keresési címkék nyomtatása](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `BING_SEARCH_V7_SUBSCRIPTION_KEY`nevű Bing Visual Search kulcshoz.


A Main metódusban mindenképpen használja az előfizetési kulcsot egy [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) objektum létrehozásához.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Vizuális keresési kérelem küldése

Egy új metódusban küldje el a képbájt tömböt (amely a `main()` metódusban lett létrehozva) az ügyfél [bingImages (). visualSearch ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) metódusának használatával. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>A képelemzési jogkivonat és a vizuális keresési címkék nyomtatása

Ellenőrizze, hogy a [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) objektum null értékű-e. Ha nem, nyomtassa ki a képelemzési tokent, a címkék számát, a műveletek számát és az első művelet típusát.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```

Az alkalmazás futtatása a `run` céllal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](tutorial-bing-visual-search-single-page-app.md)
