---
title: Az ég imátvisszatükröződései
description: Az égi tükröződések környezeti térképeinek beállítása
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680608"
---
# <a name="sky-reflections"></a>Az ég imátvisszatükröződései

Az Azure távoli renderelés, a sky textúra segítségével világítobjektumok reálisan. A kiterjesztett valóság alkalmazások, ez a textúra kell hasonlítania a valós környezetben, hogy tárgyak jelennek meg meggyőző. Ez a cikk bemutatja, hogyan módosíthatja az ég textúráját.

> [!NOTE]
> Az ég textúráját *környezettérképnek is nevezik.* Ezeket a kifejezéseket szinonimaként használjuk.

## <a name="object-lighting"></a>Objektum megvilágítása

Az Azure Remote Rendering fizikai *alapú renderelést* (PBR) alkalmaz a valósághű megvilágítási számításokhoz. Bár a jelenethez [fényforrásokat](lights.md) is hozzáadhat, a jó égi textúra használata a legnagyobb hatással van.

Az alábbi képek azt mutatják, hogy a különböző felületek megvilágításának eredményei csak égi textúrával jelennek meg:

| Érdesség  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Nem fém  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark (ZöldPointPark)](media/dielectric-0.25.png)  | ![GreenPointPark (ZöldPointPark)](media/dielectric-0.5.png)  | ![GreenPointPark (ZöldPointPark)](media/dielectric-0.75.png)  | ![GreenPointPark (ZöldPointPark)](media/dielectric-1.png)  |
| Fém      | ![GreenPointPark (ZöldPointPark)](media/metallic-0.png)  | ![GreenPointPark (ZöldPointPark)](media/metallic-0.25.png)    | ![GreenPointPark (ZöldPointPark)](media/metallic-0.5.png)    | ![GreenPointPark (ZöldPointPark)](media/metallic-0.75.png)    | ![GreenPointPark (ZöldPointPark)](media/metallic-1.png)    |

A világítási modellről további információt az [anyagokról](../../concepts/materials.md) szóló fejezetben talál.

> [!IMPORTANT]
> Az Azure Remote Rendering csak a megvilágítási modellek hez használja az ég idomát. Ez nem teszi az eget, mint a háttér, mivel a kiterjesztett valóság alkalmazások már a megfelelő háttér - a valós világban.

## <a name="changing-the-sky-texture"></a>Az ég textúrájának megváltoztatása

A környezeti térkép módosításához mindössze annyit kell tennie, hogy `SkyReflectionSettings` [betölt egy textúrát,](../../concepts/textures.md) és megváltoztatja a munkamenetet:

``` cs
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

Ne feledje, hogy a `LoadTextureFromSASAsync` változat a fenti, mert a beépített textúra betöltődik. [Csatolt blobtárolókból](../../how-tos/create-an-account.md#link-storage-accounts)való betöltés `LoadTextureAsync` esetén használja a változatot.

## <a name="sky-texture-types"></a>Ég textúra típusai

A *[kockatérképeket](https://en.wikipedia.org/wiki/Cube_mapping)* és a *2D-textúrákat* is használhatja környezeti térképként.

Minden textúrának [támogatott textúraformátumban](../../concepts/textures.md#supported-texture-formats)kell lennie. Nem kell mipmaps-et megadnia az ég textúráihoz.

### <a name="cube-environment-maps"></a>Kocka környezet térképek

Referenciaként, itt van egy csomagolatlan cubemap:

![Csomagolatlan kockatérkép](media/Cubemap-example.png)

A `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` `TextureType.CubeMap` kockatérkép-textúrák betöltéséhez használható.

### <a name="sphere-environment-maps"></a>Gömb környezeti térképei

Ha 2D textúrát használ környezeti térképként, a képnek [gömb alakú koordinátatérben](https://en.wikipedia.org/wiki/Spherical_coordinate_system)kell lennie.

![Az ég képe gömb alakú koordinátákkal](media/spheremap-example.png)

Gömbkörnyezet `TextureType.Texture2D` térképek betöltéséhez használható. `AzureSession.Actions.LoadTextureAsync`

## <a name="built-in-environment-maps"></a>Beépített környezeti térképek

Az Azure remote rendering néhány beépített környezeti leképezést biztosít, amelyek mindig elérhetők. Minden beépített környezeti térkép kockatérkép.

|Azonosító                         | Leírás                                              | Illusztráció                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Különböző csíkos fények, fényes beltéri alapvilágítás    | ![Autókereskedés](media/autoshop.png)
|builtin://BoilerRoom               | Erős beltéri fény beállítás, több ablakvilágítás      | ![Kazánterem](media/boiler-room.png)
|builtin://ColorfulStudio           | Változó színű fények közepesen könnyű beltéri környezetben  | ![SzínesStudio](media/colorful-studio.png)
|builtin://Hangar                   | Mérsékelten világos környezeti csarnok fény                     | ![SmallHangar között](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Homályos beltéri beállítás világos-sötét kontraszttal              | ![Iparipipeandvalve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Nappali környezeti szoba fény, világos ablak terület fény     | ![Lebombo között](media/lebombo.png)
|builtin://SataraNight              | Sötét éjszakai égbolt és föld sok környező fényekkel   | ![SataraNight között](media/satara-night.png)
|builtin://SunnyVondelpark          | Erős napfény és árnyékkontraszt                      | ![SunnyVondelpark között](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Tiszta égi fény mérsékelt földi világítással            | ![Syferfontein között](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Mérsékelten változó nap és árnyék                         | ![TearsOfSteelBridge között](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Esti naplemente fény közeledik szürkület                    | ![VelenceNaplemente](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Világos, buja-zöld és fehér fénytónusok, halvány talaj | ![WhippleCreekRegionálisPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Nappali erős környezeti földi fénnyel                 | ![WinterRiver között](media/winter-river.png)
|builtin://DefaultSky               | Ugyanaz, mint a TearsOfSteelBridge                               | ![DefaultSky (Alapértelmezettsky)](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>További lépések

* [Lámpa](../../overview/features/lights.md)
* [Nyersanyagok](../../concepts/materials.md)
* [Textúrák](../../concepts/textures.md)
* [A TexConv parancssori eszköz](../../resources/tools/tex-conv.md)
