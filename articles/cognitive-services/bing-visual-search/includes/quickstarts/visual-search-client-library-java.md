---
title: Bing Visual Search Java ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 9a8ea8ff03c495411910c775e5161b8ab216097a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85805581"
---
Ezzel a rövid útmutatóval megkezdheti a rendszerképek elemzését a Bing Visual Search szolgáltatásból a Java ügyféloldali kódtár használatával. Habár a Bing Visual Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. Ennek a rövid útmutatónak a forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch)érhető el.

A Javához készült Bing Visual Search ügyféloldali kódtár a következőre használható:

* Töltsön fel egy képet egy vizuális keresési kérelem küldéséhez.
* Szerezze be a kép Insight-tokenjét és a vizuális keresési címkéket.

[Dokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)  |  Összetevő [(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőség-kezelő

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Miután megszerezte a kulcsot az erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) nevű kulcshoz `BING_SEARCH_V7_SUBSCRIPTION_KEY` .

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

Az alkalmazás `main` metódusában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt. Ezután hozzon létre egy olyan `byte[]` képet, amelyet feltölt. Hozzon létre egy `try` blokkot a később definiálni kívánt módszerekhez, és töltse be a képet, és konvertálja bájtra a használatával `toByteArray()` .

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
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) nevű Bing Visual Search kulcshoz `BING_SEARCH_V7_SUBSCRIPTION_KEY` .


A Main metódusban mindenképpen használja az előfizetési kulcsot egy [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) objektum létrehozásához.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Vizuális keresési kérelem küldése

Egy új metódusban küldje el a (metódusban létrehozott) rendszerkép-bájt tömböt `main()` az ügyfél [bingImages (). visualSearch ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) metódusának használatával. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>A képelemzési jogkivonat és a vizuális keresési címkék nyomtatása

Ellenőrizze, hogy a [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) objektum null értékű-e. Ha nem, nyomtassa ki a képelemzési tokent, a címkék számát, a műveletek számát és az első művelet típusát.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Alkalmazás futtatása

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```

Futtassa az alkalmazást a `run` célnak megfelelően:

```console
gradle run
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](../../tutorial-bing-visual-search-single-page-app.md)
