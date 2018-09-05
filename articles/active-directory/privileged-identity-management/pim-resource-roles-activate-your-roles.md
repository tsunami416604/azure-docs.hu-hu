---
title: A PIM saját Azure-erőforrás szerepkörök aktiválása |} A Microsoft Docs
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
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666247"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>A PIM saját Azure-erőforrás szerepkörök aktiválása

Az Azure AD Privileged Identity Management (PIM) használ, jogosult szerepkör-tagok az Azure-erőforrások egy jövőbeli dátumot és időpontot az aktiválási ütemezheti. Ezek is kiválaszthatja, hogy egy adott aktiválási időtartamát, a maximális (a rendszergazdák által konfigurált) belül.

Ez a cikk olyan tagjai, akik a PIM az Azure-erőforrás szerepkörük aktiválnia kell.

## <a name="activate-a-role"></a>A szerepkör aktiválása

Amikor szüksége van egy Azure-erőforrás szerepkör, aktiválás segítségével kérheti a **saját szerepkörök** navigációs lehetőség az PIM-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**. A PIM-csempét az irányítópulton adásával kapcsolatos információkért lásd: [használatához a PIM](pim-getting-started.md).

1. Kattintson a **saját szerepkörök** a jogosult listájának megtekintéséhez az Azure AD-címtárbeli szerepkörök és az Azure-erőforrások szerepköreihez.

    ![Az Azure AD-címtárbeli szerepkörök és az Azure-erőforrások szerepköreihez – saját szerepkörök](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Az a **Azure-erőforrásszerepkörök** list, keresse meg a szerepkör aktiválásához.

    ![Azure-erőforrások szerepköreihez - szerepkörök listáját](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Kattintson a **aktiválás** az aktiválás panel megnyitásához.

1. Ha a szerepkör megköveteli a multi-factor authentication (MFA), kattintson a **a folytatás előtt azonosítsa magát**. Csak egyszer munkamenetenként hitelesítésre van.

    ![Ellenőrizze a többtényezős hitelesítéssel, mielőtt a szerepkör aktiválása](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Kattintson a **saját identitás ellenőrzése** és kövesse az utasításokat a további biztonsági ellenőrzést nyújtanak.

    ![További biztonsági ellenőrzés](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Ha meg szeretné határozni a csökkentett hatókört, kattintson a **hatókör** az erőforrás-szűrő ablaktábla megnyitásához.

    Ajánlott eljárás az, csak a szükséges erőforrások hozzáférés kérése. A Resource szűrő panelen az erőforráscsoport vagy erőforrás-hozzáférést is megadhat.

    ![Aktiválja - Erőforrásszűrő](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Ha szükséges, adja meg egy egyéni Aktiválás kezdete. A tag aktiválása a kijelölt idő után az.

1. Az a **OK** adja meg az aktiválási kérés okát.

    ![Befejezett aktiválás panel](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Kattintson a **aktiválása**.

    Ha a szerepkör nem igényel jóváhagyási, most már aktiválva van, és a szerepkör megjelenik az aktív szerepkörök listájában. Ha a [a szerepkör megköveteli a jóváhagyási](pim-resource-roles-approval-workflow.md) aktiválni, egy értesítés jelenik meg a böngészőben arról értesíti, a kérelem van függőben lévő jóváhagyási jobb felső sarkában található.

    ![Értesítés függőben lévő kérelem](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

Megtekintheti a függőben lévő aktiválási kéréseit állapotát.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **saját kérések** kéri az Azure AD-címtárbeli szerepkör és az Azure-erőforrás szerepkör listájának megtekintéséhez.

    ![Az Azure AD-címtárbeli szerepkörök és az Azure-erőforrások szerepköreihez – saját kérések](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Görgessen jobbra megtekintéséhez a **állapot kérése** oszlop.

## <a name="use-a-role-immediately-after-activation"></a>Olyan szerepkört használjon az aktiválás után azonnal

Gyorsítótárazás miatt aktiválások nem történik meg azonnal a frissítés nélkül az Azure Portalon. Ha az késleltetések lehetőségét csökkentheti a szerepkör aktiválása után van szüksége, használhatja a **alkalmazás-hozzáférés** lapot a portálon. Ezen a lapon elérhető alkalmazások azonnal ellenőrizze az új szerepkör-hozzárendeléseket.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **alkalmazás-hozzáférés** lapot.

    ![A PIM-alkalmazás-hozzáférés – képernyőkép](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Kattintson a **Azure-erőforrások** , nyissa meg újra a portálon a a **összes erőforrás** lapot.

    Ha erre a hivatkozásra kattint, frissítésének kényszerítése, és a egy új Azure-erőforrás szerepkör-hozzárendelések keresése van.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Jóváhagyást igénylő szerepkörök aktiválási nincs szükség, ha egy függőben lévő kérelem bármikor visszavonhatja.

1. Nyissa meg az Azure AD Privileged Identity Management.

1. Kattintson a **saját kérések**.

1. A szerepkör, amely megszakítja, kattintson a **Mégse** hivatkozásra.

    Ha a Mégse gombra kattint, a kérelem megszakítása A szerepkör aktiválását ismét el küldjön új aktiválási kérelmet.

   ![Függőben lévő kérelem megszakítása](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure-erőforrás szerepkörök meghosszabbítása vagy megújítása](pim-resource-roles-renew-extend.md)
- [A PIM az Azure AD-címtárbeli szerepkörök aktiválása](pim-how-to-activate-role.md)