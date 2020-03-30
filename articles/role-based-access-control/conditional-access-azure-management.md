---
title: Az Azure-kezeléshez való hozzáférés kezelése feltételes hozzáféréssel az Azure AD-ben
description: Ismerje meg, hogy az Azure AD feltételes hozzáféréshasználatával kezelheti az Azure-kezeléshez való hozzáférést.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137412"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Az Azure-kezeléshez való hozzáférés kezelése feltételes hozzáféréssel

> [!CAUTION]
> Győződjön meg arról, hogy a feltételes hozzáférés hogyan működik, mielőtt az Azure-kezeléshez való hozzáférés kezelésére szolgáló szabályzatot hozlétre. Győződjön meg arról, hogy nem hoz létre olyan feltételeket, amelyek letilthatják a saját hozzáférést a portálhoz.

Feltételes hozzáférés az Azure Active Directoryban (Azure AD) szabályozza a felhőalapú alkalmazásokhoz való hozzáférést a megadott feltételek alapján. A hozzáférés engedélyezéséhez olyan feltételes hozzáférési házirendeket hozhat létre, amelyek engedélyezik vagy letiltják a hozzáférést aházirend követelményei nek megfelelően. 

Általában feltételes hozzáférés sel szabályozhatja a felhőalapú alkalmazásokhoz való hozzáférést. Szabályzatokat is beállíthat az Azure-kezeléshez való hozzáférés szabályozására bizonyos feltételek (például a bejelentkezési kockázat, a hely vagy az eszköz) alapján, és kényszerítheti a követelményeket, például a többtényezős hitelesítést.

Az Azure-felügyelet házirendjének létrehozásához válassza a **Microsoft Azure Management** lehetőséget a **Felhőalapú alkalmazások** csoportban, amikor kiválasztja azt az alkalmazást, amelyre a szabályzatot alkalmazni szeretné.

![Feltételes hozzáférés az Azure felügyeleti szolgáltatásaihoz](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A létrehozott szabályzat az összes Azure-kezelési végpontra vonatkozik, beleértve a következőket:

- Azure portál
- Azure Resource Manager-szolgáltató
- Klasszikus szolgáltatáskezelési API-k
- Azure PowerShell
- Visual Studio-előfizetések rendszergazdai portálja
- Azure DevOps
- Az Azure Data Factory portálja

Vegye figyelembe, hogy a szabályzat az Azure PowerShellre vonatkozik, amely meghívja az Azure Resource Manager API-t. Nem vonatkozik az [Azure AD PowerShellre,](/powershell/azure/active-directory/install-adv2)amely meghívja a Microsoft Graph-ot.


A feltételes hozzáférés beállításáról és használatáról a [Feltételes hozzáférés az Azure Active Directoryban](../active-directory/active-directory-conditional-access-azure-portal.md)című témakörben talál további információt.
