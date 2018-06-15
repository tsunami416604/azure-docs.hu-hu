---
title: Az Azure felügyeleti feltételes hozzáférés az Azure Active Directoryban való hozzáférés kezelése
description: További tudnivalók az Azure AD feltételes hozzáférés használata Azure-felügyelet elérésének kezeléséhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: dcf701c15ee20b7d9aeea65f3d06b794006a5202
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203930"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Az Azure-felügyelettel való hozzáférés kezelése feltételes hozzáféréssel

Feltételes hozzáférés az Azure Active Directory (Azure AD) szabályozza a hozzáférést a felhőalapú alkalmazások bizonyos megadott feltételeknek. Engedélyezi a hozzáférést, hozzon létre feltételes hozzáférési házirendeket, hogy engedélyezzék vagy letiltsák-e a házirendben teljesülnek alapján. 

Általában elérés feltételes hozzáférés segítségével a felhőalapú alkalmazásokat. Is beállíthat házirendek az Azure-felügyelettel bizonyos körülmények (például a bejelentkezési kockázat, hely vagy eszköz) alapuló hozzáférés-vezérlésének és követelményeket, például a többtényezős hitelesítés kényszerítéséhez.

Az Azure felügyeleti házirend létrehozásához válassza **Microsoft Azure Management** alatt **felhőalapú alkalmazásokba** kiválasztásakor az alkalmazást, amelyre a házirendet alkalmazni szeretné.

![Feltételes hozzáférés az Azure felügyeleti szolgáltatásaihoz](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A házirend létrehozása érvényes Azure felügyeleti végpontjai, beleértve a klasszikus Azure portál, Azure-portál, Azure Resource Manager-szolgáltató, klasszikus szolgáltatásfelügyeleti API és az Azure PowerShell.

> [!CAUTION]
> Győződjön meg arról, hogy feltételes hozzáférési házirend beállítása az Azure-felügyelet elérésének kezelése előtt működik. Győződjön meg arról, hogy ne hozzon létre a feltételeket, amelyek sikerült letiltja a saját a portálon való hozzáférést.

Állítsa be, és a feltételes hozzáférés használata a további információkért lásd: [feltételes hozzáférés az Azure Active Directoryban](../active-directory/active-directory-conditional-access-azure-portal.md).