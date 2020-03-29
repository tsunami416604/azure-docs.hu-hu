---
title: Project Acoustics Unity integráció és üzembe helyezés
titlesuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan integrálhatja a Project Acoustics Unity beépülő modult az Unity projektbe.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243001"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity integráció
Ez a cikk bemutatja, hogyan integrálhatja a Project Acoustics Unity beépülő modult az Unity projektbe.

Szoftverkövetelmények:
* [Unity 2018.2+](https://unity3d.com) Windows-hoz
* [Project Acoustics Unity csomag](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>A beépülő modul importálása
1. Importálja az akusztikaunity UnityPackage a projektbe. 
 A Unity, go to **Assets** > **Import Package** > **Custom Package**.

    ![A Unity importálási csomag menü](media/import-package.png)  

1. Válassza **a ProjectAcoustics.unitypackage lehetőséget.**

1. Az **Importálás** gombra kattintva integrálhatja az Egység csomagot a projektbe.

    ![A Unity importálási csomag párbeszédpanel](media/import-dialog.png)  

Ha a beépülő modult egy meglévő projektbe importálja, előfordulhat, hogy a projektben már van egy *mcs.rsp* fájl a projekt gyökérben. Ez a fájl megadja a C# fordító beállításait. A fájl tartalmának egyesítése a Project Acoustics beépülő modulhoz kapott mcs.rsp fájllal.

## <a name="enable-the-plug-in"></a>A beépülő modul engedélyezése
Az akusztikai eszközkészlet sütni része a .NET 4.the bake portion of the acoustics toolkit requires the .NET 4. *x* scripting runtime verzió. A csomagimportálás frissíti a Unity lejátszó beállításait. A beállítás érvénybe léptetéséhez indítsa újra az Egységet.

![A Unity Player Beállítások panelje](media/player-settings.png)

![A Unity Player Settings panelen a .NET 4.5 van kiválasztva](media/net45.png)

## <a name="set-up-audio-dsp"></a>HangDSP beállítása
A Project Acoustics olyan hangfutásidejű DSP-t tartalmaz, amely integrálódik a Unity audiomotor spatializer keretrendszerébe. Ez magában foglalja mind a HRTF-alapú és pásztázás térbeli. A Project Acoustics DSP engedélyezéséhez nyissa meg a**Projektbeállítások** > **hangjának** **szerkesztése** > lehetőséget az Unity hangbeállítások megnyitásához. Válassza ki **a Project Acoustics** lehetőséget **a projekt térbeli beépülő** moduljaként. Győződjön meg arról, hogy a **DSP puffermérete** a *legjobb teljesítmény.*

![A Unity projekt beállítások menüje](media/project-settings.png)  

![A Unity Spatializer beállítások panel, amelyen a Project Acoustics spatializer van kiválasztva](media/choose-spatializer.png)

Ezután nyissa meg a Hangkeverőt (**Ablak** > **audiokeverő**). Győződjön meg arról, hogy legalább egy keverővel rendelkezik, egy csoporttal. Ha még nem rendelkezik ilyenvel, válassza a **+** Keverők gombbal jobbra található **gombot.** Kattintson a jobb gombbal a csatornasáv aljára a hatások szakaszban, és adja hozzá a **Microsoft Acoustics Mixer** effektust. Egyszerre csak egy Project Acoustics keverő támogatott.

![A Project Acoustics mixert tároló Unity Audio Mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Hangforrásokon az akusztika engedélyezése
Hangforrás létrehozása: Jelölje be a **Spatialize** jelölőnégyzetet az AudioSource-felügyelő panel alján. Győződjön meg arról, hogy a **Térbeli keverés** teljes *3D-re*van állítva.  

![A Unity Audio Source panel](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akusztikus tervezés engedélyezése
Az *AkusztikaI-beállítás parancsfájl* csatolása a jelenet egyik hangforrásához további forrástervezési paraméterek engedélyezéséhez: Válassza **az Összetevő hozzáadása lehetőséget,** és válassza a **Parancsfájlok** > **akusztikabeállítása lehetőséget.**

![A Unity AcousticsAdjust szkript](media/acoustics-adjust.png)

## <a name="next-steps"></a>További lépések
* [Süsse meg a jelenetet project akusztika egység](unity-baking.md).
* [Hozzon létre egy Azure Batch-fiókot,](create-azure-account.md) hogy a felhőben süsse a jelenetet.
* Fedezze fel a [Project Acoustics Unity tervezési folyamatát.](unity-workflow.md)
