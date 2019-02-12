---
title: Ismerkedés az Azure DevTest Labs használatával
description: Ez a cikk az elsődleges együttesével biztosít az Azure DevTest Labs szolgáltatásban és a két általános elérési segítségével megkezdheti a szolgáltatás használatát a szervezetben.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: christianreddington
manager: femila
editor: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: chredd
ms.reviewer: anthdela,juselph
ms.openlocfilehash: dfb73778a9d30ba50e69bff6414381235ad8b1d1
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005233"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Ismerkedés az Azure DevTest Labs használatával
Ha úgy dönt, hogy ismerje meg a DevTest Labs, vannak-e két előre – általános elérési méretezett üzembe helyezés a koncepció igazolása vs. 

A **üzembe helyezési horizontálisan** hét/hónap áttekintése és üzembe helyezése a DevTest Labs a teljes vállalat, amely rendelkezik, több száz vagy ezer főig megjelölésű tervezési áll. 

A **fogalom telepítési megvalósíthatósági** szervezeti érték egy csoportot a koncentrált erőfeszítés összpontosít. Úgy gondolja, hogy méretezett központi telepítés csábító lehet, amíg a megközelítést általában többször a koncepció igazolása fogalom beállítás sikertelen. Ezért azt javasoljuk, hogy kezdhetik, ismerje meg az első csapat, ismételje meg a sémának két-három további csapatok számára, és majd szerzett ismeretek alapján vertikálisan telepítés megtervezését. Sikeres a koncepció igazolása javasoljuk, hogy válasszon ki egy vagy két csapat, és a saját forgatókönyveinek (a fejlesztési környezet és tesztelési környezetek), dokumentálni az aktuális használati esetek, és üzembe helyezése a DevTest Labs. 

## <a name="scenarios"></a>Forgatókönyvek
Nincsenek a DevTest Labs megvalósításának három alapvető forgatókönyv: 

- Fejlesztői asztalok
- Tesztelési környezetek
- Labs/training/hackathon

## <a name="developer-desktops"></a>Fejlesztői asztalok
A fejlesztők gyakran rendelkeznek fejlesztői gépek különböző projektek különböző követelmények vonatkoznak. A DevTest Labs szolgáltatással a fejlesztők hozzáférhet a leggyakoribb forgatókönyvek igényeinek megfelelően konfigurált igény szerinti virtuális gépeket. DevTest Labs a következő előnyöket nyújtja:

- Szervezet is nyújt a közös fejlesztési és tesztelési környezetek különböző csapatokkal konzisztencia biztosítása
- A fejlesztők gyorsan üzembe helyezhet igény szerinti fejlesztői gépek
- A fejlesztők kioszthatja erőforrásait önkiszolgáló módon előfizetés-szintű engedélyek nélkül
- Informatikai vagy rendszergazdák előre adhatja meg a hálózati topológiát, és a fejlesztők közvetlenül kihasználhatják, egyszerű és intuitív módon anélkül, hogy semmilyen különleges hozzáférési
- A fejlesztők egyszerűen testreszabhatja fejlesztési gépeik igény szerint
- A rendszergazdák biztosításával, hogy szabályozhatja költségeit:
    - A fejlesztők nem olvasható be a fejlesztési szükségük több virtuális gépet
    - Virtuális gépek leállnak, le, amikor nincs használatban
    - Mindezzel egy részét az adott munkaterhelés konkrét Virtuálisgép-példányok mérete

## <a name="test-environments"></a>Tesztelési környezetek
Létrehozása és tesztelési környezetek kezelése a vállalaton belül akkor is egy jelentős erőfeszítésekre van szükség. A DevTest Labs szolgáltatással tesztelési környezetek is lehet könnyen létrehozott, frissített vagy lettek duplikálva, amely lehetővé teszi a csapatok egy teljes körűen konfigurált környezet eléréséhez szükséges. Ebben a forgatókönyvben a DevTest Labs a következő előnyöket nyújtja:

- Tesztelőknek újrahasznosítható sablonok és összetevők használatával Windows és Linux-környezetek gyors létrehozásával tesztelheti a legfrissebb verziójának alkalmazását.
- Tesztelőknek is növelheti a terhelésteszt több tesztügynök kiépítésével
- Rendszergazdák kapcsolódhatnak a labor létrehozása az Azure DevOps, DevOps-forgatókönyvekre engedélyezése
- A rendszergazdák biztosításával, hogy szabályozhatja költségeit:
    - Tesztelőknek szükségük több virtuális gépet nem lehet lekérni.
    - Virtuális gépek leállnak, le, amikor nincs használatban
    - Mindezzel egy részét az adott munkaterhelés konkrét Virtuálisgép-példányok mérete

## <a name="labs-workshops-trainings-and-hackathons"></a>Laborok, workshopok, betanítások és ötletbörzékhez  
Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet átmeneti tevékenységeket, például workshopokat, gyakorlati labormodulok, képzési vagy ötletbörzékhez nagyszerű tárolójaként szolgál. A szolgáltatás lehetővé teszi, hogy egy tesztkörnyezet, ahol megadhatja, hogy minden tanuló segítségével képzéshez azonosak, és elkülönített környezeteket hozhat létre egyéni sablonok létrehozása. Annak érdekében, hogy képzési környezetek elérhető minden tanuló, csak akkor, amikor szükség van rájuk, és tartalmaz elég erőforrások – például a virtuális gépek – szükséges a képzés-szabályzatok alkalmazása. Végül könnyedén megoszthatja a labor résztvevők, amely egyetlen kattintással eléréséhez. Miután az osztály lezárult, könnyebbé vált a törli a labor létrehozása és az összes kapcsolódó erőforrás.


## <a name="next-steps"></a>További lépések
Ez a sorozat következő cikkében talál: [A DevTest Labs-környezet](devtest-lab-guidance-scale.md)
