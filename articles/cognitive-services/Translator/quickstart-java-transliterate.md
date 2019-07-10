---
title: 'Gyors útmutató: Konvertálja a szöveget parancsfájl, a Java - Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban fog elsajátíthatja átírás (átalakítás) szöveg egy parancsfájlból származó Java és a Translator Text REST API egy másik használatával. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: ee6ad0d98dd5fd45e7b32e80350fd9e3b7534f1e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704464"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-java"></a>Gyors útmutató: A Translator Text API használatával átbetűzés szöveg Java használatával

Ebben a rövid útmutatóban fog elsajátíthatja átírás (átalakítás) szöveg egy parancsfájlból származó Java és a Translator Text REST API egy másik használatával. A megadott példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

>[!TIP]
> Ha szeretné egyszerre az összes kód megtekintéséhez, ehhez a mintához forráskódja elérhető a [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Java).

## <a name="prerequisites"></a>Előfeltételek

* [JDK 7 vagy újabb verzió](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="initialize-a-project-with-gradle"></a>Gradle-projekt inicializálása

Először hozzon létre egy könyvtárat a projekthez. A parancssor (vagy a Terminálszolgáltatások) a következő parancs futtatásával:

```console
mkdir transliterate-sample
cd transliterate-sample
```

Ezután fog inicializálni a Gradle-projektet. Ezzel a paranccsal fájlokat hoz létre alapvető build gradle-t, a legfontosabb, a `build.gradle.kts`, amelyek futásidőben használatos létrehozni és konfigurálni az alkalmazást. Futtassa ezt a parancsot a munkakönyvtárban:

```console
gradle init --type basic
```

Amikor a rendszer kéri, válassza ki a **DSL**válassza **Kotlin**.

## <a name="configure-the-build-file"></a>A build-fájl konfigurálása

Keresse meg `build.gradle.kts` és nyissa meg a kedvenc IDE-szerkesztőben vagy szövegszerkesztőben. Ezután másolja a build konfigurációját:

```
plugins {
    java
    application
}
application {
    mainClassName = "Transliterate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Jegyezze fel, hogy ez a minta a HTTP-kéréseket OkHttp és Gson kezelni, és JSON elemzése függőségekkel rendelkezik. További tudnivalók a felépítéskonfigurációkban váltogatni szeretné, ha [új buildek Gradle létrehozása](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Hozzon létre egy Java-fájlt

Hozzunk létre egy mappát a mintaalkalmazást. Futtassa a munkakönyvtárban:

```console
mkdir -p src\main\java
```

Ezt követően ebben a mappában hozzon létre egy fájlt `Transliterate.java`.

## <a name="import-required-libraries"></a>Importálja a szükséges kódtárak

Nyissa meg `Transliterate.java` , és adja hozzá ezeket kimutatások importálása:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Változók meghatározása

Először hozzon létre egy nyilvános osztályt a projekthez lesz szüksége:

```java
public class Transliterate {
  // All project code goes here...
}
```

Adja hozzá ezeket a sorokat a `Transliterate` osztály. Láthatja, hogy az a `api-version`, két további paraméterek hozzáfűzött lett a `url`. Ezek a paraméterek segítségével állítsa be a beviteli nyelv és a parancsfájlok átbetűzésű. Ebben a példában japán nyelvre van beállítva (`jpan`) és a Latin (`latn`). Ellenőrizze, hogy frissíti az előfizetési kulcs értékét.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```
Ha egy több szolgáltatást a Cognitive Services-előfizetést használ, akkor is tartalmaznia kell a `Ocp-Apim-Subscription-Region` az a kérelem paramétereit. [További információ a több szolgáltatásos előfizetéshez való hitelesítés közben](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Hozzon létre egy ügyfél és a egy kérelem létrehozása

Adja hozzá a sort, hogy a `Transliterate` osztály példányt létrehozni a `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Következő lépésként létrehozzuk a POST-kérés. Nyugodtan módosítsa átbetűzésű szövegét.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>A válasz elemzéséhez függvény létrehozása

Ez a függvény egyszerű elemzi, és a JSON-válasz a Translator Text szolgáltatásból prettifies.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés, hogy a kérés és válasz érkezik. Ezek a sorok hozzáadása a projekthez:

```java
public static void main(String[] args) {
    try {
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) keresse meg a munkakönyvtárban gyökérkönyvtárában, és futtassa:

```console
gradle build
```

A build elkészültekor futtassa:

```console
gradle run
```

## <a name="sample-response"></a>Mintaválasz

```json
[
  {
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>További lépések

Vessen egy pillantást az API-referencia az elvégezhető műveletek a Translator Text API ismertetése.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Lásd még

* [Szöveg lefordítása](quickstart-java-translate.md)
* [A beviteli nyelv azonosítása](quickstart-java-detect.md)
* [Alternatív fordítások beolvasása](quickstart-java-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-java-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-java-sentences.md)
