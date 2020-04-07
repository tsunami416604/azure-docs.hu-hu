---
title: Unity játék tárgyak és alkatrészek
description: A Távoli renderelési entitásokkal és összetevőkkel való együttműködésre szolgáló Unity-specifikus módszerek ismertetése.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: 872f0e60943a674e5febf269dcb5b09fd08d77c9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681154"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Kölcsönhatásba egység játék tárgyak és alkatrészek

Az Azure remote rendering (ARR) nagy számú objektumra van optimalizálva [(lásd: Korlátok](../../reference/limits.md)). Bár lehetséges a nagy és összetett hierarchiák kezelése a gazdagépen, az alacsony energiaellátású eszközökön a Unity-ben történő replikálása nem valósítható meg.

Ezért amikor egy modell be van töltve az állomásra, az Azure távoli renderelés tükrözi az ügyféleszközön lévő modellszerkezetre vonatkozó információkat (amely hálózati forgalmat fog kitölteni), de nem replikálja az objektumokat és az összetevőket a Unityben. Ehelyett elvárja, hogy manuálisan kérje a szükséges Unity játékobjektumokat és -összetevőket, így korlátozhatja a többletterhelést arra, amire ténylegesen szükség van. Így jobban szabályozhatja az ügyféloldali teljesítményt.

Következésképpen az Azure remote rendering unity integrációja további funkciókkal rendelkezik a távoli renderelési struktúra igény szerinti replikálásához.

## <a name="load-a-model-in-unity"></a>Modell betöltése unity

Amikor betölt egy modellt, a betöltött modell gyökérobjektumára mutató hivatkozást kap. Ez a hivatkozás nem egy Unity játék objektum, de lehet `Entity.GetOrCreateGameObject()`alakítani egy a kiterjesztés módszer . Ez a függvény egy `UnityCreationMode`típusú argumentumot vár. Ha átmegy `CreateUnityComponents`, az újonnan létrehozott Unity játékobjektum ezen felül lesz feltöltve az állomáson lévő összes távoli renderelési összetevő proxyösszetevőivel. Javasoljuk azonban, hogy inkább `DoNotCreateUnityComponents`, hogy tartsa a felső minimális.

### <a name="load-model-with-task"></a>Modell betöltése feladattal

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

### <a name="load-model-with-unity-coroutines"></a>Terhelési modell Unity korutinokkal

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

### <a name="load-model-with-await-pattern"></a>Betöltési modell a várt mintával

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

A fenti kódminták a modell betöltési útvonalát használták a SAS-on keresztül, mert a beépített modell be van töltve. A modell kezelése blob tárolók (használatával `LoadModelAsync` és `LoadModelParams`) teljes mértékben hasonlóan működik.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject objektum

Létrehozása Unity játék objektum implicit `RemoteEntitySyncObject` módon hozzáad egy összetevőt a játék objektumot. Ez az összetevő az entitásátalakítás kiszolgálóval való szinkronizálására szolgál. Alapértelmezés `RemoteEntitySyncObject` szerint a felhasználónak `SyncToRemote()` explicit módon kell hívnia a helyi Egység állapot nak a kiszolgálóval való szinkronizálásához. Az `SyncEveryFrame` engedélyezés automatikusan szinkronizálja az objektumot.

Az a `RemoteEntitySyncObject` objektummal rendelkező objektumok távoli gyermekeiket a Gyermekek **megjelenítése** gombon keresztül az Egység szerkesztőben példányosítottak és jeleníthetik meg.

![RemoteEntitySyncObject objektum](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Burkoló alkatrészek

[A](../../concepts/components.md) távoli renderelési entitásokhoz csatlakoztatott összetevők `MonoBehavior`proxykon keresztül vannak kitéve az Egységnek. Ezek a proxyk képviselik a távoli komponens egység, és továbbítja az összes módosítást a fogadó.

Proxy távleképezési összetevőinek `GetOrCreateArrComponent`létrehozásához használja a bővítménymódszert:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Összekapcsolt élettartamok

Egy távoli [entitás](../../concepts/entities.md) és egy Unity játékobjektum élettartama összekapcsolódik, miközben egy `RemoteEntitySyncObject`. Ha ilyen `UnityEngine.Object.Destroy(...)` játékobjektummal hív, a távoli entitás is eltávolításra kerül.

A Unity játékobjektum megsemmisítéséhez, anélkül, hogy befolyásolná `Unbind()` a `RemoteEntitySyncObject`távoli entitást, először meg kell hívnia a .

Ugyanez igaz az összes proxy összetevőre. Ha csak az ügyféloldali képviseletet szeretné `Unbind()` megsemmisíteni, először a proxyösszetevőt kell meghívnia:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>További lépések

* [Távoli renderelés beállítása egységre](unity-setup.md)
* [Oktatóanyag: Távoli entitások együttműködése unityben](../../tutorials/unity/working-with-remote-entities.md)
