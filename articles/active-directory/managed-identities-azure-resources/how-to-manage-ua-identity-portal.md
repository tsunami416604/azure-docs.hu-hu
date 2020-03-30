---
title: Felhasználó által hozzárendelt felügyelt identitás kezelése az Azure Portalon – Azure AD
description: Lépésenkénti útmutató a szerepkör létrehozásához, listázásához, törléséhez és hozzárendeléséhez a felhasználó által hozzárendelt felügyelt identitáshoz.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244133"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Szerepkör létrehozása, listázása, törlése vagy hozzárendelése egy felhasználó által hozzárendelt felügyelt identitáshoz az Azure Portalon keresztül

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az Azure-erőforrások felügyelt identitásai felügyelt identitást biztosítaz Azure-szolgáltatások felügyelt identitással az Azure Active Directoryban. Ezzel az identitással hitelesítheti az Azure AD-hitelesítést támogató szolgáltatások, anélkül, hogy hitelesítő adatokat a kódban. 

Ebben a cikkben megtudhatja, hogyan hozhat létre, listázhat, törölhet vagy rendelhet hozzá egy szerepkört egy felhasználó által hozzárendelt felügyelt identitáshoz az Azure Portalhasználatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak szüksége van a [felügyelt identitás közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával a felhasználó által hozzárendelt felügyelt identitás létrehozásához.
2. A keresőmezőbe írja be a *Felügyelt identitások*kifejezést, és a **Szolgáltatások csoportban**kattintson a **Felügyelt identitások**elemre.
3. Kattintson **a Hozzáadás gombra,** és írja be az értékeket a következő mezőkben a **Felhasználó által hozzárendelt felügyelt** identitás létrehozása ablaktáblában:
   - **Erőforrás neve**: Ez a felhasználó által hozzárendelt felügyelt identitás, például UAI1 neve.
   - **Előfizetés**: Válassza ki azt az előfizetést, amelyhez létre szeretné hozni a felhasználó által hozzárendelt felügyelt identitást a
   - **Erőforráscsoport:** Hozzon létre egy új erőforráscsoportot a felhasználó által hozzárendelt felügyelt identitás tárolására, vagy válassza a **Meglévő használata lehetőséget** a felhasználó által hozzárendelt felügyelt identitás létrehozásához egy meglévő erőforráscsoportban.
   - **Hely**: Válassza ki a felhasználó által hozzárendelt felügyelt identitás központi telepítésének helyét, például **az USA nyugati része.**
4. Kattintson **a Létrehozás gombra.**

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

A felhasználó által hozzárendelt felügyelt identitás okának listázásához/olvasásához a fióknak szüksége van a [Felügyelt identitásoperátorra](/azure/role-based-access-control/built-in-roles#managed-identity-operator) vagy a [Felügyelt identitásközreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával a felhasználó által hozzárendelt felügyelt identitások listázásához.
2. A keresőmezőbe írja be a *Felügyelt identitások*kifejezést , és a Szolgáltatások csoportban kattintson a **Felügyelt identitások**elemre.
3. A rendszer visszaadja az előfizetéshez a felhasználó által hozzárendelt felügyelt identitások listáját.  A felhasználó által hozzárendelt felügyelt identitás részleteinek megtekintéséhez kattintson a nevére.

![Felhasználó által hozzárendelt felügyelt identitás listázása](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

A felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak szüksége van a [Felügyelt identitás közreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelésre.

A felhasználó által hozzárendelt identitás törlése nem távolítja el azt a virtuális gépvagy erőforrás volt hozzárendelve.  Ha el szeretné távolítani a felhasználó által hozzárendelt identitást egy virtuális gépről [lásd: Távolítsa el a felhasználó által kijelölt felügyelt identitást a virtuális gépről.](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm)

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával a felhasználó által hozzárendelt felügyelt identitás törléséhez.
2. Jelölje ki a felhasználó által hozzárendelt felügyelt identitást, és kattintson a **Törlés gombra.**
3. A megerősítő mezőben válassza az **Igen**lehetőséget.

![Felhasználó által hozzárendelt felügyelt identitás törlése](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Szerepkör hozzárendelése felhasználó által hozzárendelt felügyelt identitáshoz 

Ha szerepkört szeretne hozzárendelni egy felhasználó által hozzárendelt felügyelt identitáshoz, a fióknak szüksége van a [Felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator) szerepkör-hozzárendelésre.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával a felhasználó által hozzárendelt felügyelt identitások listázásához.
2. A keresőmezőbe írja be a *Felügyelt identitások*kifejezést , és a Szolgáltatások csoportban kattintson a **Felügyelt identitások**elemre.
3. A rendszer visszaadja az előfizetéshez a felhasználó által hozzárendelt felügyelt identitások listáját.  Válassza ki a szerepkörhöz rendelni kívánt, felhasználó által hozzárendelt felügyelt identitást.
4. Válassza a **Hozzáférés-vezérlés (IAM) lehetőséget,** majd a **Szerepkör-hozzárendelés hozzáadása**lehetőséget.

   ![Felhasználó által hozzárendelt felügyelt identitás indítása](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. A Szerepkör-hozzárendelés hozzáadása panelen konfigurálja a következő értékeket, majd kattintson a **Mentés**gombra:
   - **Szerep** - a hozzárendelendő szerepkör
   - **Hozzáférés hozzárendelése** - az erőforrás a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez
   - **Válassza ki** - a hozzáférést hozzárendelni kívánt tag
   
   ![Felhasználó által hozzárendelt felügyelt identitás IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
