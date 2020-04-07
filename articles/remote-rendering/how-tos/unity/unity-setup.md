---
title: Távoli renderelés beállítása egységre
description: Az Azure remote rendering inicializálása Unity-projektben
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681141"
---
# <a name="set-up-remote-rendering-for-unity"></a>Távoli renderelés beállítása egységre

Az Azure Remote Rendering (ARR) unity engedélyezéséhez olyan dedikált módszereket biztosítunk, amelyek gondoskodnak néhány Unity-specifikus szempontból.

## <a name="startup-and-shutdown"></a>Indítás és leállítás

A távoli renderelés `RemoteManagerUnity`inicializálásához használja a használatát. Ez az osztály `RemoteManager` felhívja az általános, de már megvalósítja Unity-specifikus részleteket az Ön számára. A Unity például egy adott koordináta-rendszert használ. Híváskor `RemoteManagerUnity.Initialize`a megfelelő konvenció lesz beállítva. A hívás azt is megköveteli, hogy biztosítsa az Unity kamerát, amelyet a távolról megjelenített tartalom megjelenítéséhez kell használni.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

A távoli renderelés leállításához hívja a hívást. `RemoteManagerStatic.ShutdownRemoteRendering()`

Miután `AzureSession` egy létrehozást és elsődleges renderelési munkamenetnek lett kiválasztva, azt a következőben `RemoteManagerUnity`kell regisztrálni:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Teljes példakód

Az alábbi kód bemutatja az Azure remote rendering inicializálásához szükséges összes lépést egységben:

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

## <a name="convenience-functions"></a>Kényelmi funkciók

### <a name="session-state-events"></a>Munkamenet-állapot eseményei

`RemoteManagerUnity.OnSessionUpdate`eseményeket bocsát ki, amikor a munkamenet állapota megváltozik, a részleteket a kód dokumentációjában találja.

### <a name="arrserviceunity"></a>ARRServiceUnity között

`ARRServiceUnity`egy opcionális összetevő, amely egyszerűsíti a telepítést és a munkamenet-kezelést. Olyan beállításokat tartalmaz, amelyek automatikusan leállítják a munkamenetet, amikor az alkalmazás kilép, vagy a lejátszási mód kilép a szerkesztőből, valamint szükség esetén automatikusan megújítja a munkamenet-bérletet. Gyorsítótárazi az adatokat, például `LastProperties` a munkamenet tulajdonságait (lásd a változót), és megjeleníti a munkamenet-állapot változásainak és a munkamenet-hibák eseményeinek.

Egyszerre csak egy példány `ARRServiceUnity` lehet. Ez azt jelentette, hogy a kezdés gyorsabb megvalósítása néhány közös funkció. Egy nagyobb alkalmazás lehet előnyösebb, hogy ezeket a dolgokat magad, mégis.

Például a beállítás és `ARRServiceUnity` a használat című témaköroktatóanyag: [Egységprojekt beállítása a semmiből című témakörben.](../../tutorials/unity/project-setup.md)

## <a name="next-steps"></a>További lépések

* [A Unity távoli renderelési csomagjának telepítése](install-remote-rendering-unity-package.md)
* [Oktatóanyag: Unity-projekt beállítása a semmiből](../../tutorials/unity/project-setup.md)
