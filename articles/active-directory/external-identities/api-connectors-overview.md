---
title: Az önkiszolgáló bejelentkezési folyamatokban lévő API-összekötők – Azure AD
description: A Azure Active Directory (Azure AD) API-összekötők használatával testreszabhatja és kiterjesztheti az önkiszolgáló bejelentkezési felhasználói folyamatokat webes API-k használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c5e546c6eac77c4952a0d32d360f49d4251d49d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909701"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>API-összekötők használata az önkiszolgáló regisztráció testreszabásához és kiterjesztéséhez 

## <a name="overview"></a>Áttekintés 
Fejlesztőként vagy rendszergazdaként az API-összekötők használatával integrálhatja az önkiszolgáló [bejelentkezési felhasználói folyamatokat](self-service-sign-up-overview.md) a külső rendszerekkel a webes API-k használatával. Az API-összekötők például a következőket használhatják:

- [**Integrálás egyéni jóváhagyási munkafolyamattal**](self-service-sign-up-add-approvals.md). Kapcsolódás egyéni jóváhagyási rendszerhez a fiókok létrehozásának kezeléséhez.
- [**Személyazonosság-ellenőrzés végrehajtása**](code-samples-self-service-sign-up.md#identity-verification). A személyazonosság-ellenőrző szolgáltatással további biztonsági szintet adhat hozzá a fiókok létrehozásával kapcsolatos döntésekhez.
- **Felhasználói bemeneti adatok érvényesítése**. A helytelenül formázott vagy érvénytelen felhasználói adatértékek ellenőrzése. Ellenőrizheti például, hogy a felhasználó által megadott adatok egy külső adattáron vagy a megengedett értékek listáján lévő meglévő adatokon keresztül érvényesíthetők-e. Ha érvénytelen, megkérheti a felhasználót, hogy adjon meg érvényes adatmennyiséget, vagy tiltsa le a felhasználót a regisztrációs folyamat folytatásához.
- **Felhasználói attribútumok felülírása**. Formázza vagy rendeljen hozzá egy értéket a felhasználótól gyűjtött attribútumhoz. Ha például egy felhasználó az első nevet adja meg az összes kisbetűs vagy az összes nagybetűvel, a nevet csak az első nagybetűvel formázhatja. 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- **Egyéni üzleti logikát futtathat**. Leküldéses értesítések küldéséhez, a vállalati adatbázisok frissítéséhez, az engedélyek kezeléséhez, a naplózási adatbázisokhoz és egyéb egyéni műveletek elvégzéséhez aktiválhatja az alárendelt eseményeket a felhőalapú rendszerekben.

Az API-összekötők az Azure AD és egy API-végpont közötti szerződést jelölik a HTTP-végpont, a hitelesítés, a kérelem és a várt válasz definiálásával. Az API-összekötők konfigurálása után engedélyezheti azt egy adott lépéshez a felhasználói folyamatokban. Ha a felhasználó eléri ezt a lépést a regisztrációs folyamat során, az API-összekötőt a rendszer HTTP POST kérelemként hívja meg, és a kiválasztott jogcímeket kulcs-érték párokként küldi el egy JSON-törzsben. Az API-válasz hatással lehet a felhasználói folyamat végrehajtására. Az API-válasz például blokkolhatja egy felhasználó regisztrációját, megkérheti a felhasználót, hogy írja be újra az adatokat, vagy írja felül a felhasználói attribútumok felülírását és hozzáfűzését.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Az API-összekötők felhasználói folyamatokban való engedélyezése

A felhasználói folyamatoknak két helye van, ahol engedélyezheti az API-összekötőt:

- Az identitás-szolgáltatóval való bejelentkezés után
- A felhasználó létrehozása előtt

Mindkét esetben az API-összekötőket a rendszer a regisztráció során hívja meg, nem pedig a bejelentkezéshez.

### <a name="after-signing-in-with-an-identity-provider"></a>Az identitás-szolgáltatóval való bejelentkezés után

A regisztrálási folyamat ezen lépésében szereplő API-összekötőt közvetlenül a felhasználó személyazonosság-szolgáltatóval (Google, Facebook, Azure AD) való hitelesítése után hívja meg a rendszer. Ez a lépés megelőzi az ***attribútum-gyűjtemény lapot***, amely a felhasználó számára a felhasználói attribútumok összegyűjtésére bemutatott űrlap. Az alábbi példák az API-összekötők olyan forgatókönyveit ismertetik, amelyeket a következő lépésben engedélyezhet:

- A felhasználó által megadott e-mail-vagy összevont identitás használatával kereshet jogcímeket egy meglévő rendszeren. Adja vissza ezeket a jogcímeket a meglévő rendszerből, töltse ki előre az attribútum-gyűjtemény lapot, és tegye elérhetővé őket a tokenben való visszatéréshez.
- Ellenőrizze, hogy a felhasználó szerepel-e az engedélyezési vagy a letiltási listán, és szabályozhatja, hogy folytathatja-e a regisztrációs folyamatot.

### <a name="before-creating-the-user"></a>A felhasználó létrehozása előtt

A regisztrációs folyamat ezen lépésében található API-összekötőt a rendszer az attribútum-gyűjtemény lap után hívja meg, ha van ilyen. Ez a lépés mindig a felhasználói fiók Azure AD-ben való létrehozása előtt kerül meghívásra. A következő példák olyan forgatókönyveket mutatnak be, amelyeket a regisztráció során lehet engedélyezni:

- Érvényesítse a felhasználói bemeneti adatokat, és kérje meg a felhasználót, hogy küldje el újra az adatokat.
- Felhasználói regisztráció letiltása a felhasználó által megadott adatbevitel alapján.
- Személyazonosság-ellenőrzés végrehajtása.
- Külső rendszerek lekérdezése a felhasználóról az alkalmazási jogkivonatban visszaadott vagy az Azure AD-ben tárolt meglévő információkhoz.

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="next-steps"></a>Következő lépések
- Útmutató [API-összekötők felhasználói folyamathoz való hozzáadásához](self-service-sign-up-add-api-connector.md)
- Ismerje meg, hogyan [adhat hozzá egyéni jóváhagyási rendszereket önkiszolgáló regisztrációhoz](self-service-sign-up-add-approvals.md)