---
title: Projekt akusztikai egységének integrációja és üzembe helyezése
titlesuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan integrálhatja a projekt akusztikai egységének beépülő modulját az Unity-projektbe.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243001"
---
# <a name="project-acoustics-unity-integration"></a>Projekt akusztikai egységének integrációja
Ez a cikk bemutatja, hogyan integrálhatja a projekt akusztikai egységének beépülő modulját az Unity-projektbe.

A szoftverre vonatkozó követelmények:
* [Unity 2018.2 +](https://unity3d.com) Windowshoz
* [Projekt akusztikai egység csomag](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>A beépülő modul importálása
1. Importálja az akusztikai UnityPackage a projektbe. 
 Az egység területen lépjen az **eszközök** > **Importálás csomag** > **Egyéni csomag**elemre.

    ![Az egység importálása csomag menü](media/import-package.png)  

1. Válassza a **ProjectAcoustics. unitypackage**elemet.

1. Válassza az **Importálás** gombot az egység csomag projektbe való integrálásához.

    ![Az egység importálása csomag párbeszédpanel](media/import-dialog.png)  

Ha a beépülő modult egy meglévő projektbe importálja, előfordulhat, hogy a projektben már van egy *MCS. RSP* fájl a projekt gyökerében. Ez a fájl a C# fordító beállításait adja meg. Egyesítse a fájl tartalmát a projekt akusztikai beépülő moduljában található MCS. RSP fájllal.

## <a name="enable-the-plug-in"></a>Beépülő modul engedélyezése
A akusztikai eszközkészlet sütni részében a .NET 4 szükséges. *x* parancsfájl-futtatókörnyezet verziója. A csomag importálása frissíti az egység lejátszójának beállításait. A beállítás érvénybe léptetéséhez indítsa újra az egységet.

![A Unity Player beállítások panel](media/player-settings.png)

![A Unity Player Settings panel a .NET 4,5 kiválasztott](media/net45.png)

## <a name="set-up-audio-dsp"></a>Audio DSP beállítása
A Project Acoustics olyan audio Runtime DSP-t tartalmaz, amely integrálható az Unity audio Engine spatializer-keretrendszerbe. Magában foglalja a HRTF-alapú és a pásztázási spatialization is. Ha engedélyezni szeretné a Project akusztikai DSP **-t, lépjen az @no__t-** 1**projekt beállításai** > **hangra** , és nyissa meg az Unity hangbeállításokat. Válassza a **Project akusztika** lehetőséget a projekthez tartozó **Spatializer beépülő modulként** . Győződjön meg arról, hogy a **DSP-puffer mérete** a *legjobb teljesítmény*értékre van beállítva.

![Az Unity Project Settings menü](media/project-settings.png)  

![A Unity Spatializer beállítások panel a Project Acoustics Spatializer kijelölve](media/choose-spatializer.png)

Ezután nyissa meg az hangkeverőt (**ablak** > **keverő**). Győződjön meg arról, hogy van legalább egy keverője egy csoporttal. Ha még nem rendelkezik ilyennel, válassza a **+** gombot a **keverők**jobb oldalán. Kattintson a jobb gombbal a csatorna szalag aljára a hatások szakaszban, és adja hozzá a **Microsoft akusztikai keverő** effektust. Egyszerre csak egy Project akusztikai keverő támogatott.

![A Project akusztikai keverőjét tároló Unity audio mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Akusztikai hangforrások engedélyezése
Hangforrás létrehozása: jelölje be a **Spatialize** jelölőnégyzetet a AudioSource-felügyelő paneljének alján. Győződjön meg arról, hogy a **térbeli elegy** a teljes *3D*értékre van beállítva.  

![Az Unity audio forrás panel](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akusztikus kialakítás engedélyezése
Csatolja a *AcousticsAdjust* szkriptet a jelenetben lévő hangforráshoz, hogy engedélyezze a további forrás-tervezési paramétereket: válassza az **összetevő hozzáadása** elemet, és válassza a **parancsfájlok** > **akusztikai beállítások**lehetőséget.

![Az Unity AcousticsAdjust-szkript](media/acoustics-adjust.png)

## <a name="next-steps"></a>Következő lépések
* [A saját jeleneteit az Unity projekt akusztikai adataival sütni](unity-baking.md).
* [Hozzon létre egy Azure batch fiókot](create-azure-account.md) a jelenet felhőben való sütni.
* Ismerje meg a [projekt akusztikai egységének tervezési folyamatát](unity-workflow.md).
