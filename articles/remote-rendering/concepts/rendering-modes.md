---
title: Renderelési módok
description: A kiszolgálóoldali leképezési módok ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681700"
---
# <a name="rendering-modes"></a>Renderelési módok

A távoli renderelés két fő üzemmódot kínál, a **TileBasedComposition** és **a DepthBasedComposition** módot. Ezek a módok határozzák meg, hogy a számítási feladatok hogyan oszlanak el a kiszolgáló több GPU-ja között. A módot a kapcsolat idején kell megadni, és futásközben nem módosítható.

Mindkét mód jön-val előnyöket, hanem a benne rejlő funkció korlátait, így a szedés a legmegfelelőbb mód használata esetspecifikus.

## <a name="modes"></a>Módok

A két mód most részletesebben tárgyalja.

### <a name="tilebasedcomposition-mode"></a>"TileBasedComposition" mód

**TileBasedComposition** módban minden érintett GPU adott subrectangles (csempe) megjelenítését jeleníti meg a képernyőn. A fő GPU a végső képet a csempékből állítja össze, mielőtt videokeretként elküldené az ügyfélnek. Ennek megfelelően minden GPU-nak ugyanazokat az erőforrásokat kell rendelkeznie a rendereléshez, így a betöltött eszközöknek egyetlen GPU memóriájába kell illeszkedniük.

A renderelésminősége ebben a módban valamivel jobb, mint **a DepthBasedComposition** módban, mivel az MSAA minden GPU teljes geometriáján működhet. A következő képernyőkép azt mutatja, hogy az élsimítás mindkét szélén is megfelelően működik:

![MSAA a TileBasedComposition-ben](./media/service-render-mode-quality.png)

Továbbá, ebben az üzemmódban minden alkatrész átkapcsolható egy átlátszó anyagra, vagy átlehet kapcsolni **átlátszó** módra a [HierarchicalStateOverrideComponent segítségével](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>"DepthBasedComposition" mód

**A DepthBasedComposition** módban minden érintett GPU teljes képernyős felbontásban renderel, de csak a szemcsék egy részhalmaza. A fő GPU végső képösszetétele gondoskodik arról, hogy az alkatrészek megfelelően egyesüljenek a mélységi információiknak megfelelően. Természetesen a memória hasznos terhelése eloszlik a GPU-k között, így lehetővé teszi a renderelési modelleket, amelyek nem férnek el egyetlen GPU memóriájába.

Minden egyes GPU MSAA-t használ a helyi tartalom ellenélezésére. Előfordulhat azonban, hogy a különböző GPU-k szélei között eredendő aliasing is lehet. Ezt a hatást a végső kép utófeldolgozása enyhíti, de az MSAA minősége még mindig rosszabb, mint **a TileBasedComposition** módban.

Az MSAA-összetevőket a következő ![kép szemlélteti: MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

Az élsimítás megfelelően működik a szobor és a függöny között, mivel mindkét rész ugyanazon a GPU-n jelenik meg. Másrészt, a széle között függöny és fal mutat néhány aliasing, mert ez a két rész áll különböző GPU-k.

Ennek a módnak a legnagyobb korlátozása az, hogy a geometriai alkatrészek nem kapcsolhatók át átlátszó anyagokra dinamikusan, és az **átlátszó** mód sem működik a [HierarchicalStateOverrideComponent esetében.](../overview/features/override-hierarchical-state.md) Más állapot felülbírálási funkciók (vázlat, színárnyalat, ...) nem működik, mégis. A konverziós idő alatt átlátszóként megjelölt anyagok is megfelelően működnek ebben a módban.

### <a name="performance"></a>Teljesítmény

A két mód teljesítményjellemzői a használati esettől függően változnak, és nehéz észhez érteni vagy általános ajánlásokat tenni. Ha nem korlátozzák a fent említett korlátozások (memória vagy átlátszóság / átlátszó), javasoljuk, hogy próbálja ki mindkét módot, és figyelje a teljesítményt különböző kamerapozíciókkal.

## <a name="setting-the-render-mode"></a>A leképezési mód beállítása

A távoli renderelési virtuális gépen `AzureSession.ConnectToRuntime` használt `ConnectToRuntimeParams`leképezési mód a .

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>További lépések

* [Munkamenetek](../concepts/sessions.md)
* [Hierarchikus állapot felülbírálása összetevő](../overview/features/override-hierarchical-state.md)
