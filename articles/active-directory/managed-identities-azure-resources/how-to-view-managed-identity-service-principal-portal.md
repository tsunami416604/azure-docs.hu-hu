---
title: Felügyelt identitás egyszerű szolgáltatásnév megtekintése a Azure Portal-Azure AD-ben
description: Részletes útmutató a felügyelt identitások szolgáltatásának a Azure Portalban való megtekintéséhez.
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
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b51aeabaa4bb7fa9884c95cf0da2dc68ba5b64b0
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184099"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Felügyelt identitás egyszerű szolgáltatásnév megtekintése a Azure Portal

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan tekintheti meg egy felügyelt identitás egyszerű szolgáltatását a Azure Portal használatával.

 > [!NOTE] 
 > Az egyszerű szolgáltatások vállalati alkalmazások. 

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A [rendszerhez rendelt identitás engedélyezése egy virtuális gépen](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazáson](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez az eljárás azt mutatja be, hogyan lehet megtekinteni egy virtuális gép egyszerű szolgáltatását, amelyen engedélyezve van a rendszer hozzárendelt identitása (az alkalmazásokra ugyanazok a lépések érvényesek).

1. Kattintson a **Azure Active Directory** , majd a **vállalati alkalmazások**elemre.
2. Az **alkalmazás típusa**területen válassza a **minden alkalmazás**lehetőséget.
3. A keresési szűrő mezőbe írja be annak a virtuális gépnek vagy alkalmazásnak a nevét, amelynek a felügyelt identitása engedélyezve van, vagy válassza ki a megjelenített listából.

   ![Felügyelt identitás szolgáltatásnév megtekintése a portálon](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Következő lépések

[Azure-erőforrások felügyelt identitásai](/azure/active-directory/managed-identities-azure-resources/overview)

