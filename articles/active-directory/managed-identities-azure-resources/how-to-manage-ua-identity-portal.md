---
title: Egy az Azure portal használatával felügyelt felhasználó által hozzárendelt identitások kezelése
description: Lépésről lépésre való létrehozásához, listázásához, törlése és szerepkör hozzárendelése felhasználóhoz felügyelt identitásnak.
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
ms.openlocfilehash: 2f2f338f7c4ddb885bc909f49815783e616b94c8
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300455"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Lista létrehozása, törlése vagy szerepkör hozzárendelése a felügyelt felhasználó által hozzárendelt identitás az Azure portal használatával

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt identitások az Azure-erőforrások egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan hozhat létre, listázása, törölje vagy szerepkör hozzárendelése a felügyelt felhasználó által hozzárendelt identitás az Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

A felügyelt felhasználó által hozzárendelt identitás létrehozása, a fióknak rendelkeznie kell a [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal társított Azure-előfizetés létrehozása a felhasználó által hozzárendelt felügyelt identitás.
2. A Keresés mezőbe írja be a *felügyelt identitások*, majd a **szolgáltatások**, kattintson a **felügyelt identitások**.
3. Kattintson a **Hozzáadás** , és adja meg az értékeket az alábbi mezőket a **hozzárendelt felhasználó létrehozása felügyelt** identitás panelen:
   - **Erőforrás neve**: a felhasználó által hozzárendelt felügyelt identitás, például UAI1 neve.
   - **Előfizetés**: válassza ki az előfizetést, amelyben létrehozza a felhasználó által hozzárendelt felügyelt identitás alatt
   - **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot, a felhasználó által hozzárendelt felügyelt identitást tartalmaz, vagy válasszon **meglévő** hozhat létre a felhasználó által hozzárendelt felügyelt identitás egy meglévő erőforráscsoportot.
   - **Hely**: válassza ki azt a helyet, például üzembe helyezése felügyelt felhasználó által hozzárendelt identitások **USA nyugati RÉGIÓJA**.
4. Kattintson a **Create** (Létrehozás) gombra.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Felügyelt identitások felhasználó által hozzárendelt listából

A felhasználó által hozzárendelt felügyelt identitás lista/Olvasás, a fióknak rendelkeznie kell a [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal az Azure-előfizetéshez társított felügyelt felhasználó által hozzárendelt identitások listázásához.
2. A Keresés mezőbe írja be a *felügyelt identitások*, és a szolgáltatások területen kattintson a **felügyelt identitások**.
3. A felhasználó által hozzárendelt felügyelt az előfizetéshez tartozó azonosítók listáját adja vissza.  Kattintson a nevére, a felhasználó által hozzárendelt felügyelt identitás részleteinek megtekintéséhez.

![Felügyelt identitás felhasználó által hozzárendelt listából](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Egy felhasználó által hozzárendelt felügyelt identitás törlése

Ha törölni szeretné egy felhasználó által hozzárendelt felügyelt identitás, a fióknak rendelkeznie kell a [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal az Azure-előfizetéshez társított törli egy felhasználó által hozzárendelt felügyelt identitás.
2. Válassza ki a felhasználó által hozzárendelt felügyelt identitást, és kattintson a **törlése**.
3. Válassza a jóváhagyás mező alatt **Igen**.

![Felhasználó által hozzárendelt felügyelt identitás törlése](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Szerepkör hozzárendelése felhasználóhoz felügyelt identitásnak

Szerepkör hozzárendelése felhasználóhoz felügyelt identitásnak, hogy a fióknak rendelkeznie kell a [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator) szerepkör-hozzárendelés.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal az Azure-előfizetéshez társított felügyelt felhasználó által hozzárendelt identitások listázásához.
2. A Keresés mezőbe írja be a *felügyelt identitások*, és a szolgáltatások területen kattintson a **felügyelt identitások**.
3. A felhasználó által hozzárendelt felügyelt az előfizetéshez tartozó azonosítók listáját adja vissza.  Válassza ki a felhasználó által hozzárendelt felügyelt identitás, amelyet szeretne rendelhet egy szerepkört.
4. Válassza ki **hozzáférés-vezérlés (IAM)** majd **Hozzáadás**.

   ![Felhasználó által hozzárendelt felügyelt identitás indítása](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Az engedélyek hozzáadása panelen konfigurálja a következő értékeket, és kattintson a **mentése**:
   - **Szerepkör** -a szerepkör hozzárendelése
   - **Hozzáférés hozzárendelése** – az erőforrás hozzárendelni a felhasználó által hozzárendelt felügyelt identitás
   - **Válassza ki** – a tag hozzáférés hozzárendelése
   
   ![Felhasználó által hozzárendelt felügyelt identitás IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  