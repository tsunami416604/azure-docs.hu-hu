---
title: Azure AD-szerepkörök biztonsági varázsló a PIM-ben – Azure Active Directory | Microsoft Docs
description: Ismerteti a biztonsági varázslót, amellyel az állandó rendszerjogosultságú Azure AD szerepkör-hozzárendeléseket átalakíthatja Azure AD Privileged Identity Management (PIM) használatára.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266571"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Azure AD-szerepkörök biztonsági varázslójának Privileged Identity Management

Ha Ön az első, aki a Privileged Identity Management (PIM) használatát használja a Azure Active Directory (Azure AD) szervezetében, a kezdéshez egy varázsló jelenik meg. A varázsló segítségével megismerheti a Kiemelt identitások biztonsági kockázatait, valamint azt, hogyan használhatja a Privileged Identity Management a kockázatok csökkentése érdekében. Ha később szeretné elvégezni, nem kell módosítania a meglévő szerepkör-hozzárendeléseket a varázslóban.

## <a name="wizard-overview"></a>Varázsló – áttekintés

A szervezet Privileged Identity Management használatának megkezdése előtt az összes szerepkör-hozzárendelés állandó: a felhasználók mindig ezekben a szerepkörökben jelennek meg, még akkor is, ha nincs rájuk szükségük. A varázsló első lépése megjeleníti a magas jogosultsági szintű szerepkörök listáját, valamint azt, hogy hány felhasználó van jelenleg ezekben a szerepkörökben. Ha szeretne többet megtudni a felhasználókról, ha egy vagy több ismeretlen, akkor egy adott szerepkörre is rámutathat.

A varázsló második lépése lehetőséget biztosít a rendszergazda szerepkör-hozzárendeléseinek módosítására.  

> [!WARNING]
> Fontos, hogy legalább egy globális rendszergazdával rendelkezzen, és egynél több kiemelt szerepkörű rendszergazda legyen egy szervezeti fiókkal (nem Microsoft-fiók). Ha csak egy kiemelt szerepkörű rendszergazda van, akkor a szervezet nem tudja kezelni Privileged Identity Management, ha a fiókot törölték.
> Emellett a szerepkör-hozzárendelések állandók maradnak abban az esetben is, ha egy felhasználó Microsoft-fiók (vagyis egy olyan fiók, amelyet a Microsoft-szolgáltatásokhoz, például a Skype-hoz és a Outlook.com) való bejelentkezéshez használ. Ha azt tervezi, hogy az aktiváláshoz többtényezős hitelesítést igényel, a felhasználót a rendszer kizárja.

## <a name="run-the-wizard"></a>A varázsló futtatása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök** lehetőséget, majd válassza a **varázsló**lehetőséget.

    ![Azure AD-szerepkörök – varázsló lapja, amely a varázsló futtatásának 3 lépését mutatja](./media/pim-security-wizard/wizard-start.png)

1. 1\. Válassza ki a **Kiemelt szerepkörök felderítése**elemet.

1. Tekintse át a Kiemelt szerepkörök listáját, és ellenőrizze, hogy mely felhasználók állandóak vagy jogosultak.

    ![Kiemelt szerepkörök felderítése – az állandó és jogosult tagokat mutató szerepkör panel](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. A **tovább** gombra kattintva kiválaszthatja azokat a felhasználókat vagy csoportokat, akik számára jogosult szeretne lenni.

    ![Tagok átalakítása jogosult lapra lehetőséggel kiválaszthatja azokat a tagokat, akik számára jogosult lehet a szerepkörökre.](./media/pim-security-wizard/convert-members-eligible.png)

1. Miután kiválasztotta a felhasználókat vagy csoportokat, válassza a **tovább**lehetőséget.

    ![Változások áttekintése oldal, amely az állandó szerepkör-hozzárendelésekkel rendelkező tagokat jeleníti meg](./media/pim-security-wizard/review-changes.png)

1. Kattintson az **OK** gombra az állandó hozzárendelések jogosultra történő átalakításához.

    Ha az átalakítás befejeződik, egy értesítés jelenik meg.

    ![Az átalakítás állapotát megjelenítő értesítés](./media/pim-security-wizard/notification-completion.png)

Ha más kiemelt jogosultsági szintű szerepkör-hozzárendeléseket is át kell alakítania a jogosultra, futtassa újra a varázslót. Ha a varázsló helyett a Privileged Identity Management felületet szeretné használni, tekintse meg [Az Azure ad-szerepkörök kiosztása a Privileged Identity Managementban](pim-how-to-add-role-to-user.md)című témakört.

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Hozzáférés biztosítása más rendszergazdáknak a Privileged Identity Management kezeléséhez](pim-how-to-give-access-to-pim.md)
