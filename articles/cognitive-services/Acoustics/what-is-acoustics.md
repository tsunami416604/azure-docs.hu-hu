---
title: Projekt Akusztika áttekintése
titlesuffix: Azure Cognitive Services
description: Projekt Akusztika egy Akusztika motor 3D interaktív élmény integrálása számlázásnak wave fizika szimuláció interaktív tervezési vezérlőkkel.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3d99ea5767c7b2e62f7228440201b4a9b6593b02
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136600"
---
# <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?
Projekt Akusztika egy olyan wave Akusztika motor 3D interaktív élmény. A modellek wave hatások, például az olyan összetett jelenetekhez diffraction, portaling és visszhang hatások manuális zóna jelölőnyelvi nélkül. Játékmotor és hang közbenső integrációs is tartalmaz. Projekt Akusztika filozófia hasonlít a statikus megvilágítási: os részletes fizika kapcsolat nélküli fizikai alapterv biztosít, és a egy egyszerűsített modult használja kifejező tervezési vezérlőkkel művészi céljai.

![Tervezési nézet](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Az interaktív Akusztika wave fizika használatával
Akusztika Ray-alapú módszerek használatával egyetlen forrás-figyelő ray típuskonverzió hangelnyelés keressen, és visszhang meghajtó helyi jelenet renderelése; kötetet, néhány sugarak becslés alapján. De ezek a technológiák megbízhatatlan lehet, mert egy pebble occludes, amennyire csak egy boulder. Sugarak egy diffraction néven ismert jelenség nem számla-objektumokat, egyébként eredményes kiegyenlíteni. Projekt Akusztika szimuláció következmények egy wave-alapú szimuláció használatával rögzíti. Több funkcióihoz kiszámítható és megbízható jön létre.

Projekt Akusztika kulcs innováció, hogy néhány akusztikai szimuláció hagyományos tervezni fogalmakat. Szimuláció eredményeit fordítja hangelnyelés, portaling és visszhang hagyományos hang DSP paramétereket. A Tervező vezérlők a fordítási folyamat használja. A projekt Akusztika mögött lévő alapvető technológiákat további információért látogasson el a [kutatási projektoldalon](https://www.microsoft.com/en-us/research/project/project-triton/).

![Tervezési nézet](media/wave-simulation.gif)

## <a name="setup"></a>Beállítás
[A projekt Akusztika Unity-integráció](unity-integration.md) fogd és vidd, és a Unity hang motor beépülő modult tartalmaz. Egy projekt Akusztika csatlakoztatásával a Unity hangforrásról vezérlők bővítésével C# vezérlők összetevő hang objektumokra.

[A projekt Akusztika Unreal integrációs](unreal-integration.md) Unreal, és a egy Wwise mixer beépülő modult tartalmaz a szerkesztő- és beépülő modulokat. Egyéni hang összetevő kibővíti az élő Akusztika ismerős Wwise-funkciót az Unreal tervezési szabályozza. Tervezési vezérlőket is az a Wwise érhetők el az a mixer beépülő modult.

## <a name="workflow"></a>Munkafolyamat
* **Üzem előtti bake:** Melyik geometriai kiválasztásával a bake beállításához kezdő válaszol Akusztika, szerint, például figyelmen kívül hagyja a világos tengelyek. Szerkesztheti, automatikus anyagi hozzárendeléseket és útmutató figyelő mintavételi navigációs területek kiválasztása. Nincs nem manuális jelölőnyelvi visszhang/portal/hely zónák.
* **Bake:** Egy elemző lépés helyileg fut, amely nem, voxelization és a többi geometriai elemzése a fenti beállítások alapján jelenet. Eredmények jelenik meg a szerkesztőben jelenet telepítés ellenőrzése. Bake beküldésekor voxel adatküldést az Azure-ba, és vissza Akusztika játék adategység.
* **Modul:** Az eszköz betöltse az szintjét, és készen áll a szinten Akusztika figyeli. Tervezés a Akusztika élő szerkesztőben részletes /-adatforrás-vezérlők használatával. A vezérlőelemek is a szolgáltatói scripting kell driven.

## <a name="platforms"></a>Platformok
A projekt Akusztika futásidejű beépülő modulok jelenleg a következő platformokon telepíthetők:
* Windows
* Android
* Xbox One

## <a name="download"></a>Letöltés
* [Projekt Akusztika beépülő modulok és minták](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
  * Az Xbox bináris fájljait és támogatás lépjen kapcsolatba velünk a regisztrációs űrlapot az alábbi
* [Projekt Akusztika fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [Iratkozzon fel a projekt Akusztika a frissítések fogadásához](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>További lépések
* Próbálja ki egy [a Unity Project Akusztika rövid](unity-quickstart.md) vagy [Unreal](unreal-quickstart.md)
* Fedezze fel a [hang-, projekt Akusztika tervezési filozófia](design-process.md)

