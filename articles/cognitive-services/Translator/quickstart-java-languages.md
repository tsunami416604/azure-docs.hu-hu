---
title: 'Gyors útmutató: Támogatott nyelvek, a Java - listájának lekérése a Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a fordítás, átbetűzésű és a Translator Text API használatával szótár keresési támogatott nyelvek listájának lekérése.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: erhopf
ms.openlocfilehash: 446f83ecf81d344163deca58ac4aaf8487292ab4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181044"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-java"></a>Gyors útmutató: Java használatával támogatott nyelvek listáját a Translator Text API használatával

Ebben a rövid útmutatóban a fordítás, átbetűzésű és a Translator Text API használatával szótár keresési támogatott nyelvek listájának lekérése.

## <a name="prerequisites"></a>Előfeltételek

* [JDK 7 vagy újabb verzió](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)

## <a name="initialize-a-project-with-gradle"></a>Gradle-projekt inicializálása

Először hozzon létre egy könyvtárat a projekthez. A parancssor (vagy a Terminálszolgáltatások) a következő parancs futtatásával:

```console
mkdir get-languages-sample
cd get-languages-sample
```

Ezután fog inicializálni a Gradle-projektet. Ezzel a paranccsal fájlokat hoz létre alapvető build gradle-t, a legfontosabb, a `build.gradle.kts`, amelyek futásidőben használatos létrehozni és konfigurálni az alkalmazást. Futtassa ezt a parancsot a munkakönyvtárban:

```console
gradle init --type basic
```

Amikor a rendszer kéri, válassza ki a **DSL**válassza **Kotlin**.

## <a name="configure-the-build-file"></a>A build-fájl konfigurálása

Keresse meg `build.gradle.kts` és nyissa meg a kedvenc IDE-szerkesztőben vagy szövegszerkesztőben. Ezután másolja a build konfigurációját:

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

Jegyezze fel, hogy ez a minta a HTTP-kéréseket OkHttp és Gson kezelni, és JSON elemzése függőségekkel rendelkezik. További tudnivalók a felépítéskonfigurációkban váltogatni szeretné, ha [új buildek Gradle létrehozása](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Hozzon létre egy Java-fájlt

Hozzunk létre egy mappát a mintaalkalmazást. Futtassa a munkakönyvtárban:

```console
mkdir -p src/main/java
```

Ezt követően ebben a mappában hozzon létre egy fájlt `GetLanguages.java`.

## <a name="import-required-libraries"></a>Importálja a szükséges kódtárak

Nyissa meg `GetLanguages.java` , és adja hozzá ezeket kimutatások importálása:

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
public class GetLanguages {
  // All project code goes here...
}
```

Adja hozzá ezeket a sorokat a `GetLanguages` osztály:

```java
String url = "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0";
```

## <a name="create-a-client-and-build-a-request"></a>Hozzon létre egy ügyfél és a egy kérelem létrehozása

Adja hozzá a sort, hogy a `GetLanguages` osztály példányt létrehozni a `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Következő lépésként létrehozzuk a `GET` kérelmet.

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
        GetLanguages getLanguagesRequest = new GetLanguages();
        String response = getLanguagesRequest.Get();
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

Az ország rövidítés találja a jelen [nyelvek listája](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support).

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

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és az átírást is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A Java-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Lásd még

* [Szöveg lefordítása](quickstart-java-translate.md)
* [Szöveg átírása](quickstart-java-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-java-detect.md)
* [Alternatív fordítások beolvasása](quickstart-java-dictionary.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-java-sentences.md)
