---
title: Támogatott fiókok típusai – Microsoft-identitásplatform | Azure
description: Az alkalmazások ban található közönségek és támogatott fióktípusok koncepcionális dokumentációja
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de90c81f56b6017b2d53ecbfb2c400a4c9f05d81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262281"
---
# <a name="supported-account-types"></a>Támogatott fióktípusok

Ez a cikk ismerteti, hogy milyen fióktípusok (néha elnevezett közönségek) támogatottak az alkalmazásokban.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Támogatott fióktípusok a Microsoft Identity platformalkalmazásokban

A Microsoft Azure nyilvános felhőben a legtöbb típusú alkalmazás bármilyen közönséggel bejelentkezhet a felhasználókhoz:

- Ha egy üzletági (LOB) alkalmazást ír, bejelentkezhet a saját szervezetében lévő felhasználókba. Az ilyen alkalmazásokat néha **egyetlen bérlőnek nevezik.**
- If you're an ISV, you can write an application which signs-in users:

  - Bármelyik szervezetben. Egy ilyen alkalmazás neve egy **több-bérlős** webalkalmazás. Előfordulhat, hogy a felhasználók munkahelyi vagy iskolai fiókkal jelentkeznek be.
  - A munkahelyi vagy iskolai vagy személyes Microsoft-fiókjukkal.
  - Csak személyes Microsoft-fiókkal.
    > [!NOTE]
    > A Microsoft-identitásplatform jelenleg csak akkor támogatja a személyes Microsoft-fiókokat, ha regisztrál egy alkalmazást **munkahelyi, iskolai vagy Microsoft-személyes fiókokhoz,** majd korlátozza az alkalmazás `https://login.microsoftonline.com/consumers`kódjába való bejelentkezést az Azure AD-jogosultság megadásával az alkalmazás létrehozásakor, például .

- Ha üzleti tevékenységet ír a fogyasztóknak, az Azure AD B2C használatával is bejelentkezhet a felhasználóka közösségi identitásukkal.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Bizonyos hitelesítési folyamatok nem támogatják az összes fióktípust

Egyes fióktípusok nem használhatók bizonyos hitelesítési folyamatokesetén. Asztali, UWP- vagy démonalkalmazásokban például:

- A démonalkalmazások csak az Azure Active Directory-szervezetekkel használhatók. Nincs értelme démonalkalmazásokat használni a Microsoft személyes fiókjainak manipulálására (a rendszergazdai hozzájárulás soha nem adható meg).  
- Az integrált Windows-hitelesítési folyamat csak munkahelyi vagy iskolai fiókokkal (a szervezetben vagy bármely szervezetben) használható. Az integrált Windows-hitelesítés tartományi fiókokkal működik, és megköveteli, hogy a gépek tartományhoz csatlakozzanak, vagy az Azure AD-hez. Ennek a folyamatnak nincs értelme a személyes Microsoft-fiókok esetében.
- Az [erőforrás-tulajdonosjelszó-támogatás](./v2-oauth-ropc.md) (felhasználónév/jelszó) nem használható személyes Microsoft-fiókokkal. A személyes Microsoft-fiókok megkövetelik, hogy a felhasználó minden bejelentkezési munkamenetben hozzájáruljon a személyes erőforrások eléréséhez. Ezért ez a viselkedés nem kompatibilis a nem interaktív folyamatokkal.
- Az eszközkód-folyamat még nem működik a személyes Microsoft-fiókokkal.

## <a name="supported-account-types-in-national-clouds"></a>Támogatott fióktípusok a nemzeti felhőkben

 Az alkalmazások a nemzeti felhőkben is bejelentkezhetnek a [felhasználókba.](authentication-national-cloud.md) A Microsoft személyes fiókjait azonban ezek a felhők nem támogatják (ezeknek a felhőknek a definíciója szerint). Ezért a támogatott fióktípusok csökkennek, ezek a felhők, a szervezet (egybérlős) vagy bármely szervezet (több-bérlős alkalmazások).

## <a name="next-steps"></a>További lépések

- További információ a Tenancy szolgáltatásról [az Azure Active Directoryban](./single-and-multi-tenant-apps.md)
- További információ a [nemzeti felhőkről](./authentication-national-cloud.md)
