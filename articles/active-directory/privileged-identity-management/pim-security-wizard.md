---
title: Az Azure AD szerepkörök biztonsági varázslója a PIM szolgáltatásban – Azure Active Directory | Microsoft dokumentumok
description: A biztonsági varázsló, amely segítségével konvertálhatja az állandó kiemelt Azure AD-szerepkör-hozzárendelések jogosult az Azure AD kiemelt identitáskezelés (PIM) használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266571"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Az Azure AD szerepkörök biztonsági varázslója a Kiemelt identitáskezelés ben

Ha ön az első, aki az Azure Active Directory (Azure AD) szervezetében használja a kiemelt identitáskezelést (PIM), a kezdéshez egy varázsló jelenik meg. A varázsló segít megérteni a kiemelt identitások biztonsági kockázatait, és hogyan használhatja a Kiemelt identitáskezelés a kockázatok csökkentésére. Ha később szeretné módosítani a meglévő szerepkör-hozzárendeléseket, nem kell módosítania a meglévő szerepkör-hozzárendeléseket.

## <a name="wizard-overview"></a>Varázsló – áttekintés

Mielőtt a szervezet elkezdi használni a kiemelt identitáskezelés, minden szerepkör-hozzárendelések állandó: a felhasználók mindig ezeket a szerepköröket akkor is, ha jelenleg nincs szükségük a jogosultságokat. A varázsló első lépése a magas szintű jogosultságokkal rendelkező szerepkörök listáját jeleníti meg, és azt, hogy jelenleg hány felhasználó van ezekben a szerepkörekben. Egy adott szerepkörbe részletezve többet tudhat meg a felhasználókról, ha egy vagy több ismeretlen.

A varázsló második lépése lehetőséget ad a rendszergazda szerepkör-hozzárendeléseinek módosítására.  

> [!WARNING]
> Fontos, hogy legalább egy globális rendszergazdával és egynél több, szervezeti fiókkal (nem Microsoft-fiókkal) rendelkező kiemelt szereppel rendelkező szerepkör-rendszergazdával rendelkezik. Ha csak egy kiemelt szerepkör-rendszergazda, a szervezet nem tudja kezelni a kiemelt identitáskezelés, ha a fiók törlődik.
> A szerepkör-hozzárendeléseket akkor is tartsa állandónak, ha a felhasználó rendelkezik Microsoft-fiókkal (más szóval olyan fiókkal, amelyet a Microsoft-szolgáltatásokba, például a Skype-ba és a Outlook.com való bejelentkezéshez használnak). Ha azt tervezi, hogy többtényezős hitelesítést igényel az adott szerepkör aktiválásához, akkor a rendszer zárolja a felhasználót.

## <a name="run-the-wizard"></a>A varázsló futtatása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök,** majd **a Varázsló**lehetőséget.

    ![Azure AD-szerepkörök – Varázsló lap a varázsló futtatásához szükséges 3 lépéssel](./media/pim-security-wizard/wizard-start.png)

1. Válassza az **1 Jogosultsággal rendelkező szerepkörök felderítése lehetőséget.**

1. Tekintse át a kiemelt szerepkörök listáját, és tekintse meg, hogy mely felhasználók állandóak vagy jogosultak.

    ![Kiemelt szerepkörök felfedezése – Szerepkör ablaktábla állandó és jogosult tagokat megjelenítő](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Válassza a **Tovább** gombot, ha ki szeretné jelölni azokat a felhasználókat vagy csoportokat, amelyeket jogosulttá szeretne tenni.

    ![A tagok konvertálása jogosult lapra a kiválasztott tagok kiválasztásának lehetőségeivel, akiket jogosulttá szeretne tenni a szerepkörökre](./media/pim-security-wizard/convert-members-eligible.png)

1. Miután kiválasztotta a felhasználókat vagy csoportokat, válassza a **Tovább**gombot.

    ![A konvertálandó állandó szerepkör-hozzárendelésekkel rendelkező tagokat megjelenítő változások lap áttekintése](./media/pim-security-wizard/review-changes.png)

1. Az **OK gombra** választva az állandó hozzárendeléseket jogosulttá szeretné alakítani.

    Amikor a konverzió befejeződik, megjelenik egy értesítés.

    ![Az átalakítás állapotáról szóló értesítés](./media/pim-security-wizard/notification-completion.png)

Ha más kiemelt szerepkör-hozzárendeléseket is jogosulttá kell konvertálnia, futtassa újra a varázslót. Ha a varázsló helyett a kiemelt identitáskezelési felületet szeretné használni, olvassa el [az Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelés ben című témakört.](pim-how-to-add-role-to-user.md)

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-how-to-add-role-to-user.md)
- [Hozzáférés megadása más rendszergazdáknak a kiemelt identitáskezelés kezeléséhez](pim-how-to-give-access-to-pim.md)
