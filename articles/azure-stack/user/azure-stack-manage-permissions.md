---
title: "Kezeli az engedélyeket a verem Azure-ban felhasználónként erőforrásokhoz |} Microsoft Docs"
description: "Szolgáltatás-rendszergazdaként vagy bérlői megtudhatja, hogyan kezeli az RBAC-engedélyeket."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: dfec5648ff383fd98965c1918cdab6472bb3c17f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A felhasználó Azure verem lehet egy olvasó, tulajdonos vagy közreműködő egy előfizetés, erőforráscsoporthoz vagy szolgáltatás minden egyes példányánál. Például a felhasználó előfordulhat, hogy olvasási engedélye előfizetés 1, de virtuális gép 7 tulajdonos jogosult.

* Olvasó: A felhasználó mindent megtekinthetnek, de nem módosíthatja.
* Társszerző: Felhasználói kivételével mindent felügyelhetnek erőforrásokhoz való hozzáférést.
* Tulajdonos: Felhasználói mindent felügyelhetnek, beleértve az erőforrásokhoz való hozzáférést.

## <a name="set-access-permissions-for-a-user"></a>A felhasználó hozzáférési engedélyeinek beállítása
1. Olyan fiókkal jelentkezzen be, amely a kezelni kívánt erőforrás tulajdonos jogosult.
2. Az erőforrás panelen kattintson a **hozzáférés** ikon ![](media/azure-stack-manage-permissions/image1.png).
3. Az a **felhasználók** panelen kattintson a **szerepkörök**.
4. Az a **szerepkörök** panelen kattintson a **Hozzáadás** hozzáférés biztosítása a felhasználónak.

## <a name="next-steps"></a>További lépések


