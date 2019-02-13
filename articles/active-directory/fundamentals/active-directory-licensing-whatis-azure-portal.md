---
title: Mi az a Csoportalapú licencelés – Azure Active Directory |} A Microsoft Docs
description: További információ az Azure Active Directory Csoportalapú licencelést, beleértve annak működését és ajánlott eljárásokat.
services: active-directory
keywords: Az Azure AD licencelése
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a6f7eaa91faabfea7bb951eb9863ffecd2f5499
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182374"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?

A Microsoft fizetős felhőszolgáltatásainak, például az Office 365, az Enterprise Mobility + Security, a Dynamics 365 és más hasonló termékek használatához licencre van szükség. A licenceket olyan felhasználókhoz kell rendelni, akiknek hozzáférésre van szükségük ezekhez a szolgáltatásokhoz. A licencek kezeléséhez a felügyeleti portálok (Office vagy Azure) valamelyikét vagy PowerShell-parancsmagokat használhatnak a rendszergazdák. Az Azure Active Directory (Azure AD) a Microsoft felhőszolgáltatásainak identitáskezelését támogató, alapul szolgál infrastruktúra. Az Azure AD tárolja a felhasználók licenc-hozzárendelési állapotával kapcsolatos információkat.

A licencek hozzárendelése eddig csak az egyes felhasználók szintjén volt lehetséges, ami megnehezíthette a kezelést nagy számú felhasználó esetén. A rendszergazdáknak például gyakran kellett összetett PowerShell-szkriptet írniuk a felhasználói licencek hozzáadásához vagy eltávolításához a szervezeti változásoknak megfelelően, például amikor felhasználók csatlakoztak a szervezethez vagy egy részleghez, illetve elhagyták azokat. A szkript egymástól független hívásokat kezdeményezett a felhőszolgáltatás felé.

Ezen kihívások kezelésére biztosítja az Azure AD a csoportalapú licencelést. A terméklicenceket hozzárendelheti egy csoporthoz. Ezután az Azure AD gondoskodik arról, hogy megtörténjen a licencek hozzárendelése a csoport mindegyik tagjához. A csoporthoz csatlakozó új tagokhoz is hozzárendeli a megfelelő licenceket. Amikor a felhasználók elhagyják a csoportot, eltávolítja a licenceket. Ez a licencelési felügyeleti kiküszöböli a automatizálása a PowerShell-lel Licenckezelés változásainak a szervezet és a részlegszintű struktúra felhasználónkénti alapon.

## <a name="licensing-requirements"></a>Licencelési követelményeket
A következő licencek használata a Csoportalapú licencelés egyikét kell rendelkeznie:

- Az Azure AD alapszintű fizetős vagy próba-előfizetést

- Fizetős vagy próba kiadás, az Office 365 nagyvállalati E3 vagy Office 365 a3-as vagy újabb verzióhoz

### <a name="required-number-of-licenses"></a>Szükséges licencek száma
Minden olyan licenc hozzárendelése csoportok esetén is rendelkezik licenccel minden egyedi tag. Nem kell minden egyes tagja a csoport egy licencet hozzárendelni, amíg minden tagot legalább elegendő licenccel kell rendelkeznie. Például ha 1000 egyedi tagjai, akik a saját bérlőjében licenccel rendelkező csoportok részét képezik, meg kell rendelkeznie legalább 1000 licenceket felel meg a licencszerződést.

## <a name="features"></a>Szolgáltatások

A csoportalapú licencelés fő funkciói az alábbiak:

- A licencek bármilyen biztonsági csoporthoz hozzárendelhetők az Azure AD-ben. Biztonsági csoportok is szinkronizálható a helyszíni, az Azure AD Connect használatával. Biztonsági csoportokat közvetlenül az Azure AD-ben is létrehozhat (csak felhőalapú csoportok), vagy pedig automatikusan, az Azure AD dinamikus csoportok kezelésére szolgáló funkciójával.

- Egy terméklicencnek csoporthoz való hozzárendelésekor a rendszergazda letilthat szolgáltatáscsomagokat a termékben. Ez a hozzárendelés általában történik, ha a szervezet még nem áll készen a termékben szolgáltatás használatának megkezdéséhez. A rendszergazda hozzárendelheti például az Office 365-öt egy részleghez, de átmenetileg letilthatja a Yammer szolgáltatást.

- A felhasználószintű licencelést igénylő Microsoft-felhőszolgáltatások mindegyike támogatott. Ez a támogatás magában foglalja a minden Office 365 termékek, Enterprise Mobility + Security és Dynamics 365.

- A csoportalapú licencelés jelenleg csak az [Azure Portalon](https://portal.azure.com) érhető el. Ha a felhasználók és csoportok kezelésének elsődleges eszköze egy másik felügyeleti portál, például az Office 365 portál, folytathatja annak használatát. A licencek csoportszintű kezeléséhez azonban az Azure Portalt kell használnia.

- Az Azure AD automatikusan kezeli a csoporttagság változásaiból eredő licencmódosításokat. A licencmódosítások általában a tagság változása után pár percen belül érvénybe lépnek.

- A felhasználók egyszerre több, eltérő licencszabályzattal rendelkező csoport tagjai is lehetnek. A felhasználók emellett rendelkezhetnek a csoportokon kívül, közvetlenül hozzárendelt licencekkel is. A megjelenő felhasználóállapot a hozzárendelt termék- és szolgáltatáslicencek mindegyikének összességét tükrözi. A felhasználó ugyanolyan licenc van hozzárendelve, több forrásból származó, a licenc csak egyszer fognak használni.

- Bizonyos esetekben nem rendelhető licenc a felhasználókhoz. Lehetséges például, hogy nincs elegendő rendelkezésre álló licenc a bérlőben, vagy ütköző szolgáltatások lettek egy időben hozzárendelve. A rendszergazdák hozzáférhetnek azokkal a felhasználókkal kapcsolatos információkhoz, akik esetén az Azure AD nem tudta teljes mértékben feldolgozni a csoportlicenceket. Ezután korrekciós műveleteket hajthatnak végre az információk alapján.

## <a name="your-feedback-is-welcome"></a>Szívesen vesszük visszajelzését!

Ha visszajelzést vagy funkciókérést, megoszthatja velünk a kapcsolatot [az Azure AD rendszergazdai fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>További lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](../users-groups-roles/licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](../users-groups-roles/licensing-groups-migrate-users.md)
* [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](../users-groups-roles/licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)
