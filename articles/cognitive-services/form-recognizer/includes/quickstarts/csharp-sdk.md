---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtára a .NET-hez'
description: Ebben a rövid útmutatóban megismerheti a .NET-hez készült űrlap-felismerő ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 480c1e991225f707b6497849b6e8d8b5c4124f21
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505401"
---
[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services).
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adatkészletének](https://go.microsoft.com/fwlink/?linkid=2090451) **vonattal** mappában található fájlokat.
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-form-recognizer-azure-resource"></a>Űrlap-felismerő Azure-erőforrás létrehozása

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Környezeti változók létrehozása

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `formrecognizer-quickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: _program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Ezután hozza létre az alkalmazást az alábbiakkal:

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

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Ezután adja hozzá a következő kódot az alkalmazás **fő** metódusához. Ezt az aszinkron feladatot később is megadhatja. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült űrlap-felismerő ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült űrlap-felismerő ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Űrlap tartalmának felismerése](#recognize-form-content)
* [Visszaigazolások felismerése](#recognize-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A `Main` metódus alatt adja meg a hivatkozott feladatot `Main` . Itt két ügyfél-objektumot kell hitelesítenie a fent megadott előfizetési változók használatával. **AzureKeyCredential** objektumot fog használni, így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]


### <a name="call-client-specific-methods"></a>Ügyfél-specifikus metódusok hívása

A kód következő blokkja az ügyfélalkalmazások használatával hívja meg az egyes főbb feladatok metódusait az űrlap-felismerő SDK-ban. Ezeket a metódusokat később is megadhatja.

Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat. 
* Az egyéni modell betanítási adataihoz tartozó SAS URL-cím lekéréséhez nyissa meg a Microsoft Azure Storage Explorer, kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférésű aláírás beolvasása**elemet. Győződjön meg arról, hogy az **olvasási** és a **listázási** engedély be van jelölve, majd kattintson a **Létrehozás**gombra. Ezután másolja az értéket az **URL** szakaszban. A formátumnak a következőket kell tartalmaznia: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Egy olyan űrlap URL-címének lekéréséhez, amely a teszteléshez használható, a fenti lépésekkel lekérheti egy egyedi dokumentum SAS URL-címét a blob Storage-ban. Vagy vegyen fel egy máshol található dokumentum URL-címét.
* A fenti módszer használatával beolvashatja a beérkezési képek URL-címét is, vagy használhatja a mintaként megadott képet.

> [!NOTE]
> Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_calls)]

## <a name="recognize-form-content"></a>Űrlap tartalmának felismerése

Az űrlap-felismerő használatával felismerheti a dokumentumokban szereplő táblákat, vonalakat és szavakat, anélkül, hogy egy modellt kellene betanítania.

Egy adott URI-fájl tartalmának felismeréséhez használja a **StartRecognizeContentFromUri** metódust.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]


A visszaadott érték egy **FormPage** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. Az alábbi kód megismétli ezeket az objektumokat, és kinyomtatja a kinyert kulcs/érték párokat és a táblák adatait.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]


## <a name="recognize-receipts"></a>Visszaigazolások felismerése

Ez a szakasz bemutatja, hogyan ismerheti fel és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával.

A visszaigazolások URI-ból való felismeréséhez használja a **StartRecognizeReceiptsFromUri** metódust. A visszaadott érték egy **RecognizedReceipt** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. A következő kód egy nyugtát dolgoz fel a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]


A kód következő blokkja megismétli a nyugtán észlelt egyes elemeket, és kiírja az adataikat a-konzolra.
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_item_print)]

Végül a kód utolsó blokkja kinyomtatja a nyugtán lévő teljes értéket.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_total_print)]

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték felismeréséhez a betanítási dokumentumok manuális címkézése nélkül.

A következő metódus egy modellt hoz létre egy adott dokumentumon, és kiírja a modell állapotát a konzolra. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

A visszaadott **CustomFormModel** objektum a modell által felismerhető űrlap-típusokkal és az egyes űrlapokból kinyerhető mezőkkel kapcsolatos információkat tartalmaz. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Végezetül ez a metódus a modell egyedi AZONOSÍTÓját adja vissza.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. Ha címkéket szeretne betanítani, a blob Storage-tárolóban a betanítási dokumentumokkal együtt speciális feliratú információs fájlokat (* \<filename\>.pdf.labels.js*) kell használnia. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Miután megadta őket, meghívhatja a **StartTrainingAsync** metódust a *uselabels* paraméterrel, amely a következőre van beállítva: `true` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

A visszaadott **CustomFormModel** jelzi a modell által kinyerhető mezőket, valamint az egyes mezők becsült pontosságát. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A **StartRecognizeCustomFormsFromUri** metódust fogja használni. A visszaadott érték egy **RecognizedForm** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

A következő kód kinyomtatja az elemzési eredményeket a-konzolra. Kinyomtatja az egyes felismert mezőket és a hozzá tartozó értékeket, valamint a megbízhatósági pontszámot.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. A következő kód egyetlen metódusban végzi el az összes modell-felügyeleti feladatot, példaként. Először másolja az alábbi metódus-aláírást:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>A FormRecognizer-erőforrás fiókban található modellek számának megkeresése

A következő kódrészlet ellenőrzi, hogy az űrlap-felismerő fiókban hány modell lett mentve, és összehasonlítja azt a fiókra vonatkozó korláttal.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrás-fiókban jelenleg tárolt modellek listázása

A következő kódrészlet felsorolja a fiókban lévő aktuális modelleket, és kiírja az adataikat a-konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell beszerzése a modell AZONOSÍTÓjának használatával

A következő kód blokkolja az új modellt (akárcsak a [betanítási modell](#train-a-model-without-labels) szakaszban), majd egy második hivatkozást is beolvas az azonosítójának használatával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a .NET SDK-val használja a Cognitive Services űrlap-felismerő ügyféloldali függvénytárát, a szolgáltatás által visszaadott hibák a-t fogják eredményezni `RequestFailedException` . A rendszer ugyanazt a HTTP-állapotkódot fogja tartalmazni, amelyet egy REST API kérelem visszaadott volna.

Ha például egy érvénytelen URI azonosítóval ellátott nyugtát küld, a rendszer `400` hibaüzenetet küld, amely a "hibás kérést" jelzi.

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Megfigyelheti, hogy a művelethez hasonló további információk (például az ügyfél-kérelmek azonosítója) naplózva vannak.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban az űrlap-felismerő .NET ügyféloldali kódtárat használta a modellek betanításához és az űrlapok különböző módokon történő elemzéséhez. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* A jelen útmutatóban szereplő mintakód (és egyebek) megtalálható a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
