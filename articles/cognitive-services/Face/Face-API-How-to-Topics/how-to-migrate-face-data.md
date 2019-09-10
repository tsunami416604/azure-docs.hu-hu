---
title: Arc-adatáttelepítés az előfizetések között – Face API
titleSuffix: Azure Cognitive Services
description: Ebből az útmutatóból megtudhatja, hogyan telepíthet át tárolt arc-adatait egy Face API-előfizetésből egy másikba.
services: cognitive-services
author: lewlu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: lewlu
ms.openlocfilehash: 49b92037fed6436d28f777761b18cf5f66e03025
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859164"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Az Arcfelismerés átmigrálása egy másik Face-előfizetésbe

Ez az útmutató bemutatja, hogyan helyezheti át az Arcfelismerés (például egy mentett PersonGroup-objektum arcokkal) egy másik Azure Cognitive Services Face API-előfizetésbe. Az adatok áthelyezéséhez használja a pillanatkép funkciót. Így elkerülhető, hogy a műveletek áthelyezésekor vagy kibontásakor ne kelljen ismétlődően létrehozni és betanítani egy PersonGroup vagy FaceList objektumot. Tegyük fel például, hogy létrehozott egy PersonGroup objektumot egy ingyenes próbaverziós előfizetéssel, és most át szeretné telepíteni a fizetős előfizetésre. Vagy előfordulhat, hogy a nagyméretű vállalati műveletekhez különböző régiókban lévő előfizetések között kell szinkronizálnia a Face-adatait.

Ugyanez az áttelepítési stratégia a LargePersonGroup és a LargeFaceList objektumra is érvényes. Ha nem ismeri az útmutatóban szereplő fogalmakat, tekintse meg a definíciókat a [Face Recognition fogalmakat](../concepts/face-recognition.md) ismertető útmutatóban. Ez az útmutató a Face API .NET ügyféloldali kódtárat C#használja a következővel:.

## <a name="prerequisites"></a>Előfeltételek

A következő elemek szükségesek:

- Két Face API előfizetési kulcs, egy a meglévő adattal, egy pedig a következőre:. A Face API szolgáltatásra való előfizetéshez és a kulcs beszerzéséhez kövesse az [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)című témakör utasításait.
- A cél előfizetésnek megfelelő Face API előfizetés-azonosító karakterlánca. A kereséshez válassza az **Áttekintés** lehetőséget a Azure Portal. 
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

Ez az útmutató egy egyszerű konzolos alkalmazást használ a Face-adatáttelepítés futtatásához. A teljes körű megvalósításhoz tekintse meg a [Face API pillanatkép-mintát](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) a githubon.

1. A Visual Studióban hozzon létre egy új Console app .NET-keretrendszer-projektet. Nevezze el **FaceApiSnapshotSample**.
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelő, majd válassza a **NuGet-csomagok kezelése**lehetőséget. Válassza a **Tallózás** lapot, és válassza az **előzetes verzió belefoglalása**lehetőséget. Keresse meg és telepítse a következő csomagot:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Arc-ügyfelek létrehozása

A Program.cs **fő** metódusábanhozzon létre két [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) -példányt a forrás és a cél előfizetések számára. Ez a példa egy arc-előfizetést használ a Kelet-Ázsia régióban, mint a forrásként és az USA nyugati régiójának előfizetését célként. Ez a példa azt szemlélteti, hogyan lehet áttelepíteni egy Azure-régióból egy másikba az adatok áttelepítését. 

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

Adja meg a forrás és a cél előfizetések előfizetési kulcsának értékeit és a végpont URL-címeit.


## <a name="prepare-a-persongroup-for-migration"></a>PersonGroup előkészítése az áttelepítéshez

Szüksége lesz a forrás-előfizetéshez tartozó PersonGroup AZONOSÍTÓra, hogy áttelepítse a cél előfizetésre. A PersonGroup-objektumok listájának lekéréséhez használja a [PersonGroupOperationsExtensions. ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) metódust. Ezután töltse le a [PersonGroup. PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) tulajdonságot. Ez a folyamat a PersonGroup-objektumok alapján eltérő módon néz ki. Ebben az útmutatóban a forrás PersonGroup- `personGroupId`azonosítóját tárolja a rendszer.

> [!NOTE]
> A [mintakód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) létrehoz és betanít egy új PersonGroup az áttelepíteni. A legtöbb esetben már rendelkeznie kell egy PersonGroup.

## <a name="take-a-snapshot-of-a-persongroup"></a>Pillanatkép készítése egy PersonGroup

A pillanatképek ideiglenes távoli tárterületet biztosítanak bizonyos arc adattípusokhoz. Úgy működik, mint egy olyan vágólap, amely az adatok egyik előfizetésből egy másikba való másolását teszi ki. Először készítsen pillanatképet a forrás-előfizetésben található adatokról. Ezt követően alkalmazza azt egy új adatobjektumra a cél előfizetésben.

A forrás-előfizetés FaceClient példánya segítségével készítsen pillanatképet a PersonGroup. Használja a [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) -t a PERSONGROUP-azonosítóval és a célként megadott előfizetés azonosítójával. Ha több cél előfizetéssel rendelkezik, adja őket tömb bejegyzéseiként a harmadik paraméterben.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> A pillanatképek felvételének és alkalmazásának folyamata nem zavarja a forrás vagy a cél PersonGroups vagy FaceLists irányuló normál hívásokat. Ne hozzon egyidejű hívásokat, például a [FaceList-felügyeleti hívásokat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) vagy a PersonGroup- [vonat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) hívását. Előfordulhat, hogy a pillanatkép-művelet a műveletek előtt vagy után is fut, vagy hibákat észlel.

## <a name="retrieve-the-snapshot-id"></a>A Pillanatkép-azonosító lekérése

A pillanatképek készítéséhez használt metódus aszinkron módon történik, ezért meg kell várnia a befejezését. Nem lehet megszakítani a pillanatképek műveleteit. Ebben a kódban a `WaitForOperation` metódus figyeli az aszinkron hívást. 100 MS-ban ellenőrzi az állapotot. A művelet befejezése után a `OperationLocation` mező elemzésével kérje le a művelet azonosítóját. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Egy tipikus `OperationLocation` érték a következőre hasonlít:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

A `WaitForOperation` segítő módszer itt található:

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

A művelet állapotának megjelenítése `Succeeded`után szerezze be a pillanatkép-azonosítót a visszaadott OperationStatus-példány `ResourceLocation` mezőjének elemzésével.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Egy tipikus `resourceLocation` érték a következőre hasonlít:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Pillanatkép alkalmazása cél-előfizetésre

Ezután hozza létre az új PersonGroup a cél előfizetésben egy véletlenszerűen generált azonosító használatával. Ezután használja a cél előfizetés FaceClient példányát, hogy alkalmazza a pillanatképet erre a PersonGroup. Adja át a pillanatkép-azonosítót és az új PersonGroup-azonosítót.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> A pillanatkép-objektumok csak 48 óráig érvényesek. Csak akkor készítsen pillanatképet, ha később az adatok áttelepítésére kívánja használni.

Egy pillanatkép-alkalmazási kérelem egy másik műveleti azonosítót ad vissza. Az azonosító beszerzéséhez elemezze `OperationLocation` a visszaadott applySnapshotResult-példány mezőjét. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

A pillanatkép-alkalmazás folyamata aszinkron módon is elvégezhető `WaitForOperation` , ezért várjon, amíg befejeződik.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Az adatáttelepítés tesztelése

A pillanatkép alkalmazása után a cél előfizetésben szereplő új PersonGroup az eredeti adatokkal együtt töltődik fel. Alapértelmezés szerint a rendszer a betanítási eredményeket is másolja. Az új PersonGroup újraképzés nélkül készen áll a Face Identification hívásokra.

Az adatok áttelepítésének teszteléséhez futtassa a következő műveleteket, és hasonlítsa össze a konzolra nyomtatandó eredményeket:

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

Most már használhatja az új PersonGroup a cél előfizetésben. 

Ha a jövőben újra szeretné frissíteni a cél PersonGroup, hozzon létre egy új PersonGroup a pillanatkép fogadásához. Ehhez kövesse az útmutató lépéseit. Egyetlen PersonGroup objektumhoz csak egyszer lehet alkalmazni egy pillanatképet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte az adatok áttelepítését, manuálisan törölje a pillanatkép-objektumot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>További lépések

Ezután tekintse meg a megfelelő API-dokumentációt, Fedezze fel a pillanatkép szolgáltatást használó minta alkalmazást, vagy kövesse az útmutató lépéseit az itt említett egyéb API-műveletek használatának megkezdéséhez:

- [Pillanatkép-referenciák dokumentációja (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Face API pillanatkép-minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Arcok hozzáadása](how-to-add-faces.md)
- [Képeken lévő arcok észlelése](HowtoDetectFacesinImage.md)
- [Képeken lévő arcok azonosítása](HowtoIdentifyFacesinImage.md)
