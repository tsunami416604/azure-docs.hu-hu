---
title: Mi az a Project Acoustics?
titlesuffix: Azure Cognitive Services
description: A Unity-hez készült Project Acoustics beépülő modul hangelnyelést, visszhangot és térbeli hanghatást biztosít a VR-t és hagyományos képernyőket megcélzó projektekhez.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 8305eca478854eeff29268a86e4e49b697261ca2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868259"
---
# <a name="what-is-project-acoustics"></a>Mi az a Project Acoustics?
A Unity-hez készült Project Acoustics beépülő modul hangelnyelést, visszhangot és térbeli hanghatást biztosít a VR-t és hagyományos képernyőket megcélzó projektekhez. Lehetőséget biztosít egy olyan játékakusztika megtervezésére, amely a tervező elképzeléseit egy fizikán alapuló hullámszimulációra alapozza.

## <a name="why-use-acoustics-in-virtual-environments"></a>Miért használjuk az akusztikát virtuális környezetben?
Az emberek audiovizuális jelek segítségével értelmezik az őket körülvevő világot. Az akusztika és a térbeli hangok kombinációja jobban elősegíti, hogy a felhasználó elmerüljön a virtuális világokban. Az itt ismertetett akusztikai eszköz virtuális világokat elemez valósághű akusztikai szimulációk létrehozásához, és támogatja a szimulációt követő tervezési folyamatot. Az elemzés magába foglalja a geometriát és a világ minden egyes felületének anyagát is. A szimuláció olyan paramétereket is tartalmaz, mint például az érkezési irány (portaling), a visszaverődés ereje, az elhalkulás ideje, valamint a hangelnyelés és az akadályozó hatások.

## <a name="how-does-this-approach-to-acoustics-work"></a>Hogyan működik az akusztikának ez a megközelítése?
A rendszer a virtuális világ offline számításain alapul, amely így sokkal összetettebb szimulációt tesz lehetővé, mint ha az elemzés futásidőben történne. Az offline számítás egy keresési táblázatot hoz létre az akusztikai paraméterekből. A tervező meghatározza a paraméterekre futásidőben alkalmazandó szabályokat. A szabályok finomhangolásával hiperrealisztikus hatások érhetők el a magas érzelmi intenzitás érdekében, illetve a több háttérzajjal rendelkező jelenetekhez.

## <a name="design-process-comparison"></a>Tervezési folyamatok összehasonlítása
A Project Acoustics beépülő modul egy új akusztikatervezési folyamatot támogat a Unity-jelenetekben. Az új tervezési folyamat bemutatásához hasonlítsuk össze az akusztika egy manapság népszerű megközelítésével.

### <a name="typical-approach-to-acoustics-today"></a>Az akusztika manapság jellemző megközelítése
Az akusztika manapság jellemző megközelítése szerint visszhang-hangerőt rajzolunk:

![Tervezési nézet](media/reverbZonesAltSPace2.png)

Ezután finomhangoljuk az egyes zónákhoz tartozó paramétereket:

![Tervezési nézet](media/TooManyReverbParameters.png)

Végül a sugárkövetési logika hozzáadásával megfelelő elnyelést és akadályszűrést kaphatunk a jelenethez, az útkeresési logikával pedig elvégezhető az érkezési irány kezelése. Egy ilyen kód növelheti a futásidő költségét. Emellett probléma adódhat a sarkok körüli simasággal, és a szabálytalan alakú jelenetek esetében az élekkel is.

### <a name="an-alternative-approach-with-physics-based-design"></a>Egy alternatív megközelítés a fizikán alapuló tervezés révén
A Unity-hez készült Project Acoustics beépülő modul által biztosított módszerrel egy statikus jelenet formáját és anyagait adja meg. Mivel a jelenet voxelizált, és a folyamat nem használ sugárkövetést, nem kell egyszerűsített vagy hézagmentes akusztikai hálót megadnia. Szintén nem szükséges a jelenetet visszhang-hangerőkkel megjelölni. A beépülő modul feltölti a jelenetet a felhőbe, ahol az fizikán alapuló hullámszimulációt alkalmaz. Az eredmény keresési táblázatként lesz integrálva a projektbe, és módosítható az esztétikai vagy játékmenetbeli hatások szerint.

![Tervezési nézet](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>Követelmények
* Unity 2018.2 vagy újabb verzió az akusztikai elemek előzetes kialakításához és Unity 5.2 vagy újabb verzió a hangtervezéshez és üzembe helyezéshez
* Windows 64-bit Unity szerkesztő
* Azure Batch-előfizetés az akusztikai elemek előzetes kialakításához
* A Unity Scripting Runtime-nak „.NET 4.x-nek megfelelőnek ” kell lennie.

## <a name="platform-support"></a>Platformtámogatás
* Windows asztali rendszer (x86 és AMD64)
* Windows UWP (x86, AMD64 és ARM)
* Android (x86 és ARM64)

## <a name="download"></a>Letöltés
Ha szeretné kiértékelni az akusztikai beépülő modult, regisztráljon [itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) az előzetes tervezői verzióra.

## <a name="next-steps"></a>További lépések
* Tudjon meg többet a [tervezés folyamatáról](design-process.md)
* Első lépések: [az akusztika integrálása a Unity-projektbe](getting-started.md)

