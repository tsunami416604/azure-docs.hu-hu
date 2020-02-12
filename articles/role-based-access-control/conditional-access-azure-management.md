---
title: Azure-felügyelethez való hozzáférés kezelése feltételes hozzáféréssel az Azure AD-ben
description: Ismerje meg, hogyan használhatja a feltételes hozzáférést az Azure AD-ben az Azure-felügyelethez való hozzáférés kezeléséhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137412"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Az Azure-felügyelethez való hozzáférés kezelése feltételes hozzáféréssel

> [!CAUTION]
> Győződjön meg arról, hogy az Azure-felügyelethez való hozzáférés kezeléséhez szükséges szabályzat beállítása előtt meg kell ismernie a feltételes hozzáférés működését. Ügyeljen arra, hogy ne hozzon létre olyan feltételeket, amelyek letiltják a saját hozzáférését a portálhoz.

A Azure Active Directory (Azure AD) feltételes hozzáférése a megadott feltételek alapján szabályozza a felhőalapú alkalmazásokhoz való hozzáférést. A hozzáférés engedélyezéséhez olyan feltételes hozzáférési szabályzatokat hozhat létre, amelyek engedélyezik vagy letiltják a hozzáférést attól függően, hogy teljesülnek-e a szabályzat követelményei. 

A felhőalapú alkalmazásokhoz való hozzáférés szabályozása jellemzően feltételes hozzáféréssel végezhető el. Házirendeket is beállíthat az Azure-felügyelethez való hozzáférés szabályozására bizonyos feltételek (például bejelentkezési kockázat, hely vagy eszköz) alapján, valamint a követelmények érvényesítéséhez, például a többtényezős hitelesítéshez.

Az Azure Management szabályzatának létrehozásához válassza a **Microsoft Azure felügyelet** lehetőséget a **Cloud apps** alatt, amikor kiválasztja azt az alkalmazást, amelyre alkalmazni szeretné a szabályzatot.

![Feltételes hozzáférés az Azure felügyeleti szolgáltatásaihoz](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A létrehozott szabályzat az összes Azure felügyeleti végpontra vonatkozik, beleértve a következőket:

- Azure Portal
- Azure Resource Manager szolgáltató
- Klasszikus Service Management API-k
- Azure PowerShell
- Visual Studio-előfizetések felügyeleti portál
- Azure DevOps
- Azure Data Factory portál

Vegye figyelembe, hogy a házirend Azure PowerShellra vonatkozik, amely meghívja a Azure Resource Manager API-t. Ez nem vonatkozik az [Azure ad powershellre](/powershell/azure/active-directory/install-adv2), amely meghívja a Microsoft Graph.


További információ a feltételes hozzáférés beállításáról és használatáról: [Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
