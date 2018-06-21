---
title: Azure-erőforrások a Privileged Identity Management (PIM) való hozzáférés kezelése
description: További tudnivalók az Azure Privileged Identity Management (PIM) és a szerepköralapú hozzáférés-vezérlést (RBAC) használata erőforrásokhoz való hozzáférés kezelése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 838c889f2dc099b4a4c5d84521871c64eb989163
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293750"
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Azure Privileged Identity Management erőforrásokhoz való hozzáférés kezelése

Kiemelt jogosultságú fiókok védelmét a rosszindulatú számítógépes-támadások ellen, Azure Active Directory Privileged Identity Management (PIM) segítségével alacsonyabb jogosultságokat a kitettség idő, és növelje a jelentések és értesítések használatukat láthatósága. A PIM ennek érdekében, hogy csak a jogosultságait az "igény szerint" felhasználók (JIT), vagy egy rövidített időtartam elteltével jogosultságokkal vissza lenne vonva, automatikusan jogosultságok hozzárendelésével. 

Használhatja a PIM Azure szerepköralapú hozzáférés-vezérléssel (RBAC) kezelése, szabályozása és figyelése az Azure-erőforrások eléréséhez. A PIM beépített és egyéni szerepkör tagsága segítségével képes kezelni: 

- Igény szerinti, "igény szerint" az Azure erőforrások hozzáférésének engedélyezése
- Erőforrás-hozzáférés automatikusan a hozzárendelt felhasználók és csoportok lejár
- Ideiglenes hozzáférés hozzárendelése az Azure-erőforrások gyors tevékenységek és a készenléti ütemezése
- Riasztásokat kaphat, ha új felhasználók vagy csoportok vannak hozzárendelve az erőforrás-hozzáférés, illetve ha az általuk aktivált jogosult hozzárendelések

További információkért lásd: [Azure PIM szerepköralapú hozzáférés-vezérlés áttekintése](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).