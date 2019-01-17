---
title: Az Azure Active Directory feltételes hozzáférés az Azure-felügyelet elérésének kezelése
description: További információ a feltételes hozzáférés használata az Azure ad-ben az Azure-felügyelet elérésének kezeléséhez.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: b824d122a5d26c17c41a0e2ea1c595c9e2dd7206
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354348"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>A feltételes hozzáférés az Azure-felügyelet elérésének kezelése

Feltételes hozzáférés az Azure Active Directory (Azure AD) irányítja a hozzáférést a felhőalkalmazások adott Ön által megadott feltételek alapján. Engedélyezi a hozzáférést, hozzon létre feltételes hozzáférési szabályzatokat, amelyek alapján a házirendben a követelmények teljesülnek-e a hozzáférés engedélyezése vagy letiltása. 

Általában a feltételes hozzáférés elérésének szabályozására használatával a felhőalapú alkalmazások. Is állíthat be szabályzatokat bizonyos feltételek (például a bejelentkezési kockázat, hely vagy eszköz) alapján az Azure felügyeleti való hozzáférés szabályozásához és a követelményeket, például a többtényezős hitelesítés kényszerítéséhez.

Az Azure felügyeleti szabályzat létrehozásához válassza **a Microsoft Azure Management** alatt **Felhőalkalmazások** kiválasztásakor az alkalmazás, amelyre a alkalmazni a szabályzatot.

![Feltételes hozzáférés az Azure felügyeleti szolgáltatásaihoz](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Az összes az Azure felügyeleti végpontok, beleértve az Azure portal, az Azure Resource Manager-szolgáltató, a klasszikus Service Management API-k, az Azure PowerShell és a Visual Studio előfizetések adminisztrátori portál hoz létre a szabályzat vonatkozik. Vegye figyelembe, hogy a szabályzat vonatkozik, az Azure PowerShell, amely az Azure Resource Manager API-hívások. Nem alkalmazható a [az Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), amely meghívja a Microsoft Graph.

> [!CAUTION]
> Győződjön meg arról, hogy megismerni a feltételes hozzáférési szabályzat beállítása az Azure felügyeleti való hozzáférés kezelése előtt működik. Ellenőrizze, hogy nem hoz létre, amelyek letilthatják a portálon a saját hozzáférési feltételek.

Állítsa be, és a feltételes hozzáférés használata a további információkért lásd: [feltételes hozzáférés az Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).