---
title: A több-bérlős alkalmazás egyszeri bejelentkezés engedélyezése
description: A független szoftverszállítók, Azure active Directoryval való integrálását ismertető útmutató
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
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795181"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Egyszeri bejelentkezés több-bérlős alkalmazás engedélyezése  

Kínál az alkalmazás más cégeket azzal, beszerzési vagy előfizetést használja, ha az alkalmazás számára elérhetővé a saját Azure bérlők ügyfeleket. Ez más néven egy több-bérlős alkalmazást. A fogalmi áttekintése: [több-Bérlős alkalmazások az Azure-ban](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) és [az Azure Active Directoryban Bérlős](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Mi az egyszeri bejelentkezést

Egyszeri bejelentkezés (SSO) ad biztonsági és kényelmes használatra vonatkozó felhasználók bejelentkezésekor alkalmazásokhoz és egyéb identitások Azure Active Directory használatával. Ha egy alkalmazás egyszeri bejelentkezési engedélyezve van, a felhasználóknak nem kell külön hitelesítő adataival, hogy az alkalmazás eléréséhez. Teljes ismertetése az egyszeri bejelentkezést. [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások megtekintéséhez](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Miért érdemes engedélyezi az egyszeri bejelentkezéshez az alkalmazásban?

Nincsenek SSO engedélyezése a több-bérlős alkalmazásban számos előnnyel jár. Ha engedélyezi az egyszeri bejelentkezés az alkalmazáshoz:

* Az alkalmazás az Azure piactéren, ahol az alkalmazás több millió szervezetnél, az Azure Active Directory által felderíthető, is szerepel.
  * Az alkalmazás gyorsan konfigurálása az Azure AD használatát teszi lehetővé.

* Az alkalmazás felfedhető az Office 365 alkalmazást gyűjteményének az Office 365 Appindítója és a Microsoft Search Office.com webhelyen belül

* Az alkalmazás a Microsoft Graph REST API használatával, amely a felhasználói termelékenység szempontjából érhető el a Microsoft Graph az adatokkal.

* Így megkönnyíti az ügyfelek által a támogatási költségek csökkentése.
  * Alkalmazás-specifikus dokumentáció coproduced elkötelezettségünket ügyfelek használatának egyszerűbbé tétele az Azure ad-ben csapatától.
  * Ha egyetlen kattintással egyszeri bejelentkezés engedélyezve van, az ügyfelek a rendszergazdák megtudhatja, hogyan használható az alkalmazás konfigurálása a szervezet nem rendelkezik.

* Az ügyfelek lehetővé teszi az alkalmazottak és a Vendég identitásuk általi hitelesítési és engedélyezési teljesen kezeléséhez.

  * Minden fiók felügyeleti és megfelelőségi felelős az ügyfél tulajdonosával ezeket identitások helyezi el.

  * Biztosító lehetősége van engedélyezni vagy letiltani az egyszeri bejelentkezés az adott identitás-szolgáltatóktól, csoportok vagy felhasználók saját üzleti igényeihez.

* Növelheti a befektetésénél és adoptability. Számos nagyméretű szervezet megköveteli, hogy (vagy szem előtt,) az alkalmazottaknak zökkenőmentes egyszeri bejelentkezési élményt alkalmazások közötti. Fontos, hogy könnyen egyszeri Bejelentkezést.

* Csökkentheti a végfelhasználói fennakadások nélkül használható, amely a végfelhasználói használatára, és a bevétel növelésében.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Egyszeri bejelentkezés a közzétett alkalmazás engedélyezése

1. [Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazás](isv-choose-multi-tenant-federation.md).
1. Az alkalmazás egyszeri bejelentkezés megvalósítása
   - Lásd: [hitelesítési minták útmutatást](../develop/v2-app-types.md)
   - Lásd: [az Azure active Directory-Kódminták](../develop/sample-v2-code.md) OIDC és OAuth protokollok
1. [Az Azure-bérlő létrehozása](isv-tenant-multi-tenant-app.md) és tesztelheti alkalmazását
1. [Létrehozása és közzététele a webhelyen SSO-dokumentáció](isv-create-sso-documentation.md).
1. [Küldje el az alkalmazás listaelem](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) és a Microsoft található dokumentáció létrehozása Microsoft-partner.
1. [Csatlakozás a Microsoft Partner Network-(ingyenes), és hozzon létre az ideális csomag piaci](https://partner.microsoft.com/en-us/explore/commercial#gtm).
