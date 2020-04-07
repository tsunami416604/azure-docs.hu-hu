---
title: Azure előtér-API-k a hitelesítéshez
description: A C# előtér API hitelesítéshez való használata
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681349"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Az Azure előtér-API-k használata a hitelesítéshez

Ebben a szakaszban bemutatjuk, hogyan használhatja a C# API-t a hitelesítéshez.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

Az AzureFrontendAccountInfo segítségével állítsa be az ```AzureFrontend``` SDK-ban egy példány hitelesítési adatait.

A legfontosabb területek a következők:

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

A tartomány _régiórészéhez_ használjon az [Ön közelében lévő régiót.](../reference/regions.md)

A fiókadatok a portálról szerezhetők be a [fiókadatok lekérése](create-an-account.md#retrieve-the-account-information) című bekezdésben leírtak szerint.

## <a name="azure-frontend"></a>Azure előtér

A vonatkozó osztályok ```AzureFrontend``` ```AzureSession```és . ```AzureFrontend```a fiókkezelés és a fiókszintű funkciók, amelyek magukban foglalják: eszközkonverzió és renderelés munkamenet létrehozása. ```AzureSession```a munkamenetszintű funkciókhoz használatos, és a következőket tartalmazza: munkamenet-frissítés, lekérdezések, megújítás és leszerelés.

Minden megnyitott/létrehozott ```AzureSession``` megőrzi a létrehozott előtérre mutató hivatkozást. A tiszta leállításhoz az előtér felhívása előtt minden munkamenetet fel kell osztani.

Deallocating egy munkamenet nem állítja le `AzureSession.StopAsync` a virtuális gép az Azure-ban, explicit módon meg kell hívni.

Miután létrehozott egy munkamenetet, és állapota készen áll, csatlakozhat a távoli `AzureSession.ConnectToRuntime`renderelési futásidőhöz a segítségével.

### <a name="threading"></a>Fonál

Minden AzureSession és AzureFrontend async-hívás egy háttérszálon, nem pedig a fő alkalmazásszálban fejeződik be.

### <a name="conversion-apis"></a>Konverziós API-k

A konverziós szolgáltatásról további információt [a modellkonvertáló REST API című témakörben](conversion/conversion-rest-api.md)talál.

#### <a name="start-asset-conversion"></a>Eszközátalakítás megkezdése

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

#### <a name="get-conversion-status"></a>Konverziós állapot beszerezni

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

### <a name="rendering-apis"></a>API-k renderelése

A munkamenet-kezelés sel kapcsolatos részleteket a munkamenet-kezelés sel kapcsolatos részletekért tekintse meg [a munkamenet-kezelés.](session-rest-api.md)

A renderelési munkamenet dinamikusan is létrehozható a szolgáltatáson, vagy egy már meglévő munkamenet-azonosító "megnyitható" egy AzureSession-objektumba.

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

Egy meglévő munkamenet megnyitása szinkronhívás.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Aktuális megjelenítési munkamenetek beszereznie

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

#### <a name="get-rendering-session-properties"></a>Renderelési munkamenet tulajdonságainak beszereznie

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

#### <a name="stop-rendering-session"></a>Megjelenítés leállítása

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

#### <a name="connect-to-arr-inspector"></a>Csatlakozás az ARR-ellenőrhöz

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
* [Példa PowerShell-parancsfájlokra](../samples/powershell-example-scripts.md)
