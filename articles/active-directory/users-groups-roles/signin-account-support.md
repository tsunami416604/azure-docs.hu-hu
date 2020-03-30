---
title: Elfogadja az Azure AD bejelentkezési lapja a Microsoft-fiókokat | Microsoft dokumentumok
description: Hogyan tükrözi a képernyőn megjelenő üzenetküldés a felhasználónév-keresget a bejelentkezés során?
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024288"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>A Microsoft-fiókok bejelentkezési lehetőségei az Azure Active Directoryban

A Microsoft 365 bejelentkezési lap az Azure Active Directory (Azure AD) támogatja a munkahelyi vagy iskolai fiókok és a Microsoft-fiókok, de a ttól függően, hogy a felhasználó helyzetét, lehet, hogy az egyik vagy a másik, vagy mindkettő. Például az Azure AD bejelentkezési lap támogatja:

* Olyan alkalmazások, amelyek mindkét fiókból fogadnak bejelentkezéseket
* A vendégeket fogadó szervezetek

## <a name="identification"></a>Azonosítás
A felhasználónév mezőben található tippszöveg megnézésével megállapíthatja, hogy a szervezet által használt bejelentkezési lap támogatja-e a Microsoft-fiókokat. Ha a tipp szövege "E-mail, telefon vagy Skype" felirattal jelenik meg, a bejelentkezési oldal támogatja a Microsoft-fiókokat.

![Különbség a fiókbejelentkezési lapok között](./media/signin-account-support/ui-prompt.png)

[A további bejelentkezési beállítások csak személyes Microsoft-fiókok esetén működnek,](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) de nem használhatók a munkahelyi vagy iskolai fiók erőforrásaiba való bejelentkezéshez.

## <a name="next-steps"></a>További lépések

[A bejelentkezési márkajelzés testreszabása](../fundamentals/add-custom-domain.md)