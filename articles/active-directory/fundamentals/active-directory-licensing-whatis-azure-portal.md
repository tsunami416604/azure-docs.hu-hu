---
title: Mi a csoportalapú licencelés - Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg az Azure Active Directory csoportalapú licencelését, beleértve annak működését és gyakorlati tanácsait.
services: active-directory
keywords: Az Azure AD licencelése
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eec896e6cccaf58c83820161c54c4f10cfadadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561561"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Mi a csoportalapú licencelés az Azure Active Directoryban?

A Microsoft fizetős felhőszolgáltatásainak, például az Office 365, az Enterprise Mobility + Security, a Dynamics 365 és más hasonló termékek használatához licencre van szükség. A licenceket olyan felhasználókhoz kell rendelni, akiknek hozzáférésre van szükségük ezekhez a szolgáltatásokhoz. A licencek kezeléséhez a felügyeleti portálok (Office vagy Azure) valamelyikét vagy PowerShell-parancsmagokat használhatnak a rendszergazdák. Az Azure Active Directory (Azure AD) a Microsoft felhőszolgáltatásainak identitáskezelését támogató, alapul szolgál infrastruktúra. Az Azure AD tárolja a felhasználók licenc-hozzárendelési állapotával kapcsolatos információkat.

A licencek hozzárendelése eddig csak az egyes felhasználók szintjén volt lehetséges, ami megnehezíthette a kezelést nagy számú felhasználó esetén. A rendszergazdáknak például gyakran kellett összetett PowerShell-szkriptet írniuk a felhasználói licencek hozzáadásához vagy eltávolításához a szervezeti változásoknak megfelelően, például amikor felhasználók csatlakoztak a szervezethez vagy egy részleghez, illetve elhagyták azokat. A szkript egymástól független hívásokat kezdeményezett a felhőszolgáltatás felé.

Ezen kihívások kezelésére biztosítja az Azure AD a csoportalapú licencelést. A terméklicenceket hozzárendelheti egy csoporthoz. Ezután az Azure AD gondoskodik arról, hogy megtörténjen a licencek hozzárendelése a csoport mindegyik tagjához. A csoporthoz csatlakozó új tagokhoz is hozzárendeli a megfelelő licenceket. Amikor a felhasználók elhagyják a csoportot, eltávolítja a licenceket. Ez a licenckezelés szükségtelenné teszi a licenckezelés automatizálását a PowerShellen keresztül, hogy felhasználónként tükrözze a szervezet és a részlegszerkezet változásait.

## <a name="licensing-requirements"></a>Licenckövetelmények
A csoportalapú licenccel való használathoz az alábbi licencek valamelyikének kell rendelkeznie:

- Fizetett vagy próba-előfizetés az Azure AD Premium P1 és újabb verziókhoz

- Az Office 365 Nagyvállalati E3 vagy az Office 365 A3 vagy az Office 365 GCC G3 vagy az Office 365 E3 for GCCH vagy az Office 365 E3 for DOD és újabb verzió fizetett vagy próbakiadása

### <a name="required-number-of-licenses"></a>Szükséges licencek száma
A licenchez rendelt csoportokhoz minden egyes egyedi taghoz licenccel is rendelkeznie kell. Bár nem kell a csoport minden egyes tagjának licencet hozzárendelnie, legalább annyi licenccel kell rendelkeznie, hogy az összes tagot bevonja. Ha például 1000 egyedi tagja van, akik a bérlőben licenccel rendelkező csoportok részét képezik, legalább 1000 licenccel kell rendelkeznie a licencszerződés teljesítéséhez.

## <a name="features"></a>Szolgáltatások

A csoportalapú licencelés fő funkciói az alábbiak:

- A licencek bármilyen biztonsági csoporthoz hozzárendelhetők az Azure AD-ben. A biztonsági csoportok a helyszíni, az Azure AD Connect használatával szinkronizálható. Biztonsági csoportokat közvetlenül az Azure AD-ben is létrehozhat (csak felhőalapú csoportok), vagy pedig automatikusan, az Azure AD dinamikus csoportok kezelésére szolgáló funkciójával.

- Egy terméklicencnek csoporthoz való hozzárendelésekor a rendszergazda letilthat szolgáltatáscsomagokat a termékben. Ez a hozzárendelés általában akkor történik meg, ha a szervezet még nem áll készen egy termékben található szolgáltatás használatának megkezdésére. A rendszergazda hozzárendelheti például az Office 365-öt egy részleghez, de átmenetileg letilthatja a Yammer szolgáltatást.

- A felhasználószintű licencelést igénylő Microsoft-felhőszolgáltatások mindegyike támogatott. Ez a támogatás magában foglalja az összes Office 365-terméket, az Enterprise Mobility + Security és a Dynamics 365 alkalmazást.

- A csoportalapú licencelés jelenleg csak az [Azure Portalon](https://portal.azure.com)keresztül érhető el. Ha elsősorban más felügyeleti portálokat használ a felhasználó- és csoportkezeléshez, például a [Microsoft 365 Felügyeleti központot,](https://admin.microsoft.com)ezt továbbra is megteheti. A licencek csoportszintű kezeléséhez azonban az Azure Portalt kell használnia.

- Az Azure AD automatikusan kezeli a csoporttagság változásaiból eredő licencmódosításokat. A licencmódosítások általában a tagság változása után pár percen belül érvénybe lépnek.

- A felhasználók egyszerre több, eltérő licencszabályzattal rendelkező csoport tagjai is lehetnek. A felhasználók emellett rendelkezhetnek a csoportokon kívül, közvetlenül hozzárendelt licencekkel is. A megjelenő felhasználóállapot a hozzárendelt termék- és szolgáltatáslicencek mindegyikének összességét tükrözi. Ha egy felhasználóhoz több forrásból származó licenc van hozzárendelve, a licenc csak egyszer kerül felhasználásra.

- Bizonyos esetekben nem rendelhető licenc a felhasználókhoz. Lehetséges például, hogy nincs elegendő rendelkezésre álló licenc a bérlőben, vagy ütköző szolgáltatások lettek egy időben hozzárendelve. A rendszergazdák hozzáférhetnek azokkal a felhasználókkal kapcsolatos információkhoz, akik esetén az Azure AD nem tudta teljes mértékben feldolgozni a csoportlicenceket. Ezután korrekciós műveleteket hajthatnak végre az információk alapján.

## <a name="your-feedback-is-welcome"></a>Szívesen vesszük visszajelzését!

Ha visszajelzései vagy funkciókérései vannak, ossza meg velünk [az Azure AD felügyeleti fórumon.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510)

## <a name="next-steps"></a>További lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](../users-groups-roles/licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](../users-groups-roles/licensing-groups-migrate-users.md)
* [Felhasználók áttelepítése a terméklicencek között az Azure Active Directory csoportalapú licencelésével](../users-groups-roles/licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../users-groups-roles/licensing-group-advanced.md)
* [Példák a Csoportalapú licenceléshez az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)
