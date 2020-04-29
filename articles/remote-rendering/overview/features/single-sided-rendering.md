---
title: Egyoldalas renderelés
description: Az egyoldalas megjelenítési beállítások és a használati esetek ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682012"
---
# <a name="single-sided-rendering"></a>Egyoldalas renderelés

A legtöbb megjelenítő [háttérbeli selejtezést](https://en.wikipedia.org/wiki/Back-face_culling) használ a teljesítmény növelése érdekében. Ha azonban a rácsvonalak kivágása a [kivágási síkokkal](cut-planes.md)történik, a felhasználók gyakran a háromszögek hátoldalán jelennek meg. Ha ezek a háromszögek el vannak selejtezett, az eredmény nem tűnik meggyőzőnek.

A probléma megbízható megelőzése a háromszögek *kétoldalas megjelenítésének módja.* Mivel a háttérbeli selejtezés nem használ teljesítménybeli következményeket, alapértelmezés szerint az Azure távoli renderelési funkció csak a kivágási síkon feldarabolt rácsvonalak kétoldalas megjelenítésére vált.

Az *egyoldalas renderelési* beállítással testreszabhatja ezt a viselkedést.

> [!CAUTION]
> Az egyoldalas megjelenítési beállítás egy kísérleti funkció. Előfordulhat, hogy a későbbiekben újra el lesz távolítva. Ne módosítsa az alapértelmezett beállítást, kivéve, ha az alkalmazásban kritikus problémát észlel.

## <a name="prerequisites"></a>Előfeltételek

Az egyoldalas renderelési beállítás csak olyan rácsvonalak esetében lép érvénybe, amelyeket a `opaqueMaterialDefaultSidedness` következő beállítással [alakítottak át](../../how-tos/conversion/configure-model-conversion.md) :. `SingleSided` Alapértelmezés szerint ez a beállítás a `DoubleSided`következőre van beállítva:.

## <a name="single-sided-rendering-setting"></a>Egyoldalas renderelési beállítás

Három különböző mód van:

**Normál:** Ebben a módban a rácsvonalak mindig a konvertáláskor lesznek megjelenítve. Ez azt jelenti, hogy a `opaqueMaterialDefaultSidedness` készlettel `SingleSided` konvertált rácsvonalak mindig engedélyezve lesznek a háttérbeli selejtezéssel, még akkor is, ha egy kivágási síkon metszik őket.

**DynamicDoubleSiding:** Ebben a módban, amikor egy kivágási sík metszi a hálót, automatikusan a kétoldalas megjelenítésre vált. Ez az alapértelmezett mód.

**AlwaysDoubleSided:** Minden egyoldalas geometriát egyszerre kétoldalas megjelenítésre kényszerít. Ez a mód többnyire fel van téve, így egyszerűen összehasonlíthatja az egyoldalas és a kétoldalas renderelés teljesítményére gyakorolt hatást.

Az egyoldalas renderelési beállítások módosítása a következőképpen végezhető el:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>További lépések

* [Síkok kivágása](cut-planes.md)
* [A modell átalakításának konfigurálása](../../how-tos/conversion/configure-model-conversion.md)
