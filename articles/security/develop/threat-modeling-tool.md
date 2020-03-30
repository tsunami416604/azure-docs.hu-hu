---
title: A Microsoft Fenyegetésmodellezési Eszköz áttekintése – Azure
description: A Microsoft Fenyegetésmodellezési eszköz áttekintése, amely az eszköz első lépéseivel kapcsolatos információkat tartalmazza, beleértve a fenyegetésmodellezési folyamatot is.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 02/16/2017
ms.openlocfilehash: 0d800102b6f6ff77944a2b625d3bcecef69c1ee9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548747"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsoft fenyegetésmodellező eszköz

A fenyegetésmodellezési eszköz a Microsoft biztonsági fejlesztési életciklusának (SDL) központi eleme. Lehetővé teszi a szoftvertervezők számára a potenciális biztonsági problémák korai azonosítását és enyhítését, amikor azok viszonylag egyszerűek és költséghatékonyak. Ennek eredményeképpen jelentősen csökkenti a teljes fejlesztési költséget. Az eszközt nem biztonsági szakértőkszem előtt tartva terveztük, így a fenyegetésmodellezés tetszővé válik minden fejlesztő számára azáltal, hogy egyértelmű útmutatást nyújt a fenyegetésmodellek létrehozásához és elemzéséhez. 

Az eszköz lehetővé teszi, hogy bárki:

* Kommunikáció a rendszereik biztonsági felépítéséről
* Elemezze ezeket a terveket a lehetséges biztonsági problémákegy bevált módszertan
* Biztonsági problémák megoldásának javaslata és kezelése

Íme néhány eszközkezelési képesség és újítás, csak hogy néhányat említsünk:

* **Automatizálás:** Útmutatás és visszajelzés a modell rajzolása
* **LÉPÉS/elem:** A fenyegetések és a mérséklések irányított elemzése
* **Jelentés:** Biztonsági tevékenységek és tesztelés az ellenőrzési szakaszban
* **Egyedi módszertan:** Lehetővé teszi a felhasználók számára a fenyegetések jobb megjelenítését és megértését
* **Fejlesztőknek és szoftverközpontúnak tervezve:** számos megközelítés az eszközökre vagy a támadókra összpontosít. A szoftverre koncentrálunk. Olyan tevékenységekre építünk, amelyeket minden szoftverfejlesztő és építész ismer – például képekrajzolása a szoftverarchitektúrájukhoz
* **A tervezés elemzésére összpontosítva:** A "fenyegetésmodellezés" kifejezés egy követelményre vagy egy tervezési elemzési technikára utalhat. Néha a kettő összetett keverékére utal. A Microsoft SDL megközelítése fenyegetés modellezés egy koncentrált tervezési elemzési technika

## <a name="next-steps"></a>További lépések

Az alábbi táblázat fontos hivatkozásokat tartalmaz a fenyegetésmodellezési eszköz höz:

| Lépés  | Leírás                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [A fenyegetésmodellező eszköz letöltése](https://aka.ms/threatmodelingtool)                                |
| **2** | [Olvassa el az első lépések útmutatóját](threat-modeling-tool-getting-started.md)    |
| **3** | [Ismerkedjen meg a funkciókkal](threat-modeling-tool-feature-overview.md)   |
| **4** | [További információ a létrehozott fenyegetéskategóriákról](threat-modeling-tool-threats.md)   |
| **5** | [A létrehozott fenyegetések relevandulásának keresése](threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>Források

Íme néhány régebbi cikkek még mindig fontos a fenyegetés modellezés ma:

* [Cikk a fenyegetésmodellezés fontosságáról](https://docs.microsoft.com/archive/msdn-magazine/2009/january/security-briefs-getting-started-with-the-sdl-threat-modeling-tool)
* [A megbízható számítástechnika által közzétett képzés](https://www.microsoft.com/download/details.aspx?id=16420)

Nézze meg, mit tett néhány Threat Modeling Tool szakértő:

* [Fenyegetések kezelője](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi Biztonsági Blog](https://simoneonsecurity.com/)
