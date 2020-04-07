---
title: A környezet és az anyagok megváltoztatása
description: Bemutató, amely bemutatja, hogyan lehet módosítani az ég térkép és az objektum anyagok Unity jelenetet.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679617"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Oktatóanyag: A környezet és az anyagok megváltoztatása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A jelenet környezeti térképének módosítása.
> * Anyagparaméterek módosítása.
> * Töltsön be egyéni textúrákat.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy ismeri [az oktatóanyagot: Távoli entitásokkal való munka az Unity-ben](working-with-remote-entities.md). Azonban csak olyan Unity projektre van szüksége, amellyel csatlakozhat a munkamenetekhez, és betölthet egy modellt, amint az az [Oktatóanyag: Unity projekt beállítása a semmiből](project-setup.md).

> [!TIP]
> Az [ARR mintatár](https://github.com/Azure/azure-remote-rendering) tartalmazza előkészített Unity projektek minden oktató a *Unity* mappában, hogy használhatja a referenciaként.

## <a name="change-the-environment-map"></a>A környezeti térkép módosítása

Az Azure Remote Rendering támogatja a [sky dobozok](../../overview/features/sky.md) (más néven "környezeti térképek") használatát a környezeti megvilágítás szimulálására. Ez különösen akkor hasznos, ha az objektumok *[fizikai alapú renderelést](../../overview/features/pbr-materials.md)* használnak, ahogy a mintamodellek teszik. ARR is jön-val a különböző beépített ég textúrák, hogy fogjuk használni ezt a bemutató.

Hozzon létre egy új script nevű **RemoteSky** és add hozzá egy új GameObject. Nyissa meg a parancsfájlt, és cserélje le a következő kódra:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Ne feledje, hogy a `LoadTextureFromSASAsync` változat a fenti, mert a beépített textúrák betöltése. [Csatolt blobtárolókból](../../how-tos/create-an-account.md#link-storage-accounts)való betöltés `LoadTextureAsync` esetén használja a változatot. Például, hogy ez hogyan működik a modellek megtalálható [modell betöltési szakaszban](../../concepts/models.md#loading-models).

Amikor futtatja a kódot, és átkapcsolja az égi térképeket, drasztikusan eltérő megvilágítást fog észlelni a modellen. A háttér azonban fekete marad, és nem láthatja a tényleges égbolt textúráját. Ez szándékos, mivel a háttér megjelenítése zavaró lenne egy kiterjesztett valóság eszközzel. Egy megfelelő alkalmazás, akkor használja ég textúrák, amelyek hasonlóak a valós környezetben, mivel ez segít, hogy tárgyak jelennek meg valóságosabb.

## <a name="modify-materials"></a>Anyagok módosítása

Az előző oktatóanyagban az [állapot felülbírálását](../../overview/features/override-hierarchical-state.md) használtuk az összetevőket a kijelölt objektumok színszínének módosításához. Most hasonló hatást szeretnénk elérni, de ezt egy tárgy [anyagának](../../concepts/materials.md) módosításával tesszük.

Először is szükségünk van egy script felvenni tárgyakat, mint mi [a második bemutató](working-with-remote-entities.md). Ha még nem rendelkezik **RemoteRaycaster** parancsfájllal, hozza létre most. A tartalom lecserélése a következő kódra:

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
}
```

Adja hozzá az összetevőt a *RemoteRendering* játékobjektumhoz. Feladata az egér alatti objektumok kitárolása és *a RemoteModelEntity* összetevők hozzáadása a kitárolt objektumokhoz. Ez az összetevő osztály, ahol végre a tényleges anyagváltozás funkciót.

Ha még nem rendelkezik **RemoteModelEntity** parancsfájllal, hozza létre, és cserélje le annak tartalmát erre a kódra:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Amikor futtatja ezt a kódot, az egérrel lebegő objektumok kilesznek emelve. A hatás hasonló ahhoz, amit a bemutató 2, de ahogy ez megvalósult más. Itt megkapjuk a kiválasztott objektumanyagainak listáját, majd módosítjuk az elsőt, hogy más albedo színű legyen.

> [!IMPORTANT]
> Kérjük, vegye figyelembe, hogy ez a módszer kiemeli a modell helyes részeit, amodell szerzői módjától függ. Tökéletesen fog működni, ha minden tárgy pontosan egy anyagot használ. Ha azonban a modell nem rendelkezik 1:1 arányban az alkatrészek és az anyagok között, a fenti naiv kód nem fogja helyesen cselekedni.

## <a name="use-a-different-texture"></a>Másik textúra használata

[A textúrák](../../concepts/textures.md) általában egy forrásmodell részei. A [modell konvertálása](../../quickstarts/convert-model.md)során az összes textúrát a program a szükséges futásidejű formátumba konvertálja, és a végleges modellfájlba csomagolja. A textúra futásidőben történő cseréjéhez [mentse Azt DDS fájlformátumban,](https://en.wikipedia.org/wiki/DirectDraw_Surface) és töltse fel az Azure blob storage-ba. Tekintse meg [ezt a rövid útmutatót](../../quickstarts/convert-model.md) az Azure blobtároló létrehozásához. Miután rendelkezik egy blob tárolóval, megnyithatja az Azure Storage Explorerben, és feltöltheti a fájlt húzással.

A futásidejű oldalon két különböző módon címezhet meg egy textúraeszközt a blobstorage-ban:

* A textúra kezelése a SAS URI-val. Ezért kattintson a jobb gombbal a feltöltött fájlra, és válassza a **"Közös hozzáférésű aláírás beírása...**" parancsot a helyi menüből. Használja ezt a SAS URI-t a `LoadTextureFromSASAsync` függvényváltozattal (lásd az alábbi mintakódot).
* Közvetlenül a textúra blobstorage-paraméterek szerint címezz, ha a [blobtár a fiókhoz van csatolva.](../../how-tos/create-an-account.md#link-storage-accounts) Ebben az esetben a `LoadTextureAsync`megfelelő terhelési funkció .

Most nyissa meg a **RemoteModelEntity** parancsfájlt, adja hozzá a következő kódot, és távolítsa el az ismétlődő függvényeket:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Futtassa ezt a kódot, és vigye az egérmutatót a modell fölé. Ha a modell megfelelő UV koordinátákkal rendelkezik, látnia kell a textúra megjelenését. Ellenkező esetben csak színváltozást észlelhet.

## <a name="next-steps"></a>További lépések

Ezzel lezárult az Azure Remote Rendering with Unity használatával kapcsolatos bevezető sorozatunk. Következő lépésként ismerkedjen meg az ARR néhány alapvető fogalomával, például [a munkamenetekkel,](../../concepts/sessions.md) [entitásokkal](../../concepts/entities.md)és [modellekkel,](../../concepts/models.md) hogy mélyebb megértést alakítson ki. Vannak is különböző funkciók, mint [a fények,](../../overview/features/lights.md) [vázlat renderelés,](../../overview/features/outlines.md) [hierarchikus állapot felülírja,](../../overview/features/override-hierarchical-state.md)és [anyagok,](../../concepts/materials.md) hogy meg kell vizsgálni részletesebben.

> [!div class="nextstepaction"]
> [Unity játék tárgyak és alkatrészek](../../how-tos/unity/objects-components.md)
