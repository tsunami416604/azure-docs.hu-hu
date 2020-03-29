---
title: Az arcadatok áttelepítése előfizetések között - Face
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan telepítheti a tárolt arcadatokat az egyik Face-előfizetésből a másikba.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: e5ca51da7322e4eab4ea364ec5da086a1068fa9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169812"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Arcadatok áttelepítése másik Face-előfizetésbe

Ez az útmutató bemutatja, hogyan helyezheti át a face data-t, például egy arcokat megjelenítő mentett PersonGroup-objektumot egy másik Azure Cognitive Services Face-előfizetésbe. Az adatok áthelyezéséhez használja a Pillanatkép funkciót. Így elkerülheti, hogy a műveletek áthelyezésekor vagy kibontásakor ismételten létre kelljen adnia és be kell képeznie egy PersonGroup vagy FaceList objektumot. Például előfordulhat, hogy egy PersonGroup-objektumot hozott létre egy ingyenes próba-előfizetés használatával, és most át szeretné telepíteni a fizetős előfizetésbe. Vagy előfordulhat, hogy szinkronizálnia kell az arcadatokat a különböző régiókban lévő előfizetések között egy nagyvállalati művelethez.

Ugyanez az áttelepítési stratégia a LargePersonGroup és a LargeFaceList objektumokra is vonatkozik. Ha nem ismeri az útmutatóban szereplő fogalmakat, tekintse meg azok definícióit az [Arcfelismerési fogalmak](../concepts/face-recognition.md) útmutatójában. Ez az útmutató a Face .NET ügyféltárat használja C#-val.

## <a name="prerequisites"></a>Előfeltételek

A következő elemekre van szüksége:

- Két Face-előfizetési kulcs, az egyik a meglévő adatokkal, a másikaz áttelepítés. Ha elő szeretne fizetni a Face szolgáltatásra, és le szeretné szerezni a kulcsot, kövesse a [Cognitive Services-fiók létrehozása című](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)részben található utasításokat.
- A Face-előfizetés azonosító karakterlánca, amely megfelel a cél-előfizetés. A kereséséhez válassza **áttekintés az** Azure Portalon. 
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/)bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

Ez az útmutató egy egyszerű konzolalkalmazást használ az arcadat-áttelepítés futtatásához. A teljes megvalósításért tekintse meg a [Face pillanatkép-mintát](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) a GitHubon.

1. A Visual Studio-ban hozzon létre egy új konzolalkalmazás .NET Framework projektet. Nevezd **el FaceApiSnapshotSample néven.**
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza **a NuGet-csomagok kezelése parancsot.** Válassza a **Tallózás** lapot, majd az **Előzetes kiadást is beleértve**lehetőséget. Keresse meg és telepítse a következő csomagot:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Arcügyfelek létrehozása

A **fő** módszer *Program.cs*hozzon létre két [FaceClient-példányt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) a forrás- és a cél-előfizetésekhez. Ez a példa egy Face-előfizetést használ a kelet-ázsiai régióban forrásként és egy USA-beli nyugat-amerikai előfizetést célként. Ez a példa bemutatja, hogyan lehet adatokat áttelepíteni az egyik Azure-régióból a másikba. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Töltse ki az előfizetési kulcs értékeit és végponti URL-címeket a forrás- és cél-előfizetésekhez.


## <a name="prepare-a-persongroup-for-migration"></a>Személycsoport előkészítése az áttelepítéshez

A forrás-előfizetésben a PersonGroup azonosítójára van szüksége a cél-előfizetésbe való áttelepítéshez. A [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) metódus segítségével olvassa be a PersonGroup-objektumok listáját. Ezután a [PersonGroup.PersonGroupId tulajdonságot.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Ez a folyamat a ttól függően, hogy milyen PersonGroup objektumokkal rendelkezik. Ebben az útmutatóban `personGroupId`a forrás PersonGroup-azonosító a alkalmazásban tárolódik.

> [!NOTE]
> A [mintakód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) létrehoz és beiktat egy új PersonGroup-ot az áttelepítéshez. A legtöbb esetben már rendelkeznie kell egy PersonGroup használatával.

## <a name="take-a-snapshot-of-a-persongroup"></a>Pillanatkép készítése a PersonGroup csoportról

A pillanatkép bizonyos Face-adattípusok ideiglenes távtárolója. Úgy működik, mint egyfajta vágólap, hogy másolja az adatokat az egyik előfizetés a másikra. Először pillanatképet készíthet a forrás-előfizetésben lévő adatokról. Ezután alkalmazza azt egy új adatobjektumra a cél-előfizetésben.

Használja a forrás-előfizetés FaceClient példányát a PersonGroup pillanatképkészítéséhez. Használja a [TakeAsync-et](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) a PersonGroup-azonosítóval és a cél-előfizetés azonosítójával. Ha több cél-előfizetéssel rendelkezik, adja hozzá őket tömbbejegyzésként a harmadik paraméterben.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> A pillanatképek készítésének és alkalmazásának folyamata nem zavarja a forrás- vagy célszemélycsoportok vagy facelisták rendszeres hívásait. Ne kezdeményezzen egyidejű hívásokat, amelyek megváltoztatják a forrásobjektumot, például [a FaceList-felügyeleti hívásokat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) vagy a [PersonGroup Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) hívást. Előfordulhat, hogy a pillanatkép-művelet a műveletek előtt vagy után fut, vagy hibákat észlelhet.

## <a name="retrieve-the-snapshot-id"></a>A pillanatkép-azonosító lekérése

A pillanatképek készítéséhez használt módszer aszinkron, ezért meg kell várnia annak befejezését. A pillanatkép-műveletek nem szakíthatók meg. Ebben a kódban a `WaitForOperation` metódus figyeli az aszinkron hívást. 100 ms-onként ellenőrzi az állapotot. A művelet befejezése után a mező elemzésével olvassa `OperationLocation` be a műveletazonosítót. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Egy `OperationLocation` tipikus érték így néz ki:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

A `WaitForOperation` segítő módszer itt van:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Miután a `Succeeded`művelet állapota azt mutatja, a `ResourceLocation` pillanatkép-azonosítót a visszaadott OperationStatus példány mezőjének elemzésével kapjuk meg.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Egy `resourceLocation` tipikus érték így néz ki:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Pillanatkép alkalmazása cél-előfizetésre

Ezután hozza létre az új PersonGroup a cél-előfizetés segítségével egy véletlenszerűen generált azonosítót. Ezután a cél-előfizetés FaceClient-példányát használja a pillanatkép alkalmazásához erre a PersonGroupra. Adja át a pillanatkép-azonosítót és az új PersonGroup-azonosítót.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> A Snapshot objektum csak 48 óráig érvényes. Csak akkor készíthet pillanatképet, ha nem sokkal később adatáttelepítésre kívánja használni.

A pillanatkép-alkalmazási kérelem egy másik műveletazonosítót ad vissza. Az azonosító lefelvételéhez elemezd `OperationLocation` a visszaadott applySnapshotResult példány mezőjét. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

A pillanatkép alkalmazás folyamata is aszinkron, `WaitForOperation` így ismét használja, hogy várjon, amíg befejeződik.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Az adatáttelepítés tesztelése

A pillanatkép alkalmazása után a cél-előfizetésben lévő új PersonGroup feltölti az eredeti arcadatokat. Alapértelmezés szerint a betanítási eredmények is másolásra kerülnek. Az új PersonGroup készen áll az arcazonosítási hívásokra átképzés nélkül.

Az adatáttelepítés teszteléséhez futtassa a következő műveleteket, és hasonlítsa össze a konzolra nyomtatott eredményeket:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Használja a következő segítő módszereket:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Most már használhatja az új PersonGroup a cél-előfizetés. 

Ha a jövőben újra szeretné frissíteni a célszemélycsoportot, hozzon létre egy új PersonGroup csoportot a pillanatkép fogadásához. Ehhez kövesse az útmutató lépéseit. Egyetlen PersonGroup-objektumra csak egyszer alkalmazhat pillanatképet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte a lapadatok áttelepítését, manuálisan törölje a pillanatképobjektumot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>További lépések

Ezután tekintse meg a megfelelő API-referenciadokumentációt, fedezze fel a Snapshot funkciót használó mintaalkalmazást, vagy kövesse az útmutató útmutatót az itt említett egyéb API-műveletek használatának megkezdéséhez:

- [Pillanatkép referenciadokumentációja (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Arcpillanatkép-minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Arcok hozzáadása](how-to-add-faces.md)
- [Arcok felismerése a képeken](HowtoDetectFacesinImage.md)
- [A képen lévő arcok azonosítása](HowtoIdentifyFacesinImage.md)
