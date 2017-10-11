---
title: "-Microsoft fenyegetések modellezése eszköz - Azure fenyegetések |} Microsoft Docs"
description: "A Microsoft fenyegetések modellezése eszköz fenyegetés kategória lapján kategóriák tartalmazó összes elérhetővé tett jönnek létre, fenyegetéseket."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 704f9995828866d4d2e4969e3aa922ed1e23c4ea
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft fenyegetések modellezése eszköz fenyegetések

A fenyegetések modellezése eszköz a Microsoft biztonsági fejlesztési életciklus (SDL) alapeleme. Lehetővé teszi a szoftverek fejlesztők azonosítása és potenciális biztonsági problémákat korai, amelyek viszonylag egyszerű és költséghatékony megoldásához. Emiatt nagy mértékben csökkenti a fejlesztési költségek. Is azt használható az eszköz szakértőivel nem biztonsági szem előtt, könnyítve modellezni az összes létrehozására, illetve elemzése fenyegetés modellek világos útmutatást megadásával.

> Látogasson el a  **[fenyegetések modellezése eszköz](./azure-security-threat-modeling-tool.md)**  való kezdjen neki még ma!

A fenyegetések modellezése eszköz segít választ bizonyos kaphat, például az alábbi megfelelően:

* Hogyan lehet módosítani egy támadó a hitelesítési adatok?
* Mi az a hatás, ha egy támadó tudja olvasni a felhasználói profil adatait?
* Mi történik, ha nincs hozzáférése a felhasználói profil adatbázis?

## <a name="stride-model"></a>Modell STRIDE

Jobb súgót hegyes kérdéseket az ilyen típusú megfogalmazásához, Microsoft használja a STRIDE modell, amely különböző típusú fenyegetések kategorizálja és egyszerűbbé teszi az általános biztonsági beszélgetéseket.

| Kategória | Leírás |
| -------- | ----------- |
| **-Címek hamisítását** | Magában foglalja a érvénytelenül elérése és egy másik felhasználói hitelesítési adatok, például felhasználónév és jelszó használata |
| **Illetéktelen módosítása** | Magában foglalja az adatok rosszindulatú módosítását. Ilyen például a jogosulatlan végzett módosításokat állandó, többek között, amelyek azt tranzakciós két számítógép között egy nyitott hálózatokon, például az interneten keresztül egy adatbázist, és vegye figyelembe a módosítást az adatok használatban |
| **Megtagadás** | Felhasználók, akik megtagadja egy művelet végrehajtása nélkül más felek számára bármely olyan módon, ellenkező esetben igazolásához társított – például a felhasználó szabálytalan műveletet hajt egy rendszer, amely nem rendelkezik a képes nyomon követni a tiltott operatív. A nem megtagadás számláló repudiation fenyegetések rendszert jelenti. A felhasználó, aki egy elemet a későbbi szoftvervásárlások Előfordulhat például, a cikk megérkezésekor aláírásához. A szállító aláírt fogadását módon használhatja, hogy a felhasználó kapta meg a csomag tanúsítás |
| **Információk felfedése** | Magában foglalja a elérhetővé tegyék az egyéni felhasználók számára, aki nem jogosult az elérésére elvileg információkat – például a felhasználók lehetőségét, amelyet nem kaptak hozzáférést a fájl olvasásához, vagy olvassa el az adatokat átvitel közben két számítógép között egy behatoló képességét |
| **Szolgáltatásmegtagadás** | Szolgáltatásmegtagadási (DoS-) támadások szolgáltatás megtagadása érvényes felhasználók – például azáltal, hogy a webkiszolgáló, átmenetileg nem érhető el vagy nem használható. Meg kell védeni ellen DoS fenyegető veszélyek egyszerűen rendelkezésre állására, és a megbízhatóság javításához bizonyos típusú |
| **Jogok kiterjesztése** | Egy nem rendszerjogosultságú felhasználói privilegizált hozzáférést kap, és ezáltal a megfelelő hozzáférési jogosultsággal hibát okoz, vagy szüntesse meg a teljes rendszer rendelkezik. Jogosultság fenyegetések illetéktelen közé tartoznak az ilyen helyzetek, amelyben a támadó rendelkezik hatékonyan követhessék minden rendszer védelmekkel és a megbízható rendszert illeti, valóban veszélyes helyzet részét képezik |

## <a name="next-steps"></a>Következő lépések

Folytassa a  **[fenyegetések modellezése eszköz azok mérséklési](./azure-security-threat-modeling-tool-mitigations.md)**  további Azure-ral ezek a fenyegetések mérséklésére vonatkozó különböző módszereit.