---
title: Modellek
description: Leírja, hogy mi a modell az Azure Remote rendering szolgáltatásban
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 7832f999de2f6f16cfe816c061925e371f90662e
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758690"
---
# <a name="models"></a>Modellek

Az Azure Remote rendering *modellje* egy teljes objektum-ábrázolásra utal, amely [entitásokból](entities.md) és [összetevőkből](components.md)áll. A modellek a legfontosabb módszer a távoli renderelési szolgáltatásba való egyéni adatgyűjtésre.

## <a name="model-structure"></a>Modellstruktúra

A modell pontosan egy [entitást](entities.md) tartalmaz, mint a legfelső szintű csomópont. Alább látható, hogy az alárendelt entitások tetszőleges hierarchiája lehet. Modell betöltésekor a rendszer erre a gyökérszintű entitásra mutató hivatkozást ad vissza.

Előfordulhat, hogy minden entitáshoz vannak csatolva [összetevők](components.md) . A leggyakoribb esetben az entitások rendelkeznek *MeshComponents*, amelyek a [háló erőforrásokra](meshes.md)hivatkoznak.

## <a name="creating-models"></a>Modellek létrehozása

A futtatókörnyezet modelljeinek létrehozása a bemeneti modellek fájlformátumokból (például FBX és GLTF) való [átalakításával](../how-tos/conversion/model-conversion.md) érhető el. Az átalakítási folyamat kibontja az összes erőforrást, például a textúrákat, az anyagokat és a hálókat, majd konvertálja azokat optimalizált futtatókörnyezeti formátumokra. Emellett a strukturális adatokat is kinyeri és átalakítja az ARR entitás/összetevő gráf-struktúrájába.

> [!IMPORTANT]
>
> A [modell konvertálása](../how-tos/conversion/model-conversion.md) az egyetlen módszer a [Rácsvonalak](meshes.md)létrehozására. Bár a rácsvonalak megoszthatók az entitások között futásidőben, nincs más mód arra, hogy a modell betöltésén kívül más módon is beolvasson egy rácsvonalat a futtatókörnyezetbe.

## <a name="loading-models"></a>Modellek betöltése

A modell konvertálása után az Azure Blob Storage-ból tölthető be a futtatókörnyezetbe.

Két különböző betöltési függvény van, amelyek eltérnek az eszköz blob Storage-ban való címzésének módjától:

* A modellt a SAS URI-ja tudja kezelni. A megfelelő betöltési függvény `LoadModelFromSASAsync` paraméterrel van ellátva `LoadModelFromSASParams` . A [beépített modellek](../samples/sample-model.md)betöltésekor ezt a változatot is használhatja.
* A modellt közvetlenül a blob Storage-paraméterekkel lehet megoldani, abban az esetben, ha a [blob Storage a fiókhoz van társítva](../how-tos/create-an-account.md#link-storage-accounts). Ebben az esetben a megfelelő betöltési függvény a `LoadModelAsync` paraméter `LoadModelParams` .

A következő kódrészletek bemutatják, hogyan tölthetők be modellek bármelyik függvénnyel. Egy modell SAS URI-val való betöltéséhez használja az alábbihoz hasonló kódot:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Ha közvetlenül a blob Storage-paraméterek használatával szeretne betölteni egy modellt, az alábbi kódrészlethez hasonló kódot használjon:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

Ezután áthaladhat az entitás-hierarchián, és módosíthatja az entitásokat és összetevőket. Ha ugyanaz a modell többször is be van töltve, több példányt hoz létre, amelyek mindegyike az entitás/összetevő struktúrájának saját példányával rendelkezik. Mivel a rácsvonalak, az anyagok és a textúrák [közös erőforrások](../concepts/lifetime.md), az adataik azonban nem lesznek újra betöltve. Ezért a modell többszöri létrehozása nem éri el a viszonylag kevés memória terhelését.

> [!CAUTION]
> Az *ARR összes aszinkron függvénye aszinkron műveleti* objektumokat ad vissza. A művelet befejezését követően az objektumokra mutató hivatkozást kell tárolnia. Ellenkező esetben előfordulhat, hogy a C# Garbage Collector már korán törli a műveletet, és soha nem tud befejezni. A *várakozási* feltétele alatt a mintakód garantálja, hogy a (z) "loadOp" helyi változó hivatkozást tartalmaz, amíg a modell betöltése be nem fejeződik. Ha azonban a *befejezett* eseményt szeretné használni, az aszinkron műveletet egy tag változóban kell tárolnia.

## <a name="next-steps"></a>További lépések

* [Entitások](entities.md)
* [Hálók](meshes.md)
