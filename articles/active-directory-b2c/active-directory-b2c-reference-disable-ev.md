---
title: E-mail-ellenőrzés letiltása a felhasználói regisztráció során Azure Active Directory B2Cban | Microsoft Docs
description: Egy témakör, amely bemutatja, hogyan tilthatja le az e-mailek ellenőrzését a Azure Active Directory B2C a fogyasztói regisztráció során.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065627"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>E-mail-ellenőrzés letiltása a felhasználói regisztráció során Azure Active Directory B2C
Ha engedélyezve van, a Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a fogyasztó számára, hogy e-mail-cím megadásával és helyi fiók létrehozásával regisztráljon az alkalmazásokra. Azure AD B2C az érvényes e-mail-címeket biztosítja, hogy a felhasználóknak meg kell győződniük a regisztrációs folyamat során. Emellett megakadályozza, hogy egy rosszindulatú automatizált folyamat hamis fiókokat generáljon az alkalmazásokhoz.

Egyes alkalmazás-fejlesztők szívesebben szeretnék kihagyni az e-mailek ellenőrzését a regisztrációs folyamat során, így a felhasználók később is ellenőrizhetik az e-mail-címet. Ennek támogatásához Azure AD B2C konfigurálható az e-mail ellenőrzésének letiltására. Ez létrehoz egy simább regisztrációs folyamatot, és lehetővé teszi a fejlesztők számára a rugalmasságot, hogy megkülönböztessék azokat a felhasználókat, akik az e-mail-címüket ellenőrizték azoktól a fogyasztóktól, akik nem.

Alapértelmezés szerint a regisztrációs felhasználói folyamatok e-mail-ellenőrzése be van kapcsolva. A következő lépésekkel kapcsolhatja ki:

1. Kattintson a **felhasználói folyamatok**elemre.
2. Kattintson a felhasználói folyamatra (például "B2C_1_SiUp") a megnyitásához.
3. Kattintsona lapelrendezések elemre.
4. Kattintson a **helyi fiók regisztrálása lapra**.
5. Kattintson az **e-mail-cím** elemre a **felhasználói attribútumok** szakaszban található **Name (név** ) oszlopban.
6. A **szükséges ellenőrzés**területen válassza a **nem**lehetőséget.
7. Kattintson a panel tetején lévő **Mentés** elemre. Készen van!

> [!NOTE]
> Ha letiltja az e-mailek ellenőrzését a regisztrációs folyamatban, a levélszemét is vezethet. Ha letiltja az alapértelmezettet, javasoljuk, hogy adja hozzá a saját ellenőrzési rendszerét.
>
>
