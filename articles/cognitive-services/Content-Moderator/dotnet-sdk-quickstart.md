---
title: 'Rövid útmutató: Tartalommoderátor ügyféltár a .NET-hez'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval ismerkedje meg a .NET tartalommoderátor ügyféltárával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: e93cfd6d44e6a59b4b3d94a49bef766fadf4381c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770621"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Rövid útmutató: Tartalommoderátor ügyféltár a .NET-hez

Ismerkedés a tartalommoderátor ügyféltárával. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A tartalommoderátor egy kognitív szolgáltatás, amely ellenőrzi a szöveg-, kép- és videotartalmakat olyan anyagok esetében, amelyek potenciálisan sértőek, kockázatosak vagy más módon nemkívánatosak. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelni tudja a megjelölt tartalmat, hogy megfeleljen a szabályozásoknak, vagy fenntarthassa a kívánt környezetet a felhasználók számára.

A .NET tartalommoderátor ügyféltárának használata:

* [Mérsékelt szöveg](#moderate-text)
* [Mérsékelt képek](#moderate-images)
* [Vélemény létrehozása](#create-a-review)

[Referenciadokumentációkönyvtár](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [forráskódjának](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [mintái](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .

## <a name="setting-up"></a>Beállítása

### <a name="create-a-content-moderator-azure-resource"></a>Tartalommoderátor Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a tartalommoderátor számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Az erőforrás megtekintése az [Azure Portalon](https://portal.azure.com/)

Miután lekért egy kulcsot a próba-előfizetés vagy erőforrás, [hozzon létre környezeti változók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) at a kulcs és a végpont URL-cím, nevű, `CONTENT_MODERATOR_SUBSCRIPTION_KEY` illetve. `CONTENT_MODERATOR_ENDPOINT`

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core alkalmazást a kívánt szövegszerkesztőben vagy IDE-ben. 

Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal `content-moderator-quickstart`hozhat létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

```console
dotnet build
```

A build kimenetnem tartalmazhat figyelmeztetéseket vagy hibákat. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárából nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ben. Adja hozzá a következő `using`-utasításokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

A **Program** osztályban hozzon létre változókat az erőforrás végponthelyéhez és kulcsához környezeti változóként.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változókat, be kell zárnia, majd újra meg kell nyitnia a változók eléréséhez futtató szerkesztőt, IDE-t vagy rendszerhéjat.

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Az alkalmazáskönyvtáron belül telepítse a .NET tartalommoderátor ügyféltárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok kezelik a tartalommoderátor .NET SDK néhány főbb szolgáltatását.

|Name (Név)|Leírás|
|---|---|
|[ContentModerator-ügyfél](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Ez az osztály szükséges a Tartalommoderátor összes funkciójához. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket.|
|[Képmoderálás](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Ez az osztály biztosítja a felnőtteknek szánt tartalmak, személyes adatok vagy emberi arcok képeinek elemzéséhez szükséges funkciókat.|
|[TextModeráció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Ez az osztály biztosítja a nyelvi, káromkodási, hiba- és személyes adatok szövegének elemzéséhez szükséges funkciókat.|
|[Felülvizsgálatok](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Ez az osztály a véleményezési API-k funkcióit biztosítja, beleértve a munkahelyek létrehozásának módszereit, az egyéni munkafolyamatokat és az emberi véleményeket.|

## <a name="code-examples"></a>Kódpéldák


Ezek a kódrészletek megmutatják, hogyan kell elvégezni a következő feladatokat a .NET Tartalommoderátor ügyféltárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Mérsékelt szöveg](#moderate-text)
* [Mérsékelt képek](#moderate-images)
* [Vélemény létrehozása](#create-a-review)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új módszer, példányos ügyfél objektumok a végpont és a kulcs. Nem kell minden forgatókönyvhöz más ügyfél, de ez segíthet a kód rendszerezésben tartásában.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Mérsékelt szöveg

A következő kód egy tartalommoderátor-ügyfél segítségével elemzi a szövegtörzset, és kinyomtatja az eredményeket a konzolra. A **Program** osztály gyökerében definiálja a bemeneti és kimeneti fájlokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Ezután a projekt gyökerében, és adjunk hozzá egy *TextFile.txt* fájlt. Adja hozzá saját szövegét a fájlhoz, vagy használja a következő mintaszöveget:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Adja hozzá a következő `Main` metódushívást a metódushoz:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

Ezután adja meg a szövegmoderálási módszert valahol a **Program** osztályban:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Mérsékelt képek

A következő kód egy tartalommoderátor-ügyfelet és egy [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) objektumot használ a távoli képek felnőtt és pikáns tartalom elemzéséhez.

> [!NOTE]
> Elemezheti a helyi lemezkép tartalmát is. Tekintse meg a helyi lemezképekkel dolgozó metódusokat és műveleteket tartalmazó [referenciadokumentációt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)

### <a name="get-sample-images"></a>Mintaképek beszereznie

Adja meg a bemeneti és kimeneti fájlokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Ezután hozza létre a bemeneti fájlt, *ImageFiles.txt*, a gyökere a projekt. Ebben a fájlban a képek URL-címeit adja hozzá, hogy minden sorban egy URL-t elemezhet.&mdash; A következő mintaképeket használhatja:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Adja át a bemeneti és kimeneti `Main` fájlokat a következő metódushívás a metódusban. Ezt a módszert egy későbbi lépésben fogja meghatározni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Segítő osztály definiálása

Adja hozzá a következő osztálydefiníciót a **Program** osztályba. Ez a belső osztály kezeli a képmoderálás eredményeit.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>A képmoderálási módszer meghatározása

A következő módszer végighalad a szövegfájlban lévő kép URL-címein, létrehoz egy **EvaluationData** példányt, és elemzi a képet a felnőtt/pikáns tartalom, a szöveg és az emberi arcok szempontjából. Ezután hozzáadja a végleges **EvaluationData** példányt egy listához, és a visszaadott adatok teljes listáját írja a konzolra.

#### <a name="iterate-through-image-urls"></a>KépURL-ek en keresztüli iteres

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Tartalom elemzése

A tartalommoderátor által a képjellemzőkről a [Képmoderálási útmutatóban](./image-moderation-api.md) talál további információt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Moderálási eredmények fájlba írása

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Vélemény létrehozása

A tartalommoderátor .NET SDK segítségével tartalmakat táplálhat be a [Véleményezés eszközbe,](https://contentmoderator.cognitive.microsoft.com) hogy az emberi moderátorok áttekinthessék azokat. Ha többet szeretne megtudni a Véleményezés eszközről, olvassa el a [Felülvizsgálati eszköz fogalmi útmutatóját.](./review-tool-user-guide/human-in-the-loop.md)

Ebben a szakaszban a módszer a [Vélemények](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) osztály segítségével hozzon létre egy felülvizsgálatot, kérje le az azonosítóját, és ellenőrizze annak részleteit, miután megkapta az emberi bevitelt a Felülvizsgálati eszköz internetes portálján keresztül. Ez az összes információ egy kimeneti szövegfájlban. Hívja meg a `Main` módszert a módszer:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Mintaképek beszereznie

Deklarálja a következő tömböt a **Program** osztály gyökerében. Ez a változó egy mintaképre hivatkozik, amelyet a felülvizsgálat létrehozásához kell használni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Ellenőrzési hitelesítő adatok beszereznie

Jelentkezzen be a [Véleményezés eszközbe,](https://contentmoderator.cognitive.microsoft.com) és olvassa be a csapatnevét. Ezután rendelje hozzá a **Program** osztály megfelelő változójához. Szükség esetén beállíthat egy visszahívási végpontot, hogy megkapja a frissítéseket az ellenőrzés tevékenységéről.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Segítő osztály definiálása

Adja hozzá a következő osztálydefiníciót a **Program** osztályba. Ez az osztály egyetlen ellenőrző példány ábrázolására szolgál, amelyet a Véleményezés eszköz küld el.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Segítő metódus definiálása

Adja hozzá a következő módszert a **Program** osztályhoz. Ez a módszer a kimeneti szövegfájlba írja a felülvizsgálati lekérdezések eredményeit.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Az ellenőrzés létrehozási módjának meghatározása

Most már készen áll, hogy meghatározza a módszert, amely kezeli a felülvizsgálat létrehozása és lekérdezése. Adjon hozzá egy új metódust, **a CreateReviews**és a következő helyi változókat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Vélemények feladása a Véleményezés eszközben

Ezután adja hozzá a következő kódot a megadott mintaképeken keresztül, adja hozzá a metaadatokat, és küldje el őket a Felülvizsgálati eszköznek egyetlen kötegben. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Az API-hívásból visszaadott objektum egyedi azonosítóértékeket fog tartalmazni minden feltöltött képhez. A következő kód elemzi ezeket az azonosítókat, majd ezeket használja a tartalommoderátor lekérdezéséhez a kötegben lévő egyes képek állapotáról.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Véleményezési részletek beszerezni

A következő kód hatására a program megvárja a felhasználói bevitelt. Amikor futásidőben erre a lépésre érkezik, saját maga is megnyissa a [Véleményezés eszközt,](https://contentmoderator.cognitive.microsoft.com) ellenőrizze, hogy a mintakép fel lett-e töltve, és kölcsönhatásba léphet vele. Az értékelésekkel való interakcióról az [Értékelések útmutatójában](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)talál további információt. Ha végzett, bármelyik billentyű tavasa lehet a program folytatásához és az ellenőrzési folyamat eredményeinek beolvasásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Ha ebben a forgatókönyvben visszahívási végpontot használt, az alábbi formátumú eseményt kell kapnia:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `dotnet run` alkalmazáskönyvtárból a paranccsal.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Tartalommoderátor .NET függvénytárat moderálási feladatok elvégzésére. Ezután tudjon meg többet a képek vagy más médiatartalmak moderálásáról egy koncepcionális útmutató elolvasásával.

> [!div class="nextstepaction"]
> [Képmoderálási fogalmak](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Mi az az Azure Content Moderator?](./overview.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs)
