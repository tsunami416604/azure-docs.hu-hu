---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 1d7e1156e55cec546bd620f0402ad0e5946002e4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587023"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Projekt inicializálása a Gradle

Kezdjük egy munkakönyvtár létrehozásával ehhez a projekthez. Futtassa a következő parancsot a parancssorból (vagy a terminálból):

```console
mkdir alt-translation-sample
cd alt-translation-sample
```

Ezután egy Gradle-projektet fog inicializálni. Ez a parancs a Gradle nélkülözhetetlen Build-fájljait hozza létre, ami a legfontosabb `build.gradle.kts` : a, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához. Futtassa ezt a parancsot a munkakönyvtárból:

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

## <a name="configure-the-build-file"></a>A Build fájl konfigurálása

Keresse meg `build.gradle.kts` és nyissa meg kedvenc ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba:

```
plugins {
    java
    application
}
application {
    mainClassName = "AltTranslation"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Vegye figyelembe, hogy ez a minta a HTTP-kérések OkHttp, valamint a JSON kezelésére és elemzésére szolgáló Gson-függőségekkel rendelkezik. Ha többet szeretne megtudni a Build konfigurációkról, tekintse meg az [új Gradle-buildek létrehozását](https://guides.gradle.org/creating-new-gradle-builds/)ismertető témakört.

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következőt:

```console
mkdir -p src\main\java
```

Ezután a mappában hozzon létre egy nevű fájlt `AltTranslation.java` .

## <a name="import-required-libraries"></a>Szükséges kódtárak importálása

Nyissa meg `AltTranslation.java` és adja hozzá a következő importálási utasításokat:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Változók meghatározása

Először létre kell hoznia egy nyilvános osztályt a projekthez:

```java
public class AltTranslation {
  // All project code goes here...
}
```

Adja hozzá ezeket a sorokat a `AltTranslation` osztályhoz. Először is az előfizetési kulcsot és a végpontot olvassa a rendszer környezeti változókból. Ezt követően láthatja, hogy a-val együtt `api-version` két további paraméter van hozzáfűzve a következőhöz: `url` . Ezek a paraméterek a fordítás bemenetének és kimenetének beállítására szolgálnak. Ebben a példában az angol ( `en` ) és a spanyol ( `es` ).

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/dictionary/lookup?api-version=3.0&from=en&to=es";
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek is szerepelnie kell `Ocp-Apim-Subscription-Region` . [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Ügyfél létrehozása és kérelem készítése

Adja hozzá ezt a sort a `AltTranslation` osztályhoz a következő létrehozásához `OkHttpClient` :

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Ezután készítse el a POST kérést. Nyugodtan módosíthatja a fordítás szövegét.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Pineapples\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Függvény létrehozása a válasz elemzéséhez

Ez az egyszerű függvény elemzi és prettifies a Translator szolgáltatástól érkező JSON-választ.

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

Az utolsó lépés a kérelem elkészítése és a válasz beolvasása. Adja hozzá ezeket a sorokat a projekthez:

```java
public static void main(String[] args) {
    try {
        AltTranslation altTranslationRequest = new AltTranslation();
        String response = altTranslationRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ekkor készen áll a minta alkalmazás futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a munkakönyvtár gyökeréhez, és futtassa a következőt:

```console
gradle build
```

A létrehozás befejeződése után futtassa a következőket:

```console
gradle run
```

## <a name="sample-response"></a>Mintaválasz

```json
[
  {
    "normalizedSource": "pineapples",
    "displaySource": "pineapples",
    "translations": [
      {
        "normalizedTarget": "piñas",
        "displayTarget": "piñas",
        "posTag": "NOUN",
        "confidence": 0.7016,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 5,
            "frequencyCount": 158
          },
          {
            "normalizedText": "cones",
            "displayText": "cones",
            "numExamples": 5,
            "frequencyCount": 13
          },
          {
            "normalizedText": "piña",
            "displayText": "piña",
            "numExamples": 3,
            "frequencyCount": 5
          },
          {
            "normalizedText": "ganks",
            "displayText": "ganks",
            "numExamples": 2,
            "frequencyCount": 3
          }
        ]
      },
      {
        "normalizedTarget": "ananás",
        "displayTarget": "ananás",
        "posTag": "NOUN",
        "confidence": 0.2984,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 2,
            "frequencyCount": 16
          }
        ]
      }
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a fordítóval.

> [!div class="nextstepaction"]
> [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
