---
title: Az Azure Active Directory csoport törlése |} A Microsoft Docs
description: Útmutató az Azure Active Directory csoport törlése.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 014fe487d23a6c75e94ca2708ed15044bd6cf53b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574330"
---
# <a name="how-to-delete-a-group-using-azure-active-directory"></a>Útmutató: az Azure Active Directory csoport törlése
Egy csoport számtalan törölheti, de általában mert lesz-e meg:

- Helytelenül állította a **csoporttípust** helytelen beállítás

- Létrehozott tévedésből a nem a megfelelő vagy a duplikált csoportokhoz 

- A csoport már nem szükséges

## <a name="to-delete-a-group"></a>Csoport törlése
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) a címtár egy globális rendszergazdai fiók használatával.

2. Válassza ki **Azure Active Directory**, majd válassza ki **csoportok**.

3. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki a törölni kívánt csoportot. Az alábbi lépéseket fogjuk használni **mobileszköz-kezelési házirend - keleti**.

    ![Csoportok – minden csoport oldalon kiemelve csoport neve](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Az a **mobileszköz-kezelési szabályzat – kelet-áttekintés** oldalra, és kattintson **törlése**.

    A csoport az Azure Active Directory-bérlő törlődik.

    ![Mobileszköz-kezelési szabályzat – kelet-Áttekintés lapján, törölheti kiemelésével](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>További lépések

- Ha véletlenül töröl egy csoportot, újra létrehozhatja. További információkért lásd: [létrehozásához, és tagokat vehet fel](active-directory-groups-create-azure-portal.md).

- Ha véletlenül törli az Office 365-csoportot, valószínűleg visszaállítható. További információkért lásd: [visszaállítása egy törölt Office 365-csoport](../users-groups-roles/groups-restore-deleted.md).
