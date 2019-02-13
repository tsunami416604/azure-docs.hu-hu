---
title: A B2B-együttműködés – Azure Active Directory vendégmeghívás beváltása |} A Microsoft Docs
description: Ismerteti az Azure AD B2B együttműködés meghívó érvényesítési élmény a végfelhasználók számára, beleértve a szerződést, adatvédelmi feltételeket.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 543c19d41cee55a309b5345af4f811c60b82a613
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178974"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Az Azure Active Directory B2B együttműködés vendégmeghívás beváltása

Azure Active Directory (Azure AD) B2B-együttműködés keretében partnerszervezetek felhasználóinak együttműködni, meghívhatja a vendég felhasználók férhetnek hozzá a megosztott alkalmazásokat. Miután vendégfelhasználó van felvéve a címtárba, a felhasználói felületen keresztül, vagy a felhasználó Powershellen keresztül a cégbe, vendégfelhasználók ahol engedélyt megadják, az első jóváhagyási folyamat használatával kell lépnie [adatvédelmi feltételek](#privacy-policy-agreement). Ez a folyamat a következő módon történik:

- A Vendég meghívója egy megosztott alkalmazás közvetlen hivatkozást küld. A meghívott a bejelentkezési hivatkozásra kattint, fogadja el az adatvédelmi szabályzat feltételeit, és zökkenőmentesen fér hozzá a megosztott erőforrás. (A Vendég felhasználó továbbra is megkapja a meghívó e-mail beváltási URL-címet, de bizonyos speciális esetekben eltérő, már nincs rá szükség, a meghívó e-mailben.)  
- A Vendég felhasználó kap egy meghívót tartalmazó e, majd a beváltási URL-CÍMÉT. Az első bejelentkezés részeként azok kéri, hogy fogadja el az adatvédelmi szabályzat feltételeit.

## <a name="redemption-through-a-direct-link"></a>Közvetlen hivatkozás segítségével elvégezhetné beváltása

A vendégmeghívó meghívhatja a vendégfelhasználó küldésével, egy [közvetlen hivatkozás egy megosztott alkalmazás](../manage-apps/end-user-experiences.md#direct-sign-on-links). A vendégfelhasználót a beváltási élmény megírásához jelentkezik be a velük megosztott alkalmazást. Akkor is egy hivatkozásra kattintva az alkalmazás, tekintse át és fogadja el az adatvédelmi szabályzat feltételeit, és zökkenőmentesen hozzáférhessen az alkalmazáshoz. A legtöbb esetben a vendégfelhasználók már nincs szüksége kattintson a meghívó e-mailben beváltási URL.

Ha a felhasználói felületen keresztül vendégfelhasználókat, vagy úgy döntött, a meghívást tartalmazó e-mail küldése a PowerShell-meghívó élmény részeként, a meghívott felhasználó továbbra is kap egy meghívót tartalmazó e. E-mailt a következő speciális esetekben hasznos:

- A felhasználó nem rendelkezik egy Azure AD-fiókot vagy egy Microsoft-fiókkal (MSA). Ebben az esetben a felhasználó előtt létre kell hoznia egy MSA a hivatkozásra kattintanak, vagy a meghívó e-mailben a beváltási URL-címet használhatnak. Az érvényesítési folyamat automatikusan kéri a felhasználót, hozzon létre egy MSA.
- Egyes esetekben a meghívott felhasználó objektumot nem lehet egy e-mail-cím kapcsolatobjektummal (például az Outlook kapcsolattartási objektum) való ütközés miatt. Ebben az esetben a felhasználónak rá kell kattintania a beváltási URL-címet a meghívó e-mailben.
- A felhasználó lehet, hogy jelentkezzen be az e-mail-cím, amely meghívták alias. (Alias az e-mail-fiók társított további e-mail címmel.) Ebben az esetben a felhasználónak rá kell kattintania a beváltási URL-címet a meghívó e-mailben.

Ha ilyen speciális esetekben fontos a szervezetben, azt javasoljuk, hogy a felhasználók a meghívó e-mailben is elküldheti módszerrel meghívása. Emellett a felhasználó nem tartozik egy különleges esetben, ha továbbra is kattintva törölheti az URL-cím meghívó e-mailben érhet el.

## <a name="redemption-through-the-invitation-email"></a>A meghívó e-mailes beváltása

Meghívott módszerrel, amely a meghívó e-mailt küld, ha felhasználók is beválthatja a meghívó e-mailes meghívót. Egy meghívott felhasználó kattintson a beváltási URL-címe az e-mailben, és tekintse át és fogadja el az adatvédelmi szabályzat feltételeit. A folyamat Itt részletesen leírt:

1.  Meghívásának, miután a meghívott személy által küldött e-mailes meghívót kap **Microsoft Invitations**.
2.  A meghívott személy kijelölése **Ismerkedés** az e-mailben.
3.  Ha a meghívott személy nem rendelkezik egy Azure AD-fiókot vagy egy MSA, hozzon létre egy MSA kell adnia.
4.  A meghívott a rendszer átirányítja a **tekintse át az engedélyek** képernyőn, ahonnan a meghívó szervezetet adatvédelmi nyilatkozat tekintse át és fogadja el a feltételeket.

## <a name="privacy-policy-agreement"></a>Adatvédelmi szabályzat szerződés

Bármely Vendég felhasználó bejelentkezése után az első alkalommal egy fiókpartner-szervezet erőforrásaihoz, megjelenik egy **tekintse át az engedélyek** képernyő. Áttekintheti itt, azok a meghívó szervezetet adatvédelmi nyilatkozatát. A felhasználó el kell fogadnia azok az információk felhasználása a meghívó szervezetet adatvédelmi szabályzatokat továbbra is megfelelően.

![Képernyőfelvétel a hozzáférési panelen felhasználói beállításokat:](media/redemption-experience/ConsentScreen.png) 

További információ a hogyan bérlői rendszergazdaként hozzákapcsolhatja a szervezete adatvédelmi nyilatkozatát: [Útmutató: Adja hozzá a szervezet adatvédelmi információ az Azure Active Directoryban](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Használati feltételek

A kezdeti érvényesítési folyamat során, a vendégfelhasználó használati feltételeket tartalmazó fájl is jelenthet a az Azure AD használati feltételek funkció használatával. Az Azure Active Directoryban, ez a szolgáltatás alatt elérheti **kezelés** > **szervezeti kapcsolatok** > **használati feltételeket tartalmazó fájl** vagy alatt **Biztonsági** > **feltételes hozzáférési** > **használati feltételeket tartalmazó fájl**. További információkért lásd: [az Azure AD használati feltételek funkció](../governance/active-directory-tou.md).

![Képernyőfelvétel: az új használati feltételek](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Azure Active Directory B2B együttműködés felhasználók hozzáadása az Azure Portalon](add-users-administrator.md)
- [Az információkkal dolgozó szakemberek hogyan hozzá B2B együttműködési felhasználókat az Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B együttműködési felhasználókat adja hozzá a PowerShell használatával](customize-invitation-api.md#powershell)
- [Egy szervezet meg vendégként](leave-the-organization.md)
