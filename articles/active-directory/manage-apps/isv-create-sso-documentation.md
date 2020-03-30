---
title: Az alkalmazás hoz& közzétesz egyszeri bejelentkezési dokumentációt
description: Útmutató független szoftverszállítóknak az Azure Active Directoryval való integrációhoz
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232282"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Egyszeri bejelentkezési dokumentáció létrehozása és közzététele az alkalmazáshoz   

## <a name="documentation-on-your-site"></a>Dokumentáció a webhelyen

A könnyű elfogadás jelentős tényező a vállalati szoftverekre vonatkozó döntésekben. A könnyen követhető, egyértelmű dokumentáció támogatja ügyfeleit az örökbefogadási út során, és csökkenti a támogatási költségeket. A Microsoft több ezer szoftvergyártóval együttműködve látta, hogy mi működik.

Javasoljuk, hogy a webhelyén található dokumentáció legalább a következő elemeket tartalmazza.

* Bevezetés az SSO funkcióba

  * Támogatott protokollok

  * Verzió és Termékváltozat

  * A támogatott identitásszolgáltatók listája dokumentációs hivatkozásokkal

* A kérelem licencelési adatai

* Szerepköralapú hozzáférés-vezérlés az egyszeri szolgáltatás konfigurálásához

* Az SSO konfigurációs lépései

  * SamL felhasználói felületkonfigurációs elemei a szolgáltatótól várt értékekkel

  * Az identitásszolgáltatóknak továbbítandó szolgáltatói adatok

* Ha OIDC/OAuth

  * Az üzleti indoklással való hozzájáruláshoz szükséges engedélyek listája

* Tesztelési lépések a kísérleti felhasználók számára

* Hibaelhárítási információk, például hibakódok és üzenetek

* Támogatási mechanizmusok az ügyfelek számára

## <a name="documentation-on-the-microsoft-site"></a>Dokumentáció a Microsoft webhelyén

Amikor felsorolja az alkalmazást az Azure Active Directory alkalmazástárban, amely az alkalmazást is közzéteszi az Azure Marketplace-en, a Microsoft dokumentációt hoz létre a kölcsönös ügyfeleink számára, amely ismerteti a lépésről lépésre történő folyamatot. Itt láthat [szamutat](https://aka.ms/appstutorial)egy példát. Ez a dokumentáció a katalógusba küldött beküldésealapján jön létre, és könnyen frissítheti, ha módosítja az alkalmazást a GitHub-fiókjával.

## <a name="next-steps"></a>Következő lépések

[Az alkalmazás listázása az Azure AD alkalmazásgalériában](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)