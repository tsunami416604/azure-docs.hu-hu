---
title: Az Azure AD Privileged Identity Management (PIM) az Azure-erőforrásokhoz való hozzáférés kezelése
description: Ismerje meg az Azure Active Directory Privileged Identity Management (PIM) és a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz való hozzáférés kezelése.
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
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338203"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management Azure-erőforrásokhoz való hozzáférés kezelése

Kiemelt jogosultságú fiókok védelmét a rosszindulatú kibertámadások ellen, a jogosultságok expozíciós idejének és a jelentéseket és riasztásokat keresztül használatuk átláthatóvá használhatja az Azure Active Directory Privileged Identity Management (PIM). A PIM azért teszi ezt, korlátozza a felhasználók számára csak a jogosultságai a "csak az időben" véve (JIT), vagy a jogosultságok hozzárendelése a akkor használhatja rövidített időtartam elteltével jogosultságokkal automatikusan visszavonódnak. 

Mostantól használhatja a PIM az Azure szerepköralapú hozzáférés-vezérlés (RBAC) kezelése, szabályozása és figyelése az Azure-erőforrásokhoz való hozzáférést. A PIM beépített és egyéni szerepkör tagsága segítségével kezelheti: 

- Igény szerinti, "csak az időben" az Azure erőforrások hozzáférésének engedélyezése
- Erőforrás-hozzáférés automatikusan a hozzárendelt felhasználók és csoportok elévülése
- Ideiglenes hozzáférések hozzárendelése az Azure-erőforrások gyors feladatok vagy a készenléti ütemezések
- Értesítéseket kaphat az új felhasználók vagy csoportok hozzárendelése esetén erőforrásokhoz való hozzáférést, és ha azok a jogosult hozzárendelések aktiválása

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).