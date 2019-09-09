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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 018cc54a770a0d18fa9af3d93fec2f5f720ce17b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804532"
---
# <a name="start-using-pim"></a>Ismerkedés a PIM-mel

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) segítségével felügyelheti, vezérelheti és figyelheti a szervezeten belüli hozzáférését. Ebbe a hatókörbe beletartozik az Azure-erőforrások, az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Ez a cikk bemutatja, hogyan engedélyezheti és kezdheti meg a PIM használatát.

## <a name="prerequisites"></a>Előfeltételek

A PIM használatához a következő licencek egyikével kell rendelkeznie:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

További információ: [a PIM használatára vonatkozó licencfeltételek](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Első személy a PIM használatához

Ha Ön az első, aki a PIM-t használja a címtárban, a rendszer automatikusan hozzárendeli a [biztonsági rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) és a [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkört a címtárhoz. Csak a Kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók Azure AD-beli szerepkör-hozzárendeléseit. Emellett dönthet úgy is, hogy futtatja a [biztonsági varázslót](pim-security-wizard.md) , amely végigvezeti a kezdeti felderítési és hozzárendelési élményen.

## <a name="enable-pim"></a>PIM engedélyezése

A PIM a címtárban való használatának megkezdéséhez először engedélyeznie kell a PIM-t.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a címtár globális rendszergazdájaként.

    Ha a PIM-t egy címtárhoz szeretné engedélyezni, globális rendszergazdának @yourdomain.comkell lennie egy szervezeti fiókkal (például @outlook.com:), nem pedig Microsoft-fiók (például).

1. Kattintson a **minden szolgáltatás** elemre, és keresse meg a **Azure ad Privileged Identity Management** szolgáltatást.

    ![Azure AD Privileged Identity Management az összes szolgáltatásban](./media/pim-getting-started/pim-all-services-find.png)

1. Kattintson ide a PIM rövid útmutatójának megnyitásához.

1. A listában kattintson a **beleegyezik a PIM-be**elemre.

    ![Beleegyezett a PIM-be a PIM engedélyezéséhez](./media/pim-getting-started/consent-pim.png)

1. Kattintson a **személyazonosság ellenőrzése** lehetőségre az identitás Azure MFA-vel való ellenőrzéséhez. A rendszer megkéri, hogy válasszon egy fiókot.

    ![Fiók kiválasztása ablak az identitás ellenőrzéséhez](./media/pim-getting-started/pick-account.png)

1. Ha további információra van szükség az ellenőrzéshez, végigvezeti a folyamaton. További információkért lásd: [Segítség kérése kétlépéses ellenőrzéssel](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![További információ szükséges ablak ha a szervezetnek további információra van szüksége](./media/pim-getting-started/more-information-required.png)

    Előfordulhat például, hogy a rendszer megkéri a telefonos ellenőrzés megadására.

    ![További biztonsági ellenőrző oldal, amely megkérdezi, hogyan léphet kapcsolatba Önnel](./media/pim-getting-started/additional-security-verification.png)

1. Az ellenőrzési folyamat befejezése után kattintson a **beleegyezik** gombra.

1. A megjelenő üzenetben kattintson az **Igen** gombra a PIM szolgáltatáshoz való hozzájárulásukat.

    ![Beleegyezik a PIM-üzenetbe az engedélyezési folyamat befejezéséhez](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>A PIM regisztrálása Azure AD-szerepkörökhöz

Miután engedélyezte a PIM használatát a címtárban, regisztrálnia kell a PIM-t az Azure AD-szerepkörök kezeléséhez.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure ad-szerepkörök**elemre.

    ![A PIM regisztrálása Azure AD-szerepkörökhöz](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Kattintson a **regisztráció**gombra.

1. A megjelenő üzenetben kattintson az **Igen** gombra a PIM regisztrálásához az Azure ad-szerepkörök kezeléséhez.

    ![A PIM regisztrálása Azure AD-szerepkörökhöz üzenet](./media/pim-getting-started/sign-up-pim-message.png)

    Ha a regisztráció befejeződik, az Azure AD-beállítások engedélyezve lesznek. Előfordulhat, hogy frissítenie kell a portált.

    További információ a PIM-védelemmel ellátott Azure-erőforrások felderítéséről és kiválasztásáról: [Azure-erőforrások felderítése a PIM-ben](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

Miután beállította a PIM-t, elvégezheti a személyazonosság-kezelési feladatait.

![Navigációs ablak a PIM-ban, a feladatok és a kezelési beállítások megjelenítése](./media/pim-getting-started/pim-quickstart-tasks.png)

| Feladat + kezelés | Leírás |
| --- | --- |
| **Saját szerepkörök**  | Megjeleníti az Önhöz hozzárendelt jogosult és aktív szerepkörök listáját. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket. |
| **Saját kérések** | Megjeleníti a jogosult szerepkör-hozzárendelések aktiválásához függőben lévő kéréseit. |
| **Kérések jóváhagyása** | Megjeleníti azoknak a kéréseknek a listáját, amelyekkel az Ön által jóváhagyott címtárban lévő felhasználók jogosult szerepköröket aktiválhat. |
| **Hozzáférés áttekintése** | Felsorolja a befejezéshez hozzárendelt aktív hozzáférési felülvizsgálatokat, függetlenül attól, hogy saját maga vagy valaki másnak tekinti-e meg a hozzáférést. |
| **Azure AD-szerepkörök** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure AD szerepkör-hozzárendelések kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |
| **Azure-erőforrások** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure-erőforrás szerepkör-hozzárendeléseinek kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>PIM-csempe hozzáadása az irányítópulthoz

Ahhoz, hogy könnyebb legyen a PIM megnyitása, vegyen fel egy PIM-csempét a Azure Portal irányítópultra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson a **minden szolgáltatás** elemre, és keresse meg a **Azure ad Privileged Identity Management** szolgáltatást.

    ![Azure AD Privileged Identity Management az összes szolgáltatásban](./media/pim-getting-started/pim-all-services-find.png)

1. Kattintson ide a PIM rövid útmutatójának megnyitásához.

1. Az irányítópultra rögzítheti **az irányítópulton** a PIM gyors üzembe helyezése panelt.

    ![Gombostű ikon a PIM panel rögzítéséhez az irányítópulton](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Az Azure-irányítópulton a következőhöz hasonló csempe látható:

    ![PIM gyors csempe az irányítópulton](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök kiosztása a PIM-ben](pim-how-to-add-role-to-user.md)
- [A PIM-ban kezelendő Azure-erőforrások felderítése](pim-resource-roles-discover-resources.md)
