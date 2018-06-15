---
title: Azure-készletben (szolgáltatás-rendszergazda és bérlői) felhasználónként erőforrások használatára vonatkozó engedélyek |} Microsoft Docs
description: Szolgáltatás-rendszergazdaként vagy bérlői megtudhatja, hogyan kezeli az RBAC-engedélyeket.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: fenila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: mabrigg
ms.reviewer: thomas.roettinger
ms.openlocfilehash: 0e50ea44ebb0b0a7285dab04666dd55cad480c6a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29385636"
---
# <a name="manage-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A felhasználó Azure verem lehet egy olvasó, tulajdonos vagy közreműködő egy előfizetés, erőforráscsoporthoz vagy szolgáltatás minden egyes példányánál. Például a felhasználó előfordulhat, hogy előfizetés egy olvasási engedéllyel rendelkezik, de a virtuális gép hét tulajdonos jogosult.

 - Olvasó: A felhasználó mindent megtekinthetnek, de nem módosíthatja.
 - Társszerző: Felhasználói kivételével mindent felügyelhetnek erőforrásokhoz való hozzáférést.
 - Tulajdonos: Felhasználói mindent felügyelhetnek, beleértve az erőforrásokhoz való hozzáférést.

## <a name="set-access-permissions-for-a-user"></a>A felhasználó hozzáférési engedélyeinek beállítása

1. Olyan fiókkal jelentkezzen be, amely a kezelni kívánt erőforrás tulajdonos jogosult.
2. Az erőforrás panelen kattintson a **hozzáférés** ikon ![](media/azure-stack-manage-permissions/image1.png).
3. Az a **felhasználók** panelen kattintson a **szerepkörök**.
4. Az a **szerepkörök** panelen kattintson a **Hozzáadás** hozzáférés biztosítása a felhasználónak.

## <a name="set-access-permissions-for-a-universal-group"></a>Az univerzális csoportok hozzáférési engedélyeinek beállítása 

> [!Note]  
Csak alkalmazandó Active Directory összevont szolgáltatások (AD FS).

1. Olyan fiókkal jelentkezzen be, amely a kezelni kívánt erőforrás tulajdonos jogosult.
2. Az erőforrás panelen kattintson a **hozzáférés** ikon ![](media/azure-stack-manage-permissions/image1.png).
3. Az a **felhasználók** panelen kattintson a **szerepkörök**.
4. Az a **szerepkörök** panelen kattintson a **Hozzáadás** hozzáférés biztosítása az univerzális csoport az Active Directory-csoport.

## <a name="next-steps"></a>További lépések
[Azure Stack-bérlő hozzáadása](azure-stack-add-new-user-aad.md)

