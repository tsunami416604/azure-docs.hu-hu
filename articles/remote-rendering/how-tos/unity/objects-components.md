---
title: Unity game Objects és Components
description: A távoli renderelési entitásokkal és összetevőkkel való együttműködéshez szükséges Unity-módszereket ismerteti.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: e55589a388a1883f42284f2e20c6d5619b63f48f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565480"
---
# <a name="interact-with-unity-game-objects-and-components"></a>A Unity játékobjektumainak és összetevőinek használata

Az Azure távoli renderelés (ARR) nagy mennyiségű objektumra van optimalizálva (lásd a [korlátozásokat](../../reference/limits.md)). Habár lehetséges a nagyméretű és összetett hierarchiák kezelése a gazdagépen, az összes, az alacsony teljesítményű eszközökre való replikálása nem valósítható meg.

Ezért, amikor egy modell be van töltve a gazdagépre, az Azure-alapú távoli renderelés a modell szerkezetére vonatkozó információkat az ügyfélszámítógépen (amely a hálózati forgalmat terheli), de nem replikálja az objektumokat és összetevőket az egységben. Ehelyett a szükséges Unity-játék-objektumok és-összetevők manuális igénylésére vár, így korlátozhatja a terhelést a ténylegesen szükségesnél. Így jobban kézben tarthatja az ügyféloldali teljesítményt.

Ennek következtében az Azure-alapú távoli renderelés Unity-integrációja további funkciókkal rendelkezik a távoli renderelési struktúra igény szerinti replikálásához.

## <a name="load-a-model-in-unity"></a>Modell betöltése Unity

Modell betöltésekor a betöltött modell gyökerére mutató hivatkozás fog megjelenni. Ez a hivatkozás nem Unity game-objektum, de a bővítmény metódussal is kikapcsolható `Entity.GetOrCreateGameObject()` . Ez a függvény egy típusú argumentumot vár `UnityCreationMode` . Ha átadja `CreateUnityComponents` , az újonnan létrehozott Unity game objektum emellett a gazdagépen található összes távoli renderelési összetevőhöz tartozó proxy-összetevőkkel is feltöltve lesz. Ajánlott azonban `DoNotCreateUnityComponents` a minimális terhelés megtartása érdekében.

### <a name="load-model-with-task"></a>Modell betöltése a feladattal

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Modell betöltése Unity-munkarutinokkal

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Modell betöltése várakozási mintával

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

A fenti kód a modellnek az SAS-n keresztüli elérési útját használta, mert a beépített modell be van töltve. A modell kezelése blob-tárolókkal (a és a használatával `LoadModelAsync` `LoadModelParams` ) teljesen analogously működik.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Egy Unity game objektum létrehozása implicit módon hozzáadja egy `RemoteEntitySyncObject` összetevőt a game objektumhoz. Ezzel az összetevővel lehet szinkronizálni az entitás átalakítását a-kiszolgálóval. Alapértelmezés szerint `RemoteEntitySyncObject` a felhasználónak explicit módon meg kell hívnia `SyncToRemote()` , hogy szinkronizálja a helyi egység állapotát a-kiszolgálóval. Az engedélyezéssel `SyncEveryFrame` automatikusan szinkronizálja az objektumot.

A-val rendelkező objektumok `RemoteEntitySyncObject` létrehozhatják a távoli gyermekeiket, és megjelennek az Unity Editorban a **:::no-loc text="Show children":::** gomb segítségével.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Burkoló összetevők

A távoli renderelési entitásokhoz csatolt [összetevők](../../concepts/components.md) a proxyn keresztül vannak kitéve az egységnek `MonoBehavior` . Ezek a proxyk a távoli összetevőt képviselik az egységben, és minden módosítást továbbítanak a gazdagépnek.

A proxy távoli renderelési összetevőinek létrehozásához használja a következő kiterjesztési módszert `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Összekapcsolt élettartamok

A távoli [entitások](../../concepts/entities.md) élettartama és a Unity game objektum egymáshoz kötve van `RemoteEntitySyncObject` . Ha `UnityEngine.Object.Destroy(...)` egy ilyen game objektumot hív meg, akkor a távoli entitás is el lesz távolítva.

Ha el szeretné pusztítani a Unity game objektumot anélkül, hogy befolyásolná a távoli entitást, először meg kell hívnia a következőt: `Unbind()` `RemoteEntitySyncObject` .

Ugyanez vonatkozik az összes proxy-összetevőre. Csak az ügyféloldali ábrázolás megsemmisítéséhez először meg kell hívnia a `Unbind()` proxy összetevőt:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>További lépések

* [A Remote Rendering beállítása a Unityben](unity-setup.md)
* [Oktatóanyag: távoli entitások kezelése az egységben](../../tutorials/unity/manipulate-models/manipulate-models.md)
