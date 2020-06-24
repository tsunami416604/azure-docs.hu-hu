---
title: Az SSO engedélyezése több-bérlős alkalmazások esetében
description: Útmutató független szoftvergyártók számára az Azure Active Directoryval való integráláshoz
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763278"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Egyszeri bejelentkezés engedélyezése a több-bérlős alkalmazáshoz  

Ha a vásárlás vagy előfizetés keretében más vállalatok is használhatják az alkalmazást, az alkalmazás elérhetővé válik az ügyfelek számára a saját Azure-bérlőn belül. Ezt nevezzük több-bérlős alkalmazás létrehozásakor. A koncepció áttekintését lásd: több [-bérlős alkalmazások az Azure-ban](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) és [Az Azure Active Directory-ban való bérlet](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Mi az egyszeri bejelentkezés?

Az egyszeri bejelentkezés (SSO) biztonsági és kényelmi funkciókat biztosít, amikor a felhasználók Azure Active Directory és más identitások használatával jelentkeznek be az alkalmazásokba. Ha egy alkalmazás egyszeri bejelentkezésre van engedélyezve, a felhasználóknak nem kell külön hitelesítő adatokat megadniuk az alkalmazás eléréséhez. Az egyszeri bejelentkezés teljes körű magyarázata. [Lásd: egyszeri bejelentkezés a Azure Active Directory lévő alkalmazásokba](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Miért érdemes engedélyezni az egyszeri bejelentkezést az alkalmazásban?

Számos előnnyel jár az egyszeri bejelentkezés engedélyezése a több-bérlős alkalmazásokban. Ha engedélyezi az egyszeri bejelentkezést az alkalmazáshoz:

* Az alkalmazás az Azure Marketplace-en is megtekinthető, ahol az alkalmazást Azure Active Directory több millió Szervezete is felhasználhatja.
  * Lehetővé teszi, hogy az ügyfelek gyorsan konfigurálhatják az alkalmazást az Azure AD-vel.

* Az alkalmazás az Office 365 App Gallery, az Office 365 app Launcher és a Microsoft Search on Office.com

* Az alkalmazás az Microsoft Graph REST API használatával férhet hozzá a Microsoft Graph által elérhetővé vált felhasználói hatékonyságot eredményező adatokhoz.

* A támogatási költségeket csökkentheti ügyfelei számára.
  * A kölcsönös ügyfeleinknek szóló, az Azure AD-csapattal együtt készített, alkalmazásspecifikus dokumentáció megkönnyíti a bevezetést.
  * Ha az egyszeri bejelentkezés engedélyezve van, az ügyfelek informatikai rendszergazdái nem kell megismerniük, hogyan kell konfigurálni az alkalmazást a szervezetében való használatra.

* Lehetővé teszi ügyfelei számára, hogy teljes körűen kezeljék az alkalmazottak és a vendég identitások hitelesítését és engedélyezését.

  * Az összes Fiókkezelés és megfelelőségi feladat elhelyezése az ügyfelek tulajdonosával.

  * Lehetővé teszi az egyszeri bejelentkezés engedélyezését vagy letiltását bizonyos identitás-szolgáltatók, csoportok vagy felhasználók számára az üzleti igények kielégítése érdekében.

* Növelheti a piacot és a bevezetést. Számos nagy szervezet megköveteli, hogy az alkalmazottaik zökkenőmentes egyszeri bejelentkezéses felhasználói élményt nyújtsanak az összes alkalmazásban. Az egyszeri bejelentkezés egyszerűvé tétele fontos.

* Csökkentheti a végfelhasználói súrlódást, ami növelheti a végfelhasználói használatot, és növelheti a bevételt.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Egyszeri bejelentkezés engedélyezése a közzétett alkalmazásban

1. [Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazáshoz](isv-choose-multi-tenant-federation.md).
1. Egyszeri bejelentkezés implementálása az alkalmazásban
   - Lásd: [útmutató a hitelesítési mintákhoz](../develop/v2-app-types.md)
   - Lásd: [Azure Active Directory-mintakód](../develop/sample-v2-code.md) a OIDC és a OAuth protokollokhoz
1. [Az Azure-bérlő létrehozása](isv-tenant-multi-tenant-app.md) és az alkalmazás tesztelése
1. [SSO-dokumentáció létrehozása és közzététele a webhelyen](isv-create-sso-documentation.md).
1. [Küldje el alkalmazásait](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) a Microsoft webhelyéről, és ossza meg a Microsofttal.
1. [Csatlakozzon a Microsoft partner Networkhoz (ingyenes), és hozzon létre egy piacra lépési tervet](https://partner.microsoft.com/en-us/explore/commercial#gtm).
