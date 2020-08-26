---
title: Égbolt tükröződése
description: Ismerteti, hogyan lehet környezeti térképeket beállítani a Sky-tükrözésekhez
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 6c040c909225deb92594853ad18814a6e8e94b57
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892830"
---
# <a name="sky-reflections"></a>Égbolt tükröződése

Az Azure Remote rendering szolgáltatásban egy Sky-textúrát használunk az objektumok reális megvilágítására. A kibővített valóságot használó alkalmazások esetében ennek a textúrának hasonlónak kell lennie a valós környezetéhez, hogy az objektumok meggyőzőek legyenek. Ez a cikk az égbolt textúrájának módosítását ismerteti.

> [!NOTE]
> Az égbolt textúráját *környezeti térképnek*is nevezzük. Ezek a kifejezések szinonimaként használatosak.

## <a name="object-lighting"></a>Objektum megvilágítása

Az Azure távoli renderelése *fizikailag alapú renderelést* (pbr) alkalmaz a reális megvilágítás-számításokhoz. Bár a megfelelő Felhőbeli textúra használatával hozzáadhat [fényforrásokat](lights.md) a jelenethez, a legnagyobb hatással van.

Az alábbi képek a különböző felületek világításának eredményeit jelenítik meg, csak a Sky textúrával:

| Érdesség  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Nem fém  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Fém      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

A világítástechnikai modellel kapcsolatos további információkért tekintse meg az [anyagok](../../concepts/materials.md) című fejezetet.

> [!IMPORTANT]
> Az Azure Remote rendering csak a világítási modellekhez használja az égbolt textúráját. Nem háttérként jeleníti meg az eget, mivel a kibővített valóságot használó alkalmazások már rendelkeznek megfelelő háttérrel – a valós világban.

## <a name="changing-the-sky-texture"></a>Az égbolt textúrájának módosítása

A környezeti Térkép módosításához mindössze annyit kell tennie, hogy [betölti a textúrát](../../concepts/textures.md) , és megváltoztatja a munkamenetet `SkyReflectionSettings` :

```cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!");
            }
        });
}

```

Vegye figyelembe, hogy a `LoadTextureFromSASAsync` rendszer a fenti változatot használja, mert a beépített textúra be van töltve. Ha [csatolt blob-tárolóból](../../how-tos/create-an-account.md#link-storage-accounts)tölt be betöltést, használja a `LoadTextureAsync` Variant elemet.

## <a name="sky-texture-types"></a>Égbolt típusú textúrák

A *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* és a *2D textúrákat* is használhatja környezeti térképként.

Az összes textúrának [támogatott textúra formátumúnak](../../concepts/textures.md#supported-texture-formats)kell lennie. Nem kell megadnia a Mipmap a Sky-textúrákhoz.

### <a name="cube-environment-maps"></a>Adatkocka-környezet térképe

Hivatkozásként itt látható egy csomagolatlan cubemap:

![Csomagolatlan cubemap](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` A with paranccsal `TextureType.CubeMap` betöltheti a cubemap-textúrákat.

### <a name="sphere-environment-maps"></a>Gömb-környezeti térképek

A 2D-mintázatok környezeti térképként való használatakor a képnek [gömb-koordináta-térben](https://en.wikipedia.org/wiki/Spherical_coordinate_system)kell lennie.

![Egy Sky-rendszerkép a gömb koordinátáiban](media/spheremap-example.png)

`AzureSession.Actions.LoadTextureAsync`A with paranccsal `TextureType.Texture2D` betöltheti a gömb-környezeti térképeket.

## <a name="built-in-environment-maps"></a>Beépített környezeti térképek

Az Azure Remote rendering számos beépített környezeti leképezést biztosít, amelyek mindig elérhetők. Az összes beépített környezeti Térkép a cubemaps.

|Azonosító                         | Leírás                                              | Ábrán                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Különféle szalagos fények, világos beltéri alapszintű megvilágítás    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Világos beltéri fény beállítása, több ablakos fények      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Változóan színes fények Közepes fényű beltéri környezetben  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Közepesen fényes környezeti csarnok                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Halvány beltéri beállítás világos sötét kontraszttal              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Nappali környezeti helyiség világos, világos ablakos fény     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Sötét éjszakai égbolt és számos környező fénnyel   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Napfény és árnyék kontrasztja                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Tiszta égbolt            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Mérsékelten változó nap és árnyalat                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Esti naplemente a félhomályban                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Világos, buja-zöld és fehér fény tónusok, halvány terep | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Nappali és világos környezeti alapfény                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | Ugyanaz, mint a TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>További lépések

* [Fények](../../overview/features/lights.md)
* [Anyagok](../../concepts/materials.md)
* [Textúrák](../../concepts/textures.md)
* [A TexConv parancssori eszköz](../../resources/tools/tex-conv.md)
