---
title: Azure AD-szerepkörök biztonsági varázsló a PIM-ben – Azure Active Directory | Microsoft Docs
description: Ismerteti a biztonsági varázslót, amellyel az állandó rendszerjogosultságú Azure AD szerepkör-hozzárendeléseket átalakíthatja Azure AD Privileged Identity Management (PIM) használatára.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d94df29ba16ecee06d70f5edac15a96a4299d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804012"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Azure AD-szerepkörök biztonsági varázslója a PIM-ben

Ha Ön az első, aki a szervezete Azure Active Directory (Azure AD) Privileged Identity Management (PIM) futtatását végzi, egy varázsló fog megjelenni. A varázsló segítségével megismerheti a Kiemelt identitások biztonsági kockázatait, és a PIM használatával csökkentheti a kockázatokat. Ha később szeretné elvégezni, nem kell módosítania a meglévő szerepkör-hozzárendeléseket a varázslóban.

## <a name="wizard-overview"></a>Varázsló – áttekintés

Mielőtt a szervezet elkezdi a PIM használatát, az összes szerepkör-hozzárendelés állandó: a felhasználók mindig ezekben a szerepkörökben jelennek meg, még akkor is, ha nincs rájuk szükségük. A varázsló első lépése megjeleníti a magas jogosultsági szintű szerepkörök listáját, valamint azt, hogy hány felhasználó van jelenleg ezekben a szerepkörökben. Ha szeretne többet megtudni a felhasználókról, ha egy vagy több ismeretlen, akkor egy adott szerepkörre is rámutathat.

A varázsló második lépése lehetőséget biztosít a rendszergazda szerepkör-hozzárendeléseinek módosítására.  

> [!WARNING]
> Fontos, hogy legalább egy globális rendszergazdával rendelkezzen, és egynél több kiemelt szerepkörű rendszergazda legyen egy szervezeti fiókkal (nem Microsoft-fiók). Ha csak egy kiemelt szerepkörű rendszergazda van, a szervezet nem fogja tudni kezelni a PIM-t, ha a fiókot törölték.
> Emellett a szerepkör-hozzárendelések állandók maradnak, ha a felhasználó rendelkezik egy Microsoft-fiókkal (a Microsoft-szolgáltatásokba, például a Skype-ba vagy a Outlook.com-ba való bejelentkezéshez használt fiók). Ha azt tervezi, hogy az MFA-t az adott szerepkör aktiválásához szeretné megkövetelni, akkor a rendszer kizárja a felhasználót.

## <a name="run-the-wizard"></a>A varázsló futtatása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök** elemre, majd a **varázsló**elemre.

    ![Azure AD-szerepkörök – varázsló lapja, amely a varázsló futtatásának 3 lépését mutatja](./media/pim-security-wizard/wizard-start.png)

1. Kattintson **1 a Kiemelt jogosultságú szerepkörök felderítése**elemre.

1. Tekintse át a Kiemelt szerepkörök listáját, és ellenőrizze, hogy mely felhasználók állandóak vagy jogosultak.

    ![Kiemelt szerepkörök felderítése – az állandó és jogosult tagokat mutató szerepkör panel](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. A **tovább** gombra kattintva kiválaszthatja azokat a tagokat, akik számára jogosult szeretne lenni.

    ![Tagok átalakítása jogosult lapra lehetőséggel kiválaszthatja azokat a tagokat, akik számára jogosult lehet a szerepkörökre.](./media/pim-security-wizard/convert-members-eligible.png)

1. Miután kiválasztotta a tagokat, kattintson a **tovább**gombra.

    ![Változások áttekintése oldal, amely az állandó szerepkör-hozzárendelésekkel rendelkező tagokat jeleníti meg](./media/pim-security-wizard/review-changes.png)

1. Kattintson az **OK** gombra az állandó hozzárendelések jogosultra történő átalakításához.

    Ha az átalakítás befejeződik, egy értesítés jelenik meg.

    ![Az átalakítás állapotát megjelenítő értesítés](./media/pim-security-wizard/notification-completion.png)

Ha más kiemelt jogosultsági szintű szerepkör-hozzárendeléseket is át kell alakítania a jogosultra, futtassa újra a varázslót. Ha a varázsló helyett a PIM felületet szeretné használni, tekintse meg az [Azure ad-szerepkörök kiosztása a PIM-ben](pim-how-to-add-role-to-user.md)című témakört.

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök kiosztása a PIM-ben](pim-how-to-add-role-to-user.md)
- [Hozzáférés biztosítása más rendszergazdák számára a PIM kezeléséhez](pim-how-to-give-access-to-pim.md)
