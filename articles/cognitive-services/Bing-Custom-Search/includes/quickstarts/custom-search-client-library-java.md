---
title: Bing Egyéni keresés Java-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: e175d77ec4684053070e817f8a6ec7e9455668b1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78252897"
---
Ismerkedés a Java-hoz való Egyéni keresés ügyféltárral. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A Bing egyéni keresési API lehetővé teszi, hogy személyre szabott, hirdetésmentes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. A minta forráskódja megtalálható a [GitHubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

A Bing Egyéni keresés javabeli ügyféltár segítségével:

* Keresse meg a keresési eredményeket az interneten a Bing egyéni keresés példányából. 

[Referenciadokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [-összetevő (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)aktuális verziója.
* A [Gradle build eszköz](https://gradle.org/install/)vagy egy másik függőségi menedzser.
* Bing egyéni keresési példánya. További információ: További információ [az első Bing egyéni keresési példány létrehozása](../../quick-start.md) című témakörben.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Miután bekért egy kulcsot a próba-előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcshoz, amelynek neve . `AZURE_BING_CUSTOM_SEARCH_API_KEY`

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

> [!TIP]
> Ha nem a Gradle-t használja, a [Maven központi adattárban megtalálhatja](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)a többi függőségi kezelő kliens-kezelőjének adatait.

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárából. Ez a parancs alapvető buildfájlokat hoz létre a Gradle számára, beleértve egy *build.gradle.kts* fájlt, amelyet futásidőben használnak az alkalmazás konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

## <a name="install-the-client-library"></a>Az ügyféltár telepítése 

Keresse *meg a build.gradle.kts fájlt,* és nyissa meg a kívánt IDE-vel vagy szövegszerkesztővel. Ezután másolja ebben a buildkonfigurációban. Ügyeljen arra, hogy az `dependencies`ügyfélkönyvtárat a következő alá foglalja:

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

Hozzon létre egy mappát a mintaalkalmazáshoz. A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir src/main/java
```

Nyissa meg az új mappát, és hozzon létre egy *BingCustomSearchSample.java*nevű fájlt. Nyissa meg, és `import` adja hozzá a következő állításokat:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Nevű osztály létrehozása`BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Az osztályban hozzon létre egy metódust `main` és egy változót az erőforrás kulcsához. Ha az alkalmazás elindítása után hozta létre a környezeti változót, zárja be és nyissa meg újra a szerkesztőt, az IDE-t vagy a rendszerhéjat, hogy hozzáférjen a változóhoz. A módszereket később fogja meghatározni.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objektummodell

A Bing Egyéni keresés ügyfél egy [BingCustomSearchAPI-objektum,](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) amely a [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) objektum [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) metódusából jön létre. Az ügyfél [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) metódusával keresési kérelmet küldhet.

Az API-válasz egy [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) objektum, amely a keresési lekérdezésadatait és a keresési eredményeket tartalmazza.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Bing Egyéni keresés javaügyfél-tárral:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Keresési eredmények beszerezni az egyéni keresési példányból](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A fő módszernek tartalmaznia kell egy [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) `authenticate()`objektumot, amely elveszi a kulcsot, és meghívja a .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Keresési eredmények beszerezni az egyéni keresési példányból

Az ügyfél [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) függvényének használatával keresési lekérdezést küldhet az egyéni példánynak. Állítsa `withCustomConfig` be az egyéni konfigurációs azonosítót, vagy az alapértelmezett beállítást. `1` Miután megkapta a választ az API-tól, ellenőrizze, hogy találtak-e keresési eredményeket. Ha igen, az első keresési eredményt a `webPages().value().get()` válasz függvényének meghívásával kapja meg, és nyomtassa ki az eredmény nevét és URL-címét. 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást a következő paranccsal hozhatja létre a projekt fő könyvtárából:

```console
gradle build
```

Futtassa az `run` alkalmazást a következő céllal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési webalkalmazás létrehozása](../../tutorials/custom-search-web-page.md)
