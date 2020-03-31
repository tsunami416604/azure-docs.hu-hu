---
title: Többtényezős hitelesítés az Azure Active Directory B2C könyvtárában | Microsoft dokumentumok
description: A többtényezős hitelesítés engedélyezése az Azure Active Directory B2C által védett, fogyasztói ügyfélbarát alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25724ba82e57c5e3800fa1a989dd4f504df1c163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189276"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Többtényezős hitelesítés engedélyezése az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) közvetlenül integrálható [az Azure többtényezős hitelesítésével,](../active-directory/authentication/multi-factor-authentication.md) így egy második biztonsági réteget adhat hozzá a regisztrációhoz és a bejelentkezési élményhez az alkalmazásokban. A többtényezős hitelesítést egyetlen kódsor írása nélkül engedélyezheti. Ha már létrehozott regisztráció és bejelentkezésfelhasználói folyamatok, továbbra is engedélyezheti a többtényezős hitelesítést.

Ez a szolgáltatás segít az alkalmazásoknak a következő forgatókönyvek kezelésében:

- Nincs szükség többtényezős hitelesítésre egy alkalmazás eléréséhez, de egy másik eléréséhez szükséges. Például az ügyfél bejelentkezhet egy autóbiztosítási alkalmazásba egy közösségi vagy helyi fiókkal, de ellenőriznie kell a telefonszámot, mielőtt hozzáférne az ugyanabban a címtárban regisztrált lakásbiztosítási alkalmazáshoz.
- Az alkalmazások eléréséhez általában nincs szükség többtényezős hitelesítésre, de a bizalmas részek eléréséhez általában szükség van rá. Az ügyfél például közösségi vagy helyi fiókkal jelentkezhet be egy banki alkalmazásba, és ellenőrizheti a számlaegyenlegét, de az átutalás megkísérlése előtt ellenőriznie kell a telefonszámot.

## <a name="set-multi-factor-authentication"></a>Többtényezős hitelesítés beállítása

Felhasználói folyamat létrehozásakor engedélyezheti a többtényezős hitelesítést.

![Többtényezős hitelesítés beállítása](./media/custom-policy-multi-factor-authentication/add-policy.png)

Állítsa a **többtényezős hitelesítést** **Engedélyezve értékre.**

A **felhasználói folyamat futtatása** segítségével ellenőrizheti a felhasználói élményt. Erősítse meg a következő forgatókönyvet:

Egy ügyfélfiók jön létre a bérlőben, mielőtt a többtényezős hitelesítési lépés bekövetkezne. A lépés során az ügyfélnek meg kell adnia egy telefonszámot, és ellenőriznie kell azt. Ha az ellenőrzés sikeres, a telefonszám a fiókhoz lesz csatolva későbbi használatra. Még akkor is, ha az ügyfél megszakítja vagy kiesik, az ügyfél kérheti, hogy ellenőrizze a telefonszámot újra a következő bejelentkezés során a többtényezős hitelesítés engedélyezve van.

## <a name="add-multi-factor-authentication"></a>Többtényezős hitelesítés hozzáadása

Lehetőség van a többtényezős hitelesítés engedélyezésére a korábban létrehozott felhasználói folyamaton.

A többtényezős hitelesítés engedélyezése:

1. Nyissa meg a felhasználói folyamatot, és válassza **a Tulajdonságok lehetőséget.**
2. A **Többtényezős hitelesítés**csoportban válassza **az Engedélyezve**lehetőséget.
3. Kattintson az oldal tetején lévő **Mentés** elemre.


