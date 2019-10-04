---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9ce4b25d26c0294cf7618b5851c0956af7687ee7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837547"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Projekt inicializálása a Gradle

Kezdjük egy munkakönyvtár létrehozásával ehhez a projekthez. Futtassa a következő parancsot a parancssorból (vagy a terminálból):

```console
mkdir detect-sample
cd detect-sample
```

Ezután egy Gradle-projektet fog inicializálni. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle, ami a legfontosabb, a `build.gradle.kts`, amely a futásidőben használatos az alkalmazás létrehozásához és konfigurálásához. Futtassa ezt a parancsot a munkakönyvtárból:

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

## <a name="configure-the-build-file"></a>A Build fájl konfigurálása

Keresse meg `build.gradle.kts`, majd nyissa meg a kedvenc IDE-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba:

```
plugins {
    java
    application
}
application {
    mainClassName = "Detect"
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

## <a name="create-a-java-file"></a>Hozzon létre egy Java-fájlt

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következőt:

```console
mkdir -p src/main/java
```

Ezután a mappában hozzon létre egy `Detect.java` nevű fájlt.

## <a name="import-required-libraries"></a>Szükséges kódtárak importálása

Nyissa meg `Detect.java`, és adja hozzá a következő importálási utasításokat:

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
public class Detect {
  // All project code goes here...
}
```

Adja hozzá ezeket a sorokat a `Detect` osztályhoz. Figyelje meg, hogy az előfizetési kulcs és a végpont beolvasása környezeti változókból történik:

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/detect?api-version=3.0";
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Ügyfél létrehozása és kérelem készítése

Adja hozzá ezt a sort a `Detect` osztályhoz a `OkHttpClient` létrehozásához:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Ezután készítse el a POST kérést. Nyugodtan módosíthatja a nyelvi észlelés szövegét.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Függvény létrehozása a válasz elemzéséhez

Ez az egyszerű függvény elemzi és prettifies a Translator Text szolgáltatás JSON-válaszát.

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
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
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

A minta futtatása után a következőnek kell megjelennie a terminálon:

> [!NOTE]
> Keresse meg az ország/régió rövidítést ebben a [listában](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
