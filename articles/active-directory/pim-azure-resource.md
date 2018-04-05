---
title: Azure-erőforrások a Privileged Identity Management (PIM) való hozzáférés kezelése
description: Ismerje meg a szerepköralapú hozzáférés-kezelés a PIM Azure-erőforrások eléréséhez használt.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 1e74579ef2f0e18f23a40dfc573177938b9b726f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Azure Privileged Identity Management erőforrásokhoz való hozzáférés kezelése

Kiemelt jogosultságú fiókok védelmét a rosszindulatú számítógépes-támadások ellen, Azure Active Directory Privileged Identity Management (PIM) segítségével alacsonyabb jogosultságokat a kitettség idő, és növelje a jelentések és értesítések használatukat láthatósága. A PIM ennek érdekében, hogy csak a jogosultságait az "igény szerint" felhasználók (JIT), vagy egy rövidített időtartam elteltével jogosultságokkal vissza lenne vonva, automatikusan jogosultságok hozzárendelésével. 

Használhatja a PIM rendelkező átruházásához hozzáférés-vezérlés (RBAC) kezelése, szabályozása és figyelése az Azure-erőforrások eléréséhez. A PIM beépített és egyéni szerepkör tagsága segítségével képes kezelni: 

- Igény szerinti, "igény szerint" az Azure erőforrások hozzáférésének engedélyezése
- Erőforrás-hozzáférés automatikusan a hozzárendelt felhasználók és csoportok lejár
- Ideiglenes hozzáférés hozzárendelése az Azure-erőforrások gyors tevékenységek és a készenléti ütemezése
- Riasztásokat kaphat, ha új felhasználók vagy csoportok vannak hozzárendelve az erőforrás-hozzáférés, illetve ha az általuk aktivált jogosult hozzárendelések

További információkért lásd: [Overview of Role-Based hozzáférés-vezérlés az Azure PIM](privileged-identity-management/azure-pim-resource-rbac.md).