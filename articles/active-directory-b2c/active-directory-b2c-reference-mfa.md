---
title: Multi-factor Authentication szolgáltatás Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan többtényezős hitelesítés engedélyezése az Azure Active Directory B2C által védett felhasználók felé néző alkalmazásokban.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3d18e1b2e45aba4e83989e29c533cfc7bf5033fc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442708"
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Az Azure Active Directory B2C: Többtényezős hitelesítés engedélyezése a felhasználók felé néző alkalmazásokban
Az Azure Active Directory (Azure AD) B2C közvetlenül integrálódik az [Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) , hogy a felhasználók felé néző alkalmazásokban való regisztrációs és bejelentkezési élményt egy második biztonsági réteggel is hozzáadhat. És ezt megteheti egy egyetlen sor kód megírása nélkül is. Jelenleg tesztenként telefonhívási és SMS-ellenőrzéseket. Ha már létrehozott regisztrációs és bejelentkezési szabályzatok, a multi-factor Authentication szolgáltatás továbbra is engedélyezheti.

> [!NOTE]
> Multi-factor Authentication hitelesítés is engedélyezhető, nem csak a meglévő házirendek szerkesztésével regisztrációs és bejelentkezési szabályzatok létrehozásakor.
> 
> 

Ez a szolgáltatás segít az alkalmazások kezelésére a következőkhöz hasonló forgatókönyveket:

* Hozzáférni egy alkalmazáshoz a multi-factor Authentication hitelesítés nem feltétlenül szükséges, de van szüksége, hogy egy másik eléréséhez. A fogyasztó például közösségi vagy helyi fiókkal automatikus biztosítási alkalmazás be tud jelentkezni, de a telefonszám ellenőriznie kell, mielőtt az otthoni biztosítási alkalmazás elérésének ugyanabban a címtárban regisztrált.
* Az általános hozzáférni egy alkalmazáshoz a multi-factor Authentication hitelesítés nem feltétlenül szükséges, de van szüksége, hogy a benne lévő bizalmas részeket eléréséhez. A fogyasztó például egy közösségi vagy helyi fiók, és ellenőrizze fiókja egyenlege a banki alkalmazás jelentkezhetnek be, de a wire átvitel megkísérlése előtt ellenőriznie kell a telefonszámot.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>A multi-factor Authentication szolgáltatás engedélyezése a regisztrálási szabályzat módosítása
1. [A B2C funkciók panelje az Azure Portalon lépjen a következő lépésekkel](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **Regisztrálási szabályzatok** lehetőségre.
3. Kattintson a regisztrációs szabályzatban (például "B2C_1_SiUp") való megnyitásához.
4. Kattintson a **multi-factor authentication** , és kapcsolja be a **állapot** való **ON**. Kattintson az **OK** gombra.
5. Kattintson a **mentése** a panel tetején.

A házirend a "Futtatás most" funkció használatával ellenőrizze a felhasználói élményt. Ellenőrizze az alábbiakat:

Egy felhasználói fiókot a címtárban létrejön a többtényezős hitelesítés lépés bekövetkezte előtt. A lépés során adja meg a saját telefonszámát, majd ellenőrzi, hogy a fogyasztó kell adnia. Ha az ellenőrzés sikeres, a telefonszámot a felhasználói fiók későbbi használatra van csatolva. Akkor is, ha a fogyasztó megszakítja, vagy csökken, ő is megkéri, hogy egy telefonszám ellenőrzése során újra a következő bejelentkezéskor (a többtényezős hitelesítés engedélyezve van).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>A multi-factor Authentication szolgáltatás engedélyezése a bejelentkezési szabályzat módosítása
1. [A B2C funkciók panelje az Azure Portalon lépjen a következő lépésekkel](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **bejelentkezési szabályzatok**.
3. Kattintson a bejelentkezési szabályzat (például "B2C_1_SiIn") való megnyitásához. Kattintson a **szerkesztése** a panel tetején.
4. Kattintson a **multi-factor authentication** , és kapcsolja be a **állapot** való **ON**. Kattintson az **OK** gombra.
5. Kattintson a **mentése** a panel tetején.

A házirend a "Futtatás most" funkció használatával ellenőrizze a felhasználói élményt. Ellenőrizze az alábbiakat:

Ha a fogyasztó használatával jelentkezik be (egy közösségi vagy helyi fiók), egy ellenőrzött telefonszámot a felhasználói fiók van csatlakoztatva, ő ekkor ellenőrzi, hogy az. A telefonszámmal nem van csatolva, ha a fogyasztó felkéri, hogy segíthessünk, majd ellenőrzi, hogy. Sikeres ellenőrzés a telefonszámot a felhasználói fiók későbbi használatra van csatolva.

## <a name="multi-factor-authentication-on-other-policies"></a>Egyéb házirendek a multi-factor Authentication
Regisztrációs és bejelentkezési szabályzatok fenti leírtak a multi-factor authentication szolgáltatás engedélyezése a regisztrációs lehetőség arra is, vagy bejelentkezési szabályzatok és a jelszó alaphelyzetbe állítása a szabályzatokat. Profilszerkesztési szabályzatok hamarosan elérhető lesz.

