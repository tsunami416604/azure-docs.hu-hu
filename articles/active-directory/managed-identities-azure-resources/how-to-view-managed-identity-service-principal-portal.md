---
title: A szolgáltatásnév, egy felügyelt identitás megtekintése az Azure Portalon
description: Részletes utasítások a szolgáltatásnév, egy felügyelt identitás megtekintéséhez az Azure Portalon.
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
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 123cd1c808ad833d9b10148e4cb292b335075a8d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882576"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>A szolgáltatásnév, egy felügyelt identitás megtekintése az Azure Portalon

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan megtekintéséhez az egyszerű szolgáltatás egy felügyelt identitás, az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem rendelkezik Azure-fiók [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Engedélyezése [virtuális gépen a rendszer által hozzárendelt identitással](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazás](/azure/app-service/app-service-managed-service-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez az eljárás azt ismerteti, hogyan az egyszerű szolgáltatás a virtuális gépek megtekintése a rendszerhez rendelt identitáshoz engedélyezve (az alkalmazás alkalmazza ugyanazokat a lépéseket).

1. Kattintson a **Azure Active Directory** majd **vállalati alkalmazások**.
2. A **alkalmazástípus**, válassza a **minden alkalmazás**.
3. Szűrő keresőmezőbe írja be a nevet a virtuális gép és az alkalmazás, amely felügyelt identitás engedélyezve van, vagy válassza ki azt a megjelenő listában.

   ![Felügyelt identitás szolgáltatásnév megtekintése a portálon](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>További lépések

[Azure-erőforrások felügyelt identitásai](/azure/active-directory/managed-identities-azure-resources/overview)

