---
title: Renderelés hibakeresése
description: A kiszolgálóoldali hibakeresési renderelési hatások áttekintése
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868161"
---
# <a name="debug-rendering"></a>Renderelés hibakeresése

A hibakeresési renderelési API számos globális lehetőséget kínál a kiszolgálóoldali renderelés különböző hibakeresési hatásokkal történő módosításához.

## <a name="available-debug-rendering-effects"></a>Elérhető hibakeresési renderelési hatások

|Beállítás                          | Hatás                               |
|---------------------------------|:-------------------------------------|
|Keretszámláló                    | Szövegátfedés megjelenítése a keret bal felső sarkába. A szöveg az aktuális kiszolgálóoldali keretazonosítót mutatja, amely a renderelés előrehaladtával folyamatosan növekszik. |
|Sokszögek száma                    | Szövegátfedés megjelenítése a keret bal felső sarkába. A szöveg a jelenleg megjelenített sokszögek mennyiségét mutatja, amely megegyezik a [kiszolgálóoldali teljesítménylekérdezések](performance-queries.md) által lekérdezett értékkel| 
|Drótváz                        | Ha engedélyezve van, a kiszolgálóra betöltött összes objektumgeometria vezetékes módban jelenik meg. Ebben a módban csak a sokszögek szélei raszterizálódnak. |

A következő kód lehetővé teszi a következő hibakeresési hatásokat:

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![Hibakeresési renderelés](./media/debug-rendering.png)

> [!NOTE]
> A hibakeresési renderelési effektusok globális beállítások, amelyek a teljes keretet érintik.

## <a name="use-cases"></a>Használati esetek

A hibakeresési renderelési API egyszerű hibakeresési feladatokra szolgál, például a szolgáltatáskapcsolat megfelelő működésének ellenőrzésére. A szövegrenderelési beállítások közvetlenül befolyásolják a lefelé streamelt videoképkockákat. Az engedélyezésük ellenőrzi, hogy az új képkockák megfelelően érkeznek-e, és megfelelően vannak-e dekódolva.

A megadott hatások azonban nem adnak részletes önvizsgálatot a szolgáltatás állapotába. A [kiszolgálóoldali teljesítménylekérdezések](performance-queries.md) ebben a használati esetben ajánlottak.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

* A szövegátfedések engedélyezése kevés vagy semmilyen teljesítményterhelést jelent.
* A drótváz üzemmód engedélyezése nem triviális teljesítményterheléssel jár, bár a jelenettől függően változhat. Összetett jelenetek esetén ez a mód azt eredményezheti, hogy a képkockasebesség a 60 Hz-es cél alá csökken.

## <a name="next-steps"></a>További lépések

* [Renderelési módok](../../concepts/rendering-modes.md)
* [Kiszolgálóoldali teljesítménylekérdezések](performance-queries.md)
