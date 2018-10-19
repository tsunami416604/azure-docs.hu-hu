---
title: 'Példa: Project Acoustics'
titlesuffix: Azure Cognitive Services
description: Ez az útmutató a Unity nevű Project Acoustics-mintajelenetet ismerteti, beleértve az asztali számítógépen és VR-ban történő üzembe helyezést is.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: f5ea565e68579dfad601d1037daeb4113e3daa43
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901158"
---
# <a name="unity-sample-walkthrough"></a>Unity mintaútmutató
Ez egy Project Acoustics-mintaútmutató. A Project Acousticsról további információkat a [Project Acoustics bevezető részében](what-is-acoustics.md) talál. Ha segítségre van szüksége a Project Acoustics-csomag már meglévő Unity-projekthez való hozzáadásában, tekintse meg az [első lépéseket ismertető útmutatót](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>A mintaprojekt futtatásához kapcsolódó követelmények
* Unity 2018.2+, .NET 4.x Scripting Runtime-verzióval
* Windows 64-bit Unity szerkesztő
* A minta támogatja a Windows asztali verzióját, az UWP-t és az androidos céleszközöket, például a fejre erősített kijelzőket (HMD-ket) is
* A beépítési folyamathoz Azure Batch-előfizetés szükséges

## <a name="sample-project-setup"></a>A mintaprojekt beállítása
Töltse le és importálja a **MicrosoftAcoustics.Sample.unitypackage** csomagot. Importáláskor a beépülő modul követelményeinek megfeleljenek frissülnek a projektbeállítások (például a **Spatializer** és a **Scripting Runtime Version** (Scripting Runtime-verzió)). Ezt követően az **AcousticsGeometry.cs** hibaüzenet küld a Unity konzoljára, hogy a Scripting Runtime Version (Scripting Runtime-verzió) beállítás értéke **.NET 4.x-nek megfelelőre** módosult. A beállítások módosítása a csomag importálásával megtörtént, de azok érvénybe lépéséhez a Unity újraindítása szükséges. Indítsa újra a Unityt.

## <a name="running-the-sample"></a>A minta futtatása
A minta egy bemutatójelenetet (**Assets/AcousticsDemo/ProjectAcousticsDemo.unity**) is tartalmaz. A jelenethez három hangforrás tartozik. Alapértelmezés szerint csak az egyik hangforrás aktív, a másik kettő szüneteltetve van. Ezek a **Hierarchy** (Hierarchia) nézet **Sound Sources** (Hangforrások) területén találhatók meg. Az általános navigációs szkript létrehozásának megkönnyítése érdekében a Main Camera a CameraHolder objektum gyermekeleme. 

![Hierarchy (Hierarchia) nézet](media/SampleHierarchyView.png)

A jelenet beépítése már megtörtént, és a **Hierarchy** (Hierarchia) nézetben egy ACE-fájl van társítva az előgyártott **MicrosoftAcoustics** elemhez. 

A Unity szerkesztőjének Play (Lejátszás) gombjára kattintva ellenőrizheti a jelenet hangját. Asztali számítógépen a W, A, S, D billentyűkkel és az egérrel navigálhat. Ha meg szeretné hallgatni, hogy milyen lenne a jelenet hangja az Acoustics használata nélkül, tartsa lenyomva az **R** billentyűt, amíg vörös színnel meg nem jelenik az Acoustics: Disabled (Acoustics: Letiltva) átfedő szöveg. További billentyűparancsok megtekintéséhez nyomja le az **F1** billentyűt. Mindegyik vezérlő esetében lehetőség van a jobb gombbal kattintva kijelölni, majd a bal egérgombbal kattintva végrehajtani a műveletet.

## <a name="targeting-other-platforms"></a>További célplatformok
A minta a következő platformokhoz tartalmaz futtatási beállításokat: Windows asztali rendszer, UWP, Windows Mixed Reality, Android és Oculus Go. Alapértelmezés szerint a projekt Windows asztali rendszerre van konfigurálva. VR-célplatform esetén nyissa meg a lejátszó beállításait (**Edit (Szerkesztés) > Project Settings (Projekt beállításai) > Player (Lejátszó)**), keresse meg az **XR Settings** (XR-beállítások) elemet, majd jelölje be a **Virtual Reality Supported** (Virtuális valóság támogatása) jelölőnégyzetet.

![A VR engedélyezése](media/VRSupport.png)  

Csatlakoztasson egy VR-headsetet a számítógéphez. A **File (Fájl) > Build Settings (Létrehozási beállítások)** menüpontban a **Build and Run** (Létrehozás és futtatás) gombra kattintva telepítheti a mintát a VR-headsetre. A jelenetben a headset mozgásvezérlőivel vagy a billentyűzet W, A, S, D billentyűivel navigálhat.    
Android és Oculus Go esetében a **Build Settings** (Létrehozási beállítások) menüben válassza az Android lehetőséget. Kattintson a **Switch Target** (Cél váltása) elemre, majd a **Build and Run** (Létrehozás és futtatás) gombra. Ezzel telepíti a mintajelenetet a csatlakoztatott androidos eszközre. Az Android rendszerre történő Unity-fejlesztésről további információt [a Unity dokumentációjában](https://docs.unity3d.com/Manual/android-GettingStarted.html) talál.

![Androidos cél](media/TargetAndroid.png)  

## <a name="next-steps"></a>További lépések
* [Azure-fiók létrehozása](create-azure-account.md) beépíteni kívánt saját tartalmak számára
* A [tervezési folyamat](design-process.md) áttekintése

