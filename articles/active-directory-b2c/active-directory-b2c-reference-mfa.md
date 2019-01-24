---
title: Multi-factor Authentication szolgáltatás Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan többtényezős hitelesítés engedélyezése az Azure Active Directory B2C által védett felhasználók felé néző alkalmazásokban.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9dd69c7dd43e7dd709a32c1d869252ed6a495666
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853680"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C-t a többtényezős hitelesítés engedélyezése

Az Azure Active Directory (Azure AD) B2C közvetlenül integrálódik az [Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) , hogy az alkalmazások a regisztrációs és bejelentkezési élményt egy második biztonsági réteggel is hozzáadhat. Engedélyezi a multi-factor authentication szolgáltatás egy egyetlen sor kód írása nélkül. Ha már létrehozott jelentkezzen be, és jelentkezzen be a felhasználói folyamatok, a multi-factor authentication továbbra is engedélyezheti.

Ez a szolgáltatás segít az alkalmazások kezelésére a következőkhöz hasonló forgatókönyveket:

- Hozzáférni egy alkalmazáshoz a multi-factor Authentication hitelesítés nem feltétlenül szükséges, de van szüksége, hogy egy másik eléréséhez. Például az ügyfél egy közösségi vagy helyi fiók automatikus biztosítási alkalmazás be tud jelentkezni, de a telefonszám ellenőriznie kell, mielőtt az otthoni biztosítási alkalmazás elérésének ugyanabban a címtárban regisztrált.
- Az általános hozzáférni egy alkalmazáshoz a multi-factor Authentication hitelesítés nem feltétlenül szükséges, de van szüksége, hogy a benne lévő bizalmas részeket eléréséhez. Például az ügyfél tud bejelentkezni a banki alkalmazás egy TAJ- vagy helyi fiók, és ellenőrizze a fiók elosztása, de a wire átvitel megkísérlése előtt ellenőriznie kell a telefonszámot.

## <a name="set-multi-factor-authentication"></a>Többtényezős hitelesítés beállítása

Amikor egy felhasználó folyamatot hoz létre, lehetősége van a multi-factor authentication szolgáltatás engedélyezése.

![Többtényezős hitelesítés beállítása](./media/active-directory-b2c-reference-mfa/add-policy.png)

Állítsa be **többtényezős hitelesítés** való **engedélyezve**.

Használhat **felhasználói folyamat futtatása** ellenőrizheti a felhasználói élményt. Erősítse meg az alábbi forgatókönyvet:

Egy felhasználói fiók a bérlő jön létre, akkor fordul elő, a multi-factor Authentication hitelesítés lépés előtt. A lépés során az ügyfél ekkor adja meg egy telefonszámot, majd ellenőrzi, hogy az. Ha az ellenőrzés sikeres, a telefonszámot a fiók későbbi használatra van csatolva. Akkor is, ha az ügyfél lemond vagy csökken, az ügyfél is megkéri, hogy egy telefonszám ellenőrzése során újra a következő bejelentkezéskor és a többtényezős hitelesítés engedélyezve van.

## <a name="add-multi-factor-authentication"></a>A multi-factor authentication szolgáltatás hozzáadása

Akkor lehet multi-factor authentication szolgáltatás engedélyezése a korábban létrehozott felhasználói folyamat. 

A multi-factor authentication szolgáltatás engedélyezése:

1. Nyissa meg a felhasználói folyamatot, majd **tulajdonságok**. 
2. A **többtényezős hitelesítés**válassza **engedélyezve**.
3. Kattintson az oldal tetején lévő **Mentés** elemre.


