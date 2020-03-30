---
title: Felügyelt identitás-hozzáférés hozzárendelése egy erőforráshoz az Azure Portalon – Azure AD
description: Részletes utasítások a felügyelt identitás hozzárendeléséhez egy erőforrás-hozzáférés egy másik erőforráshoz, az Azure Portal használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547284"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Felügyelt identitás-hozzáférés hozzárendelése egy erőforráshoz az Azure Portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást felügyelt identitással, a felügyelt identitás hozzáférést adhat egy másik erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a cikk bemutatja, hogyan adhat egy Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport felügyelt identitás-hozzáférés egy Azure-tárfiókhoz az Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés hozzárendelése az RBAC segítségével egy másik erőforráshoz

Miután engedélyezte a felügyelt identitást egy Azure-erőforrásban, például egy [Azure virtuális gépen](qs-configure-portal-windows-vm.md) vagy [az Azure VMSS-en:](qs-configure-portal-windows-vmss.md)

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-előfizetéshez társított fiók használatával, amely nek megfelelően konfigurálta a felügyelt identitást.

2. Keresse meg azt a kívánt erőforrást, amelyen módosítani szeretné a hozzáférés-vezérlést. Ebben a példában egy Azure virtuális gép hozzáférést biztosítunk egy tárfiókhoz, így a tárfiókhoz navigálunk.

3. Jelölje ki az erőforrás **Hozzáférés-vezérlési (IAM)** lapját, majd a **+ Szerepkör-hozzárendelés hozzáadása**lehetőséget. Ezután adja meg a **szerepkört**, **a Hozzáférés hozzárendelése**a hoz, és adja meg a megfelelő **előfizetést.** A keresési feltételek területen meg kell jelennie az erőforrásnak. Jelölje ki az erőforrást, és válassza a **Mentés gombot.** 

   ![Hozzáférés-vezérlés (IAM) képernyőképe](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>További lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- Felügyelt identitás engedélyezése egy Azure virtuális gépen, [lásd: Felügyelt identitások konfigurálása az Azure-erőforrások egy virtuális gépen az Azure Portalon.](qs-configure-portal-windows-vm.md)
- Felügyelt identitás engedélyezése egy Azure virtuálisgép-méretezési csoport, [az Azure-erőforrások felügyelt identitások konfigurálása az Azure-erőforrások az Azure Portalhasználatával felügyelt identitások konfigurálása.](qs-configure-portal-windows-vmss.md)


