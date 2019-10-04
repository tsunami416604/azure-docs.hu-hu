---
title: Projekt akusztikai egységének integrációja és üzembe helyezése
titlesuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan integrálhatja a Project akusztikai egység beépülő modulját az Unity-projektbe.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 54bc98e0ddba0292c6a5dbb07f2bbdfce6a1cb45
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933145"
---
# <a name="project-acoustics-unity-integration"></a>Projekt akusztikai egységének integrációja
Ez az útmutató bemutatja, hogyan integrálhatja a Project akusztikai egység beépülő modulját az Unity-projektbe.

A szoftverre vonatkozó követelmények:
* [Unity 2018.2 +](https://unity3d.com) Windowshoz
* [Projekt akusztikai egység csomag](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>A beépülő modul importálása
Importálja az akusztikai UnityPackage a projektbe. 
* Az egység területen lépjen az **eszközök > importálás csomag > egyéni csomag...**

    ![Képernyőfelvétel az egység importálási csomag menüjéről](media/import-package.png)  

* Válassza a **ProjectAcoustics. unitypackage elemet.**

* Kattintson az **Importálás** gombra az egység csomag projektbe való integrálásához  

    ![Képernyőfelvétel az egység importálási csomag párbeszédpanelről](media/import-dialog.png)  

Ha a beépülő modult egy meglévő projektbe importálja, előfordulhat, hogy a projektben már van egy **MCS. RSP** fájl a projekt gyökerében, amely C# a fordító beállításait adja meg. Egyesíteni kell a fájl tartalmát a Project Acoustics beépülő modulhoz tartozó MCS. RSP fájllal.

## <a name="enable-the-plugin"></a>Beépülő modul engedélyezése
A akusztikai eszközkészlet sütni részének a .NET 4. x parancsfájl-futtatási verziójának kell lennie. A csomag importálásakor a rendszer frissíti az Unity Player beállításait. A beállítás érvénybe léptetéséhez indítsa újra az egységet.

![Képernyőfelvétel az Unity Player beállítások panelről](media/player-settings.png)

![Képernyőfelvétel az Unity Player Settings panelről a .NET 4,5 kiválasztva](media/net45.png)

## <a name="set-up-audio-dsp"></a>Audio DSP beállítása
A Project Acoustics olyan audio Runtime DSP-t tartalmaz, amely integrálható az Unity audio Engine spatializer-keretrendszerbe. Magában foglalja a HRTF-alapú és a pásztázási spatialization is. Engedélyezze a Project akusztikai DSP-t, ehhez nyissa meg az Unity hangbeállításokat az **Edit > Project settings > audio**paranccsal, majd válassza a **Project Acoustics** elemet a projekthez tartozó **Spatializer beépülő modulként** . Győződjön meg arról, hogy a **DSP-puffer mérete** a legjobb teljesítmény értékre van állítva.

![Képernyőfelvétel az Unity Project Settings panelről](media/project-settings.png)  

![Képernyőfelvétel az Unity Spatializer Settings panelről a Project Acoustics Spatializer kiválasztott](media/choose-spatializer.png)

Ezután nyissa meg az hangkeverőt (**ablak >** keverője). Győződjön meg arról, hogy van legalább egy keverője egy csoporttal. Ha nem, kattintson a "+" gombra a keverők jobb oldalán. Kattintson a jobb gombbal a csatorna szalag aljára a hatások szakaszban, és adja hozzá a **Project akusztikai keverő** hatását. Vegye figyelembe, hogy egyszerre csak egy Project akusztikai keverő támogatott.

![Képernyőfelvétel az Unity hangkeverőről, amely a Project Acoustics keverőjét üzemelteti](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Akusztikai hangforrások engedélyezése
Hozzon létre egy hangforrást. Kattintson a AudioSource felügyelő paneljének alján található jelölőnégyzetre, amely a következőt mondja: **Spatialize**. Győződjön meg arról, hogy a **térbeli Blend** a teljes 3D értékre van beállítva.  

![Képernyőfelvétel az Unity audio forrás paneljéről](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akusztikus kialakítás engedélyezése
Csatolja a szkript **AcousticsAdjust** egy hangforráshoz a jelenetben, hogy engedélyezze a további forrás-tervezési paramétereket, ehhez kattintson az **összetevő hozzáadása** elemre, és válassza a **Parancsfájlok > akusztika beállítása**:

![Képernyőfelvétel az Unity AcousticsAdjust parancsfájlról](media/acoustics-adjust.png)

## <a name="next-steps"></a>További lépések
* [A saját jeleneteit az Unity projekt akusztikai adataival sütni](unity-baking.md)
* [Azure batch-fiók létrehozása](create-azure-account.md) a jelenet felhőbe való sütni
* Ismerje meg a [projekt akusztikai egységének tervezési folyamatát](unity-workflow.md).

