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
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351148"
---
# <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?
A Project Acoustics egy Wave akusztikus motor a 3D interaktív élményekhez. Olyan hullám-effektusokat modellez, mint például az elzáródás, az elzáródás, a portál és a visszaverődés hatása az összetett jeleneteknél anélkül, hogy manuális zónát kellene megjelölni vagy a CPU-igényes raytracing. Emellett magában foglalja a Game Engine és a audio middleware integrációját is. A Project Acoustics filozófiája hasonló a statikus világításhoz: a részletes fizikát offline állapotba helyezheti a fizikai alapkonfiguráció biztosításához, és egy egyszerű, kifejező tervezési vezérlőkkel rendelkező, a virtuális világ akusztikai céljaihoz alkalmazkodó, könnyű kialakítást biztosít.

![Képernyőkép a Gears of War 4-ről, amely akusztikai voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>A Wave Physics használata az interaktív akusztika számára
A Ray-alapú akusztikai módszerek egyetlen forrás – figyelő Ray Cast használatával is ellenőrizhetők, a helyi jelenet kötetének becslésével. Ezek a technikák azonban megbízhatatlanok lehetnek, mert egy kavicsos occludes, mint a Boulder. A sugarak nem veszik figyelembe, hogy a hang meghajlítja az objektumok körét, ami a diffrakciós néven ismert jelenség. A Project akusztikai szimulációja ezeket a hatásokat a Wave-alapú szimulációk használatával rögzíti. Az akusztika sokkal kiszámítható, pontos és zökkenőmentes.

A Project akusztika kulcsfontosságú újítása a valós hanghullám-alapú akusztikus szimulációk és a hagyományos hangtervezési fogalmak összevetése. A szimuláció eredményeit hagyományos hangalapú DSP-paraméterekre fordítja az elzáródás, a portál és a reverb számára. A tervező ezeket a fordítási folyamatokat használja. A Project Acoustics mögötti alapvető technológiákkal kapcsolatos további információkért látogasson el a [kutatási projekt lapra](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animáció, amely egy jeleneten keresztüli hullámos propagálás vízszintes 2D szeletét ábrázolja](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Videós bemutató a GDC 2019 (~ 30 perc)
[@no__t – 1Project akusztikai videó](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Kattintson ide a videó lejátszásához")

## <a name="setup"></a>Beállítás
A [Project akusztikai egység integrációja](unity-integration.md) a drag-and-drop, és tartalmaz egy Unity audio Engine beépülő modult. Fokozza az egység hangforrásának vezérlőit úgy, hogy egy Project C# akusztikai vezérlőt csatlakoztat egy adott hangobjektumhoz.

A [Project akusztikai Unreal Integration](unreal-integration.md) az Unreal-hez készült szerkesztő és game plugins, valamint egy Wwise keverő beépülő modult tartalmaz. Az egyéni hangösszetevő kibővíti az Wwise funkcióit az Unreal-en belül az élő akusztikai kialakítási vezérlőkkel. A tervezési vezérlők a keverő beépülő modul Wwise is elérhetők.

## <a name="workflow"></a>Munkafolyamat
* **Előre sütjük:** Kezdje a sütni beállításával, és válassza ki, hogy mely geometria reagál az akusztikara, például a könnyű tengelyek figyelmen kívül hagyásával. Ezután szerkessze az automatikus anyag-hozzárendeléseket, és válassza a navigációs területek lehetőséget a figyelő mintavételezéséhez. A reverb/Portal/Room zónákhoz nem tartozik kézi jelölés.
* **Sütni** Egy elemzési lépés helyileg fut, amely a voxelization és más geometriai elemzéseket végez a jeleneten a fenti beállítások alapján. Az eredmények a szerkesztőben láthatók a jelenet telepítőjének ellenőrzéséhez. A sütni-beküldéskor a rendszer az Voxel adatokat az Azure-ba küldi, és egy akusztikai játékbeli eszközt kap vissza.
* **Runtime** Töltse be az eszközt a szintjébe, és készen áll arra, hogy meghallgassa az akusztikai szintet. Megtervezheti az akusztikai működést a szerkesztőben, és részletesen használhatja a forráskódot. A vezérlőelemek a szint parancsfájljaiból is futtathatók.

## <a name="runtime-platforms"></a>Futtatókörnyezeti platformok
A Project akusztikai futtatókörnyezet beépülő modulja jelenleg a következő platformokon telepíthető:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Szerkesztői platformok
A Project akusztikai szerkesztő beépülő modulja a következő platformokon érhető el:
* Windows
* MacOS (csak Unity)

## <a name="download"></a>Letöltés
* [Project akusztikai egység beépülő modul és minták](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projekt-akusztikai Unreal & Wwise beépülő modulok és minták](https://www.microsoft.com/download/details.aspx?id=58090)
  * Xbox bináris fájlok és egyéb támogatás esetén vegye fel velünk a kapcsolatot a [fórumon](https://github.com/microsoft/ProjectAcoustics/issues)keresztül.

## <a name="contact-us"></a>Kapcsolat
* [A Project akusztikai vitafórum és a jelentések kiadása](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>További lépések
* Próbálja ki a [projekt akusztikai útmutatóját az Unity](unity-quickstart.md) vagy az [Unreal](unreal-quickstart.md) számára
* Ismerje meg a [Project Acoustics hangkialakítási filozófiáját](design-process.md)

