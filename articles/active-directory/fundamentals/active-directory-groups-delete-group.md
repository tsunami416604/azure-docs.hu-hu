---
title: Csoport törlése – Azure Active Directory | Microsoft Docs
description: A csoportok Azure Active Directory használatával történő törlésére vonatkozó utasítások.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869def6b3187e10727a170377f2635fb3652f98b
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797593"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Csoport törlése Azure Active Directory használatával
A Azure Active Directory (Azure AD) csoport tetszőleges számú okból törölhető, de általában az alábbiak miatt lesz lehetséges:

- A **csoport típusának** helytelen beállítása nem megfelelő.

- Helytelen vagy ismétlődő csoportot hozott létre tévedésből. 

- Már nincs szüksége a csoportra.

## <a name="to-delete-a-group"></a>Csoport törlése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory**, majd a **csoportok**lehetőséget.

3. A **csoportok – minden csoport** lapon keresse meg és válassza ki a törölni kívánt csoportot. Ezekben a lépésekben a **Mdm Policy-East**használatát fogjuk használni.

    ![Csoportok – minden csoport lap, csoport neve kiemelve](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. A **Mdm házirend – Kelet áttekintése** lapon válassza a **Törlés**lehetőséget.

    A csoport törölve lett a Azure Active Directory bérlőből.

    ![MDM házirend – Kelet – áttekintés oldal, törlési lehetőség kiemelve](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>További lépések

- Ha véletlenül töröl egy csoportot, azt újra létrehozhatja. További információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása](active-directory-groups-create-azure-portal.md).

- Ha egy Office 365-csoportot tévedésből töröl, lehetséges, hogy vissza tudja állítani. További információ: [törölt Office 365-csoport visszaállítása](../users-groups-roles/groups-restore-deleted.md).
