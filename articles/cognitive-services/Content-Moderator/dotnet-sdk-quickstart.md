---
title: 'Rövid útmutató: a .NET-hez készült ügyféloldali kódtár Content Moderator | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a .NET-hez készült Content Moderator ügyféloldali kódtáraval.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: cb812a0432e5fdb2828054751ef35f3de82226e7
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539035"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Rövid útmutató: a .NET-hez készült ügyféloldali kódtár Content Moderator

Ismerkedjen meg a .NET-hez készült Content Moderator ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Content Moderator egy olyan kognitív szolgáltatás, amely potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos anyagokra ellenőrzi a szöveget, a képeket és a videó tartalmát. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután képes kezelni a megjelölt tartalmat, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.

A .NET-hez készült Content Moderator ügyféloldali kódtára a következőre használható:

* [Mérsékelt szöveg](#moderate-text)
* [Közepes méretű képek](#moderate-images)
* [Felülvizsgálat létrehozása](#create-a-review)

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [minták](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást Content Moderator a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes hét napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Az erőforrás megtekintése a [Azure Portal](https://portal.azure.com/)

A próbaverziós előfizetésből vagy erőforrásból származó kulcs lekérése után [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és végpont URL-címéhez, `CONTENT_MODERATOR_SUBSCRIPTION_KEY` és `CONTENT_MODERATOR_ENDPOINT`névvel.

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást a kívánt szövegszerkesztőben vagy az IDE-ben. 

Egy konzolablak (például cmd, PowerShell vagy bash) használatával hozzon létre egy új, `content-moderator-quickstart`nevű Console-alkalmazást a `dotnet new` paranccsal. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C# projektet hoz létre egyetlen forrásfájlban: *program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using`-utasításokat:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

A **program** osztályban hozzon létre változókat az erőforrás végpontjának helyéhez és a kulcshoz környezeti változókként.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változókat, akkor a változók eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse az Content Moderator .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok a Content Moderator .NET SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Ez az osztály minden Content Moderator funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Ez az osztály lehetővé teszi a képek elemzését a felnőtt tartalmak, a személyes adatok vagy az emberi arcok számára.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Ez az osztály a nyelv, a káromkodás, a hibák és a személyes adatok szövegének elemzéséhez nyújt funkciókat.|
|[Értékelés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Ez az osztály a felülvizsgálati API-k funkcionalitását biztosítja, beleértve a feladatok létrehozására, az egyéni munkafolyamatokra és az emberi felülvizsgálatokra vonatkozó módszereket is.|

## <a name="code-examples"></a>Példák a kódokra


Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az Content Moderator .NET-hez készült ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Mérsékelt szöveg](#moderate-text)
* [Közepes méretű képek](#moderate-images)
* [Felülvizsgálat létrehozása](#create-a-review)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban hozza létre az ügyféloldali objektumokat a végponttal és a kulccsal. Minden forgatókönyvhöz nincs szüksége más ügyfélre, de a kód megtartható.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Mérsékelt szöveg

A következő kód egy Content Moderator ügyfelet használ egy szöveg szövegtörzsének elemzésére és az eredmények a konzolra való kinyomtatására. A **program** osztály gyökérkönyvtárában adja meg a bemeneti és kimeneti fájlokat:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Ezután a projekt gyökerében adjon hozzá egy *TextFile. txt* fájlt. Adja hozzá saját szövegét a fájlhoz, vagy használja a következő mintaszöveg:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Adja hozzá a következő metódus-hívást a `Main` metódushoz:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Ezután adja meg a Text moderációs metódust a **program** osztályban:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Közepes méretű képek

A következő kód egy Content Moderator ügyfelet használ a [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) objektummal együtt, hogy elemezze a felnőtteknek és a zamatos tartalomnak a távoli lemezképeit.

> [!NOTE]
> Elemezheti egy helyi rendszerkép tartalmát is. A helyi rendszerképekkel működő módszerekhez és műveletekhez a [dokumentációban](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) talál útmutatást.

### <a name="get-sample-images"></a>Mintaképek beolvasása

Adja meg a bemeneti és kimeneti fájlokat:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Ezután hozza létre a bemeneti fájlt ( *ImageFiles. txt*) a projekt gyökérkönyvtárában. Ebben a fájlban a rendszerképek URL-címeinek hozzáadásával elemezheti&mdash;egy URL-címet az egyes sorokon. A következő minta lemezképeket használhatja:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Adja át a bemeneti és kimeneti fájljait a következő metódus hívásának a `Main` metódusban. Ezt a metódust egy későbbi lépésben kell megadnia.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Segítő osztály definiálása

Adja hozzá a következő osztály-definíciót a **program** osztályon belül. Ez a belső osztály a képek moderálásának eredményeit fogja kezelni.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>A képmoderálási módszer megadása

A következő módszer a képurl-címek egy szövegfájlban való megismétlését, egy **EvaluationData** -példány létrehozását, valamint a felnőtt/zamatos tartalom, a szöveg és az emberi arcok rendszerképének elemzését végzi. Ezután hozzáadja a végső **EvaluationData** -példányt egy listához, és a visszaadott adatot a konzolra írja.

#### <a name="iterate-through-image-urls"></a>Iteráció a képurl-címek használatával

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Tartalom elemzése

További információ a Content Moderator képernyőt tartalmazó képattribútumokról: a [képek moderálási fogalmi](./image-moderation-api.md) útmutatója.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Moderációs eredmények írása fájlba

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Felülvizsgálat létrehozása

A Content Moderator .NET SDK használatával tartalmat adhat a [felülvizsgálati eszközhöz](https://contentmoderator.cognitive.microsoft.com) , így az emberi moderátorok áttekinthetik azt. További információ a felülvizsgálati eszközről: a [felülvizsgálati eszköz fogalmi útmutatója](./review-tool-user-guide/human-in-the-loop.md).

Az ebben a szakaszban szereplő metódus a [Reviews osztály használatával](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) hozza létre a felülvizsgálatot, beolvassa az azonosítóját, és az emberi adatoknak a felülvizsgálati eszköz webportálon való fogadása után ellenőrizze annak adatait. Az összes információt egy kimeneti szövegfájlban naplózza. Hívja meg a metódust a `Main` metódusból:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Mintaképek beolvasása

Deklarálja a következő tömböt a **program** osztályának gyökerében. Ez a változó a felülvizsgálat létrehozásához használandó mintát ábrázolja.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Felülvizsgálati hitelesítő adatok beolvasása

Jelentkezzen be a [felülvizsgálati eszközbe](https://contentmoderator.cognitive.microsoft.com) , és kérje le a csapat nevét. Ezután rendelje hozzá a **program** osztály megfelelő változóhoz. Megadhat egy visszahívási végpontot is, amely a felülvizsgálat tevékenységére vonatkozó frissítéseket fogad.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Segítő osztály definiálása

Adja hozzá a következő osztály-definíciót a **program** osztályán belül. Ez az osztály a felülvizsgálati eszközhöz elküldött egyetlen felülvizsgálati példányra lesz felhasználva.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Segítő módszer definiálása

Adja hozzá a **Program** osztályhoz a következő metódust. Ez a metódus a lekérdezési lekérdezések eredményét fogja írni a kimeneti szövegfájlba.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>A felülvizsgálati módszer megadása

Most már készen áll arra, hogy meghatározza azt a metódust, amely kezeli a felülvizsgálat létrehozását és lekérdezését. Adjon hozzá egy új metódust, **CreateReviews**, és adja meg a következő helyi változókat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>A felülvizsgálati eszközre vonatkozó felülvizsgálatok közzététele

Ezután adja hozzá a következő kódot, hogy megismételje az adott mintaképeket, adjon hozzá metaadatokat, és küldje el őket a felülvizsgálati eszközre egyetlen kötegben. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

Az API-hívás által visszaadott objektum egyedi azonosító értékeket tartalmaz minden feltöltött rendszerképhez. A következő kód elemzi ezeket az azonosítókat, majd a kötegben lévő egyes rendszerképek állapotának lekérdezéséhez használja azokat Content Moderator.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Áttekintés részleteinek beolvasása

A következő kód azt eredményezi, hogy a program megvárja a felhasználói bevitelt. Ha ezt a lépést futásidőben futtatja, nyissa meg a [felülvizsgálati eszközt](https://contentmoderator.cognitive.microsoft.com) , és ellenőrizze, hogy a minta rendszerképe fel lett-e töltve, és hogyan használhatja azt. A felülvizsgálattal való interakcióval kapcsolatos információkért tekintse meg a következő témakört: ismertető [útmutató](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Ha elkészült, a billentyű lenyomásával folytathatja a programot, és lekérheti a felülvizsgálati folyamat eredményét.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

Ha ebben a forgatókönyvben visszahívási végpontot használt, akkor a következő formátumú eseményt kell kapnia:

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

Futtassa az alkalmazást az alkalmazás könyvtárából az `dotnet run` paranccsal.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések


Ebből a rövid útmutatóból megtudhatta, hogyan használhatja a Content Moderator .NET-függvénytárat moderálási feladatok elvégzésére. Következő lépésként tekintse meg a képek vagy más média moderálását egy elméleti útmutató elolvasásával.

> [!div class="nextstepaction"]
> [Képek moderálási fogalmai](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Mi az Azure Content Moderator?](./overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs)található.
