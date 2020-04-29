---
title: Azure frontend API-k hitelesítéshez
description: A cikk azt ismerteti, hogyan használható a C# felületi API a hitelesítéshez
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681349"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Hitelesítés az Azure előtérbeli API-k használatával

Ebben a szakaszban bemutatjuk, hogyan használható a C# API a hitelesítéshez.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

A AzureFrontendAccountInfo egy ```AzureFrontend``` példány hitelesítési adatainak beállítására szolgál az SDK-ban.

A fontos mezők a következők:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

A tartomány _régió_ részén használja az [Önhöz közeli régiót](../reference/regions.md).

A fiókadatok beszerezhetők a portálról a [fiókadatok beolvasása](create-an-account.md#retrieve-the-account-information) bekezdésben leírtak szerint.

## <a name="azure-frontend"></a>Azure-előtérbeli felület

A megfelelő osztályok a ```AzureFrontend``` és ```AzureSession```a. ```AzureFrontend```a a fiókok felügyeletéhez és a fiókok szintjének működéséhez használatos, beleértve a következőket: eszközök átalakítása és renderelési munkamenetek létrehozása. ```AzureSession```a munkamenet-szintű funkciókhoz használatos, és a következőket tartalmazza: munkamenet frissítése, lekérdezések, megújítása és leszerelése.

A rendszer minden megnyitott ```AzureSession``` /létrehozott, a létrehozott előtérre mutató hivatkozást vezet. A tisztításhoz az összes munkamenetet fel kell szabadítani, mielőtt a rendszer kiosztja a felületet.

A munkamenet felszabadítása nem állítja le a virtuális gépet az Azure- `AzureSession.StopAsync` ban, explicit módon meg kell hívni.

Miután létrehozta a munkamenetet, és az állapota készként van megjelölve, csatlakozhat a távoli renderelési futtatókörnyezethez a `AzureSession.ConnectToRuntime`következővel:.

### <a name="threading"></a>Threading

Az összes AzureSession és AzureFrontend aszinkron hívás egy háttérben futó szálban fejeződik be, nem pedig a fő alkalmazás szála.

### <a name="conversion-apis"></a>Átalakítási API-k

Az átalakítási szolgáltatással kapcsolatos további információkért tekintse meg [a modell átalakítási REST API](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Eszköz átalakításának indítása

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Konverziós állapot beolvasása

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>Renderelési API-k

A munkamenetek kezelésével kapcsolatos részletekért tekintse meg [a munkamenet-felügyeleti REST API](session-rest-api.md) .

A renderelési munkamenetek dinamikusan hozhatók létre a szolgáltatásban, vagy egy már meglévő munkamenet-azonosító is megnyitható egy AzureSession objektumban.

#### <a name="create-rendering-session"></a>Renderelési munkamenet létrehozása

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Meglévő renderelési munkamenet megnyitása

Egy meglévő munkamenet megnyitása szinkron hívás.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Aktuális renderelési munkamenetek beolvasása

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>Munkamenet API-k

#### <a name="get-rendering-session-properties"></a>Renderelési munkamenet tulajdonságainak beolvasása

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Renderelési munkamenet frissítése

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Renderelési munkamenet leállítása

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Kapcsolódás az ARR Inspector-hoz

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>További lépések

* [Fiók létrehozása](create-an-account.md)
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
