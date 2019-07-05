---
title: A PIM - Azure Active Directory saját Azure-erőforrás szerepkörök aktiválása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet aktiválni az Azure-erőforrások szerepköreihez az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501662"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>A PIM saját Azure-erőforrás szerepkörök aktiválása

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használ, jogosult szerepkör-tagok az Azure-erőforrások egy jövőbeli dátumot és időpontot az aktiválási ütemezheti. Ezek is kiválaszthatja, hogy egy adott aktiválási időtartamát, a maximális (a rendszergazdák által konfigurált) belül.

Ez a cikk olyan tagjai, akik a PIM az Azure-erőforrás szerepkörük aktiválnia kell.

## <a name="activate-a-role"></a>A szerepkör aktiválása

Amikor szüksége van egy Azure-erőforrás szerepkör, aktiválás segítségével kérheti a **saját szerepkörök** navigációs lehetőség az PIM-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**. A PIM-csempét az irányítópulton adásával kapcsolatos információkért lásd: [használatához a PIM](pim-getting-started.md).

1. Kattintson a **saját szerepkörök**.

    ![Saját szerepkörök oldalról aktiválhatja szerepkörök](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Kattintson a **Azure-erőforrások szerepköreihez** a jogosult Azure-erőforrások szerepköreihez listájának megtekintéséhez.

   ![Saját szerepkörök - Azure-erőforrás szerepkörök oldal](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Az a **Azure-erőforrásszerepkörök** list, keresse meg a szerepkör aktiválásához.

    ![Azure-erőforrások szerepköreihez - jogosult szerepkörök listáját](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Kattintson a **aktiválás** az aktiválás panel megnyitásához.

1. Ha a szerepkör megköveteli a multi-factor authentication (MFA), kattintson a **a folytatás előtt azonosítsa magát**. Csak egyszer munkamenetenként hitelesítésre van.

    ![A szerepkör aktiválása előtt MFA-val saját identitás ellenőrzése](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Kattintson a **saját identitás ellenőrzése** és kövesse az utasításokat a további biztonsági ellenőrzést nyújtanak.

    ![Képernyőn megadhatja például a PIN-kód biztonsági ellenőrzés](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Ha meg szeretné határozni a csökkentett hatókört, kattintson a **hatókör** az erőforrás-szűrő ablaktábla megnyitásához.

    Ajánlott eljárás az, csak a szükséges erőforrások hozzáférés kérése. A Resource szűrő panelen az erőforráscsoport vagy erőforrás-hozzáférést is megadhat.

    ![Aktiválja - erőforrás keresőablak hatókör megadása](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Ha szükséges, adja meg egy egyéni Aktiválás kezdete. A tag aktiválása a kijelölt idő után az.

1. Az a **OK** adja meg az aktiválási kérés okát.

    ![Hatókör, kezdő időpontja, időtartama és OK befejezett aktiválás panelről](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Kattintson a **aktiválása**.

    Ha a szerepkör nem igényel jóváhagyási, aktiválják, és hozzá az aktív szerepkörök listáját. Ha azt szeretné, a szerepkör használatához, kövesse a következő szakaszban ismertetett lépéseket.

    Ha a [a szerepkör megköveteli a jóváhagyási](pim-resource-roles-approval-workflow.md) aktiválni, egy értesítés jelenik meg a böngészőben arról értesíti, a kérelem van függőben lévő jóváhagyási jobb felső sarkában található.

    ![Aktiválási kérelem függőben lévő jóváhagyási értesítés](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Olyan szerepkört használjon az aktiválás után azonnal

Aktiválás után bármely késése esetén kövesse az alábbi lépéseket az Azure-erőforrások szerepköreihez azonnali használatát, az aktiválást követően.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **saját szerepkörök** megjeleníthetők a jogosult az Azure AD-szerepkörök és az Azure-erőforrások szerepköreihez.

1. Kattintson a **Azure-erőforrások szerepköreihez**.

1. Kattintson a **aktív szerepkörök** fülre.

1. Ha a szerepkör aktív, jelentkezzen ki a portálról, és jelentkezzen be újra.

    A szerepkör már nem használható.

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

Megtekintheti a függőben lévő aktiválási kéréseit állapotát.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **saját kérések** kéri az Azure ad-ben és az Azure resource szerepkörök listájának megtekintéséhez.

    ![Saját kérések – Azure-erőforrás oldalról a függőben lévő kérelmek](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Görgessen jobbra megtekintéséhez a **állapot kérése** oszlop.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Jóváhagyást igénylő szerepkörök aktiválási nincs szükség, ha egy függőben lévő kérelem bármikor visszavonhatja.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **saját kérések**.

1. A szerepkör, amely megszakítja, kattintson a **Mégse** hivatkozásra.

    Ha a Mégse gombra kattint, a kérelem meg lesz szakítva. A szerepkör aktiválását ismét el küldjön új aktiválási kérelmet.

   ![Saját kérések listájáról a megszakítási műveletet kiemelésével](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nem engedélyekkel a szerepkör aktiválása után

A PIM szerepkör aktiválásakor az aktiválás előfordulhat, hogy nem azonnal továbbítódik az összes portálok, a kiemelt szerepkörű igénylő. Előfordul akkor is, ha propagálja a módosítást, webes gyorsítótárazás a portál az eredményezhet a módosítás nem hatállyal. Ha az aktiválás késik, Íme, mit kell tennie.

1. Jelentkezzen ki az Azure Portalon, és majd jelentkezzen be újra.

    Egy Azure-erőforrás szerepkör aktiválásakor látni fogja az aktiválás fázisa. Ha minden szakasz befejeződött, megjelenik egy **Kijelentkezés** hivatkozásra. Ez a hivatkozás segítségével jelentkezzen ki. Aktiválási késleltetést a legtöbb esetben ez fogja megoldani.

1. A PIM ellenőrizze, hogy a szerepkör tagjaként szerepel.

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrás szerepkörök meghosszabbítása vagy megújítása](pim-resource-roles-renew-extend.md)
- [A PIM saját Azure AD-szerepkörök aktiválása](pim-how-to-activate-role.md)
