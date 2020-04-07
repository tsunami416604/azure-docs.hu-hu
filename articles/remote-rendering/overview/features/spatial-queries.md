---
title: Térbeli lekérdezések
description: Térbeli lekérdezések csinálása jelenetben
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680530"
---
# <a name="spatial-queries"></a>Térbeli lekérdezések

A térbeli lekérdezések olyan műveletek, amelyekkel megkérdezheti a távoli renderelési szolgáltatást, hogy mely objektumok találhatók egy adott területen. A térbeli lekérdezéseket gyakran használják interakciók megvalósításához, például annak kitalálására, hogy a felhasználó melyik objektumra mutat.

A rendszer minden térbeli lekérdezést kiértékel a kiszolgálón. Ennek következtében aszinkron műveletek, és az eredmények a hálózati késéstől függő késleltetéssel érkeznek. Mivel minden térbeli lekérdezés hálózati forgalmat generál, ügyeljen arra, hogy ne tegyen túl sokat egyszerre.

## <a name="collision-meshes"></a>Ütközési minta

A térbeli lekérdezéseket a [Havok fizika motor hajtja,](https://www.havok.com/products/havok-physics) és egy dedikált ütközési hálót kell bemutatni. Alapértelmezés szerint a [modellkonverzió](../../how-tos/conversion/model-conversion.md) ütközési véletleneket hoz létre. Ha nincs szükség térbeli lekérdezésekre egy összetett modellen, fontolja meg az ütközési háló létrehozásának letiltását a [konvertálási beállításokban,](../../how-tos/conversion/configure-model-conversion.md)mivel az többféle hatással van:

* [A modellátalakítás](../../how-tos/conversion/model-conversion.md) jelentősen hosszabb időt vesz igénybe.
* A konvertált modellfájl mérete észrevehetően nagyobb, ami hatással van a letöltési sebességre.
* A futásidejű betöltési idő hosszabb.
* A futásidejű CPU-memóriafelhasználás magasabb.
* Van egy kis futásidejű teljesítmény terhelés minden modellpéldány.

## <a name="ray-casts"></a>Ray vet

A *sugáröntés* olyan térbeli lekérdezés, amelyben a futásidejű ellenőrzi, hogy mely objektumokat metszi egy sugár, egy adott pozíciótól kezdve egy adott irányba mutatva. Optimalizálásként maximális sugártávolságot is megadnak, hogy ne keressünk túl távoli objektumokat.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Három találati gyűjtési mód létezik:

* **Legközelebbi:** Ebben az üzemmódban csak a legközelebbi leütés lesz jelentve.
* **Bármely:** Inkább ezt a módot, ha minden, amit tudni akarsz, *hogy* egy sugár megüt valamit, de nem érdekel, mi volt a hit pontosan. Ez a lekérdezés jelentősen olcsóbb lehet kiértékelni, de csak néhány alkalmazással is rendelkezik.
* **Minden:** Ebben az üzemmódban a sugár mentén minden találatot a távolság szerint kell rendezni. Ne használja ezt a módot, hacsak nem kell több, mint az első találat. Korlátozza a jelentett találatok `MaxHits` számát a beállítással.

Ha azt szeretné, hogy az objektumok szelektíven ne legyenek figyelembe véve a sugáröntvények nél, a [HierarchicalStateOverrideComponent](override-hierarchical-state.md) összetevő használható.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Találati eredmény

A sugárleadott lekérdezés eredménye találatok tömbje. A tömb üres, ha nem talált el objektumot.

A leadott találat a következő tulajdonságokkal rendelkezik:

* **HitEntity:** Melyik [entitást](../../concepts/entities.md) találták el.
* **AlpartId:** Melyik *részhálót* találta el a [MeshComponent .](../../concepts/meshes.md) Lehet használni, hogy `MeshComponent.UsedMaterials` index, és keresse meg az [anyagot](../../concepts/materials.md) ezen a ponton.
* **HitPozíció:** A világűr pozíciója, ahol a sugár metszi az objektumot.
* **HitNormal:** A világtér felülete normális a háló a helyzet a metszéspont.
* **TávolságToHit:** A távolság a sugár kezdő pozíciója és a találat.

## <a name="next-steps"></a>További lépések

* [Objektumhatárok](../../concepts/object-bounds.md)
* [Felülbíráló hierarchikus állapotok](override-hierarchical-state.md)
