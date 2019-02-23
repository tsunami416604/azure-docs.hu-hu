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
ms.openlocfilehash: 433386ac873b5064b3f71650480b0807e96ddbbd
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669518"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Az arcfelismerés adatmigrálás Face másik előfizetésbe való

Ez az útmutató bemutatja, hogyan face adatáthelyezést (például egy mentett **is lehet PersonGroup** téglalapot) egy másik Face API-előfizetésbe, a pillanatkép funkció használatát. Ez lehetővé teszi, hogy ne kelljen ismételten hozhat létre és betanítunk egy **is lehet PersonGroup** vagy **FaceList** áthelyezése vagy a tevékenységek kibontása. Például előfordulhat, hogy létrehozott egy **is lehet PersonGroup** használatával egy ingyenes próba-előfizetését, és most szeretné áttelepíteni a díjköteles előfizetésre, vagy szükség lehet arcfelismerési adatokat szinkronizálhat a nagyvállalati művelet régióban.

Ez ugyanolyan stratégiájának is vonatkozik **LargePersonGroup** és **LargeFaceList** objektumokat. Ha nem ismeri a jelen útmutatóban fogalmait, tekintse meg a definíciójukat a a [szószedet](../Glossary.md). Ez az útmutató a Face API .NET ügyféloldali kódtárat használja C#.

## <a name="prerequisites"></a>Előfeltételek

- Két Face API előfizetési kulcsok (egyet a meglévő adatokkal, és át). Kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.
- A Face API előfizetési azonosító karakterláncát a célként megadott előfizetés (megtalálható a **áttekintése** panel az Azure Portalon). 
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.


## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

Ez az útmutató egy egyszerű Konzolalkalmazás használatával hajtsa végre a face adatok migrálása. Teljes körű, tekintse meg a [Face API-pillanatkép minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) a Githubon.

1. A Visual Studióban hozzon létre egy új **Console app (.NET Framework)** projektre, és adja neki **FaceApiSnapshotSample**. 
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki **NuGet-csomagok kezelése**. Kattintson a **Tallózás** lapot, és válasszon **előzetes verzió**; majd keresse meg és telepítse a következő csomagot:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>Face ügyfelek létrehozása

Az a **fő** metódus az *Program.cs*, hozzon létre két **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** példányok a forrás- és előfizetés esetében. Ebben a példában használjuk forrásaként a kelet-ázsiai régióban Face előfizetés és az USA nyugati RÉGIÓJA előfizetés célként. Bemutatjuk, hogy hogyan telepítheti át adatait egy Azure-régióból a másikba. Ha az előfizetések különböző régiókban, szüksége lesz módosítani a `Endpoint` karakterláncokat.

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

Adja meg az előfizetési kulcs-értékeket és a végponti URL-címek, a forrás- és előfizetések kell.


## <a name="prepare-a-persongroup-for-migration"></a>Egy is lehet PersonGroup előkészítése az áttelepítésre

Szüksége lesz, a **is lehet PersonGroup** a célelőfizetésbe áttelepíteni a forrás-előfizetés. Használja a **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** metódussal lehet bekérni listáját a **is lehet PersonGroup** objektumok; kérje le a **[ PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)** tulajdonság. Ez a folyamat fog kinézni, attól függően, hogy milyen **is lehet PersonGroup** objektumok rendelkezik. A a jelen útmutató, a forrás **is lehet PersonGroup** tárol Azonosítót `personGroupId`.

> [!NOTE]
> A [mintakód](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) hoz létre, és a egy új betanítja **is lehet PersonGroup** áttelepíteni, de a legtöbb esetben már rendelkezik egy **is lehet PersonGroup** használatára.

## <a name="take-snapshot-of-persongroup"></a>Az is lehet PersonGroup pillanatkép készítése

Pillanatkép egy ideiglenes távtároló bizonyos Face adattípus. A vágólap másolhat adatokat egy előfizetésből egy másikba egyfajta működik. Először a felhasználó "" egy pillanatképet készít az adatokat a forrás-előfizetés, és ezek "alkalmazza" azt egy új objektum a cél előfizetésben.

A forrás-előfizetés használata **FaceClient** -példány, készítsen pillanatképet a **is lehet PersonGroup**révén **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** a az **Is lehet PersonGroup** Azonosítóját és a célként megadott előfizetés-azonosítójára. Ha a cél több előfizetéssel rendelkezik, a tömb bejegyzésként, harmadik paraméterként az felveheti őket.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Véve, és a pillanatképek alkalmazására a folyamat nem akadályozza meg a forrás- vagy normál visszavonásokat **is lehet PersonGroup**s (vagy **FaceList**s). Azonban nem ajánlott egyidejű hívás, amely módosíthatja az adatforrás-objektum ([Face lista felügyeleti hívások](https://docs.microsoft.com/rest/api/cognitiveservices/face/facelist) vagy a [személy csoport – Train](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroup/train) hívja, például), mert a pillanatkép-készítési művelet lehet végrehajtása előtt vagy után ezeket a műveleteket vagy hibákat tapasztalhat. 

## <a name="retrieve-the-snapshot-id"></a>A pillanatkép-azonosító lekéréséhez

A pillanatkép véve metódus az aszinkron, így kell megvárni a (pillanatkép-művelet nem szakítható meg). Ebben a kódban a `WaitForOperation` metódus figyeli az aszinkron hívás, az állapot ellenőrzése minden 100 MS. A művelet befejeződésekor meg fogja tudni lekérni egy műveletet. Úgy szerezhet, elemzés a `OperationLocation` mező. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Egy tipikus `OperationLocation` értéket fog kinézni:

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

Ha van megjelölve a művelet állapotának `Succeeded`, majd megkaphassa a pillanatkép-azonosító elemzésekor a `ResourceLocation` mezőt, a visszaadott **OperationStatus** példány.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Egy tipikus `resourceLocation` értéket fog kinézni:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>A célként megadott előfizetés pillanatkép alkalmazása

Következő lépésként hozzon létre az új **is lehet PersonGroup** a célul szolgáló előfizetésben használatával egy véletlenszerűen generált. Ezután használja a célként megadott előfizetés **FaceClient** alkalmazandó a pillanatkép a is lehet PersonGroup adja a pillanatkép-azonosító és az új példány **is lehet PersonGroup** azonosítóját. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> A pillanatkép-objektum csak a érvényes 48 óra. Csak kell tennie egy pillanatképet, ha szeretne használni, az adatok migrálása után minél hamarabb.

A pillanatkép alkalmazása kérelem adja vissza egy másik művelet azonosítója. Ez az azonosító megkaphassa elemzés a `OperationLocation` mezőt, a visszaadott **applySnapshotResult** példány. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

A pillanatkép folyamatának is aszinkron, ezért újra használja `WaitForOperation` , várjon, amíg annak végrehajtása befejeződik.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Az adatáttelepítés tesztelése

A pillanatkép alkalmazása után az új **is lehet PersonGroup** a cél előfizetés töltse fel az eredeti face adatokkal. Alapértelmezés szerint képzési eredményeket is másolja, ezért az új **is lehet PersonGroup** átképezési nélkül kész arcok azonosítása hívásokhoz lesz.

Az adatáttelepítés teszteléséhez futtassa a következő műveleteket, és összehasonlíthatja az eredményeket a konzolon nyomtatási.

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

Most elkezdheti használni az új **is lehet PersonGroup** a cél előfizetésben. 

Ha frissíti a cél **is lehet PersonGroup** újra a jövőben kell hozzon létre egy új **is lehet PersonGroup** (Ez az Útmutató lépéseit követve a következőket), az kapja meg. Egyetlen **is lehet PersonGroup** objektum legfeljebb egyszer alkalmazott pillanatképet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a face adatok áttelepítését végzi, javasoljuk, hogy manuálisan törölje a pillanatkép-objektumot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Pillanatkép dokumentációja (.NET SDK-val)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Face API pillanatkép minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Arcok hozzáadása](how-to-add-faces.md)
- [Hogyan lehet arcokat megtalálni a képen](HowtoDetectFacesinImage.md)
- [Hogyan kell azonosítani arcokat kép](HowtoIdentifyFacesinImage.md)
