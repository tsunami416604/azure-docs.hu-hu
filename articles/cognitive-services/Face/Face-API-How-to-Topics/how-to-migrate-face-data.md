---
title: Az arcfelismerés adatmigrálás előfizetésekben – Face API
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, a tárolt arcok adatok áttelepítése a Face API egy előfizetésből egy másikba.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65913793"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Az arcfelismerés adatmigrálás Face másik előfizetésbe való

Ez az útmutató bemutatja, hogyan arcfelismerési adatokat, például az arcok, egy mentett is lehet PersonGroup objektum áthelyezése egy másik Azure Cognitive Services Face API-előfizetésbe. Az adatok áthelyezéséhez használja a pillanatkép-szolgáltatását. Ezzel a módszerrel, ne kelljen ismételt létrehozása és betanítása egy is lehet PersonGroup vagy FaceList objektum áthelyezése, vagy bontsa ki az operations. Például talán, ingyenes próba-előfizetés használatával is lehet PersonGroup objektumot hozott létre, és most szeretné áttelepíteni a díjköteles előfizetésre. Vagy szinkronizálja az arcfelismerési adatokat nagyvállalati művelet régióban szüksége lehet.

Ez ugyanolyan stratégiájának LargePersonGroup és LargeFaceList objektumokat is vonatkozik. Ha nem ismeri a fogalmak, ebben az útmutatóban, tekintse meg a definíciójukat a a [felismerés fogalmak arc](../concepts/face-recognition.md) útmutató. Ez az útmutató a Face API .NET ügyféloldali kódtárat használja C#.

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szüksége:

- Két Face API előfizetési kulcsot, egyet a meglévő adatok és a egy történő áttelepítéséhez. A Face API-szolgáltatás, és a kulcs beszerzéséhez, kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- A Face API előfizetési azonosító karakterlánc, amely megfelel a célként megadott előfizetés. Megkereséséhez jelölje ki a **áttekintése** az Azure Portalon. 
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

Ez az útmutató egy egyszerű konzolalkalmazást használja a face adatáttelepítés futtatásához. Teljes körű, tekintse meg a [Face API pillanatkép minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) a Githubon.

1. A Visual Studióban hozzon létre egy új konzol alkalmazás .NET-keretrendszer-projektet. Nevezze el **FaceApiSnapshotSample**.
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki **NuGet-csomagok kezelése**. Válassza ki a **Tallózás** lapot, majd **előzetes verzió**. Keresse meg és telepítse a következő csomagot:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Face ügyfelek létrehozása

Az a **fő** metódus az *Program.cs*, hozzon létre két [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) példányok a forrás- és előfizetés esetében. Ebben a példában a kelet-ázsiai régióban Face előfizetés használja a forrás- és USA nyugati RÉGIÓJA és a cél előfizetést as. Ez a példa bemutatja, hogyan telepítheti át adatait egy Azure-régióból a másikba. Ha az előfizetések különböző régiókban, módosítsa a `Endpoint` karakterláncokat.

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

Adja meg az előfizetési kulcs-értékeket és a végponti URL-címek, a forrás- és előfizetés esetében.


## <a name="prepare-a-persongroup-for-migration"></a>Egy is lehet PersonGroup előkészítése az áttelepítésre

Az is lehet PersonGroup Azonosítóját kell áttelepíteni a célként megadott előfizetés, a forrás-előfizetés. Használja a [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) metódussal lehet bekérni az is lehet PersonGroup objektumok listája. Kérje le a [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) tulajdonság. Ez a folyamat megjelenése alapján milyen is lehet PersonGroup objektumok rendelkezik. Ebben az útmutatóban a forrás is lehet PersonGroup Azonosítóját tárolja `personGroupId`.

> [!NOTE]
> A [mintakód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) létrehozza és betanítja egy új is lehet PersonGroup áttelepíteni. A legtöbb esetben kell hoznia egy is lehet PersonGroup használatára.

## <a name="take-a-snapshot-of-a-persongroup"></a>Pillanatkép készítése a is lehet PersonGroup

Egy pillanatképet egy ideiglenes távoli tároló bizonyos Face adattípusokhoz. A vágólap másolhat adatokat egy előfizetésből egy másikba egyfajta működik. Először is, készítsen pillanatképet az adatokat a forrás-előfizetés. Akkor alkalmazza azt egy új objektum a cél előfizetésben.

A forrás-előfizetés FaceClient példány használatával az is lehet PersonGroup pillanatkép készítése. Használat [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) is lehet PersonGroup azonosítója és a célként megadott előfizetés-azonosítójára. Ha cél több előfizetéssel rendelkezik, vegye fel őket a harmadik paraméter tömb bejegyzéseket.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Véve, és a pillanatképek alkalmazására folyamata nem zavarja a forrás-rendszeres visszavonásokat vagy Persongroup vagy Facelist. Ne módosítsa az adatforrás-objektum, például egyidejű hívások [FaceList felügyeleti hívások](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) vagy a [is lehet PersonGroup Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) hívja, például. A pillanatkép-készítési művelet előtt vagy után ezek a műveletek futtatásával, vagy hibák jelentkezhetnek.

## <a name="retrieve-the-snapshot-id"></a>A pillanatkép-azonosító lekéréséhez

A pillanatképek használt módszer aszinkron, így meg kell várnia a befejezéséig. Pillanatkép-készítési műveletek nem szakítható meg. Ebben a kódban a `WaitForOperation` metódus az aszinkron hívás figyeli. Minden 100 ms állapotát ellenőrzi. A művelet befejezését követően egy Műveletazonosító lekéréséhez elemzés a `OperationLocation` mező. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Egy tipikus `OperationLocation` érték néz ki:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

A `WaitForOperation` segédmetódus érhető el:

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

Után a művelet állapotát jeleníti meg `Succeeded`, elemzés a pillanatkép-azonosító lekéréséhez a `ResourceLocation` mezőt, a visszaadott OperationStatus példány.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Egy tipikus `resourceLocation` érték néz ki:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Pillanatkép alkalmazni a célként megadott előfizetés

Ezután hozza létre az új is lehet PersonGroup a célul szolgáló előfizetésben használatával egy véletlenszerűen generált. A célként megadott előfizetés FaceClient példány használatával a pillanatképet az is lehet PersonGroup vonatkoznak. Adja át a pillanatkép azonosítója és az új is lehet PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> A pillanatkép-objektumot a érvényes csak 48 óra. Csak készítsen egy pillanatképet, ha szeretne használni, az adatok migrálása után minél hamarabb.

A pillanatkép alkalmazása kérelem adja vissza egy másik művelet azonosítója. Ez az azonosító lekéréséhez elemezni a `OperationLocation` mezőt, a visszaadott applySnapshotResult példány. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

A pillanatkép folyamatának is aszinkron, ezért újra használja `WaitForOperation` , várjon, amíg befejeződik.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Az adatáttelepítés tesztelése

Miután telepítette a pillanatképet, az új a célul szolgáló előfizetésben is lehet PersonGroup az eredeti face adatokkal tölti fel. Alapértelmezés szerint képzési eredményeket is másolja. Az új is lehet PersonGroup készen áll a face azonosító hívások átképezési nélkül.

Az adatáttelepítés ellenőrzéséhez futtassa a következő műveleteket, és összehasonlíthatja az eredményeket a konzolon nyomtatási:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Az alábbi segítő módszerekkel:

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

Most már használhatja az új is lehet PersonGroup a cél előfizetésben. 

A későbbiekben frissíteni is lehet PersonGroup cél, hozzon létre egy új is lehet PersonGroup, az kapja meg. Ehhez kövesse a jelen útmutató lépéseit. Egyetlen is lehet PersonGroup objektumra csak egyszer alkalmazott pillanatkép rendelkezhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Áttelepítés befejezése után adatok között, törölje kézzel a pillanatkép-objektumot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>További lépések

Ezután tekintse meg a megfelelő API referenciadokumentációt tartalmaz, ismerje meg egy mintaalkalmazást, amely a pillanatkép funkciót használja, vagy hajtsa végre a használatához itt említett egyéb API műveletek használati útmutatója:

- [Pillanatkép dokumentációja (.NET SDK-val)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [A Face API pillanatkép minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Arcok hozzáadása](how-to-add-faces.md)
- [A kép arcok észlelése](HowtoDetectFacesinImage.md)
- [Arcazonosítás képen egy](HowtoIdentifyFacesinImage.md)
