---
title: Mi az a Csoportalapú licencelés – Azure Active Directory |} A Microsoft Docs
description: További információ az Azure Active Directory Csoportalapú licencelést, beleértve annak működését és ajánlott eljárásokat.
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
ms.openlocfilehash: a455f0e1959ad496a04f531cc0c3ece240c757ad
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987153"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Mit jelent a Csoportalapú licencelés az Azure Active Directoryban?

A Microsoft fizetős felhőszolgáltatásainak, például az Office 365, az Enterprise Mobility + Security, a Dynamics 365 és más hasonló termékek használatához licencre van szükség. A licenceket olyan felhasználókhoz kell rendelni, akiknek hozzáférésre van szükségük ezekhez a szolgáltatásokhoz. A licencek kezeléséhez a felügyeleti portálok (Office vagy Azure) valamelyikét vagy PowerShell-parancsmagokat használhatnak a rendszergazdák. Az Azure Active Directory (Azure AD) a Microsoft felhőszolgáltatásainak identitáskezelését támogató, alapul szolgál infrastruktúra. Az Azure AD tárolja a felhasználók licenc-hozzárendelési állapotával kapcsolatos információkat.

A licencek hozzárendelése eddig csak az egyes felhasználók szintjén volt lehetséges, ami megnehezíthette a kezelést nagy számú felhasználó esetén. A rendszergazdáknak például gyakran kellett összetett PowerShell-szkriptet írniuk a felhasználói licencek hozzáadásához vagy eltávolításához a szervezeti változásoknak megfelelően, például amikor felhasználók csatlakoztak a szervezethez vagy egy részleghez, illetve elhagyták azokat. A szkript egymástól független hívásokat kezdeményezett a felhőszolgáltatás felé.

Ezen kihívások kezelésére biztosítja az Azure AD a csoportalapú licencelést. A terméklicenceket hozzárendelheti egy csoporthoz. Ezután az Azure AD gondoskodik arról, hogy megtörténjen a licencek hozzárendelése a csoport mindegyik tagjához. A csoporthoz csatlakozó új tagokhoz is hozzárendeli a megfelelő licenceket. Amikor a felhasználók elhagyják a csoportot, eltávolítja a licenceket. Ez a licencelési felügyelet szükségtelenné teszi, hogy a PowerShell használatával automatizálja a licencek kezelését, hogy a szervezet és a részleg struktúrájának változásait felhasználónkénti alapon tükrözze.

## <a name="licensing-requirements"></a>Licencelési követelmények
A csoport alapú licencelés használatához a következő licencek egyike szükséges:

- Fizetős vagy próbaverziós előfizetés prémium szintű Azure AD P1 és újabb rendszerekhez

- Fizetős vagy próbaverziós Office 365 Enterprise E3 vagy Office 365 a3 vagy Office 365 GCC G3 vagy újabb verzió

### <a name="required-number-of-licenses"></a>Szükséges licencek száma
A licenccel rendelkező csoportok esetében az egyes egyedi tagokhoz is licenccel kell rendelkeznie. Noha nem kell hozzárendelni az a csoport minden tagját, legalább annyi licenccel kell rendelkeznie, hogy tartalmazza az összes tagot. Ha például olyan egyedi tagokkal 1 000 rendelkezik, akik a bérlőhöz tartozó licencelt csoportok részét képezik, legalább 1 000 licenccel kell rendelkeznie a licencszerződés teljesítéséhez.

## <a name="features"></a>Szolgáltatások

A csoportalapú licencelés fő funkciói az alábbiak:

- A licencek bármilyen biztonsági csoporthoz hozzárendelhetők az Azure AD-ben. Biztonsági csoportok is szinkronizálható a helyszíni, az Azure AD Connect használatával. Biztonsági csoportokat közvetlenül az Azure AD-ben is létrehozhat (csak felhőalapú csoportok), vagy pedig automatikusan, az Azure AD dinamikus csoportok kezelésére szolgáló funkciójával.

- Egy terméklicencnek csoporthoz való hozzárendelésekor a rendszergazda letilthat szolgáltatáscsomagokat a termékben. Ezt a hozzárendelést általában akkor kell elvégezni, ha a szervezet még nem áll készen a termékben található szolgáltatás használatának megkezdésére. A rendszergazda hozzárendelheti például az Office 365-öt egy részleghez, de átmenetileg letilthatja a Yammer szolgáltatást.

- A felhasználószintű licencelést igénylő Microsoft-felhőszolgáltatások mindegyike támogatott. Ez a támogatás magában foglalja az Office 365-termékek, a Enterprise Mobility + Security és a Dynamics 365.

- A csoport alapú licencelés jelenleg csak a Azure Portalon érhető [](https://portal.azure.com)el. Ha elsődlegesen más felügyeleti portálokat használ a felhasználók és a csoportok felügyeletéhez, például a [Microsoft 365 felügyeleti](https://admin.microsoft.com)központot, továbbra is megteheti. A licencek csoportszintű kezeléséhez azonban az Azure Portalt kell használnia.

- Az Azure AD automatikusan kezeli a csoporttagság változásaiból eredő licencmódosításokat. A licencmódosítások általában a tagság változása után pár percen belül érvénybe lépnek.

- A felhasználók egyszerre több, eltérő licencszabályzattal rendelkező csoport tagjai is lehetnek. A felhasználók emellett rendelkezhetnek a csoportokon kívül, közvetlenül hozzárendelt licencekkel is. A megjelenő felhasználóállapot a hozzárendelt termék- és szolgáltatáslicencek mindegyikének összességét tükrözi. A felhasználó ugyanolyan licenc van hozzárendelve, több forrásból származó, a licenc csak egyszer fognak használni.

- Bizonyos esetekben nem rendelhető licenc a felhasználókhoz. Lehetséges például, hogy nincs elegendő rendelkezésre álló licenc a bérlőben, vagy ütköző szolgáltatások lettek egy időben hozzárendelve. A rendszergazdák hozzáférhetnek azokkal a felhasználókkal kapcsolatos információkhoz, akik esetén az Azure AD nem tudta teljes mértékben feldolgozni a csoportlicenceket. Ezután korrekciós műveleteket hajthatnak végre az információk alapján.

## <a name="your-feedback-is-welcome"></a>Szívesen vesszük visszajelzését!

Ha visszajelzést vagy szolgáltatási kérést használ, ossza meg velünk az [Azure ad felügyeleti Fórumával](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>További lépések

A csoportalapú licencelés segítségével folytatott licenckezelés egyéb forgatókönyveivel kapcsolatos további tudnivalókért tekintse át az alábbi témaköröket:

* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](../users-groups-roles/licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](../users-groups-roles/licensing-groups-migrate-users.md)
* [Felhasználók az Azure Active Directoryban Csoportalapú licencelést használ terméklicencek közötti migrálása](../users-groups-roles/licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)
