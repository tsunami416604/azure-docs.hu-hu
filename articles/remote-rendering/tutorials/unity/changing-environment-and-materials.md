---
title: A környezet és az anyagok módosítása
description: Ez az oktatóanyag bemutatja, hogyan módosíthatja az égbolt térképeit és az objektumokat egy Unity-jelenetben.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679617"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Oktatóanyag: a környezet és az anyagok módosítása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Egy jelenet környezeti térképének módosítása
> * Anyag paramétereinek módosítása
> * Egyéni textúrák betöltése.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag azt feltételezi, hogy már ismeri a következő [oktatóanyagot: távoli entitások használata az egységben](working-with-remote-entities.md). Azonban csak olyan Unity-projektre van szükség, amellyel kapcsolódhat munkamenetekhez, és betöltheti a modelleket, ahogy az [oktatóanyag: Unity-projekt létrehozása a semmiből](project-setup.md)című szakaszban is látható.

> [!TIP]
> Az [ARR-minták tárháza](https://github.com/Azure/azure-remote-rendering) előkészített Unity-projekteket tartalmaz az *Unity* mappában található összes oktatóanyaghoz, amelyet hivatkozásként használhat.

## <a name="change-the-environment-map"></a>A környezeti Térkép módosítása

Az Azure Remote rendering a környezeti megvilágítás szimulálása érdekében támogatja a [Sky Box](../../overview/features/sky.md) (más néven "környezeti térképek") használatát. Ez különösen akkor hasznos, ha az objektumok *[fizikailag alapú renderelést](../../overview/features/pbr-materials.md)* használnak, mint a minta modelljeink. Az ARR számos beépített Sky-textúrákat is tartalmaz, amelyeket ebben az oktatóanyagban fogunk használni.

Hozzon létre egy új, **RemoteSky** nevű szkriptet, és vegye fel egy új GameObject. Nyissa meg a parancsfájlt, és cserélje le a következő kódra:

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

Vegye figyelembe, `LoadTextureFromSASAsync` hogy a rendszer a fenti változatot használja, mert a beépített textúrák betöltődik. Ha [csatolt blob-tárolóból](../../how-tos/create-an-account.md#link-storage-accounts)tölt be betöltést, `LoadTextureAsync` használja a Variant elemet. Példa erre a modellekre a [modell betöltése szakaszban](../../concepts/models.md#loading-models)található.

Amikor futtatja a kódot, és átvált a Sky Maps-be, a modellen a drasztikusan eltérő megvilágítás jelenik meg. A háttér azonban feketén marad, és nem látja a tényleges égbolt textúráját. Ez szándékos, mivel a háttér megjelenítésével egy kibővített valóságot használó eszköz is zavaró lehet. A megfelelő alkalmazásokban a valós környezetéhez hasonló Sky-textúrákat kell használnia, mivel ez segít az objektumok valós megjelenésében.

## <a name="modify-materials"></a>Anyagok módosítása

Az előző oktatóanyagban az állapot- [felülbírálási összetevőket](../../overview/features/override-hierarchical-state.md) használtuk a kiválasztott objektumok árnyalatának módosításához. Most pedig egy hasonló hatást szeretnénk elérni, de ezt egy objektum [anyagának](../../concepts/materials.md) módosításával szeretnénk végrehajtani.

Először is szükség van egy szkriptre az objektumok kiválasztásához, ahogy [a második oktatóanyagban](working-with-remote-entities.md)is. Ha még nem rendelkezik **RemoteRaycaster** -parancsfájllal, hozza létre most. Cserélje le a tartalmát a következő kódra:

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

Adja hozzá az összetevőt a *RemoteRendering* játék objektumához. Az objektumok kiválogatása és az *RemoteModelEntity* -összetevők hozzáadása a kiválasztott objektumokhoz felelős. Ennek az összetevő-osztálynak a segítségével Implementáljuk az anyagok tényleges módosítási funkcióját.

Ha még nem rendelkezik **RemoteModelEntity** -parancsfájllal, hozza létre, és cserélje le a tartalmát a következő kódra:

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

A kód futtatásakor az egérrel Kiemelt objektumok láthatók. A hatás hasonló ahhoz, amit a 2. oktatóanyagban tettünk, de a megvalósítás módja eltér. Itt bemutatjuk az anyagok listáját a kiválasztott objektumon, majd az elsőt úgy kell módosítani, hogy más albedó-szín legyen.

> [!IMPORTANT]
> Vegye figyelembe, hogy ez a módszer a modell helyes részeit emeli ki, attól függ, hogy a modell hogyan lett létrehozva. Tökéletesen működni fog, ha minden objektum pontosan egy anyagot használ. Ha azonban a modell nem rendelkezik 1:1-kapcsolattal a részek és az anyagok között, a fenti naiv kód nem lesz a megfelelő dolog.

## <a name="use-a-different-texture"></a>Más textúra használata

A [textúrák](../../concepts/textures.md) általában egy forrásoldali modell részét képezik. A [modell konvertálása](../../quickstarts/convert-model.md)során a rendszer minden textúrát átalakít a szükséges futásidejű formátumba, és becsomagolja a végső modellbe. Ha a textúrát futásidőben szeretné cserélni, mentse a [fájlt a DDS fájlformátumba](https://en.wikipedia.org/wiki/DirectDraw_Surface) , és töltse fel az Azure Blob Storage-ba. Az Azure Blob-tárolók létrehozásával kapcsolatban tekintse meg [ezt a rövid útmutatót](../../quickstarts/convert-model.md) . Ha rendelkezik blob-tárolóval, megnyithatja Azure Storage Explorerban, és feltölthet fájlokat a fogd és vidd használatával.

A futásidejű oldalon két különböző módon kezelheti a blob Storage-ban lévő textúra-eszközöket:

* A textúra a SAS URI-ja alapján van megcímezve. Ehhez kattintson a jobb gombbal a feltöltött fájlra, majd válassza a helyi menü "**közös hozzáférési aláírás beolvasása..**." elemét. Használja ezt az SAS URI- `LoadTextureFromSASAsync` t a függvény variánsával (lásd az alábbi mintakód-t).
* A textúrát a blob Storage-paraméterek közvetlen használatával oldja meg, ha a [blob Storage a fiókhoz van társítva](../../how-tos/create-an-account.md#link-storage-accounts). Ebben az esetben a megfelelő betöltési függvény van `LoadTextureAsync`.

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

Futtassa ezt a kódot, és vigye a kurzort a modell fölé. Ha a modellnek megfelelő UV-koordinátái vannak, megjelenik a textúrája. Ellenkező esetben előfordulhat, hogy csak a színváltozást észleli.

## <a name="next-steps"></a>További lépések

Ez a bevezető sorozatot arra következteti, hogyan használható az Azure Remote rendering az Unity használatával. A következő lépésként Ismerkedjen meg az ARR-vel kapcsolatos alapvető fogalmakkal, például a [munkamenetek](../../concepts/sessions.md), az [entitások](../../concepts/entities.md)és a [modellek](../../concepts/models.md) mélyebb megismeréséhez. Többek között olyan funkciók is vannak, mint a [fények](../../overview/features/lights.md), a [Tagolás](../../overview/features/outlines.md), a [hierarchikus állapotú felülbírálások](../../overview/features/override-hierarchical-state.md)és a részletesebben feltárt [anyagok](../../concepts/materials.md) .

> [!div class="nextstepaction"]
> [Unity game Objects és Components](../../how-tos/unity/objects-components.md)
