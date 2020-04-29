---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: ad5c51b3d373947e8a09762b0cb27afff990e6da
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "69906645"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Projekt inicializálása a Gradle

Kezdjük egy munkakönyvtár létrehozásával ehhez a projekthez. Futtassa a következő parancsot a parancssorból (vagy a terminálból):

```console
mkdir transliterate-sample
cd transliterate-sample
```

Ezután egy Gradle-projektet fog inicializálni. Ez a parancs a Gradle nélkülözhetetlen Build-fájljait hozza létre, ami a `build.gradle.kts`legfontosabb: a, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához. Futtassa ezt a parancsot a munkakönyvtárból:

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

## <a name="configure-the-build-file"></a>A Build fájl konfigurálása

Keresse `build.gradle.kts` meg és nyissa meg kedvenc ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba:

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

Vegye figyelembe, hogy ez a minta a HTTP-kérések OkHttp, valamint a JSON kezelésére és elemzésére szolgáló Gson-függőségekkel rendelkezik. Ha többet szeretne megtudni a Build konfigurációkról, tekintse meg az [új Gradle-buildek létrehozását](https://guides.gradle.org/creating-new-gradle-builds/)ismertető témakört.

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következőt:

```console
mkdir -p src\main\java
```

Ezután a mappában hozzon létre egy nevű `Transliterate.java`fájlt.

## <a name="import-required-libraries"></a>Szükséges kódtárak importálása

Nyissa meg `Transliterate.java` és adja hozzá a következő importálási utasításokat:

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
public class Transliterate {
  // All project code goes here...
}
```

Adja hozzá ezeket a sorokat `Transliterate` a osztályhoz. Először is az előfizetési kulcsot és a végpontot olvassa a rendszer környezeti változókból. Ezt követően láthatja, hogy a `api-version`-val együtt két további paraméter van hozzáfűzve a `url`következőhöz:. Ezek a paraméterek a szövegbeviteli nyelv és az írásos parancsfájlok beállítására szolgálnak. Ebben a példában a japán (`jpan`) és a latin (`latn`) értékre van állítva. 

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```
Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Ügyfél létrehozása és kérelem készítése

Adja hozzá ezt a sort `Transliterate` a osztályhoz a következő `OkHttpClient`létrehozásához:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Ezután készítse el a POST kérést. Nyugodtan módosíthatja az írás szövegét.

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
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
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
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
