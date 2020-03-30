---
title: Azure-erőforrás-szerepkörök aktiválása a PIM-ben – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan aktiválhatja az Azure-erőforrás-szerepköröket az Azure AD kiemelt identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35c81f7bb478d91bd207327ea37c80aa1778142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023152"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Az Azure-erőforrás-szerepkörök aktiválása a kiemelt identitáskezelésben

A Kiemelt identitáskezelés (PIM) használatával engedélyezheti, hogy az Azure-erőforrások jogosult szerepkör-tagjai egy későbbi dátumra és időpontra ütemezhessék az aktiválást. Ők is kiválaszthatja egy adott aktiválásidőtartama a maximális (a rendszergazdák által konfigurált).

Ez a cikk azoknak a tagoknak szól, akiknek aktiválniuk kell az Azure-erőforrás szerepkört a kiemelt identitáskezelésben.

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy Azure-erőforrás szerepkört kell vállalnia, kérheti az aktiválást a **Saját szerepkörök** navigációs beállítás használatával a Kiemelt identitáskezelés ben.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** A Kiemelt identitáskezelés csempe irányítópulthoz való hozzáadásáról a [Kiemelt identitáskezelés használatának megkezdése című](pim-getting-started.md)témakörben talál további információt.

1. Válassza **a Saját szerepkörök lehetőséget.**

    ![Saját szerepkörök lap, amelyen az aktiválható szerepkörök láthatók](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Válassza ki **az Azure-erőforrás-szerepkörök** a jogosult Azure-erőforrás-szerepkörök listájának megtekintéséhez.

   ![Saját szerepkörök – Azure-erőforrás-szerepkörök lapja](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Az **Azure-erőforrás-szerepkörök** listájában keresse meg az aktiválni kívánt szerepkört.

    ![Azure-erőforrás-szerepkörök – Saját jogosult szerepkörök listája](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. **Az Aktiválás gombra** az Aktiválás ablaktábla megnyitásához válassza az Aktiválás lehetőséget.

1. Ha a szerepkör többtényezős hitelesítést igényel, a folytatás előtt válassza **a Személyazonosság ellenőrzése lehetőséget.** Munkamenetenként csak egyszer kell hitelesítenie magát.

    ![Személyazonosságom ellenőrzése az MFA-val a szerepkör aktiválása előtt](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Válassza **a Személyazonosságom ellenőrzése lehetőséget,** és kövesse az utasításokat a további biztonsági ellenőrzéshez.

    ![Képernyő a biztonsági ellenőrzés, például a PIN-kód biztosításához](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Ha csökkentett hatókört szeretne megadni, válassza a **Hatókör** lehetőséget az Erőforrás szűrő ablaktábla megnyitásához.

    Ajánlott eljárás, ha csak a szükséges erőforrásokhoz kér hozzáférést. Az Erőforrás szűrő ablaktáblán megadhatja azokat az erőforráscsoportokat vagy erőforrásokat, amelyekhez hozzá kell férnie.

    ![Aktiválás - Erőforrásszűrő ablaktábla a hatókör megadásához](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Szükség esetén adjon meg egy egyéni aktiválási kezdési időpontot. A tag a kiválasztott idő után aktiválódik.

1. Az **Ok** mezőbe írja be az aktiválási kérelem okát.

    ![Befejezett Aktiválás ablaktábla hatókörrel, kezdési idővel, időtartammal és okokkal](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Válassza **az Aktiválás**lehetőséget.

    Ha a szerepkör nem igényel jóváhagyást, akkor aktiválódik, és hozzáadja az aktív szerepkörök listájához. Ha használni szeretné a szerepkört, kövesse a következő szakaszlépéseit.

    Ha a [szerepkör aktiválásához jóváhagyás szükséges,](pim-resource-roles-approval-workflow.md) a böngésző jobb felső sarkában egy értesítés jelenik meg, amely tájékoztatja, hogy a kérelem jóváhagyásra vár.

    ![Az aktiválási kérelem jóváhagyási értesítésre vár](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Szerepkör használata közvetlenül az aktiválás után

Aktiválás utáni késedelem esetén kövesse az alábbi lépéseket az aktiválás után az Azure-erőforrás-szerepkörök azonnali használatához.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Válassza **a Saját szerepkörök** lehetőséget a jogosult Azure AD-szerepkörök és az Azure-erőforrás-szerepkörök listájának megtekintéséhez.

1. Válassza ki **az Azure-erőforrás-szerepköröket.**

1. Válassza az **Aktív szerepkörök** lapot.

1. Ha a szerepkör aktív, jelentkezzen ki a portálról, és jelentkezzen be újra.

    A szerepkörnek most már használhatónak kell lennie.

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

Megtekintheti az aktiválásra váró kérelmek állapotát.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Válassza **a Saját kérések** lehetőséget az Azure AD-szerepkör és az Azure-erőforrás-szerepkör-kérések listájának megtekintéséhez.

    ![Saját kérések – A függőben lévő kéréseket megjelenítő Azure-erőforráslap](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Görgessen jobbra a **Kérelem állapotoszlopának** megtekintéséhez.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem visszavonása

Ha nincs szüksége jóváhagyásra szoruló szerepkör aktiválására, bármikor visszavonhatja a függőben lévő kérelmet.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Válassza **a Saját kérések lehetőséget.**

1. A megszakítani kívánt szerepkörhöz válassza a **Mégse** hivatkozást.

    Ha a Mégse lehetőséget választja, a kérés megszakad. A szerepkör ismételt aktiválásához új aktiválási kérelmet kell benyújtania.

   ![Saját kéréslista a Kiemelve a Mégse művelettel](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>A szerepkör aktiválását követően az engedélyek kiosztása nem történik meg

Ha aktivál egy szerepkört a kiemelt identitáskezelés, az aktiválás nem feltétlenül propagálódik minden portálok, amelyek megkövetelik a kiemelt szerepkört. Néha az is előfordul, hogy még ha a módosítás propagálása meg is történik, a portálon történő webes gyorsítótárazás miatt a módosítás nem lép azonnal életbe. Ha az aktiválás késik, a következőket kell tennie.

1. Jelentkezzen ki az Azure Portalról, majd jelentkezzen be újra.

    Az Azure-erőforrás-szerepkör aktiválásakor látni fogja az aktiválás szakaszait. Ha az összes szakasz kész, megjelenik a **Kijelentkezés** hivatkozás. Ezen a hivatkozáson kijelentkezhet. Ez megoldja a legtöbb esetben az aktiválás késleltetése.

1. A Kiemelt identitáskezelés, ellenőrizze, hogy a szerepkör tagjaként szerepel.In Privileged Identity Management, verify that you are listed as the member of the role.

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás-szerepkörök bővítése vagy megújítása a kiemelt identitáskezelésben](pim-resource-roles-renew-extend.md)
- [Az Azure AD-szerepkörök aktiválása a kiemelt identitáskezelésben](pim-how-to-activate-role.md)
