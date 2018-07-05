---
title: A Privileged Identity Management (PIM) Azure-erőforrásokhoz való hozzáférés kezelése
description: Ismerje meg a Privileged Identity Management (PIM) és a szerepköralapú hozzáférés-vezérlés (RBAC) használatával az Azure-erőforrásokhoz való hozzáférés kezelése.
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
ms.openlocfilehash: 141cba29f5027ce092775d97c1abe9ecf11badf5
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436045"
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>A Privileged Identity Management Azure-erőforrásokhoz való hozzáférés kezelése

Kiemelt jogosultságú fiókok védelmét a rosszindulatú kibertámadások ellen, a jogosultságok expozíciós idejének és a jelentéseket és riasztásokat keresztül használatuk átláthatóvá használhatja az Azure Active Directory Privileged Identity Management (PIM). A PIM azért teszi ezt, korlátozza a felhasználók számára csak a jogosultságai a "csak az időben" véve (JIT), vagy a jogosultságok hozzárendelése a akkor használhatja rövidített időtartam elteltével jogosultságokkal automatikusan visszavonódnak. 

Mostantól használhatja a PIM az Azure szerepköralapú hozzáférés-vezérlés (RBAC) kezelése, szabályozása és figyelése az Azure-erőforrásokhoz való hozzáférést. A PIM beépített és egyéni szerepkör tagsága segítségével kezelheti: 

- Igény szerinti, "csak az időben" az Azure erőforrások hozzáférésének engedélyezése
- Erőforrás-hozzáférés automatikusan a hozzárendelt felhasználók és csoportok elévülése
- Ideiglenes hozzáférések hozzárendelése az Azure-erőforrások gyors feladatok vagy a készenléti ütemezések
- Értesítéseket kaphat az új felhasználók vagy csoportok hozzárendelése esetén erőforrásokhoz való hozzáférést, és ha azok a jogosult hozzárendelések aktiválása

További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure PIM-ben – áttekintés](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).