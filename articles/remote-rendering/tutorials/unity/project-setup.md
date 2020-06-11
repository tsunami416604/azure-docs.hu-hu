---
title: Unity-projekt létrehozása az alapoktól
description: A cikk azt ismerteti, hogyan lehet üres Unity-projektet beállítani az Azure távoli rendereléssel való használatra.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: c05daa998829c4ac0687f75ae5678695127a50b0
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659913"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Oktatóanyag: Unity-projekt létrehozása a semmiből

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Az ARR-hez készült kaparós Unity-projekt konfigurálása.
> * Renderelési munkamenetek létrehozása és leállítása.
> * Meglévő munkamenetek újrafelhasználása.
> * Csatlakozás és kapcsolat bontása a munkamenetek között.
> * Modellek betöltése egy renderelési munkamenetbe.
> * A kapcsolatok statisztikáinak megjelenítése.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* A fiók adatai (fiókazonosító, fiók kulcsa, előfizetés azonosítója). Ha nem rendelkezik fiókkal, [hozzon létre egy fiókot](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A Visual Studio 2019 [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)legújabb verziója. 
* [Visual Studio-eszközök vegyes valósághoz](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). A következő számítási *feladatok* telepítése kötelező:
  * **Asztali fejlesztés C++ nyelven**
  * **Univerzális Windows-platform (UWP) fejlesztése**
* GIT [(letöltés)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(letöltés)](https://unity3d.com/get-unity/download)
  * Telepítse ezeket a modulokat az Unity-ben:
    * **UWP** – univerzális Windows-platform-Build támogatása
    * **IL2CPP** – Windows Build-támogatás (IL2CPP)

> [!TIP]
> Az [ARR-minták tárháza](https://github.com/Azure/azure-remote-rendering) előkészített Unity-projekteket tartalmaz az összes oktatóanyaghoz. Ezeket a projekteket referenciáként használhatja.

## <a name="create-a-new-unity-project"></a>Új Unity-projekt létrehozása

Hozzon létre egy új projektet az Unity hub-ból.
Ebben a példában feltételezzük, hogy a projekt létrehozása egy nevű mappában történik `RemoteRendering` .

![új projekt ablak](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>A projekt jegyzékfájljának konfigurálása

Módosítania kell a `Packages/manifest.json` Unity Project mappában található fájlt. Nyissa meg a fájlt egy szövegszerkesztőben, és fűzze hozzá az alább felsorolt sorokat:

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

Az univerzális renderelési folyamat csomagja nem kötelező, de ajánlott teljesítménnyel kapcsolatos okokból.
Miután módosította és mentette a jegyzékfájlt, az Unity automatikusan frissülni fog. Erősítse meg a csomagok betöltését a *projekt* ablakban:

![csomagok importálásának megerősítése](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Győződjön meg arról, hogy a csomag legújabb verziója van

A következő lépésekkel biztosíthatja, hogy a projekt a távoli renderelési csomag legújabb verzióját használja.
1. Válassza ki a csomagot a projekt ablakban, és kattintson az :::no-loc text="package"::: ikonra: ![ a csomag ikon kiválasztása](media/package-icons.png)
1. Az Ellenőrben kattintson a "megtekintés a csomagkezelő alkalmazásban": ![ csomagkezelő](media/package-properties.png)
1. A távoli renderelési csomag csomagkezelő lapján ellenőrizze, hogy elérhető-e a frissítés gomb. Ha igen, akkor a gombra kattintva a rendszer frissíti a csomagot a legújabb elérhető verzióra: ![ az ARR csomag a csomagkezelő](media/package-manager.png)
1. Időnként előfordulhat, hogy a csomag frissítése hibákhoz vezethet a konzolon. Ha ez történik, próbálkozzon a projekt bezárásával és újbóli megnyitásával.

## <a name="configure-the-camera"></a>A kamera konfigurálása

Válassza ki a **fő kamera** csomópontot.

1. Az *átalakítás*visszaállítása:

    ![kamera átalakításának alaphelyzetbe állítása](media/camera-reset-transform.png)

1. Beállítás **:::no-loc text="Clear flags":::** értéke*:::no-loc text="Solid Color":::*

1. Beállítás **:::no-loc text="Background":::** értéke*:::no-loc text="Black":::*

1. Állítsa a a következőre: **:::no-loc text="Clipping Planes":::** *közel = 0,3* és *Far = 20*. Ez azt jelenti, hogy a renderelés a 30 cm-nél közelebbi vagy 20 méternél nagyobb méretű klipet ábrázol.

    ![Unity kamera tulajdonságai](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>A projekt beállításainak módosítása

1. Nyissa meg a *> projekt beállításainak szerkesztése...*
1. A bal oldali listában válassza a minőség lehetőséget.
1. Módosítsa az **aktív minőségi szintet** *alacsonyra* a kiválasztásával
1. Az **alapértelmezett minőségi szint** *alacsonyra* váltása

    ![a projekt minőségi beállításainak módosítása](media/settings-quality.png)

1. Válassza ki a bal oldali **grafikát** .
1. Módosítsa a **szkriptek renderelési folyamatának** beállítását *HybridRenderingPipeline*értékre. Hagyja ki ezt a lépést, ha nincs használatban az univerzális leképezési folyamat.

    ![a Project Graphics beállításainak módosítása ](media/settings-graphics-lwrp.png) időnként a felhasználói felület nem tölti fel az elérhető adatcsatorna-típusok listáját a csomagokból, ebben az esetben a *HybridRenderingPipeline* eszközt kézzel kell húzni a mezőre: a ![ Project Graphics beállításainak módosítása](media/hybrid-rendering-pipeline.png)
1. Válassza ki a bal oldali **lejátszót** .
1. Válassza a **univerzális Windows-platform beállítások** lapot.
1. Az **XR beállításainak** módosítása a Windows vegyes valóságának támogatásához: ![ lejátszó beállításai](media/xr-player-settings.png)
1. Válassza a fenti képernyőképen látható beállításokat:
    1. **Virtuális valóság támogatásának** engedélyezése
    1. **Mélységi formátum** beállítása *16 bites mélységre*
    1. A **mélységi puffer megosztásának** engedélyezése
    1. **Sztereó renderelési mód** beállítása *egyszeri pass-példányra*

1. Ugyanebben az ablakban, az *XR beállításai*felett bontsa ki a **közzétételi beállítások** elemet.
1. Görgessen le a **képességek** menüponthoz, és válassza a következőket:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Nem kötelező a fejlesztéshez: **PrivateNetworkClientServer**

      Erre a beállításra akkor van szükség, ha az Unity távoli hibakeresőt az eszközhöz szeretné kapcsolni.

1. A **támogatott eszközökhöz tartozó családokban**engedélyezze a **holografikus** és **asztali**

1. Ha a vegyes valóság eszközkészletet szeretné használni, tekintse meg a [MRTK dokumentációját](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview), ahol további információkat talál az ajánlott beállításokról és képességekről.

## <a name="validate-project-setup"></a>A projekt beállításának ellenőrzése

A következő lépések végrehajtásával ellenőrizheti, hogy helyesek-e a projekt beállításai.

1. Válassza ki a ValidateProject bejegyzést a RemoteRendering menüből az Unity Editor eszköztáron.
1. A ValidateProject ablakban keresse meg és javítsa ki a projekt beállításait, ahol szükséges.

    ![Unity Editor projekt ellenőrzése](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Parancsfájl létrehozása az Azure távoli renderelés inicializálásához

Hozzon létre egy [új parancsfájlt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) , és adja meg a **RemoteRendering**nevet. Nyissa meg a parancsfájlt, és cserélje le a teljes tartalmat az alábbi kódra:

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

Ez a szkript inicializálja az Azure távoli renderelést, mondja el, hogy melyik kamera-objektum használható a rendereléshez, és hogyan helyezheti el a **munkamenet létrehozása** gombot a nézetablakban, ha a *lejátszási mód* aktiválva van.

> [!CAUTION]
> Ha módosítja a szkriptet, és menti azt, miközben a lejátszási mód aktív az egységben, az Unity befagyasztást eredményezhet, és a Feladatkezelő segítségével le kell állítania azt. Ezért a *RemoteRendering* -szkript szerkesztése előtt mindig állítsa le a lejátszási módot.

## <a name="test-azure-remote-rendering-session-creation"></a>Azure távoli renderelési munkamenet létrehozásának tesztelése

Hozzon létre egy új GameObject a jelenetben, és adja hozzá a *RemoteRendering* összetevőt. Adja meg a távoli megjelenítési fiókhoz tartozó *fiók tartományát*, a *fiók azonosítóját*és a *fiók kulcsát* :

![Távoli megjelenítési összetevő tulajdonságai](media/remote-rendering-component.png)

Indítsa el az alkalmazást a szerkesztőben (**nyomja le a Play** vagy a CTRL + P billentyűkombinációt). A nézőpontban megjelenik a **munkamenet létrehozása** gomb. Kattintson rá az első ARR-munkamenet elindításához:

![Első munkamenet létrehozása](media/test-create.png)

Ha ez nem sikerül, ellenőrizze, hogy helyesen adta-e meg a fiók adatait a RemoteRendering összetevő tulajdonságaiban. Ellenkező esetben egy üzenet jelenik meg a konzol ablakban, amely az Önhöz rendelt munkamenet-azonosítót mutatja, és azt jelzi, hogy a munkamenet jelenleg *kezdő* állapotban van:

![Munkamenet kezdő kimenete](media/create-session-output.png)

Ezen a ponton az Azure kiépít egy kiszolgálót, és elindít egy távoli renderelési virtuális gépet. Ez általában **3 – 5 percet vesz igénybe**. Ha a virtuális gép elkészült, a rendszer `OnSessionStatusChanged` végrehajtja az Unity-szkript visszahívását, és kinyomtatja az új munkamenet állapotát:

![Munkamenetre kész kimenet](media/create-session-output-2.png)

Ez az! Az idő alatt semmi más nem fog történni. A díjak elkerülése érdekében mindig le kell állítania a munkameneteket, ha már nincs rá szükség. Ebben a példában megteheti ezt a **munkamenet leállítása** gombra kattintva vagy az egység szimulációjának leállításával. Mivel a *ARRServiceUnity* összetevő automatikusan **leállítja a munkamenet** tulajdonságot, amely alapértelmezés szerint be van kapcsolva, a munkamenet automatikusan leáll. Ha minden sikertelen, összeomlások vagy kapcsolódási problémák miatt előfordulhat, hogy a munkamenet addig fut, amíg a *MaxLeaseTime* le nem állítja a kiszolgálót.

> [!NOTE]
> A munkamenet leállítása azonnal érvénybe lép, és nem vonható vissza. Ha leállt, létre kell hoznia egy új munkamenetet ugyanazzal az indítási terheléssel.

## <a name="reusing-sessions"></a>Munkamenetek újrahasználata

Egy új munkamenet létrehozása sajnos egy időigényes művelet. Ezért az egyiknek ritkán kell létrehoznia a munkameneteket, és amikor csak lehetséges, újra fel kell használni őket.

Szúrja be a következő kódot a *RemoteRendering* -parancsfájlba, és távolítsa el az ismétlődő függvények régi verzióit:

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
> A kód futtatása előtt győződjön meg arról, hogy inaktiválja a **munkamenet automatikus leállítása** lehetőséget a RemoteRendering összetevőben. Ellenkező esetben a létrehozott munkamenetek automatikusan leállnak a szimuláció leállításakor, és az újbóli próbálkozás sikertelen lesz.

Ha megnyomja a *Lejátszás*gombot, a nézetablakban három gomb is megjelenik: **munkamenet létrehozása**, **aktív munkamenetek lekérdezése**és **meglévő munkamenet használata**. Az első gomb mindig létrehoz egy új munkamenetet. A második gomb azt kérdezi le, hogy mely *aktív* munkamenetek léteznek. Ha nem adta meg manuálisan a használni kívánt munkamenet-azonosítót, akkor ez a művelet automatikusan kijelöli a munkamenet-azonosítót későbbi használatra. A harmadik gomb megpróbál csatlakozni egy meglévő munkamenethez. Vagy egy, a *munkamenet-azonosító* összetevő tulajdonságán keresztül manuálisan megadott módon, vagy egy, az *aktív munkamenetek lekérdezése*által talált.

A **AutoStartSessionAsync** függvénnyel szimulálhatja a gomb megnyomását a szerkesztőn kívül.

> [!TIP]
> A leállított, lejárt vagy hibás állapotú munkamenetek megnyithatók. Amíg nem használhatók a rendereléshez, lekérdezheti az adatokat, miután megnyitotta az inaktív munkamenetet. A fenti kód ellenőrzi a munkamenet állapotát a alkalmazásban `ARRService_OnSessionStarted` , hogy automatikusan leálljon, ha a munkamenet használhatatlanná válik.

Ezzel a funkcióval mostantól olyan munkameneteket hozhat létre és használhat újra, amelyek jelentősen javítják a fejlesztési munkafolyamatot.

A munkamenet létrehozása általában a-kiszolgáló üzembe helyezéséhez szükséges idő miatt aktiválódik az ügyfélalkalmazáson kívül.

## <a name="connect-to-an-active-session"></a>Kapcsolódás aktív munkamenethez

Eddig létrehozott vagy megnyitott munkameneteket. A következő lépés egy munkamenethez való *Kapcsolódás* . A csatlakozás után a renderelési kiszolgáló képeket hoz létre, és videó streamet küld az alkalmazásnak.

Szúrja be a következő kódot a *RemoteRendering* -parancsfájlba, és távolítsa el az ismétlődő függvények régi verzióit:

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

1. Nyomja meg az egységben a **Lejátszás** gombot.
1. Munkamenet megnyitása:
    1. Ha már rendelkezik munkamenettel, nyomja le az aktív munkamenetek **lekérése** , majd a **meglévő munkamenet használata**.
    1. Ellenkező esetben nyomja meg a **munkamenet létrehozása**gombot.
1. Kattintson a **kapcsolat**gombra.
1. Néhány másodperc elteltével a konzol kimenetének ki kell nyomtatnia a kapcsolatot.
1. Egyelőre semmi mást sem kell történnie.
1. Kattintson a **kapcsolat bontása** vagy az egység lejátszási módjának leállítása gombra.

>[!NOTE]
> Több felhasználó is *megnyithat* egy munkamenetet az adatai lekérdezéséhez, de egyszerre csak egy felhasználó *kapcsolódhat* egy munkamenethez. Ha egy másik felhasználó már csatlakoztatva van, a kapcsolat **kézfogási hiba miatt**sikertelen lesz.

## <a name="load-a-model"></a>Modell betöltése

Szúrja be a következő kódot a *RemoteRendering* -parancsfájlba, és távolítsa el az ismétlődő függvények régi verzióit:

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

Ha most megnyomja a lejátszás gombot, nyisson meg egy munkamenetet, és kapcsolódjon hozzá, a **modell betöltése** gomb jelenik meg. Miután rákattintott, a konzol kimenete megjeleníti a betöltési folyamatot, és amikor eléri a 100%-ot, megjelenik egy motor modellje:

![Modell betöltve a szerkesztőben](media/model-loaded-replace-me.png)

A [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) a [hologramos stabilitáshoz](https://docs.microsoft.com/windows/mixed-reality/hologram-stability)használt fontos összetevő. Azonban ez csak akkor lép érvénybe, ha vegyes valóságú eszközön telepítik.

> [!TIP]
> Ha követte a rövid útmutató [: modell átalakítása renderelésre](../../quickstarts/convert-model.md), már tudja, hogyan alakíthatja át a saját modelljeit. A megjelenítéséhez mindössze annyit kell tennie, hogy az URI-t egy konvertált modellbe helyezi a *modell neve* tulajdonságba.

## <a name="display-frame-statistics"></a>Megjelenítési keret statisztikája

Az Azure-alapú távoli renderelés a kapcsolatok minőségével kapcsolatos különféle információkat követi nyomon. Az információk gyors megjelenítéséhez tegye a következőket:

Hozzon létre egy [új parancsfájlt](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) , és adja meg a **RemoteFrameStats**nevet. Nyissa meg a parancsfájlt, és cserélje le a teljes tartalmat az alábbi kódra:

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

Hozzon létre egy GameObject, és nevezze el *FrameStats*. Csatolja gyermek csomópontként a *fő kamera* objektumhoz, és állítsa be a pozícióját **x = 0, y = 0, z = 0,325**értékre. Adja hozzá az **RemoteFrameStats** összetevőt az objektumhoz.

Adjon hozzá egy **felhasználói felületet > vászon** gyermekobjektum a *FrameStats* objektumhoz, és állítsa be a tulajdonságait a következőhöz hasonló módon:

![vászon tulajdonságai](media/framestats-canvas.png)

Vegyen fel egy **felhasználói felületet > Text** objektumot a vászon gyermekéhez, és állítsa be a tulajdonságait a következőhöz hasonló módon:

![szöveg tulajdonságai](media/framestats-text.png)

Válassza ki a *FrameStats* objektumot, és töltse ki a **FrameStats mezőt** a kör ikonra kattintva, majd a **text (szöveg** ) objektum kiválasztásával:

![Text (szöveg) tulajdonság beállítása](media/framestats-set-text.png)

A távoli munkamenethez való csatlakozáskor a szövegnek a streaming statisztikáit kell megjelenítenie:

![keret statisztikájának kimenete](media/framestats-output.png)

A kód a szerkesztőn kívül letiltja a statisztikák frissítését, mivel a fej nélküli szövegmező nem vonható le. Kifinomultabb implementáció található a gyors üzembe helyezési [projektben](../../quickstarts/render-model.md) .

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte az üres Unity-projekt elvégzéséhez szükséges összes lépést, és az Azure Remote rendering használatával működik együtt. A következő oktatóanyagban alaposabban szemügyre vesszük, hogyan használhatók a távoli entitások.

> [!div class="nextstepaction"]
> [Oktatóanyag: távoli entitások használata az egységben](working-with-remote-entities.md)
