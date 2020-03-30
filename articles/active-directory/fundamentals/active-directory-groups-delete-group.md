---
title: Csoport törlése – Azure Active Directory | Microsoft dokumentumok
description: Útmutató a csoport törléséhez az Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561913"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Csoport törlése az Azure Active Directory használatával
Az Azure Active Directory (Azure AD) csoportot tetszőleges számú okból törölheti, de általában azért, mert:

- Helytelenül állítsa be a **csoporttípust** a rossz beállításra.

- Véletlenül rossz vagy ismétlődő csoportot hozott létre. 

- Már nincs szüksége a csoportra.

## <a name="to-delete-a-group"></a>Csoport törlése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza az **Azure Active Directory**lehetőséget, majd a **Csoportok**lehetőséget.

3. A **Csoportok – Minden csoport** lapon keresse meg és jelölje ki a törölni kívánt csoportot. Ezekhez a lépésekhez az **MDM-házirendet**fogjuk használni - Kelet .

    ![Csoportok - Minden csoport lap, a csoport neve kiemelve](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Az **MDM-házirend – Kelet-áttekintés** lapon válassza a **Törlés**lehetőséget.

    A csoport törlődik az Azure Active Directory-bérlő.

    ![MDM-házirend – Keleti áttekintő lap, a törlési lehetőség kiemelve](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>További lépések

- Ha véletlenül töröl egy csoportot, újra létrehozhatja azt. További információ: [Egyszerű csoport létrehozása és tagok hozzáadása.](active-directory-groups-create-azure-portal.md)

- Ha véletlenül töröl egy Office 365-csoportot, előfordulhat, hogy vissza tudja állítani. További információt a [Törölt Office 365-csoport visszaállítása című témakörben](../users-groups-roles/groups-restore-deleted.md)talál.
