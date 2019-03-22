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
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: aa7c5c513d65310bf9bffab29c1d18e7e7a85b49
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316286"
---
# <a name="project-acoustics-unity-integration"></a>Projekt Akusztika Unity-integráció
Ez az útmutató azt ismerteti, a projekt Akusztika Unity beépülő modul integrálása a Unity-projektjét.

Szoftverkövetelmények:
* [Unity 2018.2 +](http://unity3d.com) Windows esetében
* [Projekt Akusztika Unity-csomag](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>A beépülő modul importálása
Importálja a Akusztika UnityPackage a projekthez. 
* A Unity, váltson **eszközök > csomag importálása > egyéni csomag...**

    ![Képernyőkép a Unity csomag importálása menü](media/import-package.png)  

* Válasszon **ProjectAcoustics.unitypackage**

Ha a beépülő modul be egy meglévő projekt importál, a projekt már előfordulhat, hogy egy **mcs.rsp** fájlt a projekt gyökérkönyvtárában, amely azt adja meg a C# fordítóprogram lehetőségeket. Szüksége lesz a mcs.rsp fájllal, amely a projekt Akusztika beépülő modult tartalmaz a fájl tartalmát egyesíti.

## <a name="enable-the-plugin"></a>A beépülő modul engedélyezése
A Akusztika eszközkészlet bake részének .NET 4.x scripting futásidejű verzióját igényli. Csomag importálása frissíteni fogja a Unity-lejátszó beállításai. Unity-Ez a beállítás érvénybe léptetéséhez indítsa újra.

![Képernyőkép a Unity Player Beállítások panel](media/player-settings.png)

![Képernyőkép a Unity Player Beállítások panelen a kiválasztott .NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Hang DSP beállítása
Projekt Akusztika Hang modul, amely integrálható a Unity hang motor spatializer keretrendszer DSP tartalmazza. Ez magában foglalja a egyaránt HRTF-alapú és Pásztázó spatialization. A projekt Akusztika DSP engedélyezéséhez nyissa meg a Unity hang beállításokat a **szerkesztése > Project Settings > hang**, majd válassza **projekt Akusztika** , a **Spatializer beépülő modul** a projekthez. Győződjön meg arról, hogy a **DSP pufferméret** van beállítva, a legjobb teljesítmény elérése érdekében.

![Képernyőkép a Unity Project beállítások panel](media/project-settings.png)  

![Képernyőkép a Unity Spatializer beállítások panel a kiválasztott projekt Akusztika spatializer](media/choose-spatializer.png)

Nyissa meg a hang Mixer (**ablak > hang Mixer**). Ellenőrizze, hogy szükség van legalább egy Mixer egy csoportot. Ha nem, a jobb oldalán található "+" gombra kattintva **vonja**. Kattintson a jobb gombbal a hatások szakaszban a csatorna sáv alján, és adja hozzá a **projekt Akusztika Mixer** érvénybe. Vegye figyelembe, hogy egyszerre csak egy projekt Akusztika Mixer támogatott.

![Képernyőkép a Unity hang Mixer projekt Akusztika mixer üzemeltetése](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>A megbízható forrásból Akusztika engedélyezése
Hozzon létre egy hangforrásról. Kattintson a jelölőnégyzetbe, amely szerint a AudioSource vizsgáló panel alján **Spatialize**. Győződjön meg arról, hogy **térbeli Blend** teljes 3D értékre van állítva.  

![Képernyőkép a Unity hang forrása panel](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akusztikai tervezés engedélyezése
Csatolja a parancsfájl **AcousticsAdjust** kattintva engedélyezéséhez a forrás további tervezési paraméterek, a jelenet megbízható forrásra **összetevő felvétele** választva **parancsfájlok > Akusztika beállítása** :

![Képernyőkép a Unity AcousticsAdjust parancsfájl](media/acoustics-adjust.png)

## <a name="next-steps"></a>További lépések
* [A for Unity Project Akusztika jelenetet os](unity-baking.md)
* [Az Azure Batch-fiók létrehozása](create-azure-account.md) , a felhőben a jelenethez os
* Fedezze fel a [Akusztika Unity Project tervezési folyamat](unity-workflow.md).

