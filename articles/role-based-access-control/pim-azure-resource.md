---
title: Azure-erőforrásokhoz való hozzáférés kezelése az Azure AD és a PIM használatával
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést a Azure Active Directory Privileged Identity Management (PIM) és a szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 53c6375ce04425eae27f4c935e613cc27d058dc8
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138035"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Azure-erőforrásokhoz való hozzáférés kezelése Azure AD Privileged Identity Management

A rendszerjogosultságú fiókok rosszindulatú cyber-támadások elleni védelme érdekében Azure Active Directory Privileged Identity Management (PIM) használatával csökkentheti a jogosultságok expozíciós idejét, és a jelentésekkel és riasztásokkal növelheti a használat láthatóságát. A PIM ezt azáltal korlátozza, hogy a felhasználók csak az idő függvényében (JIT) használják a jogosultságokat, vagy ha jogosultságokat rendelnek a lerövidített időtartamhoz, amely után a rendszer automatikusan visszavonja a jogosultságokat. 

Mostantól használhatja a PIM-t az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrásokhoz való hozzáférés felügyeletéhez, szabályozásához és figyeléséhez. A PIM képes a beépített és egyéni szerepkörök tagságának kezelésére: 

- Igény szerinti, "egyszeri" hozzáférés engedélyezése az Azure-erőforrásokhoz
- Erőforrás-hozzáférés automatikus lejárata a hozzárendelt felhasználókhoz és csoportokhoz
- Az Azure-erőforrásokhoz való ideiglenes hozzáférés kiosztása gyors feladatok vagy hívási ütemtervek esetén
- Riasztások beszerzése új felhasználók vagy csoportok erőforrás-hozzáféréshez való hozzárendelésekor, valamint a jogosult hozzárendelések aktiválásakor

További információ: [Mi az Azure ad Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)