---
title: A PIM - Azure használatának megkezdése |} A Microsoft Docs
description: Útmutató az Azure Portalon az Azure AD Privileged Identity Management (PIM) használatának megkezdéséhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190089"
---
# <a name="start-using-pim"></a>Ismerkedés a PIM-mel

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM), is felügyelheti, ellenőrizheti és hozzáférés figyelése a szervezeten belül. Ebbe a hatókörbe beletartozik az Azure-erőforrások, az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Ez a cikk bemutatja, hogyan veheti fel az Azure AD PIM alkalmazást az Azure Portal irányítópultjára.

## <a name="first-person-to-use-pim"></a>Első, aki a PIM használata

Ha Ön az első, aki a címtárban a PIM használatát tervezi, akkor automatikusan megkapja a [biztonsági rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) és [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörökhöz a címtárban. Csak a kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók Azure AD-címtárra vonatkozó szerepkör-hozzárendeléseit. Továbbá futtathatja a [biztonsági varázslót](pim-security-wizard.md). Ez végigvezeti Önt az első felderítési és hozzárendelési élményen.

## <a name="add-pim-tile-to-the-dashboard"></a>A PIM csempe hozzáadása az irányítópulthoz

Nyissa meg a PIM megkönnyítése, hozzá kell adnia a PIM csempe az Azure portal Irányítópultjára.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a címtár globális rendszergazdájaként.

1. Kattintson a **minden szolgáltatás** , és keresse meg a **Azure AD Privileged Identity Management** szolgáltatás.

    ![Az Azure AD Privileged Identity Management szolgáltatásra szolgáltatások összes](./media/pim-getting-started/pim-all-services-find.png)

1. Kattintson a PIM a rövid útmutató megnyitásához.

1. Ellenőrizze **panel rögzítése az irányítópulton** rögzítése az irányítópulton a PIM rövid panelt.

    ![Panel rögzítése az irányítópulton](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Az Azure-irányítópulton ekkor megjelenik egy csempe ehhez hasonló:

    ![A PIM rövid csempe](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

A PIM be van állítva, ha ezt a panelt az identitáskezelési feladatokat elvégzéséhez is használhatja.

![Felső szintű PIM-tevékenységek – képernyőkép](./media/pim-getting-started/pim-quickstart-tasks.png)

| Feladat + kezelése | Leírás |
| --- | --- |
| **Saját szerepkörök**  | Önhöz rendelt elérhető és aktív szerepköröket listáját jeleníti meg. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket. |
| **Saját kérések** | Megjeleníti a függőben lévő kérések az elérhető szerepkörök hozzárendelésének aktiválására. |
| **Alkalmazás-hozzáférés** | Lehetővé teszi lehetséges késedelmeket és aktiválása után azonnal olyan szerepkört használjon. |
| **Kérések jóváhagyása** | A felhasználók a címtárban jogosult szerepkörök aktiválási kéréseit listáját jeleníti meg, amely jóváhagyásra váró jelölik. |
| **Hozzáférés áttekintése** | Hozzárendelt végrehajtásához, e, lehetőséggel megtekintheti a saját magának vagy valaki más aktív hozzáférési felülvizsgálatok sorolja fel. |
| **Az Azure AD-címtárbeli szerepkörök** | Megjeleníti egy irányítópultot és a kiemelt szerepkörű rendszergazdák számára az Azure AD-címtár szerepkör-hozzárendelések kezeléséhez beállításait. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |
| **Azure-erőforrások** | Megjeleníti egy irányítópultot és a kiemelt szerepkörű rendszergazdák kezelhetik a szerepkör-hozzárendelések az Azure resource beállításait. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-címtárbeli szerepkörök aktiválása](pim-how-to-activate-role.md)
- [A PIM saját Azure-erőforrás szerepkörök aktiválása](pim-resource-roles-activate-your-roles.md)
