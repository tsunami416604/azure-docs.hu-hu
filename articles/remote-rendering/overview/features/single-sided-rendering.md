---
title: Egyoldalas renderelés
description: Az egyoldalas renderelési beállítások és használati esetek ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682012"
---
# <a name="single-sided-rendering"></a>Egyoldalas renderelés

A legtöbb renderelő a teljesítmény javítása érdekében [visszalapozatási](https://en.wikipedia.org/wiki/Back-face_culling) eljárással javítja a teljesítményt. Amikor azonban a hálókat [vágott síkokkal](cut-planes.md)nyitják fel, a felhasználók gyakran a háromszögek hátsó oldalát nézik. Ha ezeket a háromszögeket leselejtezik, az eredmény nem tűnik meggyőzőnek.

A probléma megbízható megelőzésének módja a háromszögek *kétoldalas renderelése.* Mivel a visszalaposítás nem használ teljesítménybeli következményekkel jár, az Azure távoli renderelés alapértelmezés szerint csak kétoldalas renderelésre vált a vágott síkkal metsző szemközésű szemképekhez.

Az *egyoldalas renderelési* beállítás lehetővé teszi a viselkedés testreszabását.

> [!CAUTION]
> Az egyoldalas renderelési beállítás egy kísérleti funkció. Lehet, hogy újra eltávolítják a jövőben. Kérjük, ne módosítsa az alapértelmezett beállítást, kivéve, ha valóban megoldja az alkalmazás kritikus problémáját.

## <a name="prerequisites"></a>Előfeltételek

Az egyoldalas renderelési beállítás csak a beállítással átalakított, `opaqueMaterialDefaultSidedness` a `SingleSided`beállításával konvertált szemképekre van [hatással.](../../how-tos/conversion/configure-model-conversion.md) Alapértelmezés szerint ez a `DoubleSided`beállítás a értékre van állítva.

## <a name="single-sided-rendering-setting"></a>Egyoldalas renderelési beállítás

Három különböző mód létezik:

**Normál:** Ebben a módban a program a program mindig megjeleníti a biztonsági műveleteket, amikor konvertálják őket. Ez azt jelenti, `opaqueMaterialDefaultSidedness` hogy `SingleSided` a beállított beállítással konvertált szemközti elemek mindig akkor jelennek meg, ha a hátsó felületselejtezés engedélyezve van, még akkor is, ha metszik egymást egy kivágott síkban.

**Dinamikusduplasziding:** Ebben az üzemmódban, amikor egy kivágott sík metszi a hálót, automatikusan kétoldalas renderelésre vált. Ez az alapértelmezett mód.

**AlwaysDoubleSided:** Arra kényszeríti az összes egyoldalas geometriát, hogy mindig kétoldalas legyen. Ez a mód többnyire elérhető, így könnyen összehasonlíthatja az egyoldalas és a kétoldalas renderelés teljesítményhatását.

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

* [Vágott síkok](cut-planes.md)
* [A modellkonvertálás konfigurálása](../../how-tos/conversion/configure-model-conversion.md)
