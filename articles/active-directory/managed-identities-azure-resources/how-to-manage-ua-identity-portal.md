---
title: Egy az Azure portal használatával felügyelt felhasználó által hozzárendelt identitások kezelése
description: Részletes útmutatást létrehozása, listázása és törlése a felhasználó által hozzárendelt felügyelt identitás.
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
ms.openlocfilehash: 6729bee9bfebd8e80ae3b791dc2a8a480ac8b525
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158722"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-portal"></a>Lista létrehozása vagy törlése az Azure portal használatával felügyelt felhasználó által hozzárendelt identitás

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt identitások az Azure-erőforrások egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan hozhat létre, listázása és törlése az Azure Portal használatával felügyelt felhasználó által hozzárendelt identitás.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör létrehozása, olvasása (lista), frissítése és törlése a felhasználóhoz felügyelt identitásnak.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkört (lista) egy felhasználó által hozzárendelt felügyelt identitás tulajdonságainak olvasása.

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

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

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal az Azure-előfizetéshez társított felügyelt felhasználó által hozzárendelt identitások listázásához.
2. A Keresés mezőbe írja be a *felügyelt identitások*, és a szolgáltatások területen kattintson a **felügyelt identitások**.
3. A felhasználó által hozzárendelt felügyelt az előfizetéshez tartozó azonosítók listáját adja vissza.  Kattintson a nevére, a felhasználó által hozzárendelt felügyelt identitás részleteinek megtekintéséhez.

![Felügyelt identitás felhasználó által hozzárendelt listából](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Egy felhasználó által hozzárendelt felügyelt identitás törlése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal az Azure-előfizetéshez társított törli egy felhasználó által hozzárendelt felügyelt identitás.
2. Válassza ki a felhasználó által hozzárendelt felügyelt identitást, és kattintson a **törlése**.
3. Válassza a jóváhagyás mező alatt **Igen**.

![Felhasználó által hozzárendelt felügyelt identitás törlése](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)