---
title: Azure AD-szerepkörök aktiválása a PIM-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan aktiválhatja az Azure AD-szerepköröket Azure AD Privileged Identity Management (PIM) alkalmazásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 07/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb1041cb5fc4aa7503f5ae9de6ffc74ba72052b8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421399"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Saját Azure AD-szerepkörök aktiválása a PIM-ben

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) leegyszerűsíti, hogy a vállalatok hogyan kezelhetik az Azure AD-ban és más Microsoft-online szolgáltatások, például az Office 365-ban vagy a Microsoft Intuneban található erőforrásokhoz való  

Ha már jogosult rendszergazdai szerepkörre, aktiválnia kell a szerepkör-hozzárendelést, ha emelt szintű műveleteket kell végrehajtania. Ha például alkalmanként felügyeli az Office 365-funkciókat, a szervezete Kiemelt szerepkörű rendszergazdái nem hozhatnak állandó globális rendszergazdát, mivel ez a szerepkör más szolgáltatásokra is hatással van. Ehelyett jogosult az Azure AD-szerepkörökre, például az Exchange Online-rendszergazdára. Ha a jogosultságai szükségesek, a szerepkör aktiválásához kérheti a rendszergazda vezérlését, és megadhatja az előre meghatározott időszakot.

Ez a cikk azoknak a rendszergazdáknak szól, akiknek aktiválniuk kell az Azure AD-szerepkört Privileged Identity Managementban.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

A 2019-es verziótól kezdődően a Privileged Identity Management Azure AD-szerepkörök részét egy új verzióra frissíti a rendszer, amely megfelel az Azure-erőforrás-szerepkörökkel kapcsolatos élményeknek. Ez további funkciókat hoz létre, valamint [a meglévő API módosításait](azure-ad-roles-features.md#api-changes)is. Az új verzió bevezetését követően a cikkben ismertetett eljárások a jelenleg használt Privileged Identity Management verziójától függenek. Az ebben a szakaszban ismertetett lépéseket követve meghatározhatja, hogy a Privileged Identity Management melyik verzióját kell megadnia. A Privileged Identity Management-verziójának megismerése után kiválaszthatja a jelen cikkben szereplő, az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörrel.
1. Nyissa meg **Azure ad Privileged Identity Management**. Ha az Áttekintés oldal tetején található egy szalagcím, kövesse a jelen cikk **új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **előző verzió** lapon megjelenő utasításokat.

    [![Válassza az Azure AD > Privileged Identity Management lehetőséget.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Új verzió](#tab/new)

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy Azure AD-szerepkört kell feltételezni, az aktiválást az Privileged Identity Management-ban megjelenő **saját szerepkörök** megnyitásával kérheti le.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**. További információ a Privileged Identity Management csempe irányítópulthoz való hozzáadásáról: a [Privileged Identity Management használatának megkezdése](pim-getting-started.md).

1. Válassza a **saját szerepkörök**lehetőséget, majd válassza az **Azure ad-szerepkörök** lehetőséget a jogosult Azure ad-szerepkörök listájának megtekintéséhez.

    ![Saját szerepkörök lap, amely az aktiválható szerepköröket mutatja be](./media/pim-how-to-activate-role/my-roles.png)

1. Az **Azure ad-szerepkörök** listában keresse meg az aktiválni kívánt szerepkört.

    ![Azure AD-szerepkörök – a jogosult szerepkörök listája](./media/pim-how-to-activate-role/activate-link.png)

1. Az aktiválás lap megnyitásához kattintson az **aktiválás** elemre.

    ![Azure AD-szerepkörök – az aktiválási oldal az időtartamot és a hatókört tartalmazza](./media/pim-how-to-activate-role/activate-page.png)

1. Ha a szerepkör többtényezős hitelesítést igényel, akkor **a folytatás előtt válassza a személyazonosság ellenőrzése**lehetőséget. Egy munkamenetben csak egyszer kell hitelesítést végezni.

    ![Identitás ellenőrzése az MFA-val a szerepkör aktiválása előtt](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Válassza a **személyazonosság ellenőrzése** lehetőséget, és kövesse az utasításokat a további biztonsági ellenőrzés megadásához.

    ![Biztonsági ellenőrzés, például PIN-kód megadására szolgáló képernyő](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Ha csökkentett hatókört szeretne megadni, válassza a **hatókör** lehetőséget a szűrő ablaktábla megnyitásához. A szűrés ablaktáblán megadhatja azokat az Azure AD-erőforrásokat, amelyekhez hozzá kell férnie. A legjobb megoldás, ha csak a szükséges erőforrásokhoz kér hozzáférést.

1. Ha szükséges, egy egyéni aktiválás kezdési idejét kell megadnia. Az Azure AD-szerepkör a kiválasztott idő után aktiválódik.

1. Az **OK** mezőben adja meg az aktiválási kérelem okát.

1. Válassza az **aktiválás**lehetőséget.

    Ha a [szerepkör jóváhagyást kér](pim-resource-roles-approval-workflow.md) az aktiváláshoz, a böngésző jobb felső sarkában megjelenik egy értesítés, amely arról tájékoztatja, hogy a kérés jóváhagyása függőben van.

    ![Aktiválási kérelem függőben lévő jóváhagyási értesítés](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

A függőben lévő kérések állapotát az aktiváláshoz tekintheti meg.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Válassza a **saját kérések** lehetőséget az Azure ad-szerepkör és az Azure-erőforrás-szerepkörre vonatkozó kérések listájának megtekintéséhez.

    ![Saját kérések – az Azure AD-oldal a függőben lévő kéréseket jeleníti meg](./media/pim-how-to-activate-role/my-requests-page.png)

1. Görgessen jobbra a **kérelem állapota** oszlop megtekintéséhez.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Ha nem igényel olyan szerepkör aktiválását, amely jóváhagyást igényel, bármikor lemondhatja a függőben lévő kéréseket.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Válassza **a saját kérések**lehetőséget.

1. A megszakítani kívánt szerepkörhöz válassza a **Mégse** hivatkozást.

    A Mégse gombra kattintva a rendszer megszakítja a kérést. A szerepkör újbóli aktiválásához új aktiválási kérést kell benyújtania.

   ![Saját kérések listája a Mégse művelettel kiemelve](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>A szerepkör aktiválását követően az engedélyek kiosztása nem történik meg

Amikor Privileged Identity Managementban aktiválja a szerepkört, előfordulhat, hogy az aktiválás nem terjed ki azonnal az összes olyan portálra, amelyhez Kiemelt szerepkör szükséges. Néha az is előfordul, hogy még ha a módosítás propagálása meg is történik, a portálon történő webes gyorsítótárazás miatt a módosítás nem lép azonnal életbe. Ha az aktiválás késleltetve van, a következő műveleteket kell végrehajtania.

1. Jelentkezzen ki az Azure Portalról, majd jelentkezzen be újra.

1. A Privileged Identity Managementban ellenőrizze, hogy a szerepkör tagja-e a listához.

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy Azure AD-szerepkört kell használnia, az aktiválást az Privileged Identity Management **saját szerepkörök** navigációs lehetőségének használatával kérheti le.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**. További információ a Privileged Identity Management csempe irányítópulthoz való hozzáadásáról: a [Privileged Identity Management használatának megkezdése](pim-getting-started.md).

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. A **saját szerepkörök** lehetőségre kattintva megtekintheti a jogosult Azure ad-szerepkörök listáját.

    ![Azure AD-szerepkörök – saját szerepkörök, amelyek a jogosult vagy az aktív szerepkörök listáját jelenítik meg](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Keresse meg az aktiválni kívánt szerepkört.

    ![Azure AD-szerepkörök – a jogosult szerepkörök listája, amely az aktiválási hivatkozást mutatja](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kattintson az **aktiválás** gombra a szerepkör-aktiválás részletei ablaktábla megnyitásához.

1. Ha a szerepkörhöz többtényezős hitelesítés (MFA) szükséges, kattintson **az identitás ellenőrzése lehetőségre a folytatás előtt**. Egy munkamenetben csak egyszer kell hitelesítést végezni.

    ![A szerepkör aktiválása előtt ellenőrizze az Identitásom panelt az MFA-ben](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Kattintson a **személyazonosság ellenőrzése** lehetőségre, és kövesse az utasításokat a további biztonsági ellenőrzés megadásához.

    ![További biztonsági ellenőrző oldal, amely megkérdezi, hogyan léphet kapcsolatba Önnel](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Az aktiválás ablaktábla megnyitásához kattintson az **aktiválás** gombra.

    ![Aktiválás ablaktábla a kezdési idő, az időtartam, a jegy és az OK megadásához](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Ha szükséges, egy egyéni aktiválás kezdési idejét kell megadnia.

1. Az aktiválás időtartamának megadása.

1. Az **aktiválási ok** mezőben adja meg az aktiválási kérelem okát. Egyes szerepkörökhöz meg kell adnia egy hibakódot.

    ![Befejezett aktiválás panel egyéni kezdési idővel, időtartammal, jegygel és indoklással](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Kattintson az **aktiválás**gombra.

    Ha a szerepkörhöz nincs szükség jóváhagyásra, megjelenik egy **aktiválási állapot** ablaktábla, amely az aktiválás állapotát jeleníti meg.

    ![Aktiválás állapota lap, amely az aktiválás három szakaszát mutatja be](./media/pim-how-to-activate-role/activation-status.png)

    Miután az összes szakasz elkészült, a kijelentkezés **hivatkozásra kattintva** kijelentkezhet a Azure Portalból. Ha ismét bejelentkezik a portálra, már használhatja a szerepkört.

    Ha a [szerepkör jóváhagyást kér](./azure-ad-pim-approval-workflow.md) az aktiváláshoz, a böngésző jobb felső sarkában megjelenik egy Azure-értesítés, amely tájékoztatja, hogy a kérelem jóváhagyása függőben van.

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

A függőben lévő kérések állapotát az aktiváláshoz tekintheti meg.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. A kérések listájának megtekintéséhez kattintson **a saját kérések** lehetőségre.

    ![Azure AD-szerepkörök – saját kérelmek listája](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Szerepkör inaktiválása

Miután aktiválta a szerepkört, a automatikusan inaktiválja az időkorlátot (jogosult időtartam).

Ha a rendszergazda feladatait korán végezte el, a szerepkörök inaktiválása manuálisan is elvégezhető a Azure AD Privileged Identity Management.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson **a saját szerepkörök**elemre.

1. Az aktív szerepkörök listájának megtekintéséhez kattintson az **aktív szerepkörök** elemre.

1. Keresse meg a használni kívánt szerepkört, majd kattintson az **inaktiválás**elemre.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Ha nem igényel olyan szerepkör aktiválását, amely jóváhagyást igényel, bármikor lemondhatja a függőben lévő kéréseket.

1. Nyissa meg Azure AD Privileged Identity Management.

1. Kattintson az **Azure ad-szerepkörök**elemre.

1. Kattintson **a saját kérések**lehetőségre.

1. A megszakítani kívánt szerepkörhöz kattintson a **Mégse** gombra.

    Ha a Mégse gombra kattint, a rendszer megszakítja a kérést. A szerepkör újbóli aktiválásához új aktiválási kérést kell benyújtania.

   ![Saját kérelmek listája a Mégse gombbal kiemelve](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>A szerepkör aktiválását követően az engedélyek kiosztása nem történik meg

Amikor Privileged Identity Managementban aktiválja a szerepkört, előfordulhat, hogy az aktiválás nem terjed ki azonnal az összes olyan portálra, amelyhez Kiemelt szerepkör szükséges. Néha az is előfordul, hogy még ha a módosítás propagálása meg is történik, a portálon történő webes gyorsítótárazás miatt a módosítás nem lép azonnal életbe. Ha az aktiválás késleltetve van, a következő műveleteket kell végrehajtania.

1. Jelentkezzen ki az Azure Portalról, majd jelentkezzen be újra.

    Ha aktivál egy Azure AD-szerepkört, látni fogja az aktiválás szakaszait. Ha az összes szakasz kész, megjelenik a **Kijelentkezés** hivatkozás. Ezt a hivatkozást a kijelentkezéshez használhatja. Ez a legtöbb esetben megoldja az aktiválási késleltetést.

1. A Privileged Identity Managementban ellenőrizze, hogy a szerepkör tagja-e a listához.

 ---

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök aktiválása Privileged Identity Management](pim-how-to-activate-role.md)
