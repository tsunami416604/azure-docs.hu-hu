---
title: Azure-beli erőforrás-szerepkörök aktiválása a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan aktiválhatja Azure-beli erőforrás-szerepköreit Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e2bf1253f1c2f53b839a73034fcdb33f3aab463
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804334"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Azure-beli erőforrás-szerepkörök aktiválása a PIM-ben

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával az Azure-erőforrások jogosult szerepkör-tagjai ütemezhetik az aktiválást egy későbbi dátumra és időpontra. Kiválaszthatnak egy adott aktiválási időtartamot is a maximumon belül (a rendszergazdák által konfigurálva).

Ez a cikk azoknak a tagoknak szól, akiknek aktiválniuk kell az Azure-beli erőforrás-szerepkört a PIM-ben.

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy Azure-erőforrás szerepkört kell igénybe vennie, az aktiválást a PIM **saját szerepkörök** navigációs lehetősége használatával kérheti le.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**. További információ a PIM-csempe irányítópulthoz való hozzáadásáról: [a PIM használatának megkezdése](pim-getting-started.md).

1. Kattintson **a saját szerepkörök**elemre.

    ![Saját szerepkörök lap, amely az aktiválható szerepköröket mutatja be](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Az **Azure-erőforrás szerepköreire** kattintva megtekintheti a jogosult Azure-erőforrás-szerepkörök listáját.

   ![Saját szerepkörök – Azure Resource roles lap](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Az **Azure erőforrás-szerepkörök** listában keresse meg az aktiválni kívánt szerepkört.

    ![Azure Resource roles – a jogosult szerepkörök listája](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Az aktiválás ablaktábla megnyitásához kattintson az **aktiválás** gombra.

1. Ha a szerepkörhöz többtényezős hitelesítés (MFA) szükséges, kattintson **az identitás ellenőrzése lehetőségre a folytatás előtt**. Egy munkamenetben csak egyszer kell hitelesítést végezni.

    ![Identitás ellenőrzése az MFA-val a szerepkör aktiválása előtt](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Kattintson a **személyazonosság ellenőrzése** lehetőségre, és kövesse az utasításokat a további biztonsági ellenőrzés megadásához.

    ![Biztonsági ellenőrzés, például PIN-kód megadására szolgáló képernyő](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Ha csökkentett hatókört szeretne megadni, kattintson a **hatókör** elemre az erőforrás-szűrő ablaktábla megnyitásához.

    A legjobb megoldás, ha csak a szükséges erőforrásokhoz kér hozzáférést. Az erőforrás-szűrő panelen megadhatja azokat az erőforráscsoportokat vagy erőforrásokat, amelyekhez hozzáférésre van szüksége.

    ![Aktiválás – erőforrás-szűrő ablaktábla hatókör megadásához](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Ha szükséges, egy egyéni aktiválás kezdési idejét kell megadnia. A tag aktiválása a kiválasztott időpont után megtörtént.

1. Az **OK** mezőben adja meg az aktiválási kérelem okát.

    ![Befejezte az aktiválás panel hatókörét, kezdési idejét, időtartamát és okát](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Kattintson az **aktiválás**gombra.

    Ha a szerepkörhöz nincs szükség jóváhagyásra, az aktiválva lesz, és a rendszer hozzáadja az aktív szerepkörök listájához. Ha a szerepkört szeretné használni, kövesse a következő szakaszban ismertetett lépéseket.

    Ha a [szerepkör jóváhagyást kér](pim-resource-roles-approval-workflow.md) az aktiváláshoz, a böngésző jobb felső sarkában megjelenik egy értesítés, amely arról tájékoztatja, hogy a kérés jóváhagyása függőben van.

    ![Aktiválási kérelem függőben lévő jóváhagyási értesítés](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Az aktiválás után azonnal használjon szerepkört

Ha az aktiválást követően bármilyen késleltetést észlel, kövesse az alábbi lépéseket az Azure-erőforrások szerepköreinek azonnali használatához.

1. Nyissa meg Azure AD Privileged Identity Management.

1. A **saját szerepkörök** lehetőségre kattintva megtekintheti a jogosult Azure ad-szerepköröket és az Azure-erőforrás-szerepkörök listáját.

1. Kattintson az **Azure-erőforrás szerepkörei**elemre.

1. Kattintson az **aktív szerepkörök** fülre.

1. Ha a szerepkör aktív, jelentkezzen ki a portálról, majd jelentkezzen be újra.

    A szerepkörnek mostantól elérhetőnek kell lennie a használatra.

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

A függőben lévő kérések állapotát az aktiváláshoz tekintheti meg.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Kattintson a **saját kérések** lehetőségre az Azure ad-szerepkör és az Azure-erőforrás-szerepkörre vonatkozó kérések listájának megtekintéséhez.

    ![Saját kérések – Azure Resource oldal, amely a függőben lévő kéréseket jeleníti meg](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Görgessen jobbra a **kérelem állapota** oszlop megtekintéséhez.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Ha nem igényel olyan szerepkör aktiválását, amely jóváhagyást igényel, bármikor lemondhatja a függőben lévő kéréseket.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Kattintson **a saját kérések**lehetőségre.

1. A megszakítani kívánt szerepkörhöz kattintson a **Mégse** hivatkozásra.

    Ha a Mégse gombra kattint, a rendszer megszakítja a kérést. A szerepkör újbóli aktiválásához új aktiválási kérést kell benyújtania.

   ![Saját kérések listája a Mégse művelettel kiemelve](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>A szerepkör aktiválását követően az engedélyek kiosztása nem történik meg

Ha a PIM-ben aktivál egy szerepkört, előfordulhat, hogy az aktiválás nem terjed ki azonnal az összes olyan portálra, amely a Kiemelt szerepkört igényli. Néha az is előfordul, hogy még ha a módosítás propagálása meg is történik, a portálon történő webes gyorsítótárazás miatt a módosítás nem lép azonnal életbe. Ha az aktiválás késleltetve van, a következő műveleteket kell végrehajtania.

1. Jelentkezzen ki az Azure Portalról, majd jelentkezzen be újra.

    Ha aktivál egy Azure-erőforrás-szerepkört, megjelenik az aktiválás szakaszai. Ha az összes szakasz kész, megjelenik a **Kijelentkezés** hivatkozás. Erre a hivatkozásra kattintva kijelentkezhet. Ez a legtöbb esetben megoldja az aktiválás késésének problémáját.

1. A PIM-ben ellenőrizze, hogy szerepel-e a szerepkör tagjaként.

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás szerepköreinek kiterjesztése vagy megújítása a PIM-ben](pim-resource-roles-renew-extend.md)
- [Saját Azure AD-szerepkörök aktiválása a PIM-ben](pim-how-to-activate-role.md)
