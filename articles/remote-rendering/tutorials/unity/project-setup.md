---
title: Unity-projekt beállítása a nulláról
description: Bemutatja, hogyan konfigurálhat egy üres Unity-projektet az Azure távoli rendereléshez.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679596"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Oktatóanyag: Unity-projekt beállítása a semmiből

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Konfigurálása egy karcolás Unity projekt ARR.
> * Renderelési munkamenetek létrehozása és leállítása.
> * Meglévő munkamenetek újrafelhasználása.
> * Csatlakozás és leválasztás a munkamenetekről.
> * Modellek betöltése renderelési munkamenetbe.
> * A kapcsolati statisztikák megjelenítése.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a bemutató van szüksége:

* A fiókadatok (fiókazonosító, fiókkulcs, előfizetés-azonosító). Ha nem rendelkezik fiókkal, [hozzon létre egy fiókot.](../../how-tos/create-an-account.md)
* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A Visual Studio 2019 legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(letöltés)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(letöltés)](https://unity3d.com/get-unity/download)
  * Telepítse ezeket a modulokat unity:
    * **UWP** – Univerzális Windows-platformbuild-támogatás
    * **IL2CPP** – Windows buildtámogatás (IL2CPP)

> [!TIP]
> Az [ARR mintatár](https://github.com/Azure/azure-remote-rendering) tartalmazza előkészített Unity projektek minden oktató. Ezeket a projekteket referenciaként használhatja.

## <a name="create-a-new-unity-project"></a>Új Unity-projekt létrehozása

Az Unity Hubból hozzon létre egy új projektet.
Ebben a példában azt feltételezzük, hogy a `RemoteRendering`projekt egy .

![új projektablak](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>A projekt jegyzékfájljának konfigurálása

Módosítania kell a `Packages/manifest.json` Unity projekt mappájában található fájlt. Nyissa meg a fájlt egy szövegszerkesztőben, és fűzz hozzá az alább felsorolt sorokhoz:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

Az Univerzális renderelési folyamatcsomag nem kötelező, de teljesítménybeli okokból ajánlott.
Miután módosította és mentette a jegyzéket, az Egység automatikusan frissül. Ellenőrizze, hogy a csomagok be lettek-e töltve a *Project* ablakban:

![csomagimportálás megerősítése](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>A csomag legújabb verziójának biztosítása

A következő lépések biztosítják, hogy a projekt a távoli renderelési csomag legújabb verzióját használja.
1. Válassza ki a csomagot a Project ablakban, és kattintson a csomag ikonra: ![A csomag ikonjának kiválasztása](media/package-icons.png)
1. Az ellenőrben kattintson a "Megtekintés ![a csomagkezelőben" lehetőségre: csomagfelügyelő](media/package-properties.png)
1. A távoli renderelési csomag csomagjának csomagkezelőlapján megtekintheti, hogy a frissítésgomb elérhető-e. Ha ez így van, akkor kattintson rá frissíti ![a csomagot a legújabb elérhető verzióra: Az ARR csomag a csomagkezelőben](media/package-manager.png)
1. Előfordulhat, hogy a csomag frissítése hibákhoz vezethet a konzolon. Ebben az esetben próbálja meg bezárni és újra megnyitni a projektet.

## <a name="configure-the-camera"></a>A kamera konfigurálása

Válassza ki a **Fő kamera** csomópontot.

1. Az *átalakítás*visszaállítása:

    ![kamera átalakítása visszaállítása](media/camera-reset-transform.png)

1. Jelzők **törlése** *egyszínűre*

1. **Háttér** beállítása *fekete színre*

1. Állítsa a **vágósíkokat** *Közel = 0,3* és *Far = 20 értékre.* Ez azt jelenti, hogy a renderelés 30 cm-nél közelebb vagy 20 méternél közelebbi geometriát vág le.

    ![Unity kamera tulajdonságai](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>A projekt beállításainak módosítása

1. A *Projektbeállítások szerkesztése > megnyitása...*
1. A bal oldali listában válassza a Minőség lehetőséget.
1. Az **alapértelmezett minőségi szint** módosítása *alacsonyra*

    ![projektminőségi beállítások módosítása](media/settings-quality.png)

1. A bal oldalon válassza a **Grafika** lehetőséget.
1. Módosítsa a **parancsfájlvégrehajtható renderelési folyamat** beállítását *HybridRenderingPipeline -ra.* Hagyja ki ezt a lépést, ha az univerzális renderelési folyamat nincs használatban.

    ![a projekt grafikus](media/settings-graphics-lwrp.png) beállításainak módosítása Előfordulhat, hogy a felhasználói felület nem tartalmazza a csomagokból rendelkezésre álló folyamattípusok listáját, amely ![esetben a *HybridRenderingPipeline* eszközt manuálisan kell a mezőre húzni: a projekt grafikus beállításainak módosítása](media/hybrid-rendering-pipeline.png)
1. Válassza a bal oldali **Lejátszó** lehetőséget.
1. A **Windows Platform univerzális beállításai** lap kiválasztása
1. Az **XR-beállítások** módosítása a ![Windows Mixed Reality támogatásához: lejátszóbeállítások](media/xr-player-settings.png)
1. Válassza ki a beállításokat, mint a fenti képernyőképen:
    1. **Virtuális valóság engedélyezése támogatott**
    1. **Mélységformázás** beállítása *16 bites mélységre*
    1. **Mélységpuffer-megosztás** engedélyezése
    1. **A sztereó renderelési mód** beállítása *egyfázisú példányra*

1. Ugyanebben az ablakban az *XR-beállítások*felett bontsa ki a **Közzétételi beállítások csomópontot**
1. Görgessen le a **Képességek elemre,** és válassza a következőket:
    * **InternetClient**
    * **InternetClientServer**
    * **TérbeliÉszlelés**
    * Fejlesztésre nem kötelező: **PrivateNetworkClientServer**

      Erre a beállításra akkor van szükség, ha a Unity távoli hibakeresőt az eszközhöz szeretné csatlakoztatni.

1. A **Támogatott eszközcsaládok**ban engedélyezze **a holografikus** és **az asztali**

1. Ha a Vegyes valóság eszközkészletet szeretné használni, az ajánlott beállításokkal és képességekkel kapcsolatos további információkért tekintse meg az [MRTK dokumentációját.](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)

## <a name="validate-project-setup"></a>Projektbeállítás ellenőrzése

Hajtsa végre az alábbi lépéseket a projektbeállítások helyessére.

1. Válassza a ValidateProject bejegyzést az Egységszerkesztő eszköztár Távleképezés menüjéből.
1. A ValidateProject ablakban szükség esetén megszeretné keresni és kijavíthatja a projektbeállításokat.

    ![Unity szerkesztő projektérvényesítése](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Parancsfájl létrehozása az Azure távoli renderelésin

Hozzon létre egy [új parancsfájlt,](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) és adja meg a name **RemoteRendering**. Nyissa meg a parancsfájlt, és cserélje le a teljes tartalmát az alábbi kódra:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Ez a parancsfájl inicializálja az Azure távoli renderelést, megmondja, hogy melyik kameraobjektumot használja a rendereléshez, és a *Lejátszási mód* aktiválásakor a **Munkamenet létrehozása** gombot helyezi a nézetablakba.

> [!CAUTION]
> A szkript módosítása és mentése, miközben a lejátszási mód aktív unity-ben, az Unity befagyasztását eredményezheti, és a feladatkezelőn keresztül le kell állítania. Ezért a RemoteRendering parancsfájl szerkesztése előtt mindig állítsa le a *lejátszási* módot.

## <a name="test-azure-remote-rendering-session-creation"></a>Az Azure távoli renderelési munkamenetlétrehozásának tesztelése

Hozzon létre egy új GameObject a jelenetet, és adja hozzá a *RemoteRendering* összetevő hozzá. Töltse ki a távoli leképezési fiók megfelelő *fióktartományát*, *fiókazonosítóját*és *fiókkulcsát:*

![Távoli renderelési összetevő tulajdonságai](media/remote-rendering-component.png)

Indítsa el az alkalmazást a szerkesztőben **(nyomja le a Lejátszás** vagy a CTRL+P billentyűkombinációt). A nézetablakban megjelenik a **Munkamenet létrehozása** gomb. Kattintson rá az első ARR-munkamenet elindításához:

![Első munkamenet létrehozása](media/test-create.png)

Ha ez nem sikerül, ellenőrizze, hogy helyesen adta-e meg a fiókadatait a RemoteRendering összetevő tulajdonságaiközött. Ellenkező esetben egy üzenet jelenik meg a konzolablakban, amely az Önhöz rendelt munkamenet-azonosítót mutatja, és arról, hogy a munkamenet jelenleg *kezdő* állapotban van:

![Munkamenet kezdő kimenete](media/create-session-output.png)

Ezen a ponton az Azure kiépít egy kiszolgálót az Ön számára, és egy távoli renderelési virtuális gép indítása. Ez általában **3-5 percet vesz igénybe.** Amikor a virtuális gép készen áll, `OnSessionStatusChanged` a Unity parancsfájl visszahívása végrehajtásra kerül, és kinyomtatja az új munkamenet állapotát:

![Munkamenetre kész kimenet](media/create-session-output-2.png)

Ez az, ez az! Egyelőre semmi több nem fog történni. A díjak elkerülése érdekében mindig le kell állítania a munkameneteket, amikor már nincs rájuk szükség. Ebben a példában ezt megteheti a **Munkamenet leállítása** gombra kattintva vagy az Egység szimuláció leállításával. Az *ARRServiceUnity* összetevő **Automatikus leállítása** tulajdonsága miatt, amely alapértelmezés szerint be van kapcsolva, a munkamenet automatikusan leáll. Ha minden sikertelen, összeomlás vagy csatlakozási problémák miatt a munkamenet a *MaxLeaseTime-on* keresztül futhat, mielőtt a kiszolgáló leállítana.

> [!NOTE]
> A munkamenet leállítása azonnali hatállyal érvénybe lép, és nem lehet visszavonni. Miután leállította, létre kell hoznia egy új munkamenetet, ugyanazzal az indítási terheléssel.

## <a name="reusing-sessions"></a>Munkamenetek újrafelhasználása

Az új munkamenet létrehozása sajnos időigényes művelet. Ezért meg kell próbálnia, hogy hozzon létre ülés ritkán, és újra őket, amikor csak lehetséges.

Szúrja be a következő kódot a *RemoteRendering* parancsfájlba, és távolítsa el az ismétlődő függvények régi verzióit:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> A kód futtatása előtt győződjön meg arról, hogy inaktiválja a RemoteRendering összetevő **automatikus leállítási munkamenet** beállítását. Ellenkező esetben a szimuláció leállításakor minden létrehozott munkamenet automatikusan leáll, és az újbóli felhasználás sikertelen lesz.

A *Lejátszás*gomb megnyomásakor három gomb kerül a nézetablakba: **Munkamenet létrehozása**, Lekérdezés **aktív munkamenetek**és **Meglévő munkamenet használata**. Az első gomb mindig új munkamenetet hoz létre. A második gomb lekérdezi, hogy melyik *aktív* munkamenet létezik. Ha nem adott meg manuálisan egy használni kívánt munkamenet-azonosítót, akkor ez a művelet automatikusan kiválasztja a munkamenet-azonosítót későbbi használatra. A harmadik gomb megpróbál csatlakozni egy meglévő munkamenethez. Vagy egy olyan, amelyet manuálisan adott meg a *Munkamenet-azonosító* összetevő tulajdonságon keresztül, vagy egy, amelyet a *Query Active Sessions*talált.

Az **AutoStartSessionAsync** függvény a szerkesztőn kívüli gomblenyomások szimulálására szolgál.

> [!TIP]
> Meg lehet nyitni azokat a munkameneteket, amelyek le vannak állítva, lejártak vagy hibaállapotban vannak. Bár már nem használhatók renderelésre, az inaktív munkamenet megnyitása után lekérdezheti azok adatait. A fenti kód ellenőrzi a `ARRService_OnSessionStarted`munkamenet állapotát a alkalmazásban, hogy automatikusan leálljon, ha a munkamenet használhatatlanná válik.

Ezzel a funkcióval most már létrehozhat és újrafelhasználhatja a munkameneteket, amelyek jelentősen javíthatják a fejlesztési munkafolyamatot.

A munkamenet létrehozása általában az ügyfélalkalmazáson kívül történik, mivel a kiszolgáló felpörgetéséhez szükséges idő szükséges.

## <a name="connect-to-an-active-session"></a>Csatlakozás aktív munkamenethez

Eddig hoztunk létre, vagy nyitott ülés. A következő lépés a *munkamenethez való csatlakozás.* Miután csatlakozott, a renderelés szerver képeket készít, és küldjön egy video stream a mi alkalmazás.

Szúrja be a következő kódot a *RemoteRendering* parancsfájlba, és távolítsa el az ismétlődő függvények régi verzióit:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

A funkció tesztelése:

1. Nyomja meg a **Lejátszás** egységben gombot.
1. Munkamenet megnyitása:
    1. Ha már van munkamenete, nyomja le az **Aktív munkamenetek lekérdezése,** majd **a Meglévő munkamenet használata gombot.**
    1. Ellenkező esetben nyomja **le a Munkamenet létrehozása gombot.**
1. Nyomja **meg a Connect gombot.**
1. Néhány másodperc múlva a konzol kimenetének ki kell nyomtatnia, hogy csatlakoztatva van.
1. Egyelőre semmi másnak nem szabad történnie.
1. Nyomja **meg a Kapcsolat bontását,** vagy állítsa le a Unity lejátszási módját.

>[!NOTE]
> Több felhasználó is *megnyithat* egy munkamenetet az adatok lekérdezéséhez, de egyszerre csak egy felhasználó kapcsolódhat egy *munkamenethez.* Ha egy másik felhasználó már csatlakoztatva van, a kapcsolat **kézfogási hibával**sikertelen lesz.

## <a name="load-a-model"></a>Modell betöltése

Szúrja be a következő kódot a *RemoteRendering* parancsfájlba, és távolítsa el az ismétlődő függvények régi verzióit:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Amikor megnyomja a lejátszásgombot, megnyit egy munkamenetet, és csatlakozik hozzá, megjelenik a **Modell betöltése** gomb. Kattintás után, a konzol kimenete megmutatja a betöltési folyamatot, és amikor eléri a 100%, látnia kell a motor modelljét:

![A szerkesztőbe betöltött modell](media/model-loaded-replace-me.png)

A [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) egy fontos eleme használt [hologram stabilitás](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). Azonban csak akkor lesz hatással, ha egy vegyes valóság eszközre van telepítve.

> [!TIP]
> Ha követte a [rövid útmutató: Konvertálja a modell tetszés,](../../quickstarts/convert-model.md)már tudja, hogyan kell konvertálni a saját modelleket. Mindössze annyit kell tennie, hogy most, hogy az URI egy átalakított modell a *modell neve* tulajdonság.

## <a name="display-frame-statistics"></a>Keretstatisztika megjelenítése

Az Azure Remote Rendering nyomon követi a kapcsolat minőségére vonatkozó különböző információkat. Az információk megjelenítésének gyors módjához tegye a következőket:

Hozzon létre egy [új szkriptet,](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) és adja meg a nevét **RemoteFrameStats**. Nyissa meg a parancsfájlt, és cserélje le a teljes tartalmát az alábbi kódra:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Hozzon létre egy GameObject és a nevét *FrameStats*. Csatolja gyermekcsomópontként a *Fő kamera* objektumhoz, és állítsa a pozícióját **x = 0, y = 0, z = 0,325**értékre. Adja hozzá a **RemoteFrameStats** összetevőt az objektumhoz.

Vegyen fel egy **> vászon gyermekobjektumot** a *FrameStats* objektumhoz, és állítsa be annak tulajdonságait a következőkkel:

![vászon tulajdonságai](media/framestats-canvas.png)

Adjon hozzá egy **> szövegobjektumot** a vászon gyermeként, és állítsa be a tulajdonságait a következőképpen:

![szöveg tulajdonságai](media/framestats-text.png)

Jelölje ki a *FrameStats* objektumot, és a **Körstatisztika mezőt** a kör ikonra kattintva és a **Szöveg** objektum kiválasztásával:

![szövegtulajdonság beállítása](media/framestats-set-text.png)

Most, amikor csatlakozik a távoli munkamenethez, a szövegnek meg kell jelennie a streamelési statisztikáknak:

![keret statisztika kimenet](media/framestats-output.png)

A kód letiltja a statisztika frissítés kívül a szerkesztő, mint egy fej-zárt szövegdoboz lenne zavaró. A [rövid útmutató](../../quickstarts/render-model.md) projektben kifinomultabb implementáció található.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta az üres Unity-projekt végrehajtásához szükséges összes lépést, és az Azure Remote Rendering használatával való munkát. A következő oktatóanyagban közelebbről megvizsgáljuk, hogyan működjön együtt a távoli entitásokkal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Távoli entitások együttműködése unityben](working-with-remote-entities.md)
