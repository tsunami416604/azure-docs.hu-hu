---
title: Az Azure AD-szerepkörök aktiválása a PIM szolgáltatásban – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogyan aktiválhatja az Azure AD-szerepköröket az Azure AD kiemelt identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499184"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Saját Azure AD-szerepkörök aktiválása a PIM-ben

Az Azure Active Directory (Azure AD) privilegizált identitáskezelése (PIM) leegyszerűsíti, hogy a vállalatok hogyan kezelik az Azure AD és más Microsoft online szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való kiemelt hozzáférést.  

Ha felügyeleti szerepkörre lett jogosult, ez azt jelenti, hogy aktiválhatja ezt a szerepkört, amikor kiemelt műveleteket kell végrehajtania. Ha például alkalmanként kezeli az Office 365 szolgáltatásait, előfordulhat, hogy a szervezet kiemelt szerepkör-rendszergazdái nem teszik állandó globális rendszergazdává, mivel ez a szerepkör más szolgáltatásokra is hatással van. Ehelyett ők teszik jogosulttá az Azure AD-szerepkörök, például az Exchange Online-rendszergazda. Kérheti, hogy aktiválja ezt a szerepkört, amikor szüksége van a jogosultságait, és akkor lesz rendszergazdai ellenőrzés egy előre meghatározott ideig.

Ez a cikk rendszergazdák számára, akiknek aktiválniuk kell az Azure AD szerepkör privilegizált identitáskezelés.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

2019 novemberétől kezdődően a kiemelt identitáskezelés Azure AD-szerepkörök része frissül egy új verzióra, amely megfelel az Azure-erőforrás-szerepkörök élményének. Ez további funkciókat, valamint [a meglévő API-t is módosítja.](azure-ad-roles-features.md#api-changes) Az új verzió bevezetés alatt, hogy mely eljárásokat követi ebben a cikkben attól függ, hogy a kiemelt identitáskezelés jelenleg rendelkezik-e. Ebben a szakaszban ismertetett lépéseket követve határozza meg, hogy a Kiemelt identitáskezelés melyik verzióját használja. Miután ismeri a kiemelt identitáskezelés verzióját, kiválaszthatja a cikkben az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörrel.
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** Ha az áttekintő oldal tetején szalaghirdetés található, kövesse a cikk **Új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **Előző verzió** lap utasításait.

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Új verzió](#tab/new)

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy Azure AD-szerepkört kell vállalnia, kérheti az aktiválást a **Szerepkörök** navigációs beállítás használatával a Kiemelt identitáskezelésben.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** A Kiemelt identitáskezelés csempe irányítópulthoz való hozzáadásáról a [Kiemelt identitáskezelés használatának megkezdése című](pim-getting-started.md)témakörben talál további információt.

1. Válassza **a Saját szerepkörök**lehetőséget, majd az **Azure AD-szerepkörök** et a jogosult Azure AD-szerepkörök listájának megtekintéséhez.

    ![Saját szerepkörök lap, amelyen az aktiválható szerepkörök láthatók](./media/pim-how-to-activate-role/my-roles.png)

1. Az **Azure AD-szerepkörök** listájában keresse meg az aktiválni kívánt szerepkört.

    ![Azure AD-szerepkörök – Saját jogosult szerepkörök listája](./media/pim-how-to-activate-role/activate-link.png)

1. **Az Aktiválás gombra** az Aktiválás ablaktábla megnyitásához válassza az Aktiválás lehetőséget.

    ![Azure AD-szerepkörök – az aktiválási lap időtartamot és hatókört tartalmaz](./media/pim-how-to-activate-role/activate-page.png)

1. Ha a szerepkör többtényezős hitelesítést igényel, a folytatás előtt válassza **a Személyazonosság ellenőrzése lehetőséget.** Munkamenetenként csak egyszer kell hitelesítenie magát.

    ![Személyazonosságom ellenőrzése az MFA-val a szerepkör aktiválása előtt](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Válassza **a Személyazonosságom ellenőrzése lehetőséget,** és kövesse az utasításokat a további biztonsági ellenőrzéshez.

    ![Képernyő a biztonsági ellenőrzés, például a PIN-kód biztosításához](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Ha csökkentett hatókört szeretne megadni, válassza a **Hatókör** lehetőséget a szűrőablak megnyitásához. A szűrő ablaktáblán megadhatja az Azure AD-erőforrásokat, amelyekhez hozzáférésre van szüksége. Ajánlott csak a szükséges erőforrásokhoz kérni a hozzáférést.

1. Szükség esetén adjon meg egy egyéni aktiválási kezdési időpontot. Az Azure AD szerepkör a kiválasztott idő után aktiválódik.

1. Az **Ok** mezőbe írja be az aktiválási kérelem okát.

1. Válassza **az Aktiválás**lehetőséget.

    Ha a szerepkör nem igényel jóváhagyást, akkor aktiválódik, és hozzáadja az aktív szerepkörök listájához. Ha használni szeretné a szerepkört, kövesse a következő szakaszlépéseit.

    ![Befejezett Aktiválás ablaktábla hatókörrel, kezdési idővel, időtartammal és okokkal](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    Ha a [szerepkör aktiválásához jóváhagyás szükséges,](pim-resource-roles-approval-workflow.md) a böngésző jobb felső sarkában egy értesítés jelenik meg, amely tájékoztatja, hogy a kérelem jóváhagyásra vár.

    ![Az aktiválási kérelem jóváhagyási értesítésre vár](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Szerepkör használata közvetlenül az aktiválás után

Aktiválás utáni késedelem esetén kövesse az alábbi lépéseket az aktiválás után az Azure AD-szerepkörök azonnali használatához.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Válassza **a Saját szerepkörök** lehetőséget a jogosult Azure AD-szerepkörök és az Azure-erőforrás-szerepkörök listájának megtekintéséhez.

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza az **Aktív szerepkörök** lapot.

1. Ha a szerepkör aktív, jelentkezzen ki a portálról, és jelentkezzen be újra.

    A szerepkörnek most már használhatónak kell lennie.

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

Megtekintheti az aktiválásra váró kérelmek állapotát.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Válassza **a Saját kérések** lehetőséget az Azure AD-szerepkör és az Azure-erőforrás-szerepkör-kérések listájának megtekintéséhez.

    ![Saját kérések – Azure AD oldal a függőben lévő kéréseket megjelenítő lapon](./media/pim-how-to-activate-role/my-requests-page.png)

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

    Az Azure AD-szerepkör aktiválásakor látni fogja az aktiválás szakaszait. Ha az összes szakasz kész, megjelenik a **Kijelentkezés** hivatkozás. Ezen a hivatkozáson kijelentkezhet. Ez megoldja a legtöbb esetben az aktiválás késleltetése.

1. A Kiemelt identitáskezelés, ellenőrizze, hogy a szerepkör tagjaként szerepel.In Privileged Identity Management, verify that you are listed as the member of the role.

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="activate-a-role"></a>Szerepkör aktiválása

Ha egy Azure AD-szerepkört kell vállalnia, kérheti az aktiválást a **Szerepkörök** navigációs beállítás használatával a Kiemelt identitáskezelésben.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** A Kiemelt identitáskezelés csempe irányítópulthoz való hozzáadásáról a [Kiemelt identitáskezelés használatának megkezdése című](pim-getting-started.md)témakörben talál további információt.

1. Kattintson **az Azure AD-szerepkörök**elemre.

1. Kattintson **a Saját szerepkörök** a jogosult Azure AD-szerepkörök listájának megtekintéséhez.

    ![Azure AD-szerepkörök – A jogosult vagy aktív szerepkörök listáját megjelenítő szerepkörök](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Keresse meg az aktiválni kívánt szerepkört.

    ![Azure AD-szerepkörök – Saját jogosult szerepkörök listája az Aktiválás hivatkozással](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Kattintson az **Aktiválás gombra** a Szerepkör aktiválásának részletei ablaktábla megnyitásához.

1. Ha a szerepkör többtényezős hitelesítést (MFA) igényel, a folytatás előtt kattintson **a Személyazonosság ellenőrzése elemre.** Munkamenetenként csak egyszer kell hitelesítenie magát.

    ![Az identitáspanel ellenőrzése az MFA-val a szerepkör aktiválása előtt](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Kattintson **a Személyazonosságom ellenőrzése** elemre, és kövesse az utasításokat a további biztonsági ellenőrzéshez.

    ![További biztonsági ellenőrző oldal, amely megkérdezi, hogyan léphetünk kapcsolatba Önnel](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Az Aktiválás ablaktábla megnyitásához kattintson az **Aktiválás** gombra.

    ![Aktiválási ablaktábla a kezdési idő, az időtartam, a jegy és az ok megadásához](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Szükség esetén adjon meg egy egyéni aktiválási kezdési időpontot.

1. Adja meg az aktiválás időtartamát.

1. Az **Aktiválás oka** mezőben adja meg az aktiválási kérelem okát. Egyes szerepkörökben meg kell adnia egy problémás jegyszámot.

    ![Befejezett aktiválás idotábla egyéni kezdési idővel, időtartammal, jeggyel és okokkal](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Kattintson **az Aktiválás gombra.**

    Ha a szerepkör nem igényel jóváhagyást, megjelenik egy **Aktiválás állapotablak,** amely az aktiválás állapotát jeleníti meg.

    ![Az aktiválási állapot lap az aktiválás három szakaszát mutatja](./media/pim-how-to-activate-role/activation-status.png)

    Miután az összes szakasz befejeződött, kattintson a **Kijelentkezés** hivatkozásra az Azure Portalból való kijelentkezéshez. Amikor újra bejelentkezik a portálra, most már használhatja a szerepkört.

    Ha a [szerepkör aktiválásához jóváhagyásszükséges,](./azure-ad-pim-approval-workflow.md) egy Azure-értesítés jelenik meg a böngésző jobb felső sarkában, és tájékoztatja, hogy a kérelem jóváhagyásra vár.

## <a name="view-the-status-of-your-requests"></a>A kérések állapotának megtekintése

Megtekintheti az aktiválásra váró kérelmek állapotát.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Kattintson **az Azure AD-szerepkörök**elemre.

1. A kérések listájának megtekintéséhez kattintson a **Saját kérések** elemre.

    ![Azure AD-szerepkörök – Saját kérések listája](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Szerepkör inaktiválása

A szerepkör aktiválása után automatikusan kikapcsol, amikor eléri az időkorlátot (jogosult időtartamot).

Ha a rendszergazdai feladatok korai végrehajtása, inaktiválhatja a szerepkört manuálisan az Azure AD kiemelt identitáskezelés.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Kattintson **az Azure AD-szerepkörök**elemre.

1. Kattintson **a Saját szerepkörök gombra.**

1. Kattintson **az Aktív szerepkörök** elemre az aktív szerepkörök listájának megtekintéséhez.

1. Keresse meg a használt szerepkört, majd kattintson **az Inaktiválás gombra.**

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem visszavonása

Ha nincs szüksége jóváhagyásra szoruló szerepkör aktiválására, bármikor visszavonhatja a függőben lévő kérelmet.

1. Nyissa meg az Azure AD kiemelt identitáskezelés.

1. Kattintson **az Azure AD-szerepkörök**elemre.

1. Kattintson **a Saját kérések elemre.**

1. A megszakítani kívánt szerepkörhöz kattintson a **Mégse gombra.**

    Ha a Mégse gombra kattint, a kérés megszakad. A szerepkör ismételt aktiválásához új aktiválási kérelmet kell benyújtania.

   ![Saját kérések lista a Mégse gomb kiemelve](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="permissions-are-not-granted-after-activating-a-role"></a>A szerepkör aktiválását követően az engedélyek kiosztása nem történik meg

Ha aktivál egy szerepkört a kiemelt identitáskezelés, az aktiválás nem feltétlenül propagálódik minden portálok, amelyek megkövetelik a kiemelt szerepkört. Néha az is előfordul, hogy még ha a módosítás propagálása meg is történik, a portálon történő webes gyorsítótárazás miatt a módosítás nem lép azonnal életbe. Ha az aktiválás késik, a következőket kell tennie.

1. Jelentkezzen ki az Azure Portalról, majd jelentkezzen be újra.

    Az Azure AD-szerepkör aktiválásakor látni fogja az aktiválás szakaszait. Ha az összes szakasz kész, megjelenik a **Kijelentkezés** hivatkozás. Ezen a hivatkozáson kijelentkezhet. Ez megoldja a legtöbb esetben az aktiválás késleltetése.

1. A Kiemelt identitáskezelés, ellenőrizze, hogy a szerepkör tagjaként szerepel.In Privileged Identity Management, verify that you are listed as the member of the role.

 ---

## <a name="next-steps"></a>További lépések

- [Az Azure AD-szerepkörök aktiválása a kiemelt identitáskezelésben](pim-how-to-activate-role.md)
