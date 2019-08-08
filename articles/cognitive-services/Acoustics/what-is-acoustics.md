---
title: A Project akusztika áttekintése
titlesuffix: Azure Cognitive Services
description: A Project Acoustics egy akusztikus motor a 3D interaktív funkciókhoz, amely a kisült hullámos fizika szimulációját interaktív tervezési vezérlőkkel integrálja.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 382d4febed98b53a469ac2788e6e8c8cbf064c23
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854819"
---
# <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?
A Project Acoustics egy Wave akusztikus motor a 3D interaktív élményekhez. Az informatikai modellek olyan hullámos hatásokat mutatnak be, mint a diffrakciós, a portál-és a reverb-effektusok az összetett jeleneteknél anélkül, hogy kézi Emellett magában foglalja a Game Engine és a audio middleware integrációját is. A Project Acoustics filozófiája hasonló a statikus világításhoz: a részletes fizikát offline állapotba helyezheti a fizikai alapkonfiguráció biztosításához, és egy egyszerű, kifejező tervezési vezérlőkkel rendelkező, a művészi célok elérésére szolgáló, könnyű futtatókörnyezetet használ.

![Képernyőkép a Gears of War 4-ről, amely akusztikai voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>A Wave Physics használata az interaktív akusztika számára
A Ray-alapú akusztikai módszerek egyetlen forrás – figyelő Ray Cast használatával is ellenőrizhetők, a helyi jelenet kötetének becslésével. Ezek a technikák azonban megbízhatatlanok lehetnek, mert egy kavicsos occludes, mint a Boulder. A sugarak nem veszik figyelembe, hogy a hang meghajlítja az objektumok körét, ami a diffrakciós néven ismert jelenség. A Project akusztikai szimulációja ezeket a hatásokat a Wave-alapú szimulációk használatával rögzíti. Az eredmény sokkal kiszámítható és megbízhatóbb.

A Project akusztika kulcsfontosságú újítása az akusztikus szimulációk és a hagyományos hangtervezési fogalmak összevetése. A szimuláció eredményeit hagyományos hangalapú DSP-paraméterekre fordítja az elzáródás, a portál és a reverb számára. A tervező ezeket a fordítási folyamatokat használja. A Project Acoustics mögötti alapvető technológiákkal kapcsolatos további információkért látogasson el a [kutatási projekt lapra](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animáció, amely egy jeleneten keresztüli hullámos propagálás vízszintes 2D szeletét ábrázolja](media/wave-simulation.gif)

## <a name="setup"></a>Beállítás
A [Project akusztikai egység integrációja](unity-integration.md) a drag-and-drop, és tartalmaz egy Unity audio Engine beépülő modult. Fokozza az egység hangforrásának vezérlőit úgy, hogy egy Project C# akusztikai vezérlőt csatlakoztat egy adott hangobjektumhoz.

A [Project akusztikai Unreal Integration](unreal-integration.md) az Unreal-hez készült szerkesztő és game plugins, valamint egy Wwise keverő beépülő modult tartalmaz. Az egyéni hangösszetevő kibővíti az Wwise funkcióit az Unreal-en belül az élő akusztikai kialakítási vezérlőkkel. A tervezési vezérlők a keverő beépülő modul Wwise is elérhetők.

## <a name="workflow"></a>Munkafolyamat
* **Előre sütjük:** Kezdje a sütni beállításával, és válassza ki, hogy mely geometria reagál az akusztikara, például a könnyű tengelyek figyelmen kívül hagyásával. Ezután szerkessze az automatikus anyag-hozzárendeléseket, és válassza a navigációs területek lehetőséget a figyelő mintavételezéséhez. A reverb/Portal/Room zónákhoz nem tartozik kézi jelölés.
* **Sütni** Egy elemzési lépés helyileg fut, amely a voxelization és más geometriai elemzéseket végez a jeleneten a fenti beállítások alapján. Az eredmények a szerkesztőben láthatók a jelenet telepítőjének ellenőrzéséhez. A sütni-beküldéskor a rendszer az Voxel adatokat az Azure-ba küldi, és egy akusztikai játékbeli eszközt kap vissza.
* **Runtime** Töltse be az eszközt a szintjébe, és készen áll arra, hogy meghallgassa az akusztikai szintet. Megtervezheti az akusztikai működést a szerkesztőben, és részletesen használhatja a forráskódot. A vezérlőelemek a szint parancsfájljaiból is futtathatók.

## <a name="platforms"></a>Platformok
A Project akusztikai futtatókörnyezet beépülő modulja jelenleg a következő platformokon telepíthető:
* Windows
* Android
* Xbox One

## <a name="download"></a>Letöltés
* [Project akusztikai egység beépülő modul és minták](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projekt-akusztikai Unreal & Wwise beépülő modulok és minták](https://www.microsoft.com/download/details.aspx?id=58090)
  * Xbox bináris fájlok és támogatás esetén vegye fel velünk a kapcsolatot az alábbi regisztrációs űrlapon keresztül

## <a name="contact-us"></a>Kapcsolat
* [A Project akusztikai vitafórum és a jelentések kiadása](https://github.com/microsoft/ProjectAcoustics/issues)
* [Regisztráció a projekt akusztikai frissítéseinek fogadására](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>További lépések
* Próbálja ki a [projekt akusztikai útmutatóját az Unity](unity-quickstart.md) vagy az [Unreal](unreal-quickstart.md) számára
* Ismerje meg a [Project Acoustics](design-process.md) hangkialakítási filozófiáját

