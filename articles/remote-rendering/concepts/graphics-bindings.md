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
ms.custom: devx-track-csharp
ms.openlocfilehash: 853c71ed4803f717188568ec051c40c4f73afe95
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202871"
---
# <a name="graphics-binding"></a>Grafikus kötés

Ahhoz, hogy egy egyéni alkalmazásban használni tudja az Azure távoli renderelést, integrálni kell az alkalmazás renderelési folyamatával. Ez az integráció a grafikus kötés felelőssége.

A beállítás után a grafikus kötés hozzáférést biztosít a renderelt képet érintő különböző funkciókhoz. Ezek a függvények két kategóriába sorolhatók: az általános függvények, amelyek mindig elérhetők, és csak a kijelölt funkciókhoz szükségesek `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Grafikus kötés az egységben

Az egységben a teljes kötést az `RemoteUnityClientInit` átadott struct kezeli `RemoteManagerUnity.InitializeManager` . A grafikus mód beállításához a `GraphicsApiType` mezőt a kiválasztott kötésre kell beállítani. A mező automatikusan ki lesz töltve attól függően, hogy van-e XRDevice. A viselkedés manuálisan felülbírálható a következő viselkedésekkel:

* **2. HoloLens**: a [Windows vegyes valóság](#windows-mixed-reality) grafikájának kötése mindig használatban van.
* **Flat UWP asztali alkalmazás**: a [Szimuláció](#simulation) mindig használatban van.
* **Unity Editor**: a [Szimuláció](#simulation) mindig használatban van, kivéve, ha egy WMR VR-fülhallgató csatlakozik, amely esetében az ARR le lesz tiltva, hogy az alkalmazás nem az ARR-vel kapcsolatos részeit lehessen elhárítani. Lásd még: [holografikus távelérés](../how-tos/unity/holographic-remoting.md).

Az egység egyetlen további fontos része az [alapszintű kötés](#access)elérése, az alábbi összes további szakasz kihagyható.

## <a name="graphics-binding-setup-in-custom-applications"></a>Grafikus kötés beállítása egyéni alkalmazásokban

A grafikus kötések kiválasztásához hajtsa végre a következő két lépést: először a grafikus kötést statikusan kell inicializálni a program inicializálásakor:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

A fenti hívás az Azure távoli renderelés inicializálásához szükséges a holografikus API-khoz. Ezt a függvényt a holografikus API-k meghívása előtt meg kell hívni, mielőtt más távoli renderelési API-kat is elérhet. Hasonlóképpen a megfelelő deinit függvényt is meghívni kell, miután már nem történt meg a `RemoteManagerStatic.ShutdownRemoteRendering();` holografikus API-k hívása.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Grafikus kötés elérése

Miután beállította az ügyfelet, az alapszintű grafikus kötés a `AzureSession.GraphicsBinding` kiolvasóval érhető el. Például az utolsó keret statisztikája a következőképpen kérhető le:

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafikus API-k

Jelenleg két grafikus API lehet kijelölni, `WmrD3D11` és `SimD3D11` . Egy harmadik `Headless` létezik, de az ügyféloldali oldalon még nem támogatott.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` az alapértelmezett kötés, amely a 2. HoloLens fut. Létrehozza majd a `GraphicsBindingWmrD3d11` kötést. Ebben az üzemmódban az Azure-alapú távoli renderelés közvetlenül a holografikus API-khoz csatlakozik.

A származtatott grafikus kötések eléréséhez az alapot el `GraphicsBinding` kell érni.
Az WMR-kötés használatához két dolgot kell elvégezni:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>A használt koordináta-rendszer távoli renderelésének tájékoztatása

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Ahol a fentieknek olyan `ptr` natív objektumra mutató mutatónak kell lenniük `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` , amely meghatározza, hogy az API-ban lévő koordináták hol vannak kifejezve.

#### <a name="render-remote-image"></a>Távoli rendszerkép megjelenítése

Az egyes keretek elején a távoli keretet a hátsó pufferbe kell megjeleníteni. Ezt a meghívásával végezheti el `BlitRemoteFrame` , amely mindkét szem színét és részletes információit kitölti a jelenleg kötött megjelenítési célra. Ezért fontos, hogy ezt követően a teljes hátsó puffert leképezési célként kell kötni.

> [!WARNING]
> Miután a távoli rendszerkép be lett blit a backbuffer, a helyi tartalmat egy egyszeri továbbítású sztereó renderelési technikával kell megjeleníteni, például **SV_RenderTargetArrayIndex**használatával. Más sztereó renderelési technikák (például az egyes szemeknek külön pass-alapú renderelés) használatával jelentős teljesítmény-romlást vagy grafikus összetevőket eredményezhet, és el kell kerülni.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>A szimuláció

`GraphicsApiType.SimD3D11` a szimulált kötés, és ha be van jelölve, a a `GraphicsBindingSimD3d11` grafikus kötést hozza létre. Ez az interfész szimulálja a fej mozgását, például egy asztali alkalmazásban, és megjelenít egy monoscopic képet.

A szimulációs kötés megvalósításához fontos megérteni a helyi kamera és a távoli keret közötti különbséget a [kamera](../overview/features/camera.md) oldalon leírtak szerint.

Két kamera szükséges:

* **Helyi kamera**: Ez a kamera az aktuális kamera pozícióját jelöli, amelyet az alkalmazás logikája vezérel.
* **Proxy kamera**: Ez a kamera megegyezik a kiszolgáló által elindított aktuális *távoli kerettel* . Mivel az ügyfél a keretet kérő és a hozzá tartozó megérkezési időt késlelteti, a *távoli keret* mindig egy kicsit a helyi kamera mozgása mögött.

Az alapszintű megközelítés az, hogy a távoli rendszerkép és a helyi tartalom is egy off-screen célra jelenik meg a proxy kamera használatával. A rendszer ezután újratervezi a proxy képét a helyi kamera területére, ami további magyarázatot mutat a [késői fázisok újravetítése](../overview/features/late-stage-reprojection.md)során.

A telepítő egy kicsit nagyobb szerepet játszik, és a következőképpen működik:

#### <a name="create-proxy-render-target"></a>Proxy megjelenítési cél létrehozása

A távoli és a helyi tartalmat egy "proxy" nevű bemásolási szín/mélység megjelenítési célra kell megjeleníteni a függvény által biztosított proxy-kamerás adat használatával `GraphicsBindingSimD3d11.Update` .

A proxynak meg kell egyeznie a hátsó puffer felbontásával, és *DXGI_FORMAT_R8G8B8A8_UNORM* vagy *DXGI_FORMAT_B8G8R8A8_UNORM* formátumban kell lennie. Ha a munkamenet elkészült, a `GraphicsBindingSimD3d11.InitSimulation` csatlakozás előtt meg kell hívni a következőhöz:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Az init függvényt a natív D3D-eszközhöz tartozó mutatókkal, valamint a proxy megjelenítési célpontjának szín-és mélységmérő mintázatával kell megadni. A inicializálása után többször is `AzureSession.ConnectToRuntime` `DisconnectFromRuntime` hívható, de ha egy másik munkamenetre vált, `GraphicsBindingSimD3d11.DeinitSimulation` először a régi munkamenetben kell meghívni, mielőtt `GraphicsBindingSimD3d11.InitSimulation` egy másik munkamenetben is meghívható.

#### <a name="render-loop-update"></a>Renderelési hurok frissítése

A renderelési hurok frissítése több lépésből áll:

1. A Megjelenítés előtt minden egyes keretet a rendszer az `GraphicsBindingSimD3d11.Update` aktuális kamera-átalakítással együtt továbbít a kiszolgálónak, amelyet a rendszer a rendereléshez továbbít. Ugyanakkor a visszaadott proxy-átalakítást a proxy kamerára kell alkalmazni, hogy a proxy Render cél legyen.
Ha a visszaadott proxy frissítése `SimulationUpdate.frameId` null értékű, a rendszer még nem rendelkezik távoli adattal. Ebben az esetben a proxy renderelési céljának való megjelenítés helyett a helyi tartalmat közvetlenül a jelenlegi kamera adataival kell megjeleníteni a hátsó pufferben, és a következő két lépés kimarad.
1. Az alkalmazásnak ekkor meg kell kötnie a proxy megjelenítési célját és a hívást `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Ezzel kitölti a távoli színeket és a mélységi információkat a proxy megjelenítési céljába. A helyi tartalom mostantól a proxy kamera-átalakító használatával is megjeleníthető a proxyn.
1. Ezt követően a hátsó puffert leképezési célként kell kötni, és `GraphicsBindingSimD3d11.ReprojectProxy` a rendszer meghívja a visszaadott puffert.

```cs
AzureSession currentSession = ...;
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

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="api-documentation"></a>API-dokumentáció

* [C# RemoteManagerStatic. StartupRemoteRendering ()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C# GraphicsBinding osztály](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GraphicsBindingWmrD3d11 osztály](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GraphicsBindingSimD3d11 osztály](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [C++ RemoteRenderingInitialization struct](/cpp/api/remote-rendering/remoterenderinginitialization)
* [C++ GraphicsBinding osztály](/cpp/api/remote-rendering/graphicsbinding)
* [C++ GraphicsBindingWmrD3d11 osztály](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C++ GraphicsBindingSimD3d11 osztály](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Következő lépések

* [Fényképezőgép](../overview/features/camera.md)
* [Újravetítés késői fázisban](../overview/features/late-stage-reprojection.md)
* [Oktatóanyag: távolról megjelenített modellek megtekintése](../tutorials/unity/view-remote-models/view-remote-models.md)