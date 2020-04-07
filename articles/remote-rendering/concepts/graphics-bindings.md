---
title: Grafikus kötés
description: Grafikus kötések és használati esetek beállítása
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681882"
---
# <a name="graphics-binding"></a>Grafikus kötés

Ahhoz, hogy az Azure Remote Rendering egy egyéni alkalmazásban, azt integrálni kell az alkalmazás renderelési folyamatába. Ez az integráció a grafikus kötés felelőssége.

A beállítás után a grafikus kötés hozzáférést biztosít a renderelt képet befolyásoló különböző funkciókhoz. Ezek a függvények két kategóriába sorolhatók: általános funkciók, amelyek mindig rendelkezésre `Microsoft.Azure.RemoteRendering.GraphicsApiType`állnak , és olyan speciális funkciók, amelyek csak a kiválasztott számára relevánsak .

## <a name="graphics-binding-in-unity"></a>Grafikus kötés unity

A Unity-ben az egész kötést a `RemoteUnityClientInit` struct kezeli. `RemoteManagerUnity.InitializeManager` A grafikus mód beállításához `GraphicsApiType` a mezőt a kiválasztott kötésre kell állítani. A mező automatikusan kitöltődik attól függően, hogy van-e xreszköz. A viselkedés manuálisan felülbírálható a következő viselkedésekkel:

* **HoloLens 2**: A [Windows Mixed Reality](#windows-mixed-reality) grafikus kötésmindig használatos.
* **Lapos UWP asztali alkalmazás**: [A szimulációt](#simulation) mindig használják. A mód használatához kövesse az [Oktatóanyag: Unity projekt beállítása](../tutorials/unity/project-setup.md)teljesen új lépéseket.
* **Unity szerkesztő**: [A szimulációt](#simulation) mindig használják, kivéve, ha wmr VR headset van csatlakoztatva, amely esetben az ARR le lesz tiltva, hogy lehetővé tegye az alkalmazás nem ARR-hez kapcsolódó részeinek hibakeresését. Lásd még: [holografikus remoting](../how-tos/unity/holographic-remoting.md).

Az egyetlen másik fontos része a Unity elérése az [alapvető kötelező,](#access)az összes többi szakasz alatt lehet kihagyni.

## <a name="graphics-binding-setup-in-custom-applications"></a>Grafikus kötés beállítása egyéni alkalmazásokban

Grafikus kötés kijelöléséhez tegye a következő két lépést: Először a program inicializálásakor statikusan inicializálva kell a grafikus kötést:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

A fenti hívás szükséges az Azure Távoli renderelés inicializálásához a holografikus API-kba. Ezt a függvényt meg kell hívni, mielőtt bármilyen holografikus API-t meghívna, és mielőtt bármilyen más távoli renderelési API-hoz hozzáférne. Hasonlóképpen a megfelelő init-mentesítési függvényt `RemoteManagerStatic.ShutdownRemoteRendering();` is meg kell hívni, ha már nem hívnak holografikus API-kat.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Grafikus kötés elérése

Az ügyfél beállítása után az alapvető grafikus kötés a `AzureSession.GraphicsBinding` getter segítségével érhető el. Az utolsó képkocka-statisztika például a következőképpen olvasható:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafikus API-k

Jelenleg két grafikus API választható ki, `WmrD3D11` és `SimD3D11`. Egy harmadik `Headless` létezik, de még nem támogatott az ügyféloldalon.

### <a name="windows-mixed-reality"></a>Windows vegyes valóság

`GraphicsApiType.WmrD3D11`a HoloLens 2 alapértelmezett kötése. Ez hozza `GraphicsBindingWmrD3d11` létre a kötést. Ebben a módban az Azure Remote Renderings közvetlenül a holografikus API-kba horgak.

A származtatott grafikus kötések `GraphicsBinding` eléréséhez az alapot le kell adni.
A WMR-kötés használatához két dolgot kell tenni:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>A használt koordináta-rendszer távoli renderelésének tájékoztatása

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Ahol a `ptr` fenti nek egy `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` natív objektumra mutató mutatónak kell lennie, amely meghatározza azt a világtér koordináta-rendszert, amelyben az API koordinátái vannak kifejezve.

#### <a name="render-remote-image"></a>Távoli kép renderelése

Minden egyes képkocka elején a távoli keretet a hátsó pufferbe kell megjeleníteni. Ez a hívással `BlitRemoteFrame`történik, amely szín- és mélységadatokat is kitölt az aktuálisan kötött renderelési célba. Ezért fontos, hogy ez a hátsó puffer renderelési célként való kötése után történik.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Szimuláció

`GraphicsApiType.SimD3D11`a szimulációkötés, és ha `GraphicsBindingSimD3d11` be van jelölve, akkor létrehozza a grafikus kötést. Ez a felület a fejmozgás szimulálására szolgál, például egy asztali alkalmazásban, és egy monoszkopikus képet jelenít meg.
A beállítás egy kicsit több részt, és a következőképpen működik:

#### <a name="create-proxy-render-target"></a>Proxyleképezési cél létrehozása

Távoli és helyi tartalom kell tenni, hogy egy offscreen szín / mélység teszi cél `GraphicsBindingSimD3d11.Update` úgynevezett "proxy" segítségével a proxy kamera által szolgáltatott adatok a funkciót. A proxynak meg kell egyeznie a hátsó puffer felbontásával. Ha a munkamenet `GraphicsBindingSimD3d11.InitSimulation` készen áll, meg kell hívni, mielőtt csatlakozna hozzá:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Az init függvényt a natív d3d-eszközre, valamint a proxy rendercél színére és mélységi textúrájára mutatóval kell ellátni. Az `AzureSession.ConnectToRuntime` inicializálás után, és `DisconnectFromRuntime` többször is meghívható, de amikor másik munkamenetre vált, először a régi munkameneten kell meghívni, `GraphicsBindingSimD3d11.DeinitSimulation` mielőtt `GraphicsBindingSimD3d11.InitSimulation` egy másik munkamenetre hívható volna.

#### <a name="render-loop-update"></a>Renderelési hurok frissítése

A renderelési hurok frissítése több lépésből áll:

1. Minden képkockát, mielőtt `GraphicsBindingSimD3d11.Update` bármilyen renderelés történik, az aktuális kameraátalakítással hívják meg, amelyet a rendszer a kiszolgálónak küld renderelt. Ugyanakkor a visszaadott proxy-transzformációt kell alkalmazni a proxykamerára, hogy a proxyrender-tárolóba kerüljön.
Ha a visszaadott proxyfrissítés `SimulationUpdate.frameId` null értékű, akkor még nincsenek távoli adatok. Ebben az esetben a proxyrenderelési célba való renderelés helyett minden helyi tartalmat közvetlenül a hátsó pufferbe kell megjeleníteni az aktuális kameraadatok felhasználásával, és a következő két lépést kihagyja.
1. Az alkalmazásnak most meg kell `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`kötnie a proxy renderelési célját, és meg kell hívnia. Ez kitölti a távoli színt és mélységi adatokat a proxy renderelési célba. A proxykamera-átalakítás sal most már bármely helyi tartalom megjelenhet a proxyra.
1. Ezután a hátsó puffert renderelési `GraphicsBindingSimD3d11.ReprojectProxy` célként kell kötni, és meg kell hívni, amikor is a hátsó puffer megjelenik.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Unity-projekt beállítása a semmiből](../tutorials/unity/project-setup.md)
