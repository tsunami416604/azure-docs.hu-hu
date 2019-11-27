---
title: Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz a Azure Portal-Azure AD használatával
description: Részletes útmutató egy felügyelt identitás, az erőforrás-hozzáférés hozzárendelése egy másik erőforráshoz, az Azure portal használatával.
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
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547284"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Egy felügyelt identitás hozzáférés hozzárendelése egy erőforrást az Azure portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután beállította egy Azure-erőforrás felügyelt identitással, a felügyelt identitás hozzáférést biztosíthat más erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a cikk bemutatja az Azure virtuális gép vagy a virtuális gép méretezési csoportjának felügyelt identitás hozzáférés biztosítása az Azure storage-fiókba, az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Az RBAC használatával hozzáférést egy felügyelt identitás hozzárendelése egy másik erőforrás

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, például egy Azure-beli [virtuális gépen](qs-configure-portal-windows-vm.md) vagy egy [Azure-VMSS](qs-configure-portal-windows-vmss.md):

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure-előfizetéshez társított fiókkal, amely alatt a felügyelt identitást konfigurálta.

2. Keresse meg a kívánt erőforrást, amelyre szeretné módosítani a hozzáférés-vezérlés. Ebben a példában azt egy Azure-beli virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot, hogy lépjen a tárfiókhoz.

3. Válassza ki az erőforrás **hozzáférés-vezérlés (iam)** lapját, és válassza a **+ szerepkör-hozzárendelés hozzáadása**elemet. Ezután adja meg a **szerepkört**, **rendeljen hozzá hozzáférést**, és adja meg a megfelelő **előfizetést**. A keresési feltételek területen látnia kell az erőforrás. Válassza ki az erőforrást, és kattintson a **Mentés**gombra. 

   ![Hozzáférés-vezérlés (IAM) képernyőképe](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- Az Azure-beli virtuális gépek felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrásokhoz a Azure Portal használatával](qs-configure-portal-windows-vm.md).
- Az Azure virtuálisgép-méretezési csoport felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuálisgép-méretezési csoportokban a Azure Portal használatával](qs-configure-portal-windows-vmss.md).


