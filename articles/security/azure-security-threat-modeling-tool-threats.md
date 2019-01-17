---
title: Észlelt – Microsoft Threat Modeling Tool – Azure |} A Microsoft Docs
description: A Microsoft Threat Modeling Tool Threat kategória lapon kategóriák tartalmazó összes elérhetővé tett jönnek létre a fenyegetéseket.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: fd7c5fd929163dc7fcd22fbb045dee0fe3070394
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359034"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool fenyegetések

A Threat Modeling Tool a Microsoft biztonsági fejlesztési életciklus (SDL) alapeleme. Azonosíthatja és korai, csökkentheti a potenciális biztonsági problémákat, amelyek viszonylag egyszerű és költséghatékony oldja meg a szoftver tervezők lehetővé teszi. Ennek eredményeképpen jelentősen csökkenti a fejlődés a teljes költség. Ezenkívül alakítottuk ki az eszköz nem biztonsági szakértői szem előtt, könnyíti threat modellezési minden fejlesztő számára világos útmutatóval szolgál azáltal, hogy létrehozásával és enyhítési elemzése.

> Látogasson el a **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** és vágjon bele most!

A Threat Modeling Tool segítségével bizonyos kérdésre, például az alábbiak egyikével:

* Hogyan módosíthatja a hitelesítési adatokat a támadó?
* Mi a hatás, ha egy támadó tudja olvasni a felhasználói profil adatait?
* Mi történik, ha a hozzáférés megtagadva a felhasználói profil adatbázis?

## <a name="stride-model"></a>Modell STRIDE

Jobb súgó az ilyen típusú hegyes kérdések megfogalmazása, a Microsoft a STRIDE modellt, amely kategorizálja a különböző típusú fenyegetéseket, és egyszerűbbé teszi az általános biztonsági-beszélgetések használja.

| Kategória | Leírás |
| -------- | ----------- |
| **Címek hamisítása** | Magában foglalja a jogosulatlan hozzáférés és a egy másik felhasználói hitelesítési adatok, például a felhasználónév és jelszó használata |
| **Illetéktelen módosítása** | Magában foglalja az adatok rosszindulatú módosítását. Példák az illetéktelen módosítások állandó adatait, például, amely a nyílt hálózaton, például az interneten két számítógép között biztosítani egy adatbázis és az adatok az megváltoztatására tárolt |
| **Repudiation** | Hozzárendelt felhasználók, akik a megtagadási művelet végrehajtása nélkül más felek kellene bármilyen módon igazolásához ellenkező esetben – például egy felhasználó végez, amely lehetővé teszi a tiltott műveletek nyomon követése nem rendelkezik rendszer szabálytalan műveletet. Letagadhatatlanság jelenti, hogy a számláló letagadhatóság fenyegetések. A felhasználó, aki vásárol egy elem Előfordulhat például, az elem megérkezésekor aláírásához. A szállító használhatja aláírt fogadását, hogy a felhasználó kapott-e a csomag bizonyítékaként |
| **Információk felfedése** | Magában foglalja a kitettség feltételezhetően nem férhetnek hozzá, akik információit – például a felhasználók képessége beolvasni egy fájlt, amely nem adtak hozzá, vagy egy esetleges képességét olvassa el a két számítógép között átvitt adatok |
| **Szolgáltatásmegtagadás** | Szolgáltatásmegtagadási (DoS-) támadásokat szolgáltatás letiltása érvényes felhasználóknak – például azáltal, hogy a webkiszolgáló, átmenetileg nem érhető el vagy használhatatlanná válik. Bizonyos típusú, egyszerűen a rendszer rendelkezésre állásának és megbízhatóságának javítása érdekében szolgáltatásmegtagadási fenyegetések elleni védelmét kell beállítani |
| **Jogok kiterjesztése** | Egy nem rendszerjogosultságú felhasználói emelt szintű hozzáférést kap, és ezáltal a megfelelő hozzáférési jogosultsággal veszélyezteti, vagy szüntesse meg a teljes rendszer rendelkezik. Jogosultságszint-emelés jogosultság fenyegetések, amelyben a támadó rendelkezik hatékonyan követhessék összes rendszer védelem ilyen helyzetek közé tartozik, és megbízható magának a rendszernek, valóban egy vészhelyzetet részévé válik |

## <a name="next-steps"></a>További lépések

Lépjen tovább a **[Threat Modeling eszköz megoldások](./azure-security-threat-modeling-tool-mitigations.md)** , csökkentheti a fenyegetéseket az Azure-ral különböző módjait.