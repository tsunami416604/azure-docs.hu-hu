---
title: Bing Custom Search Java ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: e175d77ec4684053070e817f8a6ec7e9455668b1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252897"
---
Ismerkedjen meg a Javához készült Bing Custom Search-ügyfél függvénytárával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Bing Custom Search API lehetővé teszi, hogy testreszabott, ad-ingyenes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

A Javához készült Bing Custom Search ügyféloldali kódtár a következőre használható:

* Keresési eredmények keresése a weben a Bing Custom Search-példányból. 

[Dokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | összetevő [(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)aktuális verziója.
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Egy Bing Custom Search példány. További információért tekintse [meg a rövid útmutató: az első Bing Custom Search példány létrehozása](../../quick-start.md) című témakört.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) `AZURE_BING_CUSTOM_SEARCH_API_KEY`nevű kulcshoz.

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

> [!TIP]
> Ha nem használja a Gradle-t, a [Maven központi tárházában](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)megtalálhatja az ügyféloldali függvénytár részleteit más függőségi kezelők számára.

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve az alkalmazás konfigurálásához futásidőben használt *Build. Gradle. KTS* fájlt.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

## <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja ezt a Build-konfigurációt. Ügyeljen arra, hogy az ügyfél-függvénytárat `dependencies`:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *BingCustomSearchSample. Java*nevű fájlt. Nyissa meg, és adja hozzá a következő `import` utasításokat:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Hozzon létre egy `BingCustomSearchSample` nevű osztályt

```java
public class BingCustomSearchSample {
}
```

A osztályban hozzon létre egy `main` metódust és egy változót az erőforrás kulcsaként. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez nyissa meg, majd nyissa meg újra a szerkesztőt, az IDE vagy a shellt. A metódusokat később kell megadnia.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objektummodell

A Bing Custom Search ügyfél egy [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) objektum, amely a [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) objektum [hitelesítése ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) metódusból lett létrehozva. A keresési kérelmet az ügyfél [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) metódusának használatával küldheti el.

Az API-válasz egy [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) objektum, amely információkat tartalmaz a keresési lekérdezésről és a keresési eredményekről.

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült Bing Custom Search ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Keresési eredmények beolvasása az egyéni keresési példányból](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A Main metódusnak tartalmaznia kell egy [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) objektumot, amely a kulcsot veszi át, és meghívja a `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Keresési eredmények beolvasása az egyéni keresési példányból

Használja az ügyfél [BingCustomInstances. Search ()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) függvényét, hogy keresési lekérdezést küldjön az egyéni példányra. Állítsa be a `withCustomConfig`t az egyéni konfigurációs AZONOSÍTÓra, vagy az alapértelmezett értéket `1`. Az API válaszának beolvasása után ellenőrizze, hogy talált-e keresési eredményeket. Ha igen, szerezze be az első keresési eredményt úgy, hogy meghívja a válasz `webPages().value().get()` függvényt, és kinyomtatja az eredmény nevét és URL-címét. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozza létre az alkalmazást az alábbi paranccsal a projekt fő könyvtárából:

```console
gradle build
```

Az alkalmazás futtatása a `run` céllal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni keresési Webalkalmazás létrehozása](../../tutorials/custom-search-web-page.md)
