---
title: Az alkalmazások (célközönség) – a Microsoft identity platform támogatott fiókok
description: Célközönség és a támogatott fióktípus esetében az alkalmazások fogalmi dokumentációja
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d751e28859a3ae1104b28c76d0edfedb2a859eb4
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406507"
---
# <a name="supported-account-types"></a>Támogatott fióktípusok

Ez a cikk bemutatja, milyen fiókok típusok (néha nevű célközönséggel) által támogatott alkalmazások

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Támogatott fiókok típusai a Microsoft Identity platform alkalmazások

A Microsoft Azure nyilvános felhőben legtöbb típusú alkalmazásokat bármilyen célközönség-bejelentkezhetnek a felhasználók:

- Ha szeretne írni egy üzletági (LOB) alkalmazás, bejelentkezhet felhasználók a saját szervezetében. Ilyen alkalmazás néha nevű **egybérlős**.
- Ha Ön független Szoftverszállító, írhat egy alkalmazást jelentkezik be felhasználók:

  - Bármely szervezet. Ilyen alkalmazás nevű egy **több-bérlős** webes alkalmazás. Néha elolvasta fogja, hogy bejelentkezik a munkahelyi vagy iskolai fiókjaikat felhasználók.
  - A saját munkahelyi vagy iskolai vagy személyes Microsoft-fiókjával.
  - Csak személyes Microsoft-fiókkal.
    > [!NOTE]
    > Jelenleg a Microsoft identity platform támogatja a személyes Microsoft-fiókok csak az alkalmazás regisztrációja **munkahelyi, iskolai vagy személyes Microsoft-fiókokba**, és a megadásával, majd jelentkezzen be az alkalmazás kódjában korlátozása Azure AD-szolgáltatót, mint például az alkalmazás összeállításakor `https://login.onmicrosoftonline.com/consumers`.

- Ha egy üzleti felhasználók alkalmazáshoz, is bejelentkezhet az Azure AD B2C használatával a közösségi identitású felhasználók.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Bizonyos hitelesítési folyamatok minden fióktípus esetében nem támogatott.

Bizonyos hitelesítési folyamatok néhány fióktípus esetében nem használható. Például az asztali, UWP-alkalmazások vagy démon alkalmazások:

- Démon alkalmazások csak az Azure Active Directory együtt használható. Próbálják használni démon alkalmazások kezelésére a Microsoft a személyes fiókok (a rendszergazdai jóváhagyás soha nem kapnak) nem értelmezhető.  
- (A vagy a szervezet bármely szervezet) a munkahelyi vagy iskolai fiókok csak használhatja az integrált Windows-hitelesítési folyamat. Valóban integrált Windows-hitelesítés együttműködik a tartományi fiókokat, és a gépet a tartományhoz vagy Azure AD-hez szükséges. Ez a folyamat személyes Microsoft-Accounts nem értelmezhető.
- A [erőforrás tulajdonosának jelszava Grant](./v2-oauth-ropc.md) személyes Microsoft-fiókok (felhasználónév és jelszó), nem használható. Sőt személyes Microsoft-fiókok szükséges, hogy a felhasználó jóváhagy, minden egyes bejelentkezési munkamenet személyes erőforrásokhoz való hozzáférés. Ezért, ez a viselkedés nem kompatibilis az nem interaktív folyamatokat.
- Eszköz kódfolyamat még nem működik a személyes Microsoft-fiókok.

## <a name="supported-account-types-in-national-clouds"></a>A nemzeti felhőkben támogatott fióktípusok

 Alkalmazások is bejelentkezhet a felhasználói [országos felhők](authentication-national-cloud.md). Azonban személyes Microsoft-fiókok nem támogatják ezeket a felhőben lévő (ezek a felhők változatát). Ezért ezek a felhők, a szervezet (egybérlős) vagy bármely szervezet (több-bérlős alkalmazások) esetében a támogatott fióktípusok csökkentik.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Bérlős az Azure Active Directoryban](./single-and-multi-tenant-apps.md)
- Tudjon meg többet [országos felhők](./authentication-national-cloud.md)