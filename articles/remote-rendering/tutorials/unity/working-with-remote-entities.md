---
title: Távoli entitások együttműködése az Egységben
description: Oktatóanyag, amely bemutatja, hogyan kell dolgozni aRR entitások.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 97b2a0e48907ebe5b1cafdf92d14e9a4c6a28360
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679533"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Oktatóanyag: Távoli entitások együttműködése unityben

[Oktatóanyag: A teljesen új Unity-projekt beállítása megmutatta,](project-setup.md) hogyan konfigurálhat egy új Unity-projektet az Azure remote rendering-vel való együttműködésre. Ebben az oktatóanyagban megnézzük a leggyakoribb funkciókat, amelyeket minden ARR felhasználónak szüksége van.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Válasszon tárgyakat sugáröntvények használatával.
> * Az objektumállapotok felülbírálása, például színárnyalat, kijelölési állapot és láthatóság.
> * Távoli entitások törlése.
> * Távoli entitások mozgatása.
> * Használja vágott síkok belülre nézni tárgyakat.

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag a bemutató tetejére [épül: Unity-projekt beállítása a semmiből](project-setup.md).

> [!TIP]
> Az [ARR mintatár](https://github.com/Azure/azure-remote-rendering) tartalmazza előkészített Unity projektek minden oktató a *Unity* mappában, hogy használhatja a referenciaként.

## <a name="pick-objects"></a>Objektumok kivétele

Azt akarjuk, hogy kölcsönhatásba lépnek a tárgyakat, így az első dolog, amire szükségünk van, a szedés tárgyak az egér kurzor.

Hozzon létre egy [új szkriptet](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) nevű **RemoteRaycaster** és cserélje ki a teljes tartalmát az alábbi kódot:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Adja hozzá ezt az összetevőt a jelenet *RemoteRendering* objektumához.

> [!WARNING]
>
> A *RemoteRaycaster* összetevőhöz ugyanahhoz az objektumhoz *aRRServiceUnity* összetevőt kell csatolni. *Az ARRServiceUnity* egy segítő osztály, amely könnyebben hozzáfér néhány ARR funkcióhoz. Ennek az összetevőnek azonban csak egyetlen példánya lehet a jelenetben. Ezért ügyeljen arra, hogy az *ARRServiceUnity-t* igénylő összes összetevőt hozzáadja ugyanahhoz a GameObject objektumhoz.
> Ha több játékobjektumból szeretné elérni az ARR funkciót, adja hozzá az *ARRServiceUnity* összetevőt csak az egyikhez, és hivatkozzon a többi parancsfájlra, vagy közvetlenül érje el az ARR funkciót.

Nyomja meg a lejátszás gombot, csatlakozzon egy munkamenethez, és töltsön be egy modellt. Most pont tárgyak a jelenetet, és nézni a konzol kimenetét. Ki kell nyomtatnia az egérmutatót mutató minden egyes rész objektumnevét.

## <a name="highlight-objects"></a>Objektumok kiemelése

Következő lépésként vizuális visszajelzést szeretnénk adni, hogy a felhasználó a modell mely részeire mutat. Ennek elérése érdekében egy [hierarchikusStateOverrideComponent-et](../../overview/features/override-hierarchical-state.md) csatolunk a kiválasztott entitáshoz. Ez az összetevő az objektum különböző szolgáltatásainak engedélyezésére vagy letiltására használható. Itt használjuk, hogy állítsa be a színárnyalat színe, és lehetővé teszi [vázlat renderelés](../../overview/features/outlines.md).

Hozzon létre egy másik **parancsfájlt, a RemoteModelEntity** nevű fájlt, és cserélje le annak tartalmát a következő kódra:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```

Következő, meg kell bővíteni a *RemoteRaycaster* hozzá a *RemoteModelEntity* összetevő t, amit csak felvette.

Adja hozzá a következő kódot a **RemoteRaycaster** implementációhoz, és távolítsa el az ismétlődő függvényeket:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Futtassa a projektet, és mutasson egy modellre, látnia kell, hogy piros színés egy fehér kijelölési körvonal jelenik meg.

## <a name="isolate-the-selected-object"></a>A kijelölt objektum elkülönítése

A [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) egy másik használata a láthatóság felülbírálása. Ez lehetővé teszi a kijelölt objektum elkülönítését a modell többi részétől. Nyissa meg a **RemoteModelEntity** parancsfájlt, adja hozzá a következő kódot, és távolítsa el az ismétlődő függvényeket:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Ez a kód a hierarchia legfelső objektumában lévő állapotfelülírási összetevőn alapul, amely minden objektumot láthatatlanná tesz. Ezután felülírja a láthatóságot a kijelölt objektumnál, hogy láthatóvá tegye az objektumot. Ezért létre kell hoznunk egy állapotfelülírási összetevőt a gyökérobjektumnál.

Nyissa meg a **RemoteRendering** parancsfájlt, és szúrja be az alábbi kódot a *LoadModel* függvény tetejére:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Végül szükségünk van egy módja annak, hogy váltani láthatóság. Nyissa meg a **RemoteRaycaster** parancsfájlt, és cserélje le a *Frissítés* funkciót:

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Futtassa a kódot, és jobb klikk egy részét a modell. A modell többi része eltűnik, és csak a kiemelt darab marad látható.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Távoli entitások GameObject-példányainak eltávolítása

Lehet, hogy észrevette, hogy a kód folyamatosan objektumokat hoz létre, de soha nem tisztítja meg őket. Ez az objektumhierarchia panelen is látható. Amikor a szimuláció során kibontja a távoli objektumhierarchiát, egyre több objektum jelenik meg minden alkalommal, amikor a modell egy új része fölé viszi az egérmutatót.

Ha egy jelenetben sok objektum van, az negatívan befolyásolja a teljesítményt. Mindig tisztítsa meg azokat az objektumokat, amelyekre már nincs szükség.

Helyezze be az alábbi kódot a **RemoteRaycaster parancsfájlba,** és távolítsa el az ismétlődő függvényeket:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Objektumok áthelyezése

Következő lépésként egy kijelölt objektumot szeretnénk áthelyezni. A **RemoteRaycaster** parancsfájlba szúrja be ezt a kódot, és távolítsa el az ismétlődő függvényt:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Ha futtatja ezt a kódot, észre fogja venni, hogy semmi sem történik. Ennek az az oka, hogy egy objektum átalakításának módosítása teljesítménybeli okokból nem szinkronizálja automatikusan az állapotváltozást a kiszolgálóval. Ehelyett vagy manuálisan kell leadnia ezt az állapotmódosítást a kiszolgálóra, vagy engedélyeznie kell a **SyncEveryFrame** programot a *RemoteEntitySyncObject* összetevőben.

Nyissa meg a **RemoteModelEntity** parancsfájlt, és adja hozzá ezt a sort:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Ha újra futtatja a kódot, bal ról balra kell kattintania egy objektumra, és körbe kell húznia.

## <a name="add-a-cut-plane"></a>Vágott sík hozzáadása

Az utolsó funkció azt akarjuk, hogy próbálja ki a bemutató, használ [vágott síkok](../../overview/features/cut-planes.md). A vágott sík elvágja a renderelt tárgyak részeit, így belenézhet.

Hozzon létre egy új GameObject a **jelenetCutPlane**. Hozzon létre egy új szkriptet, és hívja **RemoteCutPlane**. Adja hozzá az összetevőt az új GameObject objektumhoz.

Nyissa meg a parancsfájlt, és cserélje le annak tartalmát a következő kódra:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Amikor most futtatja a kódot, látnia kell, hogyan vágja fel a modellt a gép. Kijelölheti a *CutPlane* objektumot, és áthelyezheti és elforgathatja a *Jelenet* ablakban. A vágott síkbe be- és kikapcsolhatja a vágósík objektum letiltásával.

## <a name="next-steps"></a>További lépések

Most már tudja, hogy a legfontosabb funkciók kölcsönhatásban áll a távoli tárgyakat. A következő bemutatóban megnézzük a jelenet megjelenésének testreszabását.

> [!div class="nextstepaction"]
> [Oktatóanyag: A környezet és az anyagok megváltoztatása](changing-environment-and-materials.md)
