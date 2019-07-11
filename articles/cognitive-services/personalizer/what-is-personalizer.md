---
title: Mi a Personalizer?
titleSuffix: Azure Cognitive Services
description: Personalizer egy olyan felhőalapú API szolgáltatás, amely lehetővé teszi, hogy válassza ki a legoptimálisabb megjeleníthető valós idejű viselkedésük képzés a felhasználók számára.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e5781af44732782936e1e1a87bf70bd4a9d4804d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722287"
---
# <a name="what-is-personalizer"></a>Mi a Personalizer?

Az Azure Personalizer egy felhőalapú API-szolgáltatás, amellyel a valós idejű viselkedésükből tanulva kiválaszthatja a felhasználók számára megjelenítendő legjobb élményt.

* A felhasználók és a tartalom információt biztosítanak, és a felső műveletet a megjelenítéséhez a felhasználók számára. 
* Nincs szükség, amelyek megtisztítják és adatok címke Personalizer használata előtt.
* Ha Ön kényelmesen, küldjön visszajegyzést Personalizer. 
* Valós idejű elemzés megtekintése. 
* Egy nagyobb data science erőfeszítés részeként Personalizer használatával meglévő kísérletek ellenőrzése.

## <a name="how-does-personalizer-work"></a>Hogyan működik a Personalizer?

Personalizer gépi tanulási modelleket használ környezetben legmagasabb rangú művelet felderítésére. Az ügyfélalkalmazás lehetséges olyan művelet, amely információkat tudhat meg róluk; listáját tartalmazza. és információt, amely előfordulhat, hogy a felhasználó, eszköz és egyéb információkat tartalmaznak. Personalizer elvégzendő műveletet határozza meg. Miután az ügyfélalkalmazásban használ a kiválasztott művelet, biztosít visszajelzés a Personalizer ellenszolgáltatás pontszámot formájában. A visszajelzési ciklus befejezését követően a Personalizer automatikusan frissíti a saját jövőbeli holtversenyben használt modell.

## <a name="how-do-i-use-the-personalizer"></a>Hogyan használhatom a Personalizer?

![Melyik videó megjelenítése a felhasználónak választania Personalizer használatával](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Válassza ki az alkalmazás személyre felületet nyújt.
1. Hozzon létre, és a személyre szabás szolgáltatás konfigurálása az Azure Portalon
1. SDK használata Personalizer hívja meg információkat (_funkciók_) a felhasználók számára, és a tartalom (_műveletek_). Nem kell tiszta, adjon meg adatokat feliratú Personalizer használata előtt. 
1. Az ügyfélalkalmazásban megjelenítése a felhasználónak Personalizer által kiválasztott.
1. SDK használatával visszajelzést Personalizer, amely azt jelzi, ha a felhasználó kiválasztott Personalizer a műveletet. Ez egy _pontszám díjazza_, általában 1 és 1 között.
1. Elemzés megtekintése a rendszer működését, és hogyan az adatok lehetővé teszi, hogy személyre szabása kiértékelheti, hogy az Azure Portalon.

## <a name="where-can-i-use-personalizer"></a>Hol használhatom Personalizer?

Például az ügyfélalkalmazás is adhat a Personalizer:

* Személyre szabhatja, milyen cikk kiemelt hírek-webhelyen.    
* Egy webhelyen Active elhelyezési optimalizálása.
* Vásárlási webhelyek egy személyre szabott "ajánlott elem" megjelenítése.
* Javasoljuk, például egy adott fénykép alkalmazandó szűrők felhasználói felületi elemekre.
* Válassza ki a felhasználói szándékot tisztázása, vagy a javasolt művelet egy csevegőrobot választ.
* Mi a felhasználó kell tennie egy üzleti folyamat következő lépéseként vonatkozó javaslatok sorrendet.

## <a name="personalization-for-developers"></a>A fejlesztők személyre szabása

Personalizer szolgáltatás két API-kkal rendelkezik:

* Küldje el (_funkciók_) a felhasználók és a tartalom (_műveletek_) személyre szabásához. A top művelet personalizer válaszként.
* Visszajelzés küldése Personalizer információ arról, hogy a rangsorolás működött egy általában a 0 és 1 közötti számot (az előző szakaszban említett 1 és 1). 

![Alapszintű eseménysorozatát személyre szabása](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Hozzon létre egy visszacsatolási hurokba kerülnek aC#](csharp-quickstart-commandline-feedback-loop.md)
* [Használja az interaktív bemutató](https://personalizationdemo.azurewebsites.net/)
