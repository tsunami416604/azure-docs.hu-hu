---
title: A projekt akusztikája – áttekintés
titlesuffix: Azure Cognitive Services
description: A Project Acoustics egy akusztikai motor a 3D interaktív élményekhez, amely a sült hullámfizika szimulációt interaktív tervezési vezérlőkkel integrálja.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351148"
---
# <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?
A Project Acoustics egy hullámakusztikamotor a 3D interaktív élményekhez. Ez modellek hullám hatások, mint az elzáródás, akadály, portaling és visszhanghatások összetett jelenetek nélkül kézi zóna jelölés vagy CPU intenzív raytracing. Ez is magában foglalja a játék motor és audio middleware integráció. A Project Acoustics filozófiája hasonló a statikus megvilágításhoz: részletes fizikát sütni offline, hogy fizikai alapvonalat biztosítson, és használjon könnyű futásidőt kifejező tervezési vezérlőkkel, hogy megfeleljen a művészi céloknak a virtuális világ akusztikája számára.

![Képernyőkép a Gears of War 4-ből, amely enikókusz voxeleket mutat](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Hullámfizika használata interaktív akusztikához
A sugáralapú akusztikai módszerek egyetlen forrás-figyelő sugárleadott sugárzással ellenőrizhetik az elzáródást, vagy a helyi jelenet térfogatának néhány sugarlal történő becslésével meghajthatják az elzáródást. De ezek a technikák megbízhatatlanok lehetnek, mert egy kavics elzár, mint egy szikla. A sugarak nem veszik figyelembe, ahogy a hang a tárgyak körül hajlik, ezt a jelenséget diffrakciónak nevezik. A Project Acoustics szimulációja ezeket a hatásokat egy hullámalapú szimulációval rögzíti. Az akusztika kiszámíthatóbb, pontosabb és zökkenőmentesebb.

A Project Acoustics legfontosabb újítása, hogy a valódi hanghullám-alapú akusztikus szimulációt a hagyományos hangtervezési koncepciókkal párosítsa. Ez fordítja szimulációs eredmények et hagyományos audio DSP paraméterek elzáródás, portaling és zengetés. A tervező vezérlőket használ a fordítási folyamathoz. A Project Acoustics mögött megtalálható alapvető technológiákról a [kutatási projekt oldalán](https://www.microsoft.com/en-us/research/project/project-triton/)talál további információt.

![Vízszintes 2D hullámterjedési szeletet megjelenítő animáció egy jeleneten keresztül](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Videó bemutató a GDC 2019-ből (~30 min)
[![Projekt akusztikavideó](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Kattintson a videó lejátszásához")

## <a name="setup"></a>Telepítés
[Project Acoustics Unity integráció](unity-integration.md) drag-and-drop, és tartalmaz egy Unity audio motor plugin. Bővítse a Unity hangforrás-vezérlőket úgy, hogy a Project Acoustics C# vezérlőelemet minden egyes hangobjektumhoz csatlakoztatja.

[Project Acoustics Unreal integráció](unreal-integration.md) magában foglalja a szerkesztő és a játék dugó az Unreal, és a Wwise mixer plugin. Az egyéni audiokomponens ekként bővíti az Unreal on-n belül a jól ismert Wwise funkciókat az élő akusztikai tervezési vezérlőkkel. Design ellenőrzések is ki vannak téve a Wwise a mixer plugin.

## <a name="workflow"></a>Munkafolyamat
* **Elősütni:** Kezdje a sütés beállításával, és válassza ki, hogy melyik geometria reagál az akusztikára, például a fénytengelyek figyelmen kívül hagyásával. Ezután szerkesztse az automatikus anyag-hozzárendeléseket, és válassza ki a navigációs területeket a figyelőmintavétel irányításához. Nincs manuális jelölés a zengetésre/portálra/helyiségzónára.
* **Sütés:** Az elemzési lépés helyileg fut, amely voxelization és egyéb geometriai elemzés a jelenet alapján a fenti kiválasztás. Az eredmények a szerkesztőben láthatók a jelenet beállításának ellenőrzéséhez. A sütni beküldött, voxel adatok at küld ki az Azure-ba, és kapsz vissza egy akusztikai játék eszköz.
* **Futási idő:** Töltse be az eszközt a szintjére, és készen áll arra, hogy meghallgassa az akusztikát a szintjén. Tervezze meg az akusztikát élőben a szerkesztőben a forrásonkénti részletes vezérlők használatával. A vezérlők szintalapú parancsfájlokból is vezérelhetők.

## <a name="runtime-platforms"></a>Futásidejű platformok
A Project Acoustics futásidejű beépülő moduljai jelenleg a következő platformokra telepíthetők:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Szerkesztő platformok
A Project Acoustics editor beépülő modul a következő platformokon érhető el:
* Windows
* MacOS (csak Unity)

## <a name="download"></a>Letöltés
* [Project Acoustics Unity plugin és minták](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Project Acoustics Unreal & Wwise dugó és minták](https://www.microsoft.com/download/details.aspx?id=58090)
  * Az Xbox bináris okait és egyéb támogatást, lépjen kapcsolatba velünk a [fórumon](https://github.com/microsoft/ProjectAcoustics/issues)keresztül .

## <a name="contact-us"></a>Kapcsolat
* [A Project Akusztikai vita és a problémajelentés](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>További lépések
* Próbálja ki a [Project Acoustics rövid útmutatóját az Unity](unity-quickstart.md) vagy az [Unreal](unreal-quickstart.md) számára
* Fedezze fel a [Project Acoustics hangtervezési filozófiáját](design-process.md)

