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
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cad6c41b465e14b11d4fa7b8e7fa6037d49e8eae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742912"
---
# <a name="start-using-privileged-identity-management"></a>A Privileged Identity Management használatának első lépései

Ez a cikk a Privileged Identity Management (PIM) engedélyezését és a használatának első lépéseit ismerteti.

A Privileged Identity Management (PIM) használatával felügyelheti, vezérelheti és figyelheti a hozzáférést a Azure Active Directory (Azure AD) szervezeten belül. A PIM segítségével igény szerint és az Azure-erőforrásokhoz, Azure AD-erőforrásokhoz és más Microsoft-online szolgáltatásokokhoz, például az Office 365-hez vagy Microsoft Intunehoz is igény szerinti hozzáférést biztosíthat.

## <a name="prerequisites"></a>Előfeltételek

Privileged Identity Management használatához a következő licencek egyike szükséges:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5

További információ: [a Privileged Identity Management használatára vonatkozó licencfeltételek](subscription-requirements.md).

## <a name="prepare-pim-for-azure-ad-roles"></a>A PIM előkészítése Azure AD-szerepkörökhöz

Miután engedélyezte a Privileged Identity Management a címtárban, előkészítheti Privileged Identity Management az Azure AD-szerepkörök kezelésére.

Az alábbi feladatokat javasoljuk az Azure AD-szerepkörök előkészítéséhez, sorrendben:

1. [Azure ad-szerepkör beállításainak konfigurálása](pim-how-to-change-default-settings.md).
1. [Adja meg a jogosult hozzárendeléseket](pim-how-to-add-role-to-user.md).
1. [Engedélyezze a jogosult felhasználók számára az Azure ad-szerepkör igény szerinti aktiválását](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>A PIM előkészítése az Azure-szerepkörökhöz

Miután engedélyezte a Privileged Identity Management a címtárhoz, előkészítheti a Privileged Identity Managementt az Azure-erőforrásokhoz való hozzáféréshez szükséges Azure-szerepkörök kezeléséhez az előfizetésben.

Az alábbi feladatokat javasoljuk az Azure-szerepkörök előkészítéséhez, sorrendben:

1. [Az Azure-erőforrások felfedezése](pim-resource-roles-discover-resources.md)
1. [Konfigurálja az Azure-szerepkör beállításait](pim-resource-roles-configure-role-settings.md).
1. [Adja meg a jogosult hozzárendeléseket](pim-resource-roles-assign-roles.md).
1. [Lehetővé teheti a jogosult felhasználók számára az Azure-szerepkörök aktiválását](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

A Privileged Identity Management beállítása után megismerheti a módját.

![Navigációs ablak Privileged Identity Management feladatok és kezelési beállítások megjelenítése](./media/pim-getting-started/pim-quickstart-tasks.png)

| Feladat + kezelés | Description |
| --- | --- |
| **Saját szerepkörök**  | Megjeleníti az Önhöz hozzárendelt jogosult és aktív szerepkörök listáját. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket. |
| **Saját kérések** | Megjeleníti a jogosult szerepkör-hozzárendelések aktiválásához függőben lévő kéréseit. |
| **Kérések jóváhagyása** | Megjeleníti azoknak a kéréseknek a listáját, amelyekkel az Ön által jóváhagyott címtárban lévő felhasználók jogosult szerepköröket aktiválhat. |
| **Hozzáférés áttekintése** | Felsorolja a befejezéshez hozzárendelt aktív hozzáférési felülvizsgálatokat, függetlenül attól, hogy saját maga vagy valaki másnak tekinti-e meg a hozzáférést. |
| **Azure AD-szerepkörök** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure AD szerepkör-hozzárendelések kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A saját nézet irányítópult csak az irányítópultot elérő felhasználó adatait jeleníti meg, nem pedig a teljes szervezetet. |
| **Azure-erőforrások** | Megjeleníti a Kiemelt szerepkörű rendszergazdák irányítópultját és beállításait az Azure-erőforrás szerepkör-hozzárendeléseinek kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A saját nézet irányítópult csak az irányítópultot elérő felhasználó adatait jeleníti meg, nem pedig a teljes szervezetet. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>PIM-csempe hozzáadása az irányítópulthoz

A Privileged Identity Management megnyitásának megkönnyítéséhez adjon hozzá egy PIM-csempét a Azure Portal irányítópulthoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg a **Azure ad Privileged Identity Management** szolgáltatást.

    ![Azure AD Privileged Identity Management az összes szolgáltatásban](./media/pim-getting-started/pim-all-services-find.png)

1. Válassza ki a Privileged Identity Management rövid útmutatót.

1. Az irányítópultra rögzítheti az Privileged Identity Management rövid útmutató panelt az **irányítópulton** .

    ![Gombostű ikonja Privileged Identity Management panel rögzítése az irányítópulton](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Az Azure-irányítópulton a következőhöz hasonló csempe látható:

    ![Privileged Identity Management rövid útmutató csempe az irányítópulton](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök kiosztása Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Azure-erőforrásokhoz való hozzáférés kezelése Privileged Identity Management](pim-resource-roles-discover-resources.md)
