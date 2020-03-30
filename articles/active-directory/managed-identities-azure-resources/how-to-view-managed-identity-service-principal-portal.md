---
title: Felügyelt identitás egyszerű szolgáltatásának megtekintése az Azure Portalon – Azure AD
description: A felügyelt identitás egyszerű szolgáltatásának az Azure Portalon való megtekintésének lépésenkénti útmutatója.
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
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298597"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Felügyelt identitás egyszerű szolgáltatásának megtekintése az Azure Portalon

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben megtudhatja, hogyan tekintheti meg a felügyelt identitás egyszerű szolgáltatásaz Azure Portalon keresztül.

 > [!NOTE] 
 > A szolgáltatásnévi tagok vállalati alkalmazások. 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md)
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókot.](https://azure.microsoft.com/free/)
- A [rendszerhez rendelt identitás engedélyezése virtuális gépen](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazásban.](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez az eljárás bemutatja, hogyan tekintheti meg a rendszerhez rendelt identitással rendelkező virtuális gépek egyszerű szolgáltatását (ugyanazok a lépések vonatkoznak az alkalmazásokra).

1. Kattintson **az Azure Active Directory,** majd **a Vállalati alkalmazások**elemre.
2. Az **Alkalmazástípusa**csoportban válassza a **Minden alkalmazás lehetőséget,** majd kattintson az Alkalmaz **gombra.**
3. A keresési szűrő mezőbe írja be annak a virtuális gépnek vagy alkalmazásnak a nevét, amelynek engedélyezve van a felügyelt identitás, vagy válassza ki a bemutatott listából.

   ![Felügyelt identitásszolgáltatás-egyszerű szolgáltatás megtekintése a portálon](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>További lépések

[Azure-erőforrások felügyelt identitásai](/azure/active-directory/managed-identities-azure-resources/overview)

