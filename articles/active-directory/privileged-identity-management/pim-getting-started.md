---
title: A PIM használatának megkezdése – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti és kezdheti a Azure AD Privileged Identity Management (PIM) használatát a Azure Portalban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733743f4680f3197a1754d87b4672641b2321907
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399329"
---
# <a name="start-using-privileged-identity-management"></a>A Privileged Identity Management használatának első lépései

A Privileged Identity Management (PIM) segítségével a Azure Active Directory (Azure AD) szervezeten belül kezelheti, vezérelheti és figyelheti a hozzáférést. Ez a hatókör magában foglalja az Azure-erőforrások, az Azure AD és más Microsoft-online szolgáltatások, például az Office 365 vagy a Microsoft Intune elérését.

Ez a cikk bemutatja, hogyan engedélyezheti és kezdheti a Privileged Identity Management használatát.

## <a name="prerequisites"></a>Előfeltételek

Privileged Identity Management használatához a következő licencek egyike szükséges:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5

További információ: [a Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Első személy a PIM használatához

Ha Ön az első, aki a címtárban Privileged Identity Management használni, a rendszer automatikusan hozzárendeli a [biztonsági rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) és a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkört a címtárhoz. Csak a Kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók Azure AD-beli szerepkör-hozzárendeléseit. Emellett dönthet úgy is, hogy futtatja a [biztonsági varázslót](pim-security-wizard.md) , amely végigvezeti a kezdeti felderítési és hozzárendelési élményen.

## <a name="enable-pim"></a>PIM engedélyezése

A Privileged Identity Management a címtárban való használatának megkezdéséhez először engedélyeznie kell a Privileged Identity Management.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a címtár globális rendszergazdájaként.

    A címtár Privileged Identity Managementának engedélyezéséhez globális rendszergazdának kell lennie (például @yourdomain.com), nem Microsoft-fiók (például @outlook.com).

1. Kattintson a **minden szolgáltatás** elemre, és keresse meg a **Azure ad Privileged Identity Management** szolgáltatást.

    ![Azure AD Privileged Identity Management az összes szolgáltatásban](./media/pim-getting-started/pim-all-services-find.png)

1. Kattintson ide a Privileged Identity Management rövid útmutató megnyitásához.

1. A listában kattintson a **beleegyezik a PIM-be**elemre.

    ![A Privileged Identity Management engedélyezéséhez Privileged Identity Management beleegyezett.](./media/pim-getting-started/consent-pim.png)

1. Kattintson a **személyazonosság ellenőrzése** lehetőségre az identitás Azure MFA-vel való ellenőrzéséhez. A rendszer megkéri, hogy válasszon egy fiókot.

    ![Fiók kiválasztása ablak az identitás ellenőrzéséhez](./media/pim-getting-started/pick-account.png)

1. Ha további információra van szükség az ellenőrzéshez, végigvezeti a folyamaton. További információkért lásd: [Segítség kérése kétlépéses ellenőrzéssel](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

    ![További információ szükséges ablak ha a szervezetnek további információra van szüksége](./media/pim-getting-started/more-information-required.png)

    Előfordulhat például, hogy a rendszer megkéri a telefonos ellenőrzés megadására.

    ![További biztonsági ellenőrző oldal, amely megkérdezi, hogyan léphet kapcsolatba Önnel](./media/pim-getting-started/additional-security-verification.png)

1. Az ellenőrzési folyamat befejezése után kattintson a **beleegyezik** gombra.

1. A megjelenő üzenetben kattintson az **Igen** gombra a Privileged Identity Management szolgáltatáshoz való hozzájárulásukat.

    ![Beleegyezett, hogy Privileged Identity Management üzenetet a beleegyező folyamat befejezéséhez](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>A PIM regisztrálása Azure AD-szerepkörökhöz

Miután engedélyezte a Privileged Identity Management a címtárban, regisztrálnia kell Privileged Identity Management az Azure AD-szerepkörök kezeléséhez.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

    ![Privileged Identity Management regisztrálása Azure AD-szerepkörökhöz](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Kattintson a **regisztráció**gombra.

1. A megjelenő üzenetben kattintson az **Igen** gombra az Azure ad-szerepkörök kezeléséhez Privileged Identity Management regisztrálásához.

    ![Privileged Identity Management regisztrálása az Azure AD-szerepkörökhöz üzenet](./media/pim-getting-started/sign-up-pim-message.png)

    Ha a regisztráció befejeződik, az Azure AD-beállítások engedélyezve lesznek. Előfordulhat, hogy frissítenie kell a portált.

    További információ a Privileged Identity Management-védelemmel ellátott Azure-erőforrások felderítéséről és kiválasztásáról: az [Azure-erőforrások felderítése Privileged Identity Management-ben való kezeléshez](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

A Privileged Identity Management beállítása után elindíthatja az Identitáskezelés-felügyeleti feladatait.

![Navigációs ablak Privileged Identity Management feladatok és kezelési beállítások megjelenítése](./media/pim-getting-started/pim-quickstart-tasks.png)

| Feladat + kezelés | Leírás |
| --- | --- |
| **Saját szerepkörök**  | Megjeleníti az Önhöz hozzárendelt jogosult és aktív szerepkörök listáját. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket. |
| **Saját kérések** | Megjeleníti a jogosult szerepkör-hozzárendelések aktiválásához függőben lévő kéréseit. |
| **Kérések jóváhagyása** | Megjeleníti azoknak a kéréseknek a listáját, amelyekkel az Ön által jóváhagyott címtárban lévő felhasználók jogosult szerepköröket aktiválhat. |
| **Hozzáférés áttekintése** | Felsorolja a befejezéshez hozzárendelt aktív hozzáférési felülvizsgálatokat, függetlenül attól, hogy saját maga vagy valaki másnak tekinti-e meg a hozzáférést. |
| **Azure AD-szerepkörök** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure AD szerepkör-hozzárendelések kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |
| **Azure-erőforrások** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure-erőforrás szerepkör-hozzárendeléseinek kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>PIM-csempe hozzáadása az irányítópulthoz

Annak érdekében, hogy megkönnyítse a Privileged Identity Management megnyitását, vegyen fel egy Privileged Identity Management csempét a Azure Portal irányítópultra.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

1. Kattintson a **minden szolgáltatás** elemre, és keresse meg a **Azure ad Privileged Identity Management** szolgáltatást.

    ![Azure AD Privileged Identity Management az összes szolgáltatásban](./media/pim-getting-started/pim-all-services-find.png)

1. Kattintson ide a Privileged Identity Management rövid útmutató megnyitásához.

1. Az irányítópultra rögzítheti az Privileged Identity Management rövid útmutató panelt az **irányítópulton** .

    ![Gombostű ikonja Privileged Identity Management panel rögzítése az irányítópulton](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Az Azure-irányítópulton a következőhöz hasonló csempe látható:

    ![Privileged Identity Management rövid útmutató csempe az irányítópulton](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [A Privileged Identity Management felügyelni kívánt Azure-erőforrások felderítése](pim-resource-roles-discover-resources.md)
