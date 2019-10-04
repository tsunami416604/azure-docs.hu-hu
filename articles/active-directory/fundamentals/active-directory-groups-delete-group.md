---
title: Azure Active Directory - csoport törléséhez |} A Microsoft Docs
description: Útmutatás az Azure Active Directory csoport törlése.
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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561913"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Az Azure Active Directory csoport törlése
Az Azure Active Directory (Azure AD) csoportot számtalan törölheti, de általában mert lesz-e meg:

- A **csoport típusának** helytelen beállítása nem megfelelő.

- Helytelen vagy ismétlődő csoportot hozott létre tévedésből. 

- Már nincs szüksége a csoportra.

## <a name="to-delete-a-group"></a>Csoport törlése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza ki **Azure Active Directory**, majd válassza ki **csoportok**.

3. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki a törölni kívánt csoportot. Az alábbi lépéseket fogjuk használni **mobileszköz-kezelési házirend - keleti**.

    ![Csoportok – minden csoport oldalon kiemelve csoport neve](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Az a **mobileszköz-kezelési szabályzat – kelet-áttekintés** oldalra, és kattintson **törlése**.

    A csoport az Azure Active Directory-bérlő törlődik.

    ![Mobileszköz-kezelési szabályzat – kelet-Áttekintés lapján, törölheti kiemelésével](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>További lépések

- Ha véletlenül töröl egy csoportot, újra létrehozhatja. További információkért lásd: [létrehozásához, és tagokat vehet fel](active-directory-groups-create-azure-portal.md).

- Ha véletlenül törli az Office 365-csoportot, valószínűleg visszaállítható. További információkért lásd: [visszaállítása egy törölt Office 365-csoport](../users-groups-roles/groups-restore-deleted.md).
