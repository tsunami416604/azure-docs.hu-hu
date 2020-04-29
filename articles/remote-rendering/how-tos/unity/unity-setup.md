---
title: A Remote Rendering beállítása a Unityben
description: Az Azure távoli renderelés inicializálása egy Unity-projektben
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681141"
---
# <a name="set-up-remote-rendering-for-unity"></a>A Remote Rendering beállítása a Unityben

Az Azure Remote rendering (ARR) egységben való engedélyezéséhez olyan dedikált módszereket biztosítunk, amelyek gondoskodnak bizonyos egységekre jellemző szempontokról.

## <a name="startup-and-shutdown"></a>Indítás és leállítás

A távoli renderelés inicializálásához használja `RemoteManagerUnity`a következőt:. Ez az osztály az általános `RemoteManager` , de már megvalósítja az egység-specifikus adatokat. Az Unity például egy adott koordináta-rendszer használatával működik. A hívásakor `RemoteManagerUnity.Initialize`a rendszer a megfelelő konvenciót fogja beállítani. A híváshoz azt is meg kell adnia, hogy milyen Unity kamerát kell használni a távolról renderelt tartalom megjelenítéséhez.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

A távoli renderelés leállítása érdekében hívja `RemoteManagerStatic.ShutdownRemoteRendering()`meg a következőt:.

`AzureSession` Miután létrehozta és kiválasztotta az elsődleges renderelési munkamenetet, regisztrálnia kell a `RemoteManagerUnity`következővel:

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

`RemoteManagerUnity.OnSessionUpdate`eseményeket bocsát ki a munkamenet-állapotának megváltozásakor, további részletekért tekintse meg a kód dokumentációját.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`a egy opcionális összetevő a telepítés és a munkamenet-kezelés egyszerűsítéséhez. Olyan beállításokat tartalmaz, amelyekkel automatikusan leállíthatja a munkamenetet, amikor az alkalmazás kilép, vagy a lejátszás mód ki van zárva a szerkesztőben, valamint szükség esetén automatikusan megújítja a munkamenet-bérletet. Gyorsítótárazza az adatforrásokat, például a munkamenet tulajdonságait ( `LastProperties` lásd a változót), és megjeleníti az eseményeket a munkamenet-állapot változásaihoz és a munkamenet-hibákhoz.

Egyszerre csak egy példánya `ARRServiceUnity` lehet. Ez azt jelenti, hogy néhány gyakori funkció megvalósításával gyorsabban kezdheti meg a lépéseket. A nagyobb alkalmazások esetében érdemes lehet ezeket a dolgokat is előnyben részesíteni, mégis.

Példa a beállításra és a használatra `ARRServiceUnity` : az [Unity-projekt teljesen új beállítása](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>További lépések

* [A Unity Remote Rendering-csomagjának telepítése](install-remote-rendering-unity-package.md)
* [Oktatóanyag: Unity-projekt létrehozása a semmiből](../../tutorials/unity/project-setup.md)
