---
title: A virtuális gépek mérete
description: A lefoglalható különböző virtuálisgép-méreteket ismerteti
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: aab914caa2647146639aa366f558c80bebcfde54
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84810207"
---
# <a name="vm-sizes"></a>A virtuális gépek mérete

A renderelési szolgáltatás két különböző típusú gépen működhet az Azure-ban `Standard` `Premium` .

## <a name="polygon-limits"></a>Sokszög korlátai

A virtuális gép méretének **20 000 000 sokszöge** rögzített `Standard` . A mérethez nincs ilyen korlátozás `Premium` .

Ha a szabványos virtuális gépen lévő megjelenítő eléri ezt a korlátozást, a renderelést egy Pepita-háttérre vált:

![Pepita](media/checkerboard.png)

## <a name="allocate-the-vm"></a>A virtuális gép lefoglalása

Meg kell adni a virtuális gép kívánt típusát a renderelési munkamenet inicializálási idején. Futó munkameneten belül nem módosítható. A következő kódrészletek azt mutatják be, hogy hol kell megadni a virtuális gép méretét:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

A [PowerShell-parancsfájlok például](../samples/powershell-example-scripts.md)a virtuális gép méretét kell megadni a `arrconfig.json` fájlban:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>A leképező a sokszögek számának kiértékelése

A korlátozási teszt szempontjából figyelembe veendő sokszögek száma a renderelő által ténylegesen átadott sokszögek száma. Ez a geometria általában az összes példányos modell összege, de kivételek is vannak. A következő geometriát **nem tartalmazza**:
* A nézet csonkakúpot kívül teljesen betöltött modell-példányok.
* A láthatatlanra váltott modellek vagy modellezési részek a [hierarchikus állapot felülbírálása összetevő](../overview/features/override-hierarchical-state.md)használatával.

Ennek megfelelően olyan alkalmazást is megírhat, amely a több modellt tartalmazó `standard` sokszögek számának megadását célozza meg minden egyes modellnél. Ha az alkalmazás csak egyetlen modellt jelenít meg egyszerre, a Pepita nem aktiválódik.

### <a name="how-to-determine-the-number-of-polygons"></a>A sokszögek számának meghatározása

Kétféle módon határozható meg egy modell vagy jelenet sokszögének száma, amely hozzájárul a `standard` virtuális gép méretének költségvetési korlátához:
* A modell átalakítása oldalon olvassa be a [konverziós kimenet JSON-fájlját](../how-tos/conversion/get-information.md), és a `numFaces` [ *inputStatistics* szakaszban](../how-tos/conversion/get-information.md#the-inputstatistics-section) található bejegyzést.
* Ha az alkalmazás dinamikus tartalommal foglalkozik, a megjelenített sokszögek száma a Futtatás közben dinamikusan lekérdezhető. Használjon [teljesítményteszt-lekérdezést](../overview/features/performance-queries.md#performance-assessment-queries) , és keresse meg a `polygonsRendered` tagot a struct-ban `FrameStatistics` . A Pepita háttér mindig elhalványul, és némi késéssel biztosítható, hogy a felhasználói beavatkozás az aszinkron lekérdezés után is elvégezhető legyen. A felhasználói művelet lehet például a modell példányainak elrejtése vagy törlése.

## <a name="pricing"></a>Díjszabás

A virtuális gépek díjszabásának részletes részletezését a [távoli renderelés díjszabását](https://azure.microsoft.com/pricing/details/remote-rendering) ismertető oldalon találja.

## <a name="next-steps"></a>További lépések
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
* [Modell átalakítása](../how-tos/conversion/model-conversion.md)

