---
title: Vázlat renderelése
description: A kiválasztási vázlat megjelenítésének elvégzése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4f3889a0ba121cb9a3167c1f6ac95f0bed280539
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83759013"
---
# <a name="outline-rendering"></a>Vázlat renderelése

A kijelölt objektumok vizuális megjelenítéséhez a [hierarchikus állapot felülbírálása összetevővel](../../overview/features/override-hierarchical-state.md)lehet kiemelni a tagolást. Ebből a fejezetből megtudhatja, hogyan módosulnak a globális paraméterek a vázlatos megjelenítéshez az ügyfél API-n keresztül.

A körvonal tulajdonságai globális beállítások. A tagolást használó összes objektum ugyanazt a beállítást fogja használni – az objektumon belüli körvonal színét nem lehet használni.

## <a name="parameters-for-outlinesettings"></a>Paraméterek a következőhöz:`OutlineSettings`

`OutlineSettings`Az osztály a globális vázlat tulajdonságaival kapcsolatos beállításokat tartalmazza. A következő tagokat teszi elérhetővé:

| Paraméter      | Típus    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A körvonal rajzolásához használt szín Az alfa-rész figyelmen kívül lesz hagyva.         |
| `PulseRateHz`    | lebegőpontos   | A szerkezeti rezgések másodpercenkénti száma|
| `PulseIntensity` | lebegőpontos   | A tagolási impulzus hatásának intenzitása A teljes lüktetés esetén 0,0 és 1,0 közötti értéknek kell lennie. Az intenzitás implicit módon beállítja a vázlat minimális opacitását `MinOpacity = 1.0 - PulseIntensity` . |

![A (z ](./media/outlines.png) `color` ) sárga (balra) és a magenta (Közép) és a `pulseIntensity` 0 – 0,8 (jobb) közötti érték módosításának hatását ismerteti.

## <a name="example"></a>Példa

Az alábbi kód egy példát mutat be a szerkezeti paraméterek beállítására az API-n keresztül:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = *session->Actions()->OutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->Color(outlineColor);
    outlineSettings->PulseRateHz(2.0f);
    outlineSettings->PulseIntensity(0.5f);
}
```

## <a name="performance"></a>Teljesítmény

A vázlatos renderelés jelentős hatással lehet a renderelési teljesítményre. Ez a hatás az adott keret kiválasztott és nem kijelölt objektumai közötti képernyő-térbeli kapcsolattól függ.

## <a name="next-steps"></a>További lépések

* [Hierarchikus állapot felülbírálása összetevő](../../overview/features/override-hierarchical-state.md)
