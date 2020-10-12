---
title: Vázlat renderelése
description: A kiválasztási vázlat megjelenítésének elvégzése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dd6d682c9db044763cad64eec420c1974d4ac03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613699"
---
# <a name="outline-rendering"></a>Vázlat renderelése

A kijelölt objektumok vizuális megjelenítéséhez a [hierarchikus állapot felülbírálása összetevővel](../../overview/features/override-hierarchical-state.md)lehet kiemelni a tagolást. Ebből a fejezetből megtudhatja, hogyan módosulnak a globális paraméterek a vázlatos megjelenítéshez az ügyfél API-n keresztül.

A körvonal tulajdonságai globális beállítások. A tagolást használó összes objektum ugyanazt a beállítást fogja használni – az objektumon belüli körvonal színét nem lehet használni.

## <a name="parameters-for-outlinesettings"></a>Paraméterek a következőhöz: `OutlineSettings`

`OutlineSettings`Az osztály a globális vázlat tulajdonságaival kapcsolatos beállításokat tartalmazza. A következő tagokat teszi elérhetővé:

| Paraméter      | Típus    | Leírás                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A körvonal rajzolásához használt szín Az alfa-rész figyelmen kívül lesz hagyva.         |
| `PulseRateHz`    | float   | A szerkezeti rezgések másodpercenkénti száma|
| `PulseIntensity` | float   | A tagolási impulzus hatásának intenzitása A teljes lüktetés esetén 0,0 és 1,0 közötti értéknek kell lennie. Az intenzitás implicit módon beállítja a vázlat minimális opacitását `MinOpacity = 1.0 - PulseIntensity` . |

![A különböző tagolási paraméterekkel háromszor megjelenített objektum ](./media/outlines.png) a (z `color` ) sárga (balra) és a magenta (Közép) és a `pulseIntensity` 0 – 0,8 (jobbra) közötti érték módosításának hatását eredményezi.

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
    ApiHandle<OutlineSettings> outlineSettings = session->Actions()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Teljesítmény

A vázlatos renderelés jelentős hatással lehet a renderelési teljesítményre. Ez a hatás az adott keret kiválasztott és nem kijelölt objektumai közötti képernyő-térbeli kapcsolattól függ.

## <a name="api-documentation"></a>API-dokumentáció

* [C# RemoteManager. OutlineSettings tulajdonság](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.outlinesettings)
* [C++ RemoteManager:: OutlineSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#outlinesettings)

## <a name="next-steps"></a>Következő lépések

* [Hierarchikus állapot felülbírálása összetevő](../../overview/features/override-hierarchical-state.md)
