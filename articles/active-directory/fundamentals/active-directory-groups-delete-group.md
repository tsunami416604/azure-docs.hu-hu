---
title: Azure Active Directory - csoport törléséhez |} A Microsoft Docs
description: Útmutatás az Azure Active Directory csoport törlése.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 61f6d68e873b6c8b8b901e5e065065ed42513572
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100200"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Az Azure Active Directory csoport törlése
Az Azure Active Directory (Azure AD) csoportot számtalan törölheti, de általában mert lesz-e meg:

- Helytelenül állította a **csoporttípust** , rossz lehetőséget.

- Létrehozott véletlenül nem a megfelelő vagy a duplikált csoportokhoz. 

- A csoport már nincs szüksége.

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
