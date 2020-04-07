---
title: Modellek
description: A modell leírása az Azure távoli renderelésében
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 770c88ddfb44004b76633cbeb726d28e1626a72c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681726"
---
# <a name="models"></a>Modellek

Az Azure távoli renderelésének *modellje* [entitásokból](entities.md) és [összetevőkből](components.md)álló teljes objektumábrázolásra utal. A modellek a fő módja annak, hogy egyéni adatokat kapjon a távoli renderelési szolgáltatásba.

## <a name="model-structure"></a>Modellstruktúra

A modell root csomópontja ként pontosan egy [entitást](entities.md) rendelkezik. Az alábbiakban lehet, hogy egy tetszőleges hierarchia gyermek entitások. Modell betöltésekor a rendszer erre a gyökérentitásra mutató hivatkozást ad vissza.

Minden entitáshoz [lehetnek összetevők](components.md) csatolva. A leggyakoribb esetben az entitások *MeshComponents elemekkel*rendelkeznek, amelyek [hálóerőforrásokra](meshes.md)hivatkoznak.

## <a name="creating-models"></a>Modellek létrehozása

A futásidejű modellek létrehozása a bemeneti modellek fájlformátumokból, például FBX-ből és GLTF-ből [történő konvertálásával](../how-tos/conversion/model-conversion.md) érhető el. Az átalakítási folyamat kibontja az összes erőforrást, például a textúrákat, az anyagokat és a közvetlen körülményeket, és azokat optimalizált futásidejű formátumokká alakítja. Kivonja továbbá a szerkezeti információkat, és átalakítja azokat az ARR entitás/komponens grafikonszerkezeté.

> [!IMPORTANT]
>
> [A modellkonvertálás](../how-tos/conversion/model-conversion.md) az egyetlen módja [a szemek létrehozásának.](meshes.md) Bár a hálók megoszthatók az entitások között futásidőben, nincs más módja annak, hogy egy hálót a futásidejű, más, mint a modell betöltése.

## <a name="loading-models"></a>Betöltési modellek

A modell konvertálása után az Azure blob storage-ból a futásidejű.

Két különböző betöltési függvény van, amelyek eltérnek attól, ahogyan az eszközt a blob storage-ban kezelik:

* A modell a SAS URI-val címezhető. A megfelelő `LoadModelFromSASAsync` terhelési `LoadModelFromSASParams`funkció a paraméterrel van eladva. Használja ezt a változatot a [beépített modellek](../samples/sample-model.md)betöltésekor is.
* A modell közvetlenül a blob storage-paramétereivel kezelhető, ha a [blobstorage a fiókhoz van csatolva.](../how-tos/create-an-account.md#link-storage-accounts) A vonatkozó terhelési `LoadModelAsync` funkció `LoadModelParams`ebben az esetben a paraméterrel van eladva.

A következő kódrészletek bemutatják, hogyan tölthető be a modellek bármelyik funkcióval. Ha egy modellt a SAS URI-val szeretne betölteni, használja az alábbihoz hasonló kódot:

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

Ha egy modellt közvetlenül a blobstorage-paraméterek használatával szeretne betölteni, használja a következő kódrészlethez hasonló kódot:

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

    // ... (identical to the SAS URI snippet above)
}
```

Ezt követően áthaladhat az entitáshierarchián, és módosíthatja az entitásokat és az összetevőket. Ugyanaza modell többszöri betöltése több példányt hoz létre, mindegyiknek saját példánya van az entitás/összetevő szerkezetéről. Mivel a közvetlen csak objektumok, anyagok és textúrák [megosztott erőforrások,](../concepts/lifetime.md)adataik azonban nem töltődnek be újra. Ezért egy modell többszöri példányosítása viszonylag kevés memóriaterhelést okoz.

> [!CAUTION]
> Az ARR összes *Async* függvénye aszinkron műveleti objektumokat ad vissza. A művelet befejezéséig meg kell tárolnia az objektumokra mutató hivatkozást. Ellenkező esetben a C# szemétgyűjtő törölheti a műveletet korán, és soha nem fejeződhet be. A *várt* használata feletti mintakódban garantálja, hogy a "loadOp" helyi változó hivatkozást tartalmaz a modell betöltésének befejezéséig. Ha azonban a *Befejezett* eseményt használja, akkor az aszinkron műveletet egy tagváltozóban kell tárolnia.

## <a name="next-steps"></a>További lépések

* [Entitások](entities.md)
* [Rácsvonalak](meshes.md)
