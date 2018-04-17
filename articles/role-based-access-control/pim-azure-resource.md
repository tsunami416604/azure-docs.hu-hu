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
ms.openlocfilehash: 497efb7889029ef5a9478c59ee75ccabd5b7d87b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Azure Privileged Identity Management erőforrásokhoz való hozzáférés kezelése

Kiemelt jogosultságú fiókok védelmét a rosszindulatú számítógépes-támadások ellen, Azure Active Directory Privileged Identity Management (PIM) segítségével alacsonyabb jogosultságokat a kitettség idő, és növelje a jelentések és értesítések használatukat láthatósága. A PIM ennek érdekében, hogy csak a jogosultságait az "igény szerint" felhasználók (JIT), vagy egy rövidített időtartam elteltével jogosultságokkal vissza lenne vonva, automatikusan jogosultságok hozzárendelésével. 

Használhatja a PIM rendelkező átruházásához hozzáférés-vezérlés (RBAC) kezelése, szabályozása és figyelése az Azure-erőforrások eléréséhez. A PIM beépített és egyéni szerepkör tagsága segítségével képes kezelni: 

- Igény szerinti, "igény szerint" az Azure erőforrások hozzáférésének engedélyezése
- Erőforrás-hozzáférés automatikusan a hozzárendelt felhasználók és csoportok lejár
- Ideiglenes hozzáférés hozzárendelése az Azure-erőforrások gyors tevékenységek és a készenléti ütemezése
- Riasztásokat kaphat, ha új felhasználók vagy csoportok vannak hozzárendelve az erőforrás-hozzáférés, illetve ha az általuk aktivált jogosult hozzárendelések

További információkért lásd: [Overview of Role-Based hozzáférés-vezérlés az Azure PIM](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).