---
title: Hogyan kezelheti a felhasználó hozzárendelt felügyelt identitás, az Azure portal használatával
description: Lépésről lépésre való létrehozása, listázása és törlése a felhasználó hozzárendelt felügyelt identitás.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060154"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>Lista létrehozása vagy törlése egy a felhasználóhoz hozzárendelt identitás, az Azure portal használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyeltszolgáltatás-identitás egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan hozhatók létre, lista, és törölje a a felhasználóhoz hozzárendelt identitás, az Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör létrehozása, olvasása (lista), frissítése és törlése a felhasználóhoz hozzárendelt identitás.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkört egy felhasználóhoz hozzárendelt identitás tulajdonságainak olvasása (lista).

## <a name="create-a-user-assigned-managed-identity"></a>Hozzon létre egy felügyelt identitás hozzárendelt felhasználó

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal társított Azure-előfizetés létrehozása a felhasználó hozzárendelt felügyelt identitás.
2. A Keresés mezőbe írja be a *felügyelt identitások*, majd a **szolgáltatások**, kattintson a **felügyelt identitások**.
3. Kattintson a **Hozzáadás** , és adja meg az értékeket az alábbi mezőket a **hozzárendelt felhasználó létrehozása felügyelt** identitás panelen:
   - **Erőforrás neve**: az a felhasználó által felügyelt identitást, például UAI1 azt a nevet.
   - **Előfizetés**: válassza ki az előfizetést hozhat létre felügyelt identitás alatt hozzárendelt felhasználó
   - **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot a felhasználóhoz hozzárendelt identitás tartalmaznak, vagy válasszon **meglévő** hozhat létre a felhasználó hozzárendelt felügyelt identitás egy meglévő erőforráscsoportot.
   - **Hely**: válassza ki azt a helyet, a felhasználó felügyelt identitást, például üzembe **USA nyugati RÉGIÓJA**.
4. Kattintson a **Create** (Létrehozás) gombra.

![Hozzon létre egy felügyelt identitás hozzárendelt felhasználó](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>Listát a felhasználóhoz hozzárendelt identitás

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) felügyelt identitások az Azure-előfizetéshez társított fiók használatával listázhatja a felhasználó rendelve.
2. A Keresés mezőbe írja be a *felügyelt identitások*, és a szolgáltatások területen kattintson a **felügyelt identitások**.
3. A felhasználó az előfizetéshez tartozó felügyelt identitások listáját adja vissza.  A felhasználóhoz rendelt részleteinek megtekintéséhez kattintson a nevére.

![Lista felhasználóhoz hozzárendelt felügyelt identitás](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás törlése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal az Azure-előfizetéshez társított felhasználó törlése hozzárendelt felügyelt identitás.
2. Válassza ki a felhasználóhoz hozzárendelt identitás, és kattintson a **törlése**.
3. Válassza a jóváhagyás mező alatt **Igen**.

![Felhasználó által hozzárendelt felügyelt identitás törlése](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)