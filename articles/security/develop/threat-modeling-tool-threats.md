---
title: Fenyegetések - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
description: A Microsoft Fenyegetésmodellező eszköz fenyegetéskategória-lapja, amely az összes elérhetőnek kitett fenyegetés kategóriáit tartalmazza.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727831"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>A Microsoft fenyegetésmodellező eszközfenyegetései

A fenyegetésmodellezési eszköz a Microsoft biztonsági fejlesztési életciklusának (SDL) központi eleme. Lehetővé teszi a szoftvertervezők számára a potenciális biztonsági problémák korai azonosítását és enyhítését, amikor azok viszonylag egyszerűek és költséghatékonyak. Ennek eredményeképpen jelentősen csökkenti a teljes fejlesztési költséget. Az eszközt nem biztonsági szakértőkszem előtt tartva terveztük, így a fenyegetésmodellezés tetszővé válik minden fejlesztő számára azáltal, hogy egyértelmű útmutatást nyújt a fenyegetésmodellek létrehozásához és elemzéséhez.

> Látogasson el a **[Threat Modeling Tool](threat-modeling-tool.md)** az induláshoz még ma!

A Fenyegetésmodellezési eszköz segítségével megválaszolhat bizonyos kérdéseket, például az alábbiakat:

* Hogyan módosíthatja a támadó a hitelesítési adatokat?
* Milyen hatással van, ha a támadó képes olvasni a felhasználói profil adatait?
* Mi történik, ha a felhasználói profil adatbázisához megtagadják a hozzáférést?

## <a name="stride-model"></a>STRIDE modell

Az ilyen típusú hegyes kérdések jobb megfogalmazása érdekében a Microsoft a STRIDE modellt használja, amely kategorizálja a különböző típusú fenyegetéseket, és leegyszerűsíti az általános biztonsági beszélgetéseket.

| Kategória | Leírás |
| -------- | ----------- |
| **Hamisítás** | Magában foglalja egy másik felhasználó hitelesítési adatainak, például felhasználónevének és jelszavának illegális elérését, majd felhasználását |
| **Hamisít** | Magában foglalja az adatok rosszindulatú módosítását. Ilyenek például az állandó adatok – például az adatbázisban tároltadatok – jogosulatlan módosításai, valamint az adatok módosítása, ahogy azok két számítógép között egy nyílt hálózaton, például az interneten keresztül áramlanak. |
| **Megtagadás** | Olyan felhasználókkal társítva, akik megtagadják a művelet et anélkül, hogy más felek bármilyen módon bizonyítanák az ellenkezőjét – például a felhasználó illegális műveletet hajt végre egy olyan rendszerben, amely nem képes nyomon követni a tiltott műveleteket. A megtagadás hiánya arra utal, hogy a rendszer képes fellépni a megtagadási fenyegetésekkel szemben. Előfordulhat például, hogy egy cikket megvásárló felhasználónak a kézhezvételkor alá kell írnia a cikket. A szállító ezután az aláírt nyugtát használhatja bizonyítékként arra, hogy a felhasználó megkapta a csomagot |
| **Információhoz való hozzáférés** | Magában foglalja az információk olyan személyeknek való kitettségét, akiknek nem kellene hozzáférniük – például a felhasználók azon képességét, hogy elolvassák azt a fájlt, amelyhez nem kaptak hozzáférést, vagy a behatoló azon képességét, hogy adatokat olvasson két számítógép között. |
| **Szolgáltatásmegtagadás** | A szolgáltatásmegtagadási (DoS) támadások megtagadják az érvényes felhasználók kiszolgálását – például azáltal, hogy egy webkiszolgálót ideiglenesen elérhetetlenné vagy használhatatlanná tesznek. A rendszer rendelkezésre állásának és megbízhatóságának javítása érdekében védelmet kell nyújt bizonyos típusú DoS-fenyegetések ellen. |
| **Jogosultság emelése** | A jogosultsággal nem rendelkező felhasználók privilegizált hozzáférést kapnak, és így megfelelő hozzáféréssel rendelkeznek a teljes rendszer feltöréséhez vagy megsemmisítéséhez. A jogosultsági veszélyek magasszintűvé válása közé tartoznak azok a helyzetek, amikor a támadó hatékonyan behatolt az összes rendszervédelembe, és a megbízható rendszer részévé vált, ami valóban veszélyes helyzet. |

## <a name="next-steps"></a>További lépések

Folytassa **[a fenyegetésmodellezési eszköz kockázatcsökkentési,](threat-modeling-tool-mitigations.md)** hogy megtudja, hogyan lehet csökkenteni ezeket a fenyegetéseket az Azure-ral.