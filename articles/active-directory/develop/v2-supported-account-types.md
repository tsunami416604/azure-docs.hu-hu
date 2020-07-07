---
title: Támogatott fióktípus – Microsoft Identity platform | Azure
description: A célközönségek és az alkalmazások által támogatott fióktípus fogalmi dokumentációja
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d19381094e027bd567ffc503d32f9212ef56a948
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583101"
---
# <a name="supported-account-types"></a>Támogatott fióktípusok

Ez a cikk ismerteti, hogy a Microsoft Identity platform alkalmazásai milyen típusú fiókokat (más néven *célközönségeket*) támogatnak.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>A nyilvános felhőben lévő fiókok típusai

A Microsoft Azure nyilvános felhőben a legtöbb típusú alkalmazás bejelentkezhet a felhasználók bármelyik hallgatóságával:

- Üzletági (LOB) alkalmazások írásakor a saját szervezetében lévő felhasználókat is bejelentkezhet. Ezt az alkalmazást néha *egy bérlőnek*is nevezik.
- Ha Ön ISV-t használ, írhat egy olyan alkalmazást, amely bejelentkezik a felhasználók számára:

  - Bármely szervezeten belül. Ezt az alkalmazást több- *bérlős* webalkalmazásnak nevezzük. Néha azt is elolvashatja, hogy a felhasználók munkahelyi vagy iskolai fiókjaival aláírják a felhasználókat.
  - Munkahelyi vagy iskolai vagy személyes Microsoft-fiókjával.
  - Csak személyes Microsoft-fiókokkal.
    
- Ha a vállalattól a fogyasztótól származó alkalmazást ír, akkor a felhasználók a közösségi identitásokkal is bejelentkezhetnek Azure Active Directory B2C (Azure AD B2C) használatával.

## <a name="account-type-support-in-authentication-flows"></a>Fiók típusának támogatása a hitelesítési folyamatokban

Bizonyos típusú fiókok nem használhatók bizonyos hitelesítési folyamatokkal. Például asztali, UWP vagy Daemon-alkalmazásokban:

- A Daemon-alkalmazásokat csak az Azure AD-szervezetekkel lehet használni. Nincs értelme a Microsoft személyes fiókjainak kezeléséhez használt Daemon-alkalmazásokkal próbálkozni. A rendszergazdai beleegyező engedély soha nem lesz megadva.
- Az integrált Windows-hitelesítési folyamat csak munkahelyi vagy iskolai fiókkal (a szervezetben vagy bármely szervezetben) használható. Az integrált Windows-hitelesítés tartományi fiókokkal működik, és megköveteli, hogy a gépek tartományhoz legyenek csatlakoztatva, vagy az Azure AD csatlakoztatva legyen. Ez a folyamat nem értelmezi a személyes Microsoft-fiókokat.
- Az [erőforrás-tulajdonos jelszavának hitelesítő adatai](./v2-oauth-ropc.md) (username/Password) nem használhatók személyes Microsoft-fiókokkal. A személyes Microsoft-fiókok megkövetelik, hogy a felhasználó hozzájáruljon a személyes erőforrások eléréséhez az egyes bejelentkezési munkamenetekben. Ez az oka, hogy ez a viselkedés nem kompatibilis a nem interaktív folyamatokkal.
- Az eszköz kódjának folyamata nem működik személyes Microsoft-fiókokkal.

## <a name="account-types-in-national-clouds"></a>Az országos felhőkben lévő fiókok típusai

Az alkalmazások az [országos felhőkben](authentication-national-cloud.md)is bejelentkezhetnek a felhasználókba. A Microsoft személyes fiókjai azonban ezekben a felhőkben nem támogatottak. Ezért csökkenthető a támogatott fióktípus, ezek a felhők, a szervezet (egyetlen bérlő) vagy bármely szervezet (több-bérlős alkalmazások).

## <a name="next-steps"></a>További lépések

- További információ a [Azure Active Directory-beli bérletről](./single-and-multi-tenant-apps.md).
- További információ az [országos felhőkről](./authentication-national-cloud.md).
