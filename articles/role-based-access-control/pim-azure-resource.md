---
title: Azure-erőforrásokhoz való hozzáférés kezelése az Azure AD-vel és a PIM-mel
description: Ismerje meg az Azure-erőforrásokhoz való hozzáférés kezelését az Azure Active Directory privilegizált identitáskezelés (PIM) és a szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138035"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Azure-erőforrásokhoz való hozzáférés kezelése az Azure AD kiemelt identitáskezelésével

A kiemelt jogosultságú fiókok védelme a rosszindulatú kibertámadások ellen, az Azure Active Directory kiemelt identitáskezelés (PIM) használatával csökkentheti a jogosultságok expozíciós idejét, és jelentések és riasztások révén növelheti a használatuk láthatóságát. A PIM ezt úgy éri el, hogy a felhasználókat csak a jogosultságaik "éppen időben" (JIT) vételére korlátozza, vagy rövidebb időtartamra rendel jogosultságokat, amely után a jogosultságokat automatikusan visszavonják. 

Most már használhatja a PIM-et az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrásokhoz való hozzáférés kezeléséhez, vezérléséhez és figyeléséhez. A PIM a beépített és egyéni szerepkörök tagságát a következők segítése érdekében kezelheti: 

- Igény szerinti, "éppen időben" való hozzáférés engedélyezése az Azure-erőforrásokhoz
- Erőforrás-hozzáférés automatikus lejárata hozzárendelt felhasználók és csoportok számára
- Ideiglenes hozzáférés hozzárendelése az Azure-erőforrásokhoz a gyors feladatokhoz vagy az ügyeleti ütemezésekhez
- Értesítések et kaphat, ha új felhasználók vagy csoportok vannak hozzárendelve az erőforrás-hozzáféréshez, és amikor aktiválják a jogosult hozzárendeléseket

További információ: [Mi az Azure AD kiemelt identitáskezelés?](../active-directory/privileged-identity-management/pim-configure.md).