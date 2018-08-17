---
title: Projekt Akusztika minta bemutatója – Cognitive Services
description: Ez az útmutató ismerteti a Unity-minta jelenet projekt Akusztika, beleértve az asztali és VR való üzembe helyezés.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b738cc2fc7db6987b8f4ad54a2c53cc9e69989b3
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181536"
---
# <a name="unity-sample-walkthrough"></a>Unity mintaútmutató
Ez a bemutató a projekt Akusztika minta. További információ a milyen projekt Akusztika van, tekintse meg a [projekt Akusztika bemutatása](what-is-acoustics.md). A projekt Akusztika csomagot ad hozzá egy már meglévő Unity-projektet a segítségre van szüksége, használja a [a kezdeti lépések útmutatóban](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>A mintaprojekt futtatásának követelményei
* Unity 2018.2 +, .NET 4.x scripting futásidejű verzió használatával
* Windows 64 bites Unity-szerkesztő
* A minta támogatja a Windows asztali, UWP- és Android teljesítménycéljait, beleértve a fő csatlakoztatott jeleníti meg (HMDS-t)
* Az Azure Batch-előfizetés szükséges bake folyamat

## <a name="sample-project-setup"></a>Minta-projekt beállítása
Töltse le és importálja a **MicrosoftAcoustics.Sample.unitypackage**. Importálási, a beállításokat, beleértve a projekt **Spatializer** és **Verze modulu Runtime Scripting** frissülnek, hogy a beépülő modul követelményeinek. Ha ez befejeződött, megjelenik a Unity-konzolon, a hiba **AcousticsGeometry.cs** módosításával a parancsfájl-kezelési futtatókörnyezet verziójának **.NET 4.x egyenértékű**. Ezen beállítások módosítása a csomag importálása részeként történik, de a Unity érvénybe léptetéséhez újraindítás szükséges. Indítsa újra most Unity.

## <a name="running-the-sample"></a>A minta futtatása
A minta tartalmaz egy bemutató jelenet **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. A helyszín van egy egyetlen spatialized hang lejátszása a lebegőpontos adatkocka (nevű **AudioHolder** a a **hierarchia**). Általános navigációs parancsfájl segítségével, a fő kamera CameraHolder objektum gyermek. 

![Hierarchia megtekintése](media/SampleHierarchyView.png)

A jelenet már lett számlázásnak és a egy ACE-fájlt az társított a **MicrosoftAcoustics** a prefab a **hierarchia**. 

Figyeljen, hogy a jelenet úgy érzi, a Unity-szerkesztőben a lejátszás gombra kattintva. Használat W, A, S, D és az egérrel való mozgáshoz. Hasonlítsa össze, hogy a jelenet úgy érzi, és anélkül Akusztika, kattintson a bal oldali vagy az elsődleges vezérlő gombra. Váltás a hang különböző forrásokból, kattintson a jobb gombbal, vagy a Vissza gombot a tartományvezérlőn.

## <a name="targeting-other-platforms"></a>Más platformok célzó
A minta futtatásához a Windows asztali, UWP, a Windows vegyes valóság, Android és Oculus Go beállításait tartalmazza. Alapértelmezés szerint a projekt Windows asztal konfigurálva van. VR célplatform, nyissa meg a lejátszó beállításai (**szerkesztése > Projektbeállítások > Player**), keresse meg a **XR beállítások**, és ellenőrizze a **virtuális valóságban támogatott** jelölőnégyzetet.

![VR engedélyezése](media/VRSupport.png)  

VR mikrofonos kapcsolódni a Számítógéphez. Lépjen a **fájl > Build Settings**, és kattintson a **létrehozásához és futtatásához** , a minta üzembe helyezése a VR mikrofonos. Haladjon végig a mozgásban lévő adatoknak egyaránt vezérlő használata a mikrofonos jelenet, vagy használjon W, A, S, D-t a billentyűzettel.    
Android- és Oculus Go célozza meg, válassza ki az Android a **Build Settings** menü. Kattintson a **cél váltson**, majd **létrehozásához és futtatásához**. Ez a minta jelenet telepíti a csatlakoztatott Androidos eszközön. Az Android-, Unity fejlesztési kapcsolatos információkat lásd: [Unity-dokumentáció](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Cél Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>További lépések
* [Azure-fiók létrehozása](create-azure-account.md) a saját kódfejlesztést
* Fedezze fel a [folyamat tervezése](design-process.md)

