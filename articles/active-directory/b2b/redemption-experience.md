---
title: Meghívót visszaváltás a B2B együttműködés – Azure Active Directory |} Microsoft Docs
description: Ismerteti az Azure AD B2B együttműködés meghívó érvényesítési végfelhasználói élmény, beleértve a adatvédelmi használati feltételeit.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0a0c900bbfbb2778d8fabcbb71e339fd3dabcf47
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Az Azure Active Directory B2B együttműködés meghívó érvényesítési

Az Azure Active Directory (Azure AD) B2B együttműködés a fiókpartner-szervezetek felhasználók vállalatokkal, felajánlhatja a vendégfelhasználók megosztott alkalmazások eléréséhez. Miután a Vendég felhasználó nincs hozzáadva a könyvtárba, a felhasználói felületen keresztül, vagy a felhasználó Powershellen keresztül felkérik, vendégfelhasználók be kell lépnie ahol engedélyt megadják, hogy első alkalommal hozzájárulási folyamat [adatvédelmi feltételek](#privacy-policy-agreement). Ez a folyamat történik, a következő módon:

- A Vendég meghívó küld egy közvetlen hivatkozást egy megosztott alkalmazásához. A meghívott jelentkezhet be a hivatkozásra kattint, az adatvédelmi feltételeket fogad, és zökkenőmentesen hozzáfér a megosztott erőforrás. (A Vendég felhasználó továbbra is kap egy meghívó e-mail érvényesítési URL-címet, de eltérő néhány speciális esetben ez már nem szükséges a meghívó e-mail használandó.)  
- A Vendég felhasználó meghívó e-mailt kap, majd rákattint az érvényesítési URL-címet. Az első bejelentkezés részeként azok felkéri fogadja el az adatvédelmi feltételeket.

## <a name="redemption-through-a-direct-link"></a>Visszaváltás a közvetlen kapcsolaton keresztül

A Vendég meghívó küldésével egy közvetlen hivatkozást egy megosztott alkalmazáshoz, a Vendég felhasználó meghívhat. A Vendég felhasználó az érvényesítési szolgáltatás egyszerű módon, hogy az alkalmazást, amelynek lett megosztanak velük való bejelentkezéskor. Ugyanakkor a hivatkozásra kattintva az alkalmazást, tekintse át és fogadja el az adatvédelmi feltételeket, és zavartalanul hozzáférni az alkalmazáshoz. A legtöbb esetben a vendégfelhasználók már nincs szüksége egy érvényesítési URL-cím meghívó e-mailben kattintson.

Ha vendégfelhasználók a felhasználói felületen keresztül meghívót, vagy úgy döntene, hogy a meghívó e-mail küldése a PowerShell meghívó élmény részeként, a meghívott felhasználó továbbra is megkapja meghívó e-mailt. Az e-mailt akkor hasznos, ha a következő esetekben:

- A felhasználó nem rendelkezik az Azure AD-fiókot vagy egy Microsoft-fiók (msa-t). Ebben az esetben a felhasználó előtt létre kell hoznia egy msa-t a hivatkozásra, vagy használhatják az érvényesítési URL-címet a meghívó e-mailben. Az érvényesítési folyamat automatikusan felszólítja a felhasználót, egy felügyelt Szolgáltatásfiók létrehozásához.
- Egyes esetekben a meghívott felhasználó objektumoknak nem lehet egy e-mail címet kapcsolattartási-objektum (például az Outlook kapcsolattartási objektum) ütközés miatt. Ebben az esetben a felhasználó a meghívó e-mail érvényesítési URL-cím kell kattintania.
- A felhasználó előfordulhat, hogy jelentkezzen be az e-mail cím, amely meghívták alias. (Az alias egy olyan e-mail fiókkal társított további e-mail címet). Ebben az esetben a felhasználó a meghívó e-mail érvényesítési URL-cím kell kattintania.

Ha ezekben az esetekben speciális fontos a szervezetben, azt javasoljuk, hogy módszereket, amelyek továbbra is a a meghívó e-mail küldése a meghívott felhasználóknak. Is a felhasználó nem tartozik egy különleges esetben, ha azok továbbra is rákattinthat az URL-cím meghívó e-mailben is elérheti.

## <a name="redemption-through-the-invitation-email"></a>Visszaváltás a meghívó e-mail keresztül

Meghívott módszerrel, amely a meghívó e-mail küldése, ha felhasználók is a meghívó e-mail küldésével történő meghívót is beválthatja. Egy meghívott felhasználó az érvényesítési URL-címet az e-mailt, kattintson és tekintse át és fogadja el az adatvédelmi feltételeket. Az eljárást a további részletek itt:

1.  Meghívásának, miután a meghívott kap keresztül e-mailek által küldött meghívót **Microsoft Invitations**.
2.  A meghívott kiválasztja **Ismerkedés** az e-mailben.
3.  A meghívott nem rendelkezik, az Azure AD-fiókot vagy egy felügyelt Szolgáltatásfiók, ha azok felszólítja egy felügyelt Szolgáltatásfiók létrehozásához.
4.  A meghívott a rendszer átirányítja a **tekintse át az engedélyek** képernyő, ahonnan a hívja fel szervezete adatvédelmi nyilatkozatát tekintse át és fogadja el a feltételeket.

## <a name="privacy-policy-agreement"></a>Adatvédelmi szabályzat megállapodás

A Vendég felhasználó bejelentkezése után az első alkalommal egy fiókpartner-szervezet erőforrásaihoz, láthatják a **tekintse át az engedélyek** képernyő. Itt azokat is tekintheti a hívja fel szervezete adatvédelmi nyilatkozatát. A felhasználónak el kell fogadnia az adatok használatát összhangban hívja fel a szervezet adatvédelmi szabályzatáról a folytatáshoz.

![A hozzáférési Panel felhasználói beállításait ábrázoló képernyőfelvétel](media/redemption-experience/ConsentScreen.png) 

További információ a hogyan bérlői rendszergazdaként társíthatja a szervezete adatvédelmi nyilatkozatát: [Útmutató: vegye fel a szervezet adatvédelmi információ az Azure Active Directoryban](https://aka.ms/adprivacystatement).

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Azure Active Directory B2B együttműködés felhasználók hozzáadása az Azure-portálon](add-users-administrator.md)
- [Az információkkal dolgozó szakemberek hogyan hozzá B2B együttműködés felhasználók Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B együttműködés felhasználók hozzáadása a PowerShell használatával](customize-invitation-api.md#powershell)
- [Egy szervezetet vendégfelhasználóként egy](leave-the-organization.md)