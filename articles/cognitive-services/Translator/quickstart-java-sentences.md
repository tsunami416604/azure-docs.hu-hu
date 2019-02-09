---
title: 'Gyors útmutató: Mondat visszaadni, a Java - Get a Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban fog megtudhatja, miként állapítható meg, a Java és a Translator Text API használatával mondat hossza.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: e15934fb849e38d69772117a7578289a5843801a
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963471"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-java"></a>Gyors útmutató: A Translator Text API segítségével meghatározhatja, mondat hossza Java használatával

Ebben a rövid útmutatóban fog elsajátíthatja meghatározása a mondat hosszúságok a Java és a Translator Text API használatával.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* [JDK 7 vagy újabb verzió](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="initialize-a-project-with-gradle"></a>Gradle-projekt inicializálása

Először hozzon létre egy könyvtárat a projekthez. A parancssor (vagy a Terminálszolgáltatások) a következő parancs futtatásával:

```console
mkdir break-sentence-sample
cd break-sentence-sample
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
    mainClassName = "BreakSentence"
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
mkdir -p src/main/java
```

Ezt követően ebben a mappában hozzon létre egy fájlt `BreakSentence.java`.

## <a name="import-required-libraries"></a>Importálja a szükséges kódtárak

Nyissa meg `BreakSentence.java` , és adja hozzá ezeket kimutatások importálása:

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
public class BreakSentence {
  // All project code goes here...
}
```

Adja hozzá ezeket a sorokat a `BreakSentence` osztály. Láthatja, hogy az a `api-version`, meghatározhatja a beviteli nyelv. Ebben a példában az angol.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```

## <a name="create-a-client-and-build-a-request"></a>Hozzon létre egy ügyfél és a egy kérelem létrehozása

Adja hozzá a sort, hogy a `BreakSentence` osztály példányt létrehozni a `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Következő lépésként létrehozzuk a POST-kérés. Nyugodtan módosítsa a szöveget. A szöveg escape-karakterrel.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"How are you? I am fine. What did you do today?\"\n}]");
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
        BreakSentence breakSentenceRequest = new BreakSentence();
        String response = breakSentenceRequest.Post();
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

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és az átírást is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A Java-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Lásd még

* [Szöveg lefordítása](quickstart-java-translate.md)
* [Szöveg átírása](quickstart-java-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-java-detect.md)
* [Alternatív fordítások beolvasása](quickstart-java-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-java-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-java-sentences.md)
