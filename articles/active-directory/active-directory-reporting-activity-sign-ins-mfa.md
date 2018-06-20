---
title: Azure Portal-beli többtényezős hitelesítés jelentéskészítési referenciája | Microsoft Docs
description: Azure Portal-beli többtényezős hitelesítés jelentéskészítésével kapcsolatos referenciainformációk
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 03/16/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e2d48eeaa98fd60841e2f25c09eb46bb848f78df
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221896"
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Azure Portal-beli többtényezős hitelesítés jelentéskészítési referenciája

Az [Azure Portalon](https://portal.azure.com) az [Azure Active Directory (Azure AD) jelentéskészítési funkciójával](active-directory-reporting-azure-portal.md) minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

A [bejelentkezési tevékenységek jelentései](active-directory-reporting-activity-sign-ins.md) a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel, köztük a többtényezős hitelesítés (MFA) használatával kapcsolatos információkat tartalmaznak. 

Az MFA-adatokból betekintést nyerhet, hogy a többtényezős hitelesítés hogyan működik a szervezetben. Ezáltal a következőkhöz hasonló kérdésekre kaphat választ: 

- Felmerült a bejelentkezés során MFA? 

- Hogyan végezte el a felhasználó az MFA-hitelesítést? 

- Miért nem tudta a felhasználó elvégezni az MFA-hitelesítést?  

Az összes bejelentkezési adat összesítésével behatóbban megismerheti az MFA működését a szervezetben. Az adatok segítségével a következőkhöz hasonló kérdésekre kaphat választ: 

- Hány felhasználó esetében merül fel az MFA?  

- Hány felhasználó nem tudta elvégezni az MFA-hitelesítést? 

- Melyek a gyakori MFA-problémák, amelyekkel a végfelhasználók találkoznak? 


Az adatok az Azure Portalon és a [jelentéskészítési API-n](active-directory-reporting-api-getting-started-azure-portal.md) keresztül érhetőek el. 


## <a name="data-structure"></a>Adatszerkezet


Az MFA bejelentkezési tevékenységeinek jelentéseiben a következő adatokhoz fér hozzá:

**MFA szükséges:** Kötelező-e az MFA a bejelentkezéshez vagy sem. Az MFA felhasználónkénti MFA vagy feltételes hozzáférés miatt, illetve egyéb okokból lehet szükséges. A lehetséges értékek: `Yes` és `No`.

**MFA hitelesítési módszer:** A módszer, amellyel a felhasználó végrehajtotta az MFA-hitelesítést. Lehetséges értékek: 

- Szöveges üzenet 

- Mobilalkalmazás-értesítés 

- Telefonhívás (hitelesítési telefon) 

- Mobilalkalmazásbeli ellenőrző kód 

- Telefonhívás (irodai telefon) 

- Telefonhívás (alternatív hitelesítési telefon) 

**MFA-hitelesítés részletei:** A telefonszám kitakarva, például: +X XXXXXXXX64. 

**MFA eredménye:** További részletek az MFA teljesítésével vagy megtagadásával kapcsolatban:

- Az MFA teljesítése esetén az oszlop a teljesítés módjával kapcsolatos további információkat tartalmaz. 

- Az MFA megtagadása esetén az oszlop a megtagadás okát tartalmazza. A lehetséges értékek: `Satisfied` és `Denied`. 

Az alábbi szakasz az MFA-eredménymező lehetséges sztringértékeit sorolja fel.

## <a name="status-strings"></a>Állapot-sztringek

Jelen szakasz az MFA-eredményt jelző állapotsztring lehetséges értékeit sorolja fel.

### <a name="satisfied-status-strings"></a>Teljesítési állapotok sztringjei


- Azure Multi-Factor Authentication

    - elvégezve a felhőben 

    - lejárt a bérlőn konfigurált szabályzatok miatt 

    - regisztráció felkínálva 

    - a tokenben lévő jogcím alapján teljesült 

    - külső szolgáltató által biztosított jogcím alapján teljesült 

    - erős hitelesítéssel teljesült 

    - kihagyva, mivel a végrehajtott folyamat a Windows-közvetítő bejelentkezési folyamata volt 

    - alkalmazásjelszó használata miatt kihagyva 

    - a hely miatt kihagyva 

    - regisztrált eszköz használata miatt kihagyva 
    
    - megjegyzett eszköz használata miatt kihagyva 

    - sikeresen teljesítve 

- Átirányítva külső szolgáltatóhoz többtényezős hitelesítésre 

 
### <a name="denied-status-strings"></a>Megtagadási állapot sztringjei

Azure Multi-Factor Authentication megtagadva; 

- hitelesítés folyamatban 

- duplikált hitelesítési kísérlet 

- túl sokszor lett hibás kód megadva 

- érvénytelen hitelesítés 

- érvénytelen mobilalkalmazásbeli ellenőrző kód 

- hibás konfiguráció 

- a telefonhívás üzenetrögzítőre kapcsolt 

- a telefonszám formátuma érvénytelen 

- szolgáltatáshiba 

- a felhasználó telefonja nem érhető el 

- a mobilalkalmazás-értesítés nem küldhető el az eszközre 

- a mobilalkalmazás-értesítés nem küldhető el 

- a felhasználó visszautasította a hitelesítést 

- a felhasználó nem reagált a mobilalkalmazás-értesítésre 

- a felhasználónak nincsenek regisztrált ellenőrzési módszerei 

- a felhasználó hibás kódot adott meg 

- a felhasználó hibás PIN-kódot adott meg 

- a felhasználó sikeres hitelesítés nélkül megszakította a telefonhívást 

- a felhasználó blokkolva van 

- a felhasználó soha nem adta még meg az ellenőrzőkódot 

- a felhasználó nem található 
 
- az ellenőrzőkód már volt használva 



## <a name="next-steps"></a>További lépések

További információk: [Jelentéskészítés az Azure Active Directoryban](active-directory-reporting-azure-portal.md).




























