---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 34ff0e792fc388f3083e2d490b2658822793988f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906916"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Projekt inicializálása a Gradle programmal

Kezdjük azzal, hogy létrehoz egy munkakönyvtárat ehhez a projekthez. A parancssorból (vagy terminálból) futtassa a következő parancsot:

```console
mkdir get-languages-sample
cd get-languages-sample
```

Ezután egy Gradle projektet fog inicializálni. Ez a parancs alapvető buildfájlokat hoz létre `build.gradle.kts`a Gradle számára, ami a legfontosabb, a , amelyet futásidőben az alkalmazás létrehozásához és konfigurálásához használnak. Futtassa ezt a parancsot a munkakönyvtárából:

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL**kiválasztására, válassza a **Kotlin**lehetőséget.

## <a name="configure-the-build-file"></a>A buildfájl konfigurálása

Keresse `build.gradle.kts` meg és nyissa meg kedvenc IDE-jével vagy szövegszerkesztőjével. Ezután másolja a másolást ebben a buildkonfigurációban:

```java
plugins {
    java
    application
}
application {
    mainClassName = "GetLanguages"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Vegye figyelembe, hogy ez a minta függőségek OkHttp HTTP-kérelmek, és Gson kezelésére és elemzésére JSON. Ha többet szeretne megtudni a buildkonfigurációkról, olvassa el [az Új színátmenetes buildek létrehozása (Új színátmenetek) témakört.](https://guides.gradle.org/creating-new-gradle-builds/)

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Hozzunk létre egy mappát a mintaalkalmazáshoz. A munkakönyvtárból futtassa a következőt:

```console
mkdir -p src/main/java
```

Ezután ebben a mappában `GetLanguages.java`hozzon létre egy nevű fájlt.

## <a name="import-required-libraries"></a>Szükséges tárak importálása

Nyissa `GetLanguages.java` meg és adja hozzá ezeket az importálási kimutatásokat:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```

## <a name="define-variables"></a>Változók meghatározása

Először is létre kell hoznia egy nyilvános osztályt a projekthez:

```java
public class GetLanguages {
  // All project code goes here...
}
```

Adja hozzá ezeket a sorokat az `GetLanguages` osztályhoz. Észre fogja venni, hogy az előfizetési kulcsot és a végpontot a környezeti változókból olvassa be:

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/languages?api-version=3.0";
```

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="create-a-client-and-build-a-request"></a>Ügyfél létrehozása és kérelem létrehozása

Adja hozzá ezt `GetLanguages` a sort az `OkHttpClient`osztályhoz a következők példányosításához:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Következő, nézzük építeni `GET` a kérelmet.

```java
// This function performs a GET request.
public String Get() throws IOException {
    Request request = new Request.Builder()
            .url(url).get()
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>A válasz elemzéséhez hozzon létre egy függvényt

Ez az egyszerű függvény elemzi és támogatja a Fordító szöveg szolgáltatásJSON-válaszát.

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

Az utolsó lépés az, hogy egy kérelmet, és kap választ. Adja hozzá ezeket a sorokat a projekthez:

```java
public static void main(String[] args) {
    try {
        GetLanguages getLanguagesRequest = new GetLanguages();
        String response = getLanguagesRequest.Get();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a munkakönyvtár gyökerét, és futtassa a következőket:

```console
gradle build
```

Amikor a build befejeződik, futtassa a következőket:

```console
gradle run
```

## <a name="sample-response"></a>Mintaválasz

Keresse meg az ország/régió rövidítését ebben [a nyelveklistájában.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-hivatkozást, hogy megértse, mit tehet a Translator Text API-val.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
