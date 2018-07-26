---
title: E-mail-értesítések az Azure AD PIM-ben |} A Microsoft Docs
description: Ismerteti az Azure AD Privileged Identity Management (PIM) e-mail-értesítések
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: ffac7768fefece24cb69789558ce500e1ff64d98
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257901"
---
# <a name="email-notifications-in-azure-ad-pim"></a>Az Azure AD PIM-ben az e-mail-értesítések

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

- [Hogyan kezelheti a szerepkör-aktiválási beállításokat, az Azure AD PIM-ben](pim-how-to-change-default-settings.md)
- [Az Azure AD PIM-ben jóváhagyások](azure-ad-pim-approval-workflow.md)
