---
title: A Remote Rendering beállítása a Unityben
description: Az Azure távoli renderelés inicializálása egy Unity-projektben
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a0be44d8709726e159e17e703566c6c576bc18f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018977"
---
# <a name="set-up-remote-rendering-for-unity"></a>A Remote Rendering beállítása a Unityben

Az Azure Remote rendering (ARR) egységben való engedélyezéséhez olyan dedikált módszereket biztosítunk, amelyek gondoskodnak bizonyos egységekre jellemző szempontokról.

## <a name="startup-and-shutdown"></a>Indítás és leállítás

A távoli renderelés inicializálásához használja a következőt: `RemoteManagerUnity` . Ez az osztály az általános `RemoteManager` , de már megvalósítja az egység-specifikus adatokat. Az Unity például egy adott koordináta-rendszer használatával működik. A hívásakor `RemoteManagerUnity.Initialize` a rendszer a megfelelő konvenciót fogja beállítani. A híváshoz azt is meg kell adnia, hogy milyen Unity kamerát kell használni a távolról renderelt tartalom megjelenítéséhez.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

A távoli renderelés leállítása érdekében hívja meg a következőt: `RemoteManagerStatic.ShutdownRemoteRendering()` .

Miután `AzureSession` létrehozta és kiválasztotta az elsődleges renderelési munkamenetet, regisztrálnia kell a következővel `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Teljes példa kód

Az alábbi kód az Azure Remote rendering Unity-ben történő inicializálásához szükséges összes lépést mutatja be:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Kényelmi függvények

### <a name="session-state-events"></a>Munkamenet-állapot eseményei

`RemoteManagerUnity.OnSessionUpdate` eseményeket bocsát ki a munkamenet-állapotának megváltozásakor, további részletekért tekintse meg a kód dokumentációját.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` a egy opcionális összetevő a telepítés és a munkamenet-kezelés egyszerűsítéséhez. Olyan beállításokat tartalmaz, amelyekkel automatikusan leállíthatja a munkamenetet, amikor az alkalmazás kilép, vagy a lejátszás mód ki van zárva a szerkesztőben, valamint szükség esetén automatikusan megújítja a munkamenet-bérletet. Gyorsítótárazza az adatforrásokat, például a munkamenet tulajdonságait (lásd a `LastProperties` változót), és megjeleníti az eseményeket a munkamenet-állapot változásaihoz és a munkamenet-hibákhoz.

Egyszerre csak egy példánya lehet `ARRServiceUnity` . Ez azt jelenti, hogy néhány gyakori funkció megvalósításával gyorsabban kezdheti meg a lépéseket. A nagyobb alkalmazások esetében érdemes lehet ezeket a dolgokat is előnyben részesíteni, mégis.

Példa a beállításra és a használatról: útmutató a `ARRServiceUnity` [távolról renderelt modellek megtekintéséhez](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Következő lépések

* [A Unity Remote Rendering-csomagjának telepítése](install-remote-rendering-unity-package.md)
* [Oktatóanyag: távolról megjelenített modellek megtekintése](../../tutorials/unity/view-remote-models/view-remote-models.md)
