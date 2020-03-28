---
title: 'Rövid útmutató: Bing Visual Search ügyféltár Java-hoz | Microsoft dokumentumok'
description: Töltsön fel egy képet a Bing Visual Search SDK használatával, és betekintést nyerhet róla.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 5a6d39fa6e10ad0ad102f9d25ffd252ec9e0fa8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379494"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>Rövid útmutató: Bing Visual Search ügyféltár Java-hoz

Ismerkedés a Java-hoz való Bing Visual Search ügyféltárral. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. 

A Bing Visual Search java-alapú ügyféltár segítségével:

* Töltsön fel egy képet vizuális keresési kérelem küldéséhez.
* A képbetekintési token és a vizuális keresési címkék beszerezhető.

[Referenciadokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [-összetevő (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle buildeszköz](https://gradle.org/install/)vagy más függőségkezelő

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Miután bekért egy kulcsot a próba-előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcshoz, amelynek neve . `BING_SEARCH_V7_SUBSCRIPTION_KEY`

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárából. Ez a parancs alapvető buildfájlokat hoz létre a Gradle számára, beleértve a *build.gradle.kts fájlt* is, amelyet futásidőben használnak az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

Keresse *meg a build.gradle.kts fájlt,* és nyissa meg a kívánt IDE-vel vagy szövegszerkesztővel. Ezután másolja a másolást ebben a buildkonfigurációban:

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

Hozzon létre egy mappát a mintaalkalmazáshoz. A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir -p src/main/java
```

Hozzon létre egy mappát az API-ba feltölteni kívánt lemezképhez. Helyezze a képet az **erőforrások** mappájába.

```console
mkdir -p src/main/resources
``` 

Nyissa meg az új mappát, és hozzon létre egy *BingVisualSearchSample.java*nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy `import` IDE-ben, és adja hozzá a következő állításokat:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Ezután hozzon létre egy új osztályt.

```java
public class BingVisualSearchSample {
}
```

Az alkalmazás metódusában `main` hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat. Ezután `byte[]` hozzon létre egy képet a feltöltendő képhez. Hozzon `try` létre egy blokkot a később definiálandó metódusok számára, és `toByteArray()`töltse be a képet, és alakítsa át bájttá a használatával.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Ez a rövid útmutató a Gradle-függőségkezelőt használja. Az ügyfélkönyvtárat és a többi függőségi kezelő adatait a [Maven központi adattárban](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)találja.

A projekt *build.gradle.kts* fájljában mindenképpen adja meg az `implementation` ügyfélkönyvtárat utasításként. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Bing Visual Search ügyféltárban és a Java-ban:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Vizuális keresési kérelem küldése](#send-a-visual-search-request)
* [A képbetekintési token és a vizuális keresési címkék nyomtatása](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a Bing Visual Search kulcshoz, amelynek neve `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


A fő módszerben ügyeljen arra, hogy az előfizetési kulccsal példányosítson el egy [BingVisualSearchAPI-objektumot.](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable)

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Vizuális keresési kérelem küldése

Egy új módszer, küldje el a rendszerkép bájt tömb (amely a `main()` metódusban jött létre) az ügyfél [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) metódus használatával. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>A képbetekintési token és a vizuális keresési címkék nyomtatása

Ellenőrizze, hogy az [ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) objektum null értékű-e. Ha nem, nyomtassa ki a képelemzési jogkivonatot, a címkék számát, a műveletek számát és az első művelettípust.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást a következő kkel hozhatja létre:

```console
gradle build
```

Futtassa az `run` alkalmazást a következő céllal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](tutorial-bing-visual-search-single-page-app.md)
