---
title: Felhasználó által hozzárendelt felügyelt identitás kezelése a Azure Portal-Azure AD-ben
description: Részletes útmutató egy szerepkör felhasználó által hozzárendelt felügyelt identitáshoz való létrehozásához, listázásához, törléséhez és hozzárendeléséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547463"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Szerepkörök létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory felügyelt identitásával. Ezt az identitást hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene használnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listázhat, törölhet vagy rendelhet hozzá egy szerepkört egy felhasználóhoz rendelt felügyelt identitáshoz az Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

1. A felhasználó által hozzárendelt felügyelt identitás létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával.
2. A keresőmezőbe írja be a *felügyelt identitások*kifejezést, majd a **szolgáltatások**területen kattintson a **felügyelt identitások**elemre.
3. Kattintson a **Hozzáadás** gombra, és adja meg az értékeket az alábbi mezőkben a **felhasználóhoz rendelt felügyelt identitás létrehozása** panelen:
   - **Erőforrás neve**: Ez a felhasználó által hozzárendelt felügyelt identitás neve, például UAI1.
   - **Előfizetés**: válassza ki azt az előfizetést, amely a felhasználó által hozzárendelt felügyelt identitást hozza létre
   - **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot, amely tartalmazza a felhasználóhoz rendelt felügyelt identitást, vagy válassza a **meglévő használata** lehetőséget a felhasználó által hozzárendelt felügyelt identitás létrehozásához egy meglévő erőforráscsoporthoz.
   - **Hely**: válasszon egy helyet a felhasználó által hozzárendelt felügyelt identitás telepítéséhez, például az **USA nyugati**régiójában.
4. Kattintson a  **Create** (Létrehozás) gombra.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

Felhasználó által hozzárendelt felügyelt identitás listázásához/olvasásához a fióknak a [felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre van szüksége.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure-előfizetéshez társított fiókkal a felhasználó által hozzárendelt felügyelt identitások listázásához.
2. A keresőmezőbe írja be a *felügyelt identitások*kifejezést, majd a szolgáltatások területen kattintson a **felügyelt identitások**elemre.
3. Az előfizetéshez tartozó felhasználó által hozzárendelt felügyelt identitások listáját adja vissza.  A felhasználó által hozzárendelt felügyelt identitás részleteinek megtekintéséhez kattintson a nevére.

![Felhasználó által hozzárendelt felügyelt identitások listázása](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésével.

A felhasználó által hozzárendelt identitás törlése nem távolítja el azt a virtuális gépről vagy erőforrásból, amelyhez hozzá lett rendelve.  Ha el szeretné távolítani a felhasználó által hozzárendelt identitást egy virtuális gépről, [távolítsa el a felhasználó által hozzárendelt felügyelt identitást egy virtuális](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm)gépről.

1. A felhasználó által hozzárendelt felügyelt identitás törléséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával.
2. Válassza ki a felhasználó által hozzárendelt felügyelt identitást, és kattintson a **Törlés**gombra.
3. A megerősítés mezőben válassza az **Igen**lehetőséget.

![Felhasználó által hozzárendelt felügyelt identitás törlése](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Szerepkör hozzárendelése felhasználóhoz rendelt felügyelt identitáshoz 

Ha szerepkört szeretne hozzárendelni egy felhasználóhoz rendelt felügyelt identitáshoz, a fióknak szüksége van a [felhasználói hozzáférés rendszergazdai](/azure/role-based-access-control/built-in-roles#user-access-administrator) szerepkör-hozzárendelésére.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure-előfizetéshez társított fiókkal a felhasználó által hozzárendelt felügyelt identitások listázásához.
2. A keresőmezőbe írja be a *felügyelt identitások*kifejezést, majd a szolgáltatások területen kattintson a **felügyelt identitások**elemre.
3. Az előfizetéshez tartozó felhasználó által hozzárendelt felügyelt identitások listáját adja vissza.  Válassza ki azt a felhasználó által hozzárendelt felügyelt identitást, amelyhez szerepkört szeretne hozzárendelni.
4. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása**elemet.

   ![Felhasználó által hozzárendelt felügyelt identitás kezdete](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. A szerepkör-hozzárendelés hozzáadása panelen konfigurálja a következő értékeket, majd kattintson a **Mentés**gombra:
   - **Szerepkör** – a hozzárendelni kívánt szerepkör
   - **Hozzáférés hozzárendelése** a felhasználóhoz rendelt felügyelt identitást hozzárendelő erőforráshoz
   - **Select** – a hozzáféréshez hozzárendelni kívánt tag
   
   ![Felhasználó által hozzárendelt felügyelt identitás IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
