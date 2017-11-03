---
title: "Az Azure Active Directory B2C: Többtényezős hitelesítés |} Microsoft Docs"
description: "Többtényezős hitelesítés engedélyezése az Azure Active Directory B2C által védett felhasználók felé néző alkalmazásokban"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 62ec48ab067cf02bc8409aca6da704a5418ec270
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Az Azure Active Directory B2C: Többtényezős hitelesítés engedélyezése a felhasználók felé néző alkalmazásokban
Az Azure Active Directory (Azure AD) B2C integrálható közvetlenül [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) , hogy adhat hozzá egy második biztonsági szintként, regisztrációs és a bejelentkezési élmény a felhasználók felé néző alkalmazásokban. És ehhez egyetlen kódsort írása nélkül. Jelenleg támogatott telefonhívási és az SMS-ellenőrzéseket. Ha már létrehozott regisztráció és bejelentkezés házirendek, a multi-factor Authentication továbbra is engedélyezheti.

> [!NOTE]
> Többtényezős hitelesítés is engedélyezhető, nem csak a meglévő házirendek szerkesztésével regisztráció és bejelentkezés házirendek létrehozásakor.
> 
> 

Ez a szolgáltatás segít alkalmazásokat kezeléséhez a következő lehetőségeket:

* Egy alkalmazás eléréséhez a többtényezős hitelesítés nem feltétlenül szükséges, de van szüksége egy másikat elérésére. A fogyasztó például egy alkalmazásba automatikus biztosítási közösségi vagy helyi fiókkal jelentkezhet, de előtt ellenőriznie kell a telefonszám az otthoni biztosítási alkalmazás elérésének regisztrált ugyanabban a könyvtárban.
* A multi-factor Authentication általában csatlakozni egy alkalmazáshoz nem feltétlenül szükséges, de van szüksége az érzékeny részek belül hozzáférjen. A fogyasztó például egy banki alkalmazás egy közösségi vagy helyi fiók, és ellenőrizze számlaegyenlegeit jelentkezhetnek be, de egy átviteli áthelyezés előtt ellenőriznie kell a telefonszámot.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Módosítsa a regisztrációs szabályzatban többtényezős hitelesítés engedélyezése
1. [Kövesse az alábbi lépéseket az Azure portálon a B2C funkciók panelje navigáljon](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **Regisztrálási szabályzatok** lehetőségre.
3. Kattintson a regisztrációs szabályzatban (például "B2C_1_SiUp") való megnyitásához.
4. Kattintson a **a multi-factor authentication** kapcsolja be a **állapot** való **ON**. Kattintson az **OK** gombra.
5. Kattintson a **mentése** a panel tetején.

A házirend a "Futtatás most" szolgáltatás használatával ellenőrizze a felhasználói élmény. Ellenőrizze az alábbiakat:

Egy felhasználói fiókot a címtárban végrehajtásakor létrejön, még a multi-factor Authentication lépés előtt. A lépés során a felhasználó saját telefonszámot adja meg, és győződjön meg arról, hogy kapcsolatba. Ha az ellenőrzés sikeres, a telefonszám a felhasználói fiók későbbi használatra van csatolva. Akkor is, ha a fogyasztó megszakítja, vagy elutasítja azokat a, általa is megkéri, hogy egy telefonszámot ellenőrizze újra a következő bejelentkezés során (a többtényezős hitelesítés engedélyezve).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Módosítsa a bejelentkezési házirend többtényezős hitelesítés engedélyezése
1. [Kövesse az alábbi lépéseket az Azure portálon a B2C funkciók panelje navigáljon](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kattintson a **bejelentkezési házirendek**.
3. Kattintson a bejelentkezési házirend (például "B2C_1_SiIn") való megnyitásához. Kattintson a **szerkesztése** a panel tetején.
4. Kattintson a **a multi-factor authentication** kapcsolja be a **állapot** való **ON**. Kattintson az **OK** gombra.
5. Kattintson a **mentése** a panel tetején.

A házirend a "Futtatás most" szolgáltatás használatával ellenőrizze a felhasználói élmény. Ellenőrizze az alábbiakat:

Ha a fogyasztó (fiók használatával jelentkezik be egy közösségi vagy helyi), ha ellenőrzött telefonszám csatolva van a felhasználói fiók, ő kapcsolatba kell azt. A telefonszámmal nem kapcsolódik, ha a fogyasztó biztosít, és győződjön meg arról, hogy kapcsolatba. A sikeres ellenőrzést a telefonszám a felhasználói fiók későbbi használatra van csatolva.

## <a name="multi-factor-authentication-on-other-policies"></a>Egyéb házirendek többtényezős hitelesítést
Regisztráció és bejelentkezés házirendek a fenti leírtak is lehet engedélyezni a multi-factor authentication a bejelentkezési vagy bejelentkezési házirendek és a jelszó alaphelyzetbe állítása a házirendeket. Házirendek Profilszerkesztési hamarosan elérhető lesz.

