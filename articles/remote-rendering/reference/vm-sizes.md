---
title: Kiszolgálóméretek
description: A kiosztható különböző kiszolgálói méretek leírása
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 8843f24f27f8973ad99989f743d1b3fae568679e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997149"
---
# <a name="server-sizes"></a>Kiszolgálóméretek

Az Azure távoli renderelés két kiszolgálói konfigurációban érhető el: `Standard` és `Premium` .

## <a name="polygon-limits"></a>Sokszög korlátai

A méret- `Standard` kiszolgálóval rendelkező távoli renderelés maximális mérete 20 000 000 sokszög. A mérettel rendelkező távoli renderelés `Premium` nem kényszeríti a maximális értéket, a teljesítmény azonban csökkenhet, ha a tartalom túllépi a szolgáltatás megjelenítési képességeit.

Ha a rendering on on a "standard" kiszolgáló mérete eléri ezt a korlátozást, a renderelés egy Pepita-háttérre vált:

![Pepita](media/checkerboard.png)

## <a name="specify-the-server-size"></a>A kiszolgáló méretének megadása

A kívánt kiszolgáló-konfigurációt meg kell adni a renderelési munkamenet inicializálási idején. Futó munkameneten belül nem módosítható. A következő kódrészletek azt mutatják be, hogy hol kell megadni a kiszolgáló méretét:

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

A [PowerShell-parancsfájlok például](../samples/powershell-example-scripts.md)a kívánt kiszolgáló méretét kell megadni a `arrconfig.json` fájlban:

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

Két módon határozható meg a modell vagy jelenet sokszögének száma, amelyek hozzájárulnak a konfigurációs méret költségvetési korlátához `standard` :
* A modell átalakítása oldalon olvassa be a [konverziós kimenet JSON-fájlját](../how-tos/conversion/get-information.md), és a `numFaces` [ *inputStatistics* szakaszban](../how-tos/conversion/get-information.md#the-inputstatistics-section) található bejegyzést.
* Ha az alkalmazás dinamikus tartalommal foglalkozik, a megjelenített sokszögek száma a Futtatás közben dinamikusan lekérdezhető. Használjon [teljesítményteszt-lekérdezést](../overview/features/performance-queries.md#performance-assessment-queries) , és keresse meg a `polygonsRendered` tagot a struct-ban `FrameStatistics` . A `polygonsRendered` mező akkor lesz beállítva, `bad` Ha a megjelenítő eléri a sokszög korlátozását. A Pepita háttér mindig elhalványul, és némi késéssel biztosítható, hogy a felhasználói beavatkozás az aszinkron lekérdezés után is elvégezhető legyen. A felhasználói művelet lehet például a modell példányainak elrejtése vagy törlése.

## <a name="pricing"></a>Díjszabás

Az egyes konfigurációk díjszabásának részletes részletezését a [távoli renderelés díjszabását](https://azure.microsoft.com/pricing/details/remote-rendering) ismertető oldalon találja.

## <a name="next-steps"></a>Következő lépések
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
* [Modell átalakítása](../how-tos/conversion/model-conversion.md)

