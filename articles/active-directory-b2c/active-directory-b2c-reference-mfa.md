---
title: Multi-Factor Authentication a Azure Active Directory B2Cban | Microsoft Docs
description: A Multi-Factor Authentication engedélyezése a Azure Active Directory B2C által védett, a felhasználók felé irányuló alkalmazásokban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0b872fcfbc2ead2cebdd32ff718b582c13af314e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065575"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Multi-Factor Authentication engedélyezése Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) közvetlenül az [Azure multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) integrálódik, így egy második biztonsági réteget adhat hozzá az alkalmazásaiban való regisztráláshoz és bejelentkezési élményekhez. A többtényezős hitelesítés engedélyezése egyetlen sor kód írása nélkül. Ha már létrehozott egy regisztrációs és bejelentkezési felhasználói folyamatot, továbbra is engedélyezheti a többtényezős hitelesítést.

Ez a funkció segít az alkalmazásoknak olyan forgatókönyvek kezelésében, mint például a következők:

- Nincs szükség többtényezős hitelesítésre egy alkalmazáshoz való hozzáféréshez, de ehhez egy másikra van szükség. Például az ügyfél be tud jelentkezni egy automatikus biztosítási alkalmazásba egy közösségi vagy helyi fiókkal, de ellenőriznie kell a telefonszámot, mielőtt hozzáfér az ugyanabban a címtárban regisztrált Home Insurance-alkalmazáshoz.
- A többtényezős hitelesítés nem szükséges ahhoz, hogy általánosan hozzáférjen egy alkalmazáshoz, de ehhez a bizalmas részeit is hozzá kell férnie. Például az ügyfél bejelentkezhet egy banki alkalmazásba egy közösségi vagy helyi fiókkal, és ellenőrizheti a fiók egyenlegét, de a hálózati átvitel megkísérlése előtt ellenőriznie kell a telefonszámot.

## <a name="set-multi-factor-authentication"></a>Multi-Factor Authentication beállítása

Felhasználói folyamat létrehozásakor lehetősége van a többtényezős hitelesítés engedélyezésére.

![Multi-Factor Authentication beállítása](./media/active-directory-b2c-reference-mfa/add-policy.png)

**Engedélyezze**a **többtényezős hitelesítés** beállítást.

A **felhasználói folyamat futtatásával** ellenőrizheti a felhasználói élményt. Erősítse meg a következő helyzetet:

A multi-Factor Authentication lépése előtt létrejön egy ügyfél-fiók a bérlőben. A lépés során a rendszer megkéri az ügyfelet, hogy adjon meg egy telefonszámot, és ellenőrizze azt. Ha az ellenőrzés sikeres, a telefonszámot a rendszer a fiókhoz csatolja későbbi használatra. Annak ellenére, hogy az ügyfél megszakítja vagy kiesik, az ügyfél megkérheti, hogy a következő bejelentkezéskor ismét ellenőrizze a telefonszámot a többtényezős hitelesítés engedélyezésekor.

## <a name="add-multi-factor-authentication"></a>Multi-Factor Authentication hozzáadása

A többtényezős hitelesítés engedélyezhető egy korábban létrehozott felhasználói folyamaton.

A többtényezős hitelesítés engedélyezése:

1. Nyissa meg a felhasználói folyamatot, majd válassza a **Tulajdonságok**lehetőséget.
2. A **többtényezős hitelesítés**mellett válassza az **engedélyezve**lehetőséget.
3. Kattintson az oldal tetején lévő **Mentés** elemre.


