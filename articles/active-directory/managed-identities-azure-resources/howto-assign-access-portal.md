---
title: Egy felügyelt identitás hozzáférés hozzárendelése egy Azure-erőforrást az Azure portal használatával
description: Részletes útmutató egy felügyelt identitás, az erőforrás-hozzáférés hozzárendelése egy másik erőforráshoz, az Azure portal használatával.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: priyamo
ms.openlocfilehash: 5f6c995cee823347cfecb64bc34377153299a6d3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188422"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Egy felügyelt identitás hozzáférés hozzárendelése egy erőforrást az Azure portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután beállította egy Azure-erőforrás felügyelt identitással, a felügyelt identitás hozzáférést biztosíthat más erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a cikk bemutatja az Azure virtuális gép vagy a virtuális gép méretezési csoportjának felügyelt identitás hozzáférés biztosítása az Azure storage-fiókba, az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Az RBAC használatával hozzáférést egy felügyelt identitás hozzárendelése egy másik erőforrás

Engedélyezését követően felügyelt identitás egy Azure-erőforrás, például egy [Azure virtuális gép](qs-configure-portal-windows-vm.md) vagy [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely alatt a felügyelt identitás konfigurálása Azure-előfizetéshez társított.

2. Keresse meg a kívánt erőforrást, amelyre szeretné módosítani a hozzáférés-vezérlés. Ebben a példában azt egy Azure-beli virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot, hogy lépjen a tárfiókhoz.

3. Válassza ki a **hozzáférés-vezérlés (IAM)** az erőforrást, és válassza az oldal **+ szerepkör-hozzárendelés hozzáadása**. Adja meg a **szerepkör**, **rendelhet hozzáféréseket**, és adja meg a megfelelő **előfizetés**. A keresési feltételek területen látnia kell az erőforrás. Válassza ki az erőforrást, és válassza ki **mentése**. 

   ![Hozzáférés-vezérlés (IAM) képernyőképe](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások – áttekintés](overview.md)
- Engedélyezheti a felügyelt identitás-beli virtuális gépen [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md).
- Engedélyezheti a felügyelt identitás egy Azure-beli virtuálisgép-méretezési [konfigurálása felügyelt identitások beállítása az Azure portal használatával virtuálisgép-méretezési csoportot az Azure-erőforrások](qs-configure-portal-windows-vmss.md).


