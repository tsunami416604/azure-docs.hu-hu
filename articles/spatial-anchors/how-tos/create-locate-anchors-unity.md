---
title: Hogyan hozhat létre, és keresse meg a térbeli horgonyok Azure használatával a Unity-nél horgonyok |} A Microsoft Docs
description: Bemutatja, hogyan hozhat létre, és keresse meg a térbeli horgonyok Azure használatával a Unity-nél horgonyok magyarázatra van szüksége.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: d1c5f906fef495f7ef1dde5df38a84629a6d6c61
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915951"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-unity"></a>Hogyan hozhat létre, és keresse meg a központi jellegűek Azure térbeli horgonyok használatával a Unity-nél

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure térbeli horgonyok horgonyok a világ különböző eszközök közötti megosztását teszi lehetővé. Támogatja a több különböző fejlesztési környezet. Ebben a cikkben fogja nekikezdenénk a Azure térbeli horgonyok SDK-val, a Unity-nél, hogyan:

- Helyes beállítása és kezelése az Azure térbeli horgonyok munkamenet.
- Hozzon létre, és állítsa be a helyi kapcsolatok alapjainak tulajdonságait.
- Töltse fel a felhőbe azokat.
- Keresse meg, és törölje a térbeli felhőbeli horgonyok.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához győződjön meg arról, hogy rendelkezik:

- Olvassa el a [Azure térbeli kapcsolatok alapjainak áttekintése](../overview.md).
- Befejeződött az egyik a [5 perces gyors útmutatók](../index.yml).
- Alapvető ismeretek C# és a Unity.
- Alapvető ismeretek <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> Ha azt szeretné használni az Android- vagy <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> Ha IOS-es használni kívánt.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Tudjon meg többet a [CloudSpatialAnchorSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession) osztály.

```csharp
    CloudSpatialAnchorSession cloudSession;
    // In your view handler
    this.cloudSession = new CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Tudjon meg többet a [SessionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionconfiguration) osztály.

```csharp
    this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```csharp
    this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Tudjon meg többet a [TokenRequiredDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.tokenrequireddelegate) delegálására.

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AccessToken = @"MyAccessToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AccessToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```csharp
    this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AuthenticationToken = @"MyAuthenticationToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AuthenticationToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

Tudjon meg többet a [Start](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.start) metódust.

```csharp
#if UNITY_IOS
    this.arkitSession = UnityARSessionNativeInterface.GetARSessionNativeInterface();
    this.cloudSession.Session = this.arkitSession.GetNativeSessionPtr();
#elif UNITY_ANDROID
    this.nativeSession = GoogleARCoreInternal.ARCoreAndroidLifecycleManager.Instance.NativeSession;
    this.cloudSession.Session = this.nativeSession.SessionHandle;
#elif UNITY_WSA || WINDOWS_UWP
    // No need to set a native session pointer for HoloLens.
#else
    throw new NotSupportedException("The platform is not supported.");
#endif

    this.cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Tudjon meg többet a [ProcessFrame](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.processframe) metódust.

```csharp
#if UNITY_ANDROID
    long latestFrameTimeStamp = this.nativeSession.FrameApi.GetTimestamp();
    bool newFrameToProcess = latestFrameTimeStamp > this.lastFrameProcessedTimeStamp;
    if (newFrameToProcess)
    {
        this.cloudSession.ProcessFrame(this.nativeSession.FrameHandle);
        this.lastFrameProcessedTimeStamp = latestFrameTimeStamp;
    }
#endif
#if UNITY_IOS
    UnityARSessionNativeInterface.ARFrameUpdatedEvent += UnityARSessionNativeInterface_ARFrameUpdatedEvent;

    void UnityARSessionNativeInterface_ARFrameUpdatedEvent(UnityARCamera camera)
    {
        this.cloudSession.ProcessFrame(this.arkitSession.GetNativeFramePtr());
    }
#endif
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Tudjon meg többet a [SessionUpdatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionupdateddelegate) delegálására.

```csharp
    this.cloudSession.SessionUpdated += (object sender, SessionUpdatedEventArgs args)
    {
        var status = args.Status;
        if (status.UserFeedback == SessionUserFeedback.None) return;
        this.feedback = $"Feedback: {Enum.GetName(typeof(SessionUserFeedback), status.UserFeedback)} -" +
            $" Recommend Create={status.RecommendedForCreateProgress: 0.#%}";
    };
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Tudjon meg többet a [CloudSpatialAnchor](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor) osztály.

```csharp
    // Create a local anchor, perhaps by hit-testing and spawning an object within the scene
    Vector3 hitPosition = new Vector3();
#if UNITY_IOS
    var screenPosition = Camera.main.ScreenToViewportPoint(new Vector3(0.5f, 0.5f));
    ARPoint point = new ARPoint
    {
        x = screenPosition.x,
        y = screenPosition.y
    };
    var hitResults = UnityARSessionNativeInterface.GetARSessionNativeInterface().HitTest(point, ARHitTestResultType.ARHitTestResultTypeEstimatedHorizontalPlane | ARHitTestResultType.ARHitTestResultTypeExistingPlaneUsingExtent);
    if (hitResults.Count > 0)
    {
        // The hitTest method sorts the resulting list by distance from the camera, increasing
        // The first hit result will usually be the most relevant when responding to user input
        ARHitTestResult hitResult = hitResults[0];
        hitPosition = UnityARMatrixOps.GetPosition(hitResult.worldTransform);
    }
#elif UNITY_ANDROID
    TrackableHit hit;
    TrackableHitFlags raycastFilter = TrackableHitFlags.PlaneWithinPolygon | TrackableHitFlags.FeaturePointWithSurfaceNormal;
    if (Frame.Raycast(0.5f, 0.5f, raycastFilter, out hit))
    {
        hitPosition = hit.Pose.position;
    }
#elif WINDOWS_UWP || UNITY_WSA
    RaycastHit hit;
    if (this.TryGazeHitTest(out hit))
    {
        hitPosition = hit.point;
    }
#endif

    Quaternion rotation = Quaternion.AngleAxis(0, Vector3.up);
    this.localAnchor = GameObject.Instantiate(/* some prefab */, hitPosition, rotation);
    this.localAnchor.AddARAnchor();

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
    cloudAnchor.LocalAnchor = this.localAnchor.GetNativeAnchorPointer();
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
    this.feedback = $"Created a cloud anchor with ID={cloudAnchor.Identifier}");
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Tudjon meg többet a [GetSessionStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getsessionstatusasync) metódust.

```csharp
    SessionStatus value = await this.cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Tudjon meg többet a [AppProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.appproperties) tulajdonság.

```csharp
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor() { LocalAnchor = localAnchor };
    cloudAnchor.AppProperties[@"model-type"] = @"frame";
    cloudAnchor.AppProperties[@"label"] = @"my latest picture";
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Tudjon meg többet a [UpdateAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.updateanchorpropertiesasync) metódust.

```csharp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties[@"last-user-access"] = @"just now";
    await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Tudjon meg többet a [GetAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getanchorpropertiesasync) metódust.

```csharp
    var anchor = await cloudSession.GetAnchorPropertiesAsync(@"anchorId");
    if (anchor != nullptr)
    {
        anchor.AppProperties[@"last-user-access"] = @"just now";
        await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Tudjon meg többet a [lejárati](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.expiration) tulajdonság.

```csharp
    cloudAnchor.Expiration = DateTimeOffset.Now.AddDays(7);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Tudjon meg többet a [CreateWatcher](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.createwatcher) metódust.

```csharp
    AnchorLocateCriteria criteria = new AnchorLocateCriteria();
    criteria.Identifiers = new string[] { @"id1", @"id2", @"id3" };
    this.cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Tudjon meg többet a [AnchorLocatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.anchorlocateddelegate) delegálására.

```csharp
    this.cloudSession.AnchorLocated += (object sender, AnchorLocatedEventArgs args) =>
    {
        switch (args.Status)
        {
            case LocateAnchorStatus.Located:
                CloudSpatialAnchor foundAnchor = args.Anchor;
                // Go add your anchor to the scene...
                break;
            case LocateAnchorStatus.AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus.NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus.NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Tudjon meg többet a [DeleteAnchorAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.deleteanchorasync) metódust.

```csharp
    await this.cloudSession..DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Tudjon meg többet a [leállítása](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.stop) metódust.

```csharp
    this.cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Tudjon meg többet a [alaphelyzetbe](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.reset) metódust.

```csharp
    this.cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-unity.md)]

Tudjon meg többet a [Dispose](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.dispose) metódust.

```csharp
    this.cloudSession.Dispose();
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
