---
title: E-mail-értesítések a PIM – Azure |} A Microsoft Docs
description: Az Azure AD Privileged Identity Management (PIM) e-mail-értesítések ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 1a1635e0ba16bdc0fa6dc90aa0e1a417f43e04ac
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190176"
---
# <a name="email-notifications-in-pim"></a>A PIM e-mail-értesítések

Kulcs események előfordulásakor az Azure AD Privileged Identity Management (PIM) e-mail értesítések küldését a megfelelő rendszergazdai vagy a felhasználónak. Például a PIM küld e-mailt a következő események:

- Ha egy rendszerjogosultságú szerepkör aktiválása a jóváhagyásra van
- Ha egy rendszerjogosultságú szerepkör-aktiválási kérelem jóváhagyása
- Ha a kiemelt szerepkörű aktiválva van
- Ha egy rendszerjogosultságú szerepkör hozzá van rendelve
- Ha engedélyezve van-e az Azure AD PIM-ben

2018 július végén kezdődően a PIM keresztül küldött e-mailekre fog egy új feladó e-mail címe és a egy új vizuális Tervező. Ez a frissítés mindkét PIM hatással van az Azure ad és a PIM az Azure-erőforrásokhoz. Összes esemény, amely korábban az e-mail-értesítés aktiválódik továbbra is küldje el e-mailt. Néhány e-maileket fog frissülni tartalom több célként megadott információk.

## <a name="sender-email-address"></a>Feladó e-mail-címe

2018 július végén kezdődően e-mail-értesítések rendelkezik a következő címre:

- E-mail-cím:  **azure-noreply@microsoft.com**
- Megjelenített név: Microsoft Azure

E-mail-értesítések rendelkezett a következő címre:

- E-mail-cím:  **azureadnotifications@microsoft.com**
- Megjelenített név: Microsoft Azure AD-értesítési szolgáltatás

## <a name="email-subject-line"></a>E-mail tárgysorát

Végén 2018 július, az e-mail-értesítések is az Azure AD-hez és Azure-erőforrásszerepkörök lesz egy **PIM** a tárgysorban előtag. Például:

- A PIM: A Alain Charon véglegesen a biztonsági olvasó szerepkör van hozzárendelve.

## <a name="pim-emails-for-azure-ad-roles"></a>A PIM e-mailek, az Azure AD-szerepkörök

2018 július végén kezdődően az Azure AD-szerepköröket a PIM e-mail-értesítések egy új megjelenés rendelkezik. Az alábbiakban látható egy például szolgáló e-mail érkezik, amikor egy felhasználó aktiválja a fiktív Contoso szervezet kiemelt szerepkört.

![A PIM-új e-mail cím az Azure AD-szerepkörök](./media/pim-email-notifications/email-directory-new.png)

Korábban a kiemelt szerepkörű felhasználók aktiválásakor az e-mailben kikeresi az alábbihoz hasonló.

![A PIM-régi e-mail cím az Azure AD-szerepkörök](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>A PIM e-mailek, az Azure-erőforrásszerepkörök

2018 július végén kezdődően a PIM az Azure-erőforrások szerepköreihez tartozó e-mail-értesítések egy új megjelenés rendelkezik. Az alábbiakban látható egy például szolgáló e-mail érkezik, amikor egy felhasználó hozzá van rendelve a fiktív Contoso szervezet kiemelt szerepkört.

![A PIM-új e-mail cím az Azure-erőforrásszerepkörök](./media/pim-email-notifications/email-resources-new.png)

Korábban amikor egy felhasználó rendszerjogosultságú szerepkört volt rendelve, az e-mailben kikeresi az alábbihoz hasonló.

![A PIM-régi e-mail cím az Azure-erőforrásszerepkörök](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD directory szerepkör-beállítások konfigurálása](pim-how-to-change-default-settings.md)
- [Hagyja jóvá vagy utasítsa a PIM az Azure AD-címtárbeli szerepkörökhöz tartozó kérelmek](azure-ad-pim-approval-workflow.md)
