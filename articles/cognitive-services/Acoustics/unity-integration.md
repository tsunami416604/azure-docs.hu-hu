---
title: Projekt Akusztika Unity-integráció és üzembe helyezés
titlesuffix: Azure Cognitive Services
description: Ez az útmutató azt ismerteti, a projekt Akusztika Unity beépülő modul integrálása a Unity-projektjét.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137730"
---
# <a name="project-acoustics-unity-integration"></a>Projekt Akusztika Unity-integráció
Ez az útmutató azt ismerteti, a projekt Akusztika Unity beépülő modul integrálása a Unity-projektjét.

Szoftverkövetelmények:
* [Unity 2018.2 +](http://unity3d.com) Windows esetében
* [Projekt Akusztika Unity-csomag](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>A beépülő modul importálása
Importálja a Akusztika UnityPackage a projekthez. 
* A Unity, váltson **eszközök > csomag importálása > egyéni csomag...**

    ![Csomag importálása](media/import-package.png)  

* Válasszon **ProjectAcoustics.unitypackage**

Ha a beépülő modul be egy meglévő projekt importál, a projekt már előfordulhat, hogy egy **mcs.rsp** fájlt a projekt gyökérkönyvtárában, amely azt adja meg a C# fordítóprogram lehetőségeket. Szüksége lesz a mcs.rsp fájllal, amely a projekt Akusztika beépülő modult tartalmaz a fájl tartalmát egyesíti.

## <a name="enable-the-plugin"></a>A beépülő modul engedélyezése
A Akusztika eszközkészlet bake részének .NET 4.x scripting futásidejű verzióját igényli. Csomag importálása frissíteni fogja a Unity-lejátszó beállításai. Unity-Ez a beállítás érvénybe léptetéséhez indítsa újra.

![Lejátszó beállításai](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Hang DSP beállítása
Projekt Akusztika Hang modul, amely integrálható a Unity hang motor spatializer keretrendszer DSP tartalmazza. Ez magában foglalja a egyaránt HRTF-alapú és Pásztázó spatialization. A projekt Akusztika DSP engedélyezéséhez nyissa meg a Unity hang beállításokat a **szerkesztése > Project Settings > hang**, majd válassza **projekt Akusztika** , a **Spatializer beépülő modul** a projekthez. Győződjön meg arról, hogy a **DSP pufferméret** van beállítva, a legjobb teljesítmény elérése érdekében.

![Projektbeállítások](media/project-settings.png)  

![Projekt Akusztika Spatializer](media/choose-spatializer.png)

Nyissa meg a hang Mixer (**ablak > hang Mixer**). Ellenőrizze, hogy szükség van legalább egy Mixer egy csoportot. Ha nem, a jobb oldalán található "+" gombra kattintva **vonja**. Kattintson a jobb gombbal a hatások szakaszban a csatorna sáv alján, és adja hozzá a **projekt Akusztika Mixer** érvénybe. Vegye figyelembe, hogy egyszerre csak egy projekt Akusztika Mixer támogatott.

![Hang Mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>A megbízható forrásból Akusztika engedélyezése
Hozzon létre egy hangforrásról. Kattintson a jelölőnégyzetbe, amely szerint a AudioSource vizsgáló panel alján **Spatialize**. Győződjön meg arról, hogy **térbeli Blend** teljes 3D értékre van állítva.  

![Hangbemeneti forrás](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akusztikai tervezés engedélyezése
Csatolja a parancsfájl **AcousticsAdjust** kattintva engedélyezéséhez a forrás további tervezési paraméterek, a jelenet megbízható forrásra **összetevő felvétele** választva **parancsfájlok > Akusztika beállítása** :

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>További lépések
* [A for Unity Project Akusztika jelenetet os](unity-baking.md)
* [Az Azure Batch-fiók létrehozása](create-azure-account.md) , a felhőben a jelenethez os
* Fedezze fel a [Akusztika Unity Project tervezési folyamat](unity-workflow.md).

