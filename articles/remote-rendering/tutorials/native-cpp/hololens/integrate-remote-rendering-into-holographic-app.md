---
title: Távoli renderelés integrálása C++/DirectX11 holografikus alkalmazásba
description: Ismerteti, hogyan integrálható a távoli renderelés egy egyszerű C++/DirectX11 holografikus alkalmazásba a Visual Studio Project varázslóval.
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 9db32912e86079875ad382fb23e521c720fc7fbd
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776884"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Oktatóanyag: távoli renderelés integrálása HoloLens holografikus alkalmazásba

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio használata a HoloLens üzembe helyezhető holografikus alkalmazások létrehozásához
> * Adja hozzá a szükséges kódrészleteket és a projekt beállításait a helyi renderelés távoli renderelt tartalommal való összevonásához

Ez az oktatóanyag a szükséges bitek natív mintához való hozzáadását ismerteti `Holographic App` , hogy kombinálja a helyi renderelést az Azure távoli rendereléssel. Ebben az alkalmazásban az egyetlen, a Visual Studióban található hibakeresési kimeneti panelen keresztüli visszajelzés, ezért javasoljuk, hogy a mintát a Visual studión belül indítsa el. A megfelelő alkalmazásbeli visszajelzések hozzáadása a minta hatókörén kívül esik, mert a dinamikus szövegmezők a semmiből való létrehozása sok kódolást eredményez. Jó kiindulási pont az osztály `StatusDisplay` , amely része a [távelérési](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content)szolgáltatásnak a githubon. Valójában az oktatóanyag előre definiált verziója az adott osztály helyi példányát használja.

> [!TIP]
> Az [ARR-minták tárháza](https://github.com/Azure/azure-remote-rendering) tartalmazza az oktatóanyag eredményét, amely a használatra kész Visual Studio-projekt. Emellett a felhasználói felületi osztályon keresztül is megfelelő hiba-és állapotjelentés-jelentéskészítéssel bővült `StatusDisplay` . Az oktatóanyagban az összes ARR-kiegészítés a hatókörén belül van `#ifdef USE_REMOTE_RENDERING`  /  `#endif` , így könnyen azonosítható a távoli renderelési hozzáadások.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* A fiók adatai (fiókazonosító, fiók kulcsa, előfizetés azonosítója). Ha nem rendelkezik fiókkal, [hozzon létre egy fiókot](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* A Visual Studio 2019 [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)legújabb verziója.
* A Windows vegyes valóság alkalmazás-sablonok a Visual studióhoz [(letöltés)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Új holografikus alkalmazás mintájának létrehozása

Első lépésként létrehozunk egy törzsoldatot, amely a távoli renderelési integráció alapját képezi. Nyissa meg a Visual studiót, és válassza az "új projekt létrehozása" lehetőséget, és keressen rá a "holografikus DirectX 11 alkalmazás (univerzális Windows) (C++/WinRT)" kifejezésre.

![Új projekt létrehozása](media/new-project-wizard.png)

Adja meg a kívánt projekt nevét, válasszon egy elérési utat, és válassza a létrehozás gombot.
Az új projektben állítsa át a konfigurációt **"hibakeresés/ARM64"** értékre. Most már képesnek kell lennie a csatlakoztatott HoloLens 2 eszközre való fordítására és üzembe helyezésére. Ha a HoloLens-on futtatja, egy forgó adatkockát kell látnia az Ön előtt.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Távoli renderelési függőségek hozzáadása a NuGet-n keresztül

A távoli renderelési képességek hozzáadásának első lépése az ügyféloldali függőségek hozzáadása. A kapcsolódó függőségek NuGet-csomagként érhetők el.
A Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza az **"NuGet-csomagok kezelése..."** lehetőséget a helyi menüből.

A megkérdezett párbeszédpanelen keresse meg a **"Microsoft. Azure. RemoteRendering. cpp"** nevű NuGet-csomagot:

![NuGet-csomag tallózása](media/add-nuget.png)

majd adja hozzá a projekthez a csomag kiválasztásával, majd nyomja le a "telepítés" gombot.

A NuGet csomag hozzáadja a távoli renderelési függőségeket a projekthez. Ezek a következők:
* Hivatkozás az ügyféloldali függvénytárhoz (RemoteRenderingClient. lib).
* A. dll-függőségek beállítása.
* Állítsa be a beágyazási könyvtár helyes elérési útját.

## <a name="project-preparation"></a>Projekt előkészítése

Kis módosításokat kell végezni a meglévő projektben. Ezek a változások finomak, de a távoli renderelés nélkül nem fog működni.

### <a name="enable-multithread-protection-on-directx-device"></a>A többszálas-védelem engedélyezése a DirectX-eszközön
Az `DirectX11` eszközön engedélyezve kell lennie a többszálas-védelemnek. Ennek módosításához nyissa meg a DeviceResources. cpp fájlt a "Common" mappában, majd szúrja be a következő kódot a függvény végére `DeviceResources::CreateDeviceResources()` :

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Hálózati képességek engedélyezése az alkalmazás jegyzékfájljában
A hálózati képességeket explicit módon engedélyezni kell az üzembe helyezett alkalmazáshoz. A konfigurálás nélkül a kapcsolati lekérdezések végül időtúllépéseket eredményeznek. Az engedélyezéshez kattintson duplán a `package.appxmanifest` megoldás Explorerben található elemre. A következő felhasználói felületen lépjen a **képességek** lapra, és válassza a következőket:
* Internet (ügyfél & kiszolgáló)
* Internet (Ügyfél)

![Hálózati képességek](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Távoli renderelés integrálása

Most, hogy elkészült a projekt, kezdhetjük a kóddal. Az alkalmazás megfelelő belépési pontja az osztály `HolographicAppMain` (fájl HolographicAppMain. h/CPP), mert az összes szükséges hookot tartalmaz az inicializáláshoz, a inicializáláshoz és a megjelenítéshez.

### <a name="includes"></a>Beágyazások

Kezdjük a szükséges beágyazások hozzáadásával. Adja hozzá a következőt a HolographicAppMain. h fájlhoz:

```cpp
#include <AzureRemoteRendering.h>
```

... és ez a további `include` direktíva a HolographicAppMain. cpp fájlhoz:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

A kód egyszerűsége érdekében a következő névtér-parancsikont definiáljuk a HolographicAppMain. h fájl tetején a `include` direktíva után:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Ez a parancsikon hasznos, így nem kell mindenhol kiírni a teljes névteret, de az ARR-specifikus adatstruktúrákat továbbra is felismerheti. Természetesen a `using namespace...` direktívát is használhatja.

### <a name="remote-rendering-initialization"></a>Távoli renderelés inicializálása
 
Az alkalmazás élettartama alatt meg kell tartani néhány objektumot a munkamenethez stb. Az élettartam egybeesik az alkalmazás `HolographicAppMain` objektumának élettartamával, ezért az objektumokat tagokként adja hozzá az osztályhoz `HolographicAppMain` . A következő lépés a következő osztály tagjainak hozzáadása a HolographicAppMain. h fájlban:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

A tényleges megvalósítás jó kiindulópont az osztály konstruktora `HolographicAppMain` . Három inicializálási típust kell elvégeznie:
1. A távoli renderelési rendszer egyszeri inicializálása
1. Előtér-létrehozás
1. Munkamenet létrehozása

Mindezt egymás után a konstruktorban hajtjuk végre. A valós használati esetekben azonban célszerű lehet ezeket a lépéseket külön elvégezni.

Adja hozzá a következő kódot a konstruktor törzsének elejéhez a HolographicAppMain. cpp fájlban:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
A helyi függvényben `SetNewSession` regisztrálunk egy visszahívást, amely akkor aktiválódik, amikor a kapcsolat állapota a megadott munkamenet változásakor megtörténik. Átirányítjuk a hívást a saját függvényre `OnConnectionStatusChanged` . A következő bekezdésben deklaráljuk és Implementáljuk azt (és az állapot gépi kódját is). Azt is vegye figyelembe, hogy a hitelesítő adatok a mintában nehezen kódoltak, és ki kell tölteni a helyet ([fiókazonosító, fiók kulcsa](../../../how-tos/create-an-account.md#retrieve-the-account-information)és [tartomány](../../../reference/regions.md)).

A destruktor törzsének végén a deinicializálás szimmetrikusan és fordított sorrendben történik:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Állapot gép

A távoli renderelés során a fő függvények a munkamenetek létrehozásához és a modellek betöltéséhez aszinkron függvények. Ehhez egy egyszerű állapotú gépre van szükségünk, amely lényegében a következő állapotokon keresztül automatikusan átvált:

*Inicializálás – > munkamenet létrehozása – > modell betöltése (folyamatban)*

Ennek megfelelően a következő lépésként egy kis állapotú gépi kezelést adunk hozzá az osztályhoz. Deklaráljuk a saját enumerálását `AppConnectionStatus` azon különböző állapotokhoz, amelyeken az alkalmazásunk lehet. Hasonló a szolgáltatáshoz `RR::ConnectionStatus` , de a sikertelen kapcsolódás további állapottal rendelkezik.

Adja hozzá a következő tagokat és függvényeket az osztály deklarációhoz:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

A. cpp fájl implementáció oldalán adja hozzá a következő Function szerveiket:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
}
```

### <a name="per-frame-update"></a>/Keret frissítése

Csak egyszer kell bejelölnie az ügyfelet egy szimuláció után. Az osztály `HolographicApp1Main` jó hookot biztosít a frame-alapú frissítésekhez, ezért adja hozzá a következő kódot a függvény törzséhez `HolographicApp1Main::Update` :

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>Renderelés

Az utolsó teendő a távoli tartalom megjelenítésének meghívása. Ezt a hívást a renderelési folyamaton belül a megfelelő pozícióban kell megtenni, a renderelési cél törlése után. Szúrja be a következő kódrészletet a `UseHolographicCameraResources` Lock on Inside függvénybe `HolographicAppMain::Render` :

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Minta futtatása

A mintának olyan állapotban kell lennie, amely lefordítja és futtatja a mintát.

Ha a minta megfelelően fut, az azt jelzi, hogy közvetlenül az Ön előtt, a munkamenet-létrehozás és a modell betöltését követően pedig az aktuális Head pozícióban található motor modell jelenik meg. A munkamenet-létrehozás és a modell betöltése akár néhány percet is igénybe vehet. A jelenlegi állapot csak a Visual Studio kimeneti paneljére íródik. Ezért javasoljuk, hogy a mintát a Visual studión belül indítsa el.

> [!CAUTION]
> Az ügyfél leválasztja a kiszolgálót, ha a Tick függvény nem hívása néhány másodpercig. Így a töréspontok kiváltása nagyon egyszerűvé teheti az alkalmazás leválasztását.

A megfelelő állapot megjelenítéséhez egy szöveges panelen tekintse meg az oktatóanyag előre definiált verzióját a GitHubon.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogy milyen lépéseket kell tennie ahhoz, hogy távoli renderelést lehessen felvenni egy Stock **holografikus alkalmazás** C++/DirectX11-mintába.
Saját modell átalakításához tekintse meg a következő rövid útmutatót:

> [!div class="nextstepaction"]
> [Gyors útmutató: modell átalakítása renderelésre](../../../quickstarts/convert-model.md)
