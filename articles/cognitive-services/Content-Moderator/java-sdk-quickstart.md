---
title: 'Gyors útmutató: Content Moderator a Javához készült ügyféloldali kódtár'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az Azure Cognitive Services Content Moderator Javához készült ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: 53ac5c10672c433cb16d9ad326d936affe82a8c6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381936"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Gyors útmutató: Content Moderator a Javához készült ügyféloldali kódtár

Ismerkedjen meg a Javához készült Content Moderator-ügyfél függvénytárával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Content Moderator egy olyan kognitív szolgáltatás, amely potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos anyagokra ellenőrzi a szöveget, a képeket és a videó tartalmát. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet.

A Javához készült Content Moderator ügyféloldali kódtár a következőre használható:

* Közepes méretű képek felnőtt vagy zamatos tartalomhoz, szöveghez vagy emberi arcokhoz.

[Dokumentáció](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [mintákhoz](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást Content Moderator a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes hét napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

A próbaverziós előfizetés vagy erőforrás kulcsának lekérése után [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) `AZURE_CONTENTMODERATOR_KEY`nevű kulcshoz.

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```
Futtassa az `gradle init` parancsot. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve a *Build. Gradle. KTS*fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához. Futtassa ezt a parancsot a munkakönyvtárból:

```console
gradle init --type basic
```

Amikor a rendszer kéri, hogy válasszon ki egy Build-parancsfájlt DSL-t, válassza a **Kotlin**lehetőséget.

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba. Ez a konfiguráció definiálja a projektet olyan Java-alkalmazásként, amelynek belépési pontja a **ContentModeratorQuickstart**osztály. Importálja a Content Moderator SDK-t, valamint a Gson SDK-t a JSON-szerializáláshoz.

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

A munkakönyvtárból futtassa a következő parancsot egy projekt forrás mappájának létrehozásához.

```console
mkdir -p src/main/java
```

Ezután hozzon létre egy *ContentModeratorQuickstart. Java* nevű fájlt az új mappában. Nyissa meg a fájlt az előnyben részesített szerkesztőben vagy az IDE-ben, és importálja a következő könyvtárakat felül:

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objektummodell

A következő osztályok a Content Moderator Java SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Ez az osztály minden Content Moderator funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Ez az osztály lehetővé teszi a képek elemzését a felnőtt tartalmak, a személyes adatok vagy az emberi arcok számára.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Ez az osztály a nyelv, a káromkodás, a hibák és a személyes adatok szövegének elemzéséhez nyújt funkciókat.|
|[Értékelés](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Ez az osztály a felülvizsgálati API-k funkcionalitását biztosítja, beleértve a feladatok létrehozására, az egyéni munkafolyamatokra és az emberi felülvizsgálatokra vonatkozó módszereket is.|


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült Content Moderator ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Közepes méretű képek](#moderate-images)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a lépés azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `AZURE_CONTENTMODERATOR_KEY`nevű Content moderator kulcshoz.

Az alkalmazás `main` metódusában hozzon létre egy [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) objektumot az előfizetési végpont értékének és az előfizetési kulcs környezeti változójának használatával. 

> [!NOTE]
> Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Közepes méretű képek

### <a name="get-images"></a>Rendszerképek beolvasása

A projekt **src/Main/** mappájában hozzon létre egy **erőforrás** mappát, és navigáljon hozzá. Ezután hozzon létre egy új szövegfájlt, a *ImageFiles. txt*fájlt. Ebben a fájlban a rendszerképek URL-címeinek hozzáadásával elemezheti&mdash;egy URL-címet az egyes sorokon. A következő minta lemezképeket használhatja:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Segítő osztály definiálása

Ezután adja hozzá a *ContentModeratorQuickstart. Java* fájlban a következő osztály-definíciót a **ContentModeratorQuickstart** osztályban. Ezt a belső osztályt a rendszer a képmoderálási folyamat későbbi részében fogja használni.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iteráció a képeken

Ezután adja hozzá a következő kódot a `main` metódus aljához. Azt is megteheti, hogy felveszi azt egy másik, `main`nevű metódusba. Ez a kód a _ImageFiles. txt_ fájl minden egyes sorára mutat.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Felnőtt/zamatos tartalom keresése
Ez a kódrészlet a megadott URL-címen ellenőrzi a felnőtt vagy a zamatos tartalomhoz tartozó képet. A jelen feltételekkel kapcsolatos információkért tekintse meg a képmoderálás fogalmi útmutatóját.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Szöveg keresése
Ez a kódrészlet a képen látható szöveget ellenőrzi.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Arcok keresése
Ez a kódrészlet ellenőrzi az emberi arcok képét.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Végül tárolja a visszaadott adatokat a `EvaluationData` listán.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Nyomtatási eredmények

Az `while` hurok után adja hozzá a következő kódot, amely kiírja az eredményeket a konzolra, valamint egy kimeneti fájlba, az *src/Main/Resources/ModerationOutput. JSON*fájlra.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Fejezze be a `try` utasítást, és adjon hozzá egy `catch` utasítást a metódus végrehajtásához.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```

Futtassa az alkalmazást az `gradle run` paranccsal:

```console
gradle run
```

Ezután keresse meg a *src/Main/Resources/ModerationOutput. JSON* fájlt, és tekintse meg a tartalom moderálásának eredményét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használhatja a Content Moderator Java-függvénytárat moderálási feladatok végrehajtásához. Következő lépésként tekintse meg a képek vagy más média moderálását egy elméleti útmutató elolvasásával.

> [!div class="nextstepaction"]
>[Képek moderálási fogalmai](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Mi az Azure Content Moderator?](./overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java)található.