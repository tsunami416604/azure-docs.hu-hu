---
title: Renderelés hibakeresése
description: A kiszolgálóoldali hibakeresési effektusok áttekintése
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ee6945cec4c2441334e665947568743aaf29226
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013588"
---
# <a name="debug-rendering"></a>Renderelés hibakeresése

A hibakeresést lehetővé tevő API számos globális lehetőséget biztosít a kiszolgálóoldali renderelés különböző hibakeresési effektusokkal történő megváltoztatásához.

## <a name="available-debug-rendering-effects"></a>Elérhető hibakeresési megjelenítési effektusok

|Beállítás                          | Hatás                               |
|---------------------------------|:-------------------------------------|
|Keret számlálója                    | Megjelenít egy szöveget a keret bal felső sarkába. A szöveg az aktuális kiszolgálóoldali keret AZONOSÍTÓját jeleníti meg, amely folyamatosan növekszik a renderelési folyamatban. |
|Sokszögek száma                    | Megjelenít egy szöveget a keret bal felső sarkába. A szöveg az aktuálisan megjelenített sokszögek mennyiségét jeleníti meg, ugyanazokat az értékeket, mint a [kiszolgálóoldali teljesítményű lekérdezések lekérdezése](performance-queries.md)| 
|Drótvázdiagram                        | Ha engedélyezve van, a kiszolgálón betöltött összes objektum-geometria drótváz módban lesz megjelenítve. Ebben a módban csak a sokszögek szélei lesznek raszterek. |

A következő kód engedélyezi ezeket a hibakeresési hatásokat:

```cs
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

```cpp
void EnableDebugRenderingEffects(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Actions()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![Hibakeresés megjelenítése](./media/debug-rendering.png)

> [!NOTE]
> Az összes hibakeresési renderelési effektus a teljes keretet érintő globális beállítások.

## <a name="use-cases"></a>Használati esetek

A hibakeresési renderelési API egyszerű hibakeresési feladatok céljára szolgál, például a szolgáltatási kapcsolatok ellenőrzésének megfelelő működéséhez. A szöveg megjelenítésének beállításai közvetlenül érintik a lefelé továbbított képkockákat. Annak engedélyezése, hogy az új keretek fogadása és a videó dekódolása megfelelő legyen.

A megadott effektusok azonban nem biztosítanak részletes betekintést a szolgáltatás állapotára. A [kiszolgálóoldali teljesítmény-lekérdezések](performance-queries.md) használata ajánlott ehhez a használati esethez.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

* A szöveg átfedésének engedélyezése nem lehet kisebb a teljesítmény terhelése nélkül.
* A drótváz mód engedélyezése nem triviális teljesítménybeli terhelést eredményezhet, bár a jelenettől függően változhat. Összetett jelenetek esetén ez a mód azt eredményezheti, hogy a keret sebessége a 60-Hz cél alá csökken.

## <a name="next-steps"></a>Következő lépések

* [Renderelési módok](../../concepts/rendering-modes.md)
* [Kiszolgálóoldali teljesítménylekérdezések](performance-queries.md)
