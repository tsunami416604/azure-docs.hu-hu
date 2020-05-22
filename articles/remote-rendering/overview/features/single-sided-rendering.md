---
title: Egyoldalas renderelés
description: Az egyoldalas megjelenítési beállítások és a használati esetek ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 97e0456e274adee7d678e373cfd92b5003f3d801
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759098"
---
# <a name="single-sided-rendering"></a>Egyoldalas renderelés

A legtöbb megjelenítő [háttérbeli selejtezést](https://en.wikipedia.org/wiki/Back-face_culling) használ a teljesítmény növelése érdekében. Ha azonban a rácsvonalak kivágása a [kivágási síkokkal](cut-planes.md)történik, a felhasználók gyakran a háromszögek hátoldalán jelennek meg. Ha ezek a háromszögek el vannak selejtezett, az eredmény nem tűnik meggyőzőnek.

A probléma megbízható megelőzése a háromszögek *kétoldalas megjelenítésének módja.* Mivel a háttérbeli selejtezés nem használ teljesítménybeli következményeket, alapértelmezés szerint az Azure távoli renderelési funkció csak a kivágási síkon feldarabolt rácsvonalak kétoldalas megjelenítésére vált.

Az *egyoldalas renderelési* beállítással testreszabhatja ezt a viselkedést.

> [!CAUTION]
> Az egyoldalas megjelenítési beállítás egy kísérleti funkció. Előfordulhat, hogy a későbbiekben újra el lesz távolítva. Ne módosítsa az alapértelmezett beállítást, kivéve, ha az alkalmazásban kritikus problémát észlel.

## <a name="prerequisites"></a>Előfeltételek

Az egyoldalas renderelési beállítás csak olyan rácsvonalak esetében lép érvénybe, amelyeket a következő beállítással [alakítottak át](../../how-tos/conversion/configure-model-conversion.md) : `opaqueMaterialDefaultSidedness` `SingleSided` . Alapértelmezés szerint ez a beállítás a következőre van beállítva: `DoubleSided` .

## <a name="single-sided-rendering-setting"></a>Egyoldalas renderelési beállítás

Három különböző mód van:

**Normál:** Ebben a módban a rácsvonalak mindig a konvertáláskor lesznek megjelenítve. Ez azt jelenti, hogy a készlettel konvertált rácsvonalak `opaqueMaterialDefaultSidedness` `SingleSided` mindig engedélyezve lesznek a háttérbeli selejtezéssel, még akkor is, ha egy kivágási síkon metszik őket.

**DynamicDoubleSiding:** Ebben a módban, amikor egy kivágási sík metszi a hálót, automatikusan a kétoldalas megjelenítésre vált. Ez az alapértelmezett mód.

**AlwaysDoubleSided:** Minden egyoldalas geometriát egyszerre kétoldalas megjelenítésre kényszerít. Ez a mód többnyire fel van téve, így egyszerűen összehasonlíthatja az egyoldalas és a kétoldalas renderelés teljesítményére gyakorolt hatást.

Az egyoldalas renderelési beállítások módosítása a következőképpen végezhető el:

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>További lépések

* [Síkok kivágása](cut-planes.md)
* [A modell átalakításának konfigurálása](../../how-tos/conversion/configure-model-conversion.md)
