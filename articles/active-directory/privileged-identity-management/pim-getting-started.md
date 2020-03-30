---
title: A PIM használatának megkezdése – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogyan engedélyezheti és kezdheti el használni az Azure AD kiemelt identitáskezelés (PIM) használatát az Azure Portalon.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472860"
---
# <a name="start-using-privileged-identity-management"></a>A Privileged Identity Management használatának első lépései

A kiemelt identitáskezelés (PIM) segítségével kezelheti, szabályozhatja és figyelheti a hozzáférést az Azure Active Directory (Azure AD) szervezeten belül. Ez a hatókör az Azure-erőforrásokhoz, az Azure AD-hez és más Microsoft online szolgáltatásokhoz, például az Office 365-höz vagy a Microsoft Intune-hoz való hozzáférést is magában foglalja.

Ez a cikk ismerteti, hogyan engedélyezheti és kezdheti el használni a kiemelt identitáskezelés.

## <a name="prerequisites"></a>Előfeltételek

A kiemelt identitáskezelés használatához az alábbi licencek egyikével kell rendelkeznie:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5

További információt a [Kiemelt identitáskezelés használatára vonatkozó licenckövetelmények](subscription-requirements.md)című témakörben talál.

## <a name="sign-up-pim-for-azure-ad-roles"></a>PiM-regisztráció az Azure AD-szerepkörökhöz

Miután engedélyezte a kiemelt identitáskezelést a címtárhoz, az Azure AD-szerepkörök kezeléséhez regisztrálnia kell a kiemelt identitáskezelés szolgáltatást.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

    ![Jogosultsággal rendelkező identitáskezelés az Azure AD-szerepkörökhöz](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Válassza **a Regisztráció**lehetőséget.

1. A megjelenő üzenetben kattintson az **Igen** gombra a Kiemelt identitáskezelés regisztrálásához az Azure AD-szerepkörök kezeléséhez.

    ![Jogosultsággal rendelkező identitáskezelés az Azure AD-szerepkörök üzenetéhez](./media/pim-getting-started/sign-up-pim-message.png)

    Amikor a regisztráció befejeződik, az Azure AD-beállítások engedélyezve lesznek. Előfordulhat, hogy frissítenie kell a portált.

    A kiemelt identitáskezeléssel védett Azure-erőforrások felderítéséről és kiválasztásáról az [Azure-erőforrások felderítése a Kiemelt identitáskezelés szolgáltatásban való kezeléscímű témakörben](pim-resource-roles-discover-resources.md)talál.

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

Miután a kiemelt identitáskezelés be van állítva, elindíthatja az identitáskezelési feladatokat.

![A Kiemelt identitáskezelés navigációs ablaka a Feladatok és a Kezelés beállításokkal](./media/pim-getting-started/pim-quickstart-tasks.png)

| Feladat + Kezelés | Leírás |
| --- | --- |
| **Saját szerepek**  | Megjeleníti az Önhöz rendelt jogosult és aktív szerepkörök listáját. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket. |
| **Kéréseim** | Megjeleníti a függőben lévő kérelmeket a jogosult szerepkör-hozzárendelések aktiválásához. |
| **Kérések jóváhagyása** | Megjeleníti a jogosult szerepkörök aktiválására vonatkozó kérelmek listáját a címtárban lévő felhasználók számára, akiket jóváhagyhat. |
| **Hozzáférés áttekintése** | Felsorolja azokat az aktív hozzáférési ellenőrzéseket, amelyekhez hozzá van rendelve, függetlenül attól, hogy saját maga vagy valaki más számára tekinti-e át a hozzáférést. |
| **Azure AD-szerepkörök** | Megjeleníti az irányítópultot és a kiemelt szerep-rendszergazdák beállításait az Azure AD szerepkör-hozzárendelések kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |
| **Azure-erőforrások** | Megjeleníti az irányítópultot és a kiemelt szerep-rendszergazdák beállításait az Azure-erőforrás-szerepkör-hozzárendelések kezeléséhez. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>PIM-csempe hozzáadása az irányítópulthoz

A kiemelt identitáskezelés megnyitásának megkönnyítése érdekében adjon hozzá egy emelt szintű identitáskezelési csempét az Azure Portal irányítópultjához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Válassza ki az **összes szolgáltatást,** és keresse meg az **Azure AD kiemelt identitáskezelési** szolgáltatás.

    ![Azure AD kiemelt identitáskezelés minden szolgáltatásban](./media/pim-getting-started/pim-all-services-find.png)

1. Válassza ki a kiemelt identitáskezelés rövid útmutató.

1. Jelölje be **a Pin panelt** az irányítópulthoz a Kiemelt identitáskezelés rövid útmutató panel rögzítéséhez az irányítópulton.

    ![Pushpin ikon a Kiemelt identitáskezelés panel műszerfalra rögzítéséhez](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Az Azure irányítópultján az ehhez hasonló csempe jelenik meg:

    ![Kiemelt identitáskezelés – rövid útmutató csempe az irányítópulton](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzárendelése a kiemelt identitáskezelésben](pim-how-to-add-role-to-user.md)
- [Fedezze fel az Azure-erőforrásokat a kiemelt identitáskezelés ben kezelhető](pim-resource-roles-discover-resources.md)
