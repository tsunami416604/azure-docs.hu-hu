---
title: Azure AD-szerepkörök kiosztása Privileged Identity Management-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan rendelhet hozzá Azure AD-szerepköröket Azure AD Privileged Identity Management (PIM) szolgáltatáshoz.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809204"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörök kiosztása Privileged Identity Management

A Azure Active Directory (Azure AD) használatával a globális rendszergazda **állandó** Azure ad-rendszergazdai szerepkör-hozzárendeléseket végezhet. Ezek a szerepkör-hozzárendelések a [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) vagy a [PowerShell-parancsok](/powershell/module/azuread#directory_roles)használatával hozhatók létre.

A Azure AD Privileged Identity Management (PIM) szolgáltatás azt is lehetővé teszi, hogy a Kiemelt szerepkörű rendszergazdák állandó rendszergazdai szerepkör-hozzárendeléseket hozzanak. Emellett a Kiemelt szerepkörű rendszergazdák **jogosultak** lehetnek az Azure ad rendszergazdai szerepköreire. Egy jogosult rendszergazda szükség esetén aktiválhatja a szerepkört, majd az engedélyeik lejárta után lejárnak.

## <a name="make-a-user-eligible-for-a-role"></a>Szerepkörre jogosult felhasználó létrehozása

Kövesse az alábbi lépéseket, hogy egy felhasználó jogosult legyen egy Azure AD-rendszergazdai szerepkörre.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörnek.

    További információ a Privileged Identity Management kezeléséhez szükséges további rendszergazdai hozzáférésről: [hozzáférés biztosítása más rendszergazdák számára a Privileged Identity Management kezeléséhez](pim-how-to-give-access-to-pim.md).

1. Nyissa meg **Azure ad Privileged Identity Management**.

    Ha még nem indította el Privileged Identity Management a Azure Portalban, ugorjon a [Privileged Identity Management használatának megkezdéséhez](pim-getting-started.md).

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válasszon **szerepköröket** vagy **tagokat**.

    ![Azure AD-szerepkörök Kiemelt szerepkörökkel és tagokkal menüpont kiemelve](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Válassza a **tag hozzáadása** elemet a felügyelt tagok hozzáadásának megnyitásához.

1. Válassza a **szerepkör kiválasztása**lehetőséget, válassza ki a kezelni kívánt szerepkört, majd válassza a **kiválasztás**lehetőséget.

    ![Válassza ki az Azure AD-szerepköröket felsoroló szerepkör panelt](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Válassza a **Tagok kiválasztása**lehetőséget, válassza ki a szerepkörhöz hozzárendelni kívánt felhasználókat, majd válassza a **kiválasztás**lehetőséget.

    ![Válassza ki a tagok ablaktáblát, ahol kiválaszthat egy felhasználót](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. A felügyelt Tagok hozzáadása lapon kattintson az **OK** gombra a felhasználó szerepkörhöz való hozzáadásához.

1. A szerepkörök listájában válassza ki az imént hozzárendelt szerepkört a tagok listájának megtekintéséhez.

     Ha a szerepkör hozzá van rendelve, a kiválasztott felhasználó a szerepkörhöz **jogosultként** fog megjelenni a tagok listájában.

    ![A szerepkörök tagjai az aktiválási állapotukkal együtt jelennek meg](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Most, hogy a felhasználó jogosult a szerepkörre, tájékoztassa őket arról, hogy a [Privileged Identity Management Azure ad-szerepköreinek aktiválása](pim-how-to-activate-role.md)című témakör utasításai szerint aktiválhatja azt.

    A rendszer arra kéri a jogosult rendszergazdákat, hogy regisztráljanak az Azure Multi-Factor Authentication (MFA) az aktiválás során. Ha a felhasználó nem regisztrálhat az MFA-ra, vagy egy Microsoft-fiók (általában @outlook.com) használ, akkor minden szerepkörében állandónak kell lennie.

## <a name="make-a-role-assignment-permanent"></a>Szerepkör-hozzárendelés véglegesvé tétele

Alapértelmezés szerint az új felhasználók csak Azure AD-rendszergazdai szerepkörre jogosultak. Kövesse az alábbi lépéseket, ha a szerepkör-hozzárendelést véglegesen szeretné végrehajtani.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válasszon **tagokat**.

    ![Azure AD-szerepkörök – a tagok listája a szerepkör-és aktiválási állapotot jeleníti meg](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Válasszon ki egy olyan **jogosult** szerepkört, amelyet véglegesen szeretne végezni.

1. Válassza a **továbbiak** lehetőséget, majd válassza a **make perm**lehetőséget.

    ![Ablaktábla, amely egy olyan felhasználót listáz, amely jogosult egy szerepkörre a további menüpontok megnyitásakor](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A szerepkör mostantól **állandóként**van listázva.

    ![A tagok listája a már állandó szerepkör-és aktiválási állapotot jeleníti meg](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Felhasználó eltávolítása a szerepkörből

Eltávolíthatja a felhasználókat a szerepkör-hozzárendelésekről, de győződjön meg arról, hogy mindig van legalább egy olyan felhasználó, aki állandó globális rendszergazda. Ha nem tudja biztosan, hogy mely felhasználóknak van szüksége szerepkör-hozzárendeléseire, [elindíthatja a szerepkör hozzáférési felülvizsgálatát](pim-how-to-start-security-review.md).

A következő lépésekkel távolíthat el egy adott felhasználót egy Azure AD-rendszergazdai szerepkörből.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válasszon **tagokat**.

    ![Azure AD-szerepkörök – a tagok listája, amely a szerepköröket és az aktiválási Statt mutatja](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Válassza ki az eltávolítani kívánt szerepkör-hozzárendelést.

1. Válassza a **továbbiak** , majd az **Eltávolítás**lehetőséget.

    ![Ablaktábla, amely egy állandó szerepkörrel rendelkező felhasználót listáz a további menüpontok megnyitásakor](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Ha a rendszer a művelet megerősítését kéri, válassza az **Igen**lehetőséget.

    ![Üzenet, amely megkérdezi, hogy szeretné-e eltávolítani a tagot a szerepkörből](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A szerepkör-hozzárendelés el lett távolítva.

## <a name="authorization-error-when-assigning-roles"></a>Engedélyezési hiba a szerepkörök hozzárendeléséhez

Forgatókönyv: az Azure-erőforrásokhoz tartozó aktív tulajdonosi vagy felhasználói hozzáférési rendszergazdaként megtekintheti az erőforrást Privileged Identity Managementon belül, de nem végezhet olyan műveleteket, mint például a jogosult hozzárendelés vagy a szerepkör-hozzárendelések listájának megtekintése a erőforrás-Áttekintés lap. A műveletek bármelyike hitelesítési hibát eredményez.

Szerepkörök hozzárendeléséhez az MS-PIM szolgáltatásnevet hozzá kell rendelni a [felhasználói hozzáférés rendszergazdai szerepkörhöz](../../role-based-access-control/built-in-roles.md#user-access-administrator) az Azure-erőforrásokhoz való hozzáféréshez az Azure-beli szerepköralapú hozzáférés-vezérlésben (az Azure ad felügyeleti szerepköreivel ellentétben). Ahelyett, hogy megvárná, amíg az MS-PIM hozzá nem rendeli a felhasználói hozzáférés rendszergazdai szerepkört, manuálisan is hozzárendelheti.

A következő lépésekkel rendelheti hozzá a felhasználói hozzáférés rendszergazdai szerepkört az MS-PIM szolgáltatáshoz az előfizetéshez.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) globális rendszergazdaként az Azure ad-szervezetben.

1. Válassza **a minden szolgáltatás** , majd az **előfizetések**lehetőséget.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

1. Válassza ki a **szerepkör-hozzárendeléseket** az előfizetés hatókörében lévő szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Egy előfizetés hozzáférés-vezérlés (IAM) panelje](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Győződjön meg arról, hogy az **MS-PIM** egyszerű szolgáltatásnév hozzá van-e rendelve a **felhasználói hozzáférés rendszergazdai** szerepkörhöz.

1. Ha nem, válassza a **szerepkör-hozzárendelés hozzáadása** lehetőséget a **szerepkör-hozzárendelés hozzáadása** ablaktábla megnyitásához.

1. A **szerepkör** legördülő listában válassza ki a **felhasználói hozzáférés rendszergazdai** szerepkört.

1. A **Select (kiválasztás** ) listában keresse meg és válassza ki az **MS-PIM** egyszerű szolgáltatásnevet.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása – engedélyek hozzáadása az MS-PIM egyszerű szolgáltatásnév számára](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. A szerepkör hozzárendeléséhez válassza a **Mentés** lehetőséget.

   Néhány pillanat elteltével az MS-PIM egyszerű szolgáltatásnév a felhasználói hozzáférés rendszergazdai szerepköréhez van rendelve az előfizetés hatókörében.

   ![Hozzáférés-vezérlés (IAM) panel, amely az MS-PIM felhasználói hozzáférés-felügyeleti szerepkörének hozzárendelését jeleníti meg](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD rendszergazdai szerepkör beállításainak konfigurálása Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)
