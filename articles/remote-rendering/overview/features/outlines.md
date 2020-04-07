---
title: Vázlat renderelése
description: A kijelölési vázlat renderelése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680829"
---
# <a name="outline-rendering"></a>Vázlat renderelése

A kijelölt objektumok vizuálisan kiemelhetők, ha a [hierarchikus állapot felülbírálása összetevőn](../../overview/features/override-hierarchical-state.md)keresztül hozzáadjuk a körvonalrenderést. Ez a fejezet bemutatja, hogyan változnak a vázlatrenderelés globális paraméterei az ügyfél API-n keresztül.

A tagolási tulajdonságok globális beállítások. Minden olyan objektum, amely körvonalrenderelést használ, ugyanazt a beállítást fogja használni – nem lehet objektumonkénti körvonalszínt használni.

## <a name="parameters-for-outlinesettings"></a>Paraméterek`OutlineSettings`

Az `OutlineSettings` osztály tartalmazza a globális szerkezeti tulajdonságokkal kapcsolatos beállításokat. Ez kiteszi a következő tagok:

| Paraméter      | Típus    | Leírás                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Szín4Ub | A vázlat rajzolásakor használt szín. Az alfa rész figyelmen kívül lesz hagyva.         |
| `PulseRateHz`    | lebegőpontos   | Az a sebesség, amellyel a vázlat másodpercenként oszcillál|
| `PulseIntensity` | lebegőpontos   | A körvonalimpulzus-hatás intenzitása. A pulzálás nélküli 0,0, a teljes lüktetéshez pedig 1,0 között kell lennie. Az intenzitás implicit módon a tagolás minimális `MinOpacity = 1.0 - PulseIntensity`opacitását adja meg . |

![Körvonalak](./media/outlines.png) A `color` paraméter sárgáról (balra) bíborra (középre) és `pulseIntensity` 0-ról 0,8-ra (jobbra) történő módosításának hatása.

## <a name="example"></a>Példa

A következő kód egy példát mutat be a szerkezeti paraméterek API-n keresztültörténő beállítására:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Teljesítmény

A vázlatrenderelés jelentős hatással lehet a renderelési teljesítményre. Ez a hatás az adott keret kijelölt és nem kijelölt objektumai közötti térbeli térbeli kapcsolattól függ.

## <a name="next-steps"></a>További lépések

* [Hierarchikus állapot felülbírálása összetevő](../../overview/features/override-hierarchical-state.md)
