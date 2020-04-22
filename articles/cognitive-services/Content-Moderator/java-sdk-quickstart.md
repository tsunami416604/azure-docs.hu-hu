---
title: 'Rövid útmutató: Tartalommoderátor ügyféltár Java-hoz'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az Azure Cognitive Services tartalommoderátor java-beli ügyféltárját.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: f86a54b53b73da9c798564d13d659844842e6f67
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768702"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Rövid útmutató: Tartalommoderátor ügyféltár Java-hoz

Ismerkedés a Tartalommoderátor java-ügyféltárával. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A tartalommoderátor egy kognitív szolgáltatás, amely ellenőrzi a szöveg-, kép- és videotartalmakat olyan anyagok esetében, amelyek potenciálisan sértőek, kockázatosak vagy más módon nemkívánatosak. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet.

Használja a Tartalommoderátor java-alapú ügyféltárat a következőkhöz:

* Közepes képek felnőtt vagy pikáns tartalomhoz, szöveghez vagy emberi arcokhoz.

[Referenciadokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [-műtermék (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [minták](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle build eszköz](https://gradle.org/install/)vagy egy másik függőségi menedzser.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-content-moderator-azure-resource"></a>Tartalommoderátor Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a tartalommoderátor számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Tekintse meg az erőforrást az [Azure Portalon.](https://portal.azure.com/)

Miután bekért egy kulcsot a próbaverziós előfizetésből vagy `AZURE_CONTENTMODERATOR_KEY`erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcshoz, amelynek neve .

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt. 

```console
mkdir myapp && cd myapp
```
Futtassa az `gradle init` parancsot. Ez a parancs alapvető buildfájlokat hoz létre a Gradle számára, beleértve a *build.gradle.kts fájlt,* amely et futásidőben az alkalmazás létrehozásához és konfigurálásához használják. Futtassa ezt a parancsot a munkakönyvtárából:

```console
gradle init --type basic
```

Amikor a rendszer kéri a dsl-parancsfájl kiválasztását, válassza a **Kotlin**lehetőséget.

Keresse *meg a build.gradle.kts fájlt,* és nyissa meg a kívánt IDE vagy szövegszerkesztővel. Ezután másolja a következő buildkonfiguráció. Ez a konfiguráció java alkalmazásként definiálja a projektet, amelynek belépési pontja a **ContentModeratorQuickstart**osztály. Importálja a Tartalommoderátor SDK-t, valamint a Gson sdk-t a JSON szerializációhoz.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

A munkakönyvtárból futtassa a következő parancsot a projektforrás-mappa létrehozásához.

```console
mkdir -p src/main/java
```

Ezután hozzon létre egy *ContentModeratorQuickstart.java* nevű fájlt az új mappában. Nyissa meg a fájlt a kívánt szerkesztőben vagy az IDE-ben, és importálja a következő könyvtárakat a tetején:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objektummodell

A következő osztályok kezelik a Java SDK tartalommoderátor néhány főbb jellemzőjét.

|Name (Név)|Leírás|
|---|---|
|[ContentModerator-ügyfél](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Ez az osztály szükséges a Tartalommoderátor összes funkciójához. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket.|
|[Képmoderálás](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Ez az osztály biztosítja a felnőtteknek szánt tartalmak, személyes adatok vagy emberi arcok képeinek elemzéséhez szükséges funkciókat.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Ez az osztály biztosítja a nyelvi, káromkodási, hiba- és személyes adatok szövegének elemzéséhez szükséges funkciókat.|
|[Felülvizsgálatok](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Ez az osztály a véleményezési API-k funkcióit biztosítja, beleértve a munkahelyek létrehozásának módszereit, az egyéni munkafolyamatokat és az emberi véleményeket.|


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Tartalommoderátor javajú ügyféltárban:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Mérsékelt képek](#moderate-images)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a lépés feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a tartalommoderátor kulcsához, melynek neve `AZURE_CONTENTMODERATOR_KEY`.

Az alkalmazás metódusában `main` hozzon létre egy [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) objektumot az előfizetésvégpont-érték és az előfizetési kulcs környezeti változó használatával. 

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Mérsékelt képek

### <a name="get-images"></a>Képek bekérése

A projekt **src/main/folder** mappájában hozzon létre egy **erőforrásmappát,** és keresse meg azt. Ezután hozzon létre egy új szövegfájlt, *ImageFiles.txt*. Ebben a fájlban a képek URL-címeit adja hozzá, hogy minden sorban egy URL-t elemezhet.&mdash; A következő mintaképeket használhatja:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Segítő osztály definiálása

Ezután a *ContentModeratorQuickstart.java* fájlban adja hozzá a következő osztálydefiníciót a **ContentModeratorQuickstart** osztályon belül. Ez a belső osztály a képmoderálási folyamat későbbi részében lesz használva.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Képekkel átitatva

Ezután adja hozzá a következő `main` kódot a metódus aljához. Másik lehetőségként hozzáadhatja egy külön metódushoz, `main`amelyet a-ból hívnak. Ez a kód az _ImageFiles.txt_ fájl minden sorát végighalad.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Felnőtt/pikáns tartalom ellenőrzése
Ez a kódsor ellenőrzi a képet az adott URL-címen a felnőtt vagy pikáns tartalom. A jelen feltételekkel kapcsolatos információkért tekintse meg a Képmoderálás fogalmi útmutatóját.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Szöveg ellenőrzése
Ez a kódsor ellenőrzi a képen a látható szöveget.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Arcok ellenőrzése
Ez a kódsor ellenőrzi a képet az emberi arcok.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Végül tárolja a visszaküldött `EvaluationData` adatokat a listában.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Eredmények nyomtatása

A `while` ciklus után adja hozzá a következő kódot, amely kinyomtatja az eredményeket a konzolra és egy kimeneti fájlba, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zárja be `try` az utasítást, és adjon hozzá egy `catch` utasítást a metódus befejezéséhez.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást a következő kkel hozhatja létre:

```console
gradle build
```

Futtassa az `gradle run` alkalmazást a következő paranccsal:

```console
gradle run
```

Ezután keresse meg az *src/main/resources/ModerationOutput.json* fájlt, és tekintse meg a tartalommoderálás eredményeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Tartalommoderátor Java függvénytárát moderálási feladatok végrehajtására. Ezután tudjon meg többet a képek vagy más médiatartalmak moderálásáról egy koncepcionális útmutató elolvasásával.

> [!div class="nextstepaction"]
>[Képmoderálási fogalmak](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Mi az az Azure Content Moderator?](./overview.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)