---
title: Távoli entitások használata a Unityben
description: Ez az oktatóanyag bemutatja, hogyan használható az ARR-entitások használata.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: 5d995e9a5cdb6fc18532e0c3533959e9feece908
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021245"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Oktatóanyag: távoli entitások használata az egységben

[Oktatóanyag: egy Unity-projekt létrehozása a semmiből](project-setup.md) megmutatta, hogyan konfigurálható egy új Unity-projekt az Azure távoli rendereléssel való együttműködéshez. Ebben az oktatóanyagban megtekintjük a leggyakoribb funkciókat, amelyeket minden ARR-felhasználó igényel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Objektumok kiválasztása a Ray casts használatával.
> * Felülbírálja az objektumokat, például az árnyalat színét, a kiválasztási állapotot és a láthatóságot.
> * Távoli entitások törlése.
> * Helyezze át a távoli entitásokat a köré.
> * A kivágott síkok használatával megvizsgálhatja az objektumokat.

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag az [oktatóanyagra épül: egy Unity-projekt létrehozása a semmiből](project-setup.md).

> [!TIP]
> Az [ARR-minták tárháza](https://github.com/Azure/azure-remote-rendering) előkészített Unity-projekteket tartalmaz az *Unity* mappában található összes oktatóanyaghoz, amelyet hivatkozásként használhat.

## <a name="pick-objects"></a>Objektumok kiválasztása

Szeretnénk az objektumokkal együttműködni, ezért az első szükséges, hogy az egérmutató alatt kiválasszon objektumokat.

Hozzon létre egy **RemoteRaycaster** nevű [új parancsfájlt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) , és cserélje le a teljes tartalmat az alábbi kódra:

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

Adja hozzá ezt az összetevőt a *RemoteRendering* objektumhoz a jelenetben.

> [!WARNING]
>
> A *RemoteRaycaster* összetevőhöz hozzá kell rendelni egy *ARRServiceUnity* -összetevőt ugyanahhoz az objektumhoz. A *ARRServiceUnity* egy segítő osztály, amely megkönnyíti néhány ARR funkció elérését. Ebben az összetevőben azonban csak egyetlen példány lehet a jelenetben. Ezért ügyeljen arra, hogy minden olyan összetevőt felvegyen, amely *ARRServiceUnity* igényel ugyanahhoz a GameObject.
> Ha több játékból álló objektumból szeretné elérni az ARR-funkciókat, adja hozzá a *ARRServiceUnity* összetevőt csak az egyikhez, és hivatkozzon a többi parancsfájlra, vagy közvetlenül az ARR funkció eléréséhez.

Nyomja le a play gombot, kapcsolódjon egy munkamenethez, és töltsön be egy modellt. Most mutasson a jelenet objektumaira, és tekintse meg a konzol kimenetét. A rendszernek ki kell nyomtatnia a fölé viszi az egyes részek objektumának nevét.

## <a name="highlight-objects"></a>Objektumok kiemelése

A következő lépésként vizuális visszajelzést szeretnénk adni, amely a felhasználó által megjelenített modell részét képezi. Ennek eléréséhez csatolunk egy [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) a kiválasztott entitáshoz. Ezzel az összetevővel engedélyezheti vagy letilthatja az objektumok különböző funkcióit. Itt használjuk a színárnyalat színének megadására és a [vázlatos megjelenítés](../../overview/features/outlines.md)engedélyezésére.

Hozzon létre egy **RemoteModelEntity** nevű másik parancsfájlt, és cserélje le a tartalmát a következő kódra:

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
> [!CAUTION]
> Ne rendelje hozzá ezt a szkriptet bármely Unity game-objektumhoz, mert az alábbi kód által programozott módon lesz hozzárendelve.

Ezután ki kell bővíteni a *RemoteRaycaster* , hogy hozzáadja a *RemoteModelEntity* összetevőt az imént kiválasztott objektumhoz.

Adja hozzá a következő kódot a **RemoteRaycaster** megvalósításához, és távolítsa el az ismétlődő függvényeket:

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

Futtasson egy modellt, és mutasson a modellre, és tekintse meg a piros árnyalatot és a fehér kiválasztási vázlatot.

## <a name="isolate-the-selected-object"></a>A kijelölt objektum elkülönítése

A [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) egy másik használata a láthatóság felülbírálásának lehetősége. Ez lehetővé teszi, hogy elkülönítse a kiválasztott objektumot a modell többi részétől. Nyissa meg a **RemoteModelEntity** parancsfájlt, adja hozzá a következő kódot, és távolítsa el az ismétlődő függvényeket:

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

Ez a kód arra támaszkodik, hogy a hierarchia legfelső szintjén lévő állapot-felülbírálási összetevőt használja, amely az összes objektumot láthatatlanná teszi. Ezután felülbírálja a kijelölt objektum láthatóságát, hogy az egy objektum látható legyen. Ezért létre kell hozni egy állapot-felülbírálási összetevőt a gyökérszintű objektumon.

Nyissa meg a **RemoteRendering** parancsfájlt, és szúrja be az alábbi kódot az *LoadModel* függvény elejére:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Végül meg kell váltani a láthatóságot. Nyissa meg a **RemoteRaycaster** parancsfájlt, és cserélje le a *Update* függvényt:

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

Futtassa a kódot, és kattintson a jobb gombbal a modell egy részére. A modell többi része eltűnik, és csak a kijelölt darab marad látható.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Távoli entitások GameObject-példányainak eltávolítása

Előfordulhat, hogy észrevette, hogy a kód megtartja az objektumok létrehozását, de soha nem törli őket. Ez az objektum-hierarchia panelen is látható. Amikor a szimuláció során kibontja a távoli objektum-hierarchiát, több és több objektum jelenik meg, amikor a modell új részére viszi a kurzort.

A jelenetekben sok objektum negatív hatással van a teljesítményre. Mindig törölje azokat az objektumokat, amelyek többé nem szükségesek.

Szúrja be az alábbi kódot a **RemoteRaycaster** -parancsfájlba, és távolítsa el az ismétlődő függvényeket:

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

A következő lépésként egy kiválasztott objektumot szeretnénk áthelyezni a körül. A **RemoteRaycaster** parancsfájlban szúrja be ezt a kódot, és távolítsa el az ismétlődő függvényt:

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
> Ha ezt a kódot futtatja, megfigyelheti, hogy semmi nem történik. Ennek az az oka, hogy az objektumok átalakításának módosítása a teljesítmény szempontjából nem szinkronizálja automatikusan az állapotot a-kiszolgálóra. Ehelyett manuálisan kell leküldeni ezt az állapotot a kiszolgálóra, vagy engedélyeznie kell a **SyncEveryFrame** a *RemoteEntitySyncObject* összetevőn.

Nyissa meg a **RemoteModelEntity** parancsfájlt, és adja hozzá a következő sort:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

A kód futtatásához kattintson a bal gombbal egy objektumra, és húzza körül.

## <a name="add-a-cut-plane"></a>Kivágási sík hozzáadása

Ebben az oktatóanyagban az utolsó funkciót szeretnénk kipróbálni, a [kivágott síkokat](../../overview/features/cut-planes.md)használunk. A kivágott sík elvágja a megjelenített objektumok részeit, így megtekintheti őket.

Hozzon létre egy új GameObject a jelenet **CutPlane**. Hozzon létre egy új parancsfájlt, és hívja meg a **RemoteCutPlane**. Adja hozzá az összetevőt az új GameObject.

Nyissa meg a parancsfájlt, és cserélje le a tartalmát a következő kódra:

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

Ha most futtatja a kódot, látnia kell, hogy a gép Hogyan vágja ki a modellt. Kiválaszthatja a *CutPlane* objektumot, és áthelyezheti és elforgathatja a *jelenet* ablakban. A kivágási sík ki-és bekapcsolható a kivágási sík objektum letiltásával.

## <a name="next-steps"></a>További lépések

Most már ismeri a legfontosabb funkciókat a távoli objektumokkal való interakcióhoz. A következő oktatóanyagban bemutatjuk, hogyan szabhatja testre a jelenet megjelenését.

> [!div class="nextstepaction"]
> [Oktatóanyag: a környezet és az anyagok módosítása](changing-environment-and-materials.md)
