---
title: Az Azure Stackben (szolgáltatás-rendszergazda és bérlői) felhasználónként-erőforrások kezelése |} A Microsoft Docs
description: Szolgáltatás-rendszergazda vagy bérlői megtudhatja, hogyan kezelheti az RBAC-engedélyek.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 26ce7139b856fc2f8d7d2cad549b3dd3c0f5e406
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304686"
---
# <a name="manage-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack egy felhasználó lehet egy olvasó, tulajdonos vagy közreműködő egy előfizetés, erőforráscsoport vagy szolgáltatás minden egyes példányánál. Például a felhasználó előfordulhat, hogy olvasó engedélye arra, hogy egy előfizetés, de a virtuális gép hét tulajdonosi engedélyekkel rendelkeznek.

 - Olvasó: Felhasználó, aki mindent megtekinthet, de nem módosíthatja.
 - Közreműködői: Felhasználók erőforrásokhoz való hozzáférés kivételével mindent felügyelhetnek.
 - Tulajdonos: Felhasználói mindent felügyelhetnek, beleértve az erőforrásokhoz való hozzáférést.

## <a name="set-access-permissions-for-a-user"></a>Egy felhasználó hozzáférési engedélyek beállítása

1. A felügyelni kívánt erőforrás tulajdonosi engedélyekkel rendelkező fiókkal jelentkezzen be.
2. Az erőforrás panelen kattintson a **hozzáférés** ikon ![](media/azure-stack-manage-permissions/image1.png).
3. Az a **felhasználók** panelen kattintson a **szerepkörök**.
4. Az a **szerepkörök** panelen kattintson a **Hozzáadás** hozzáférés biztosítása a felhasználónak.

## <a name="set-access-permissions-for-a-universal-group"></a>Az univerzális csoportok a hozzáférési engedélyek beállítása 

> [!Note]  
Alkalmazható, csak az Active Directory összevont szolgáltatások (AD FS).

1. A felügyelni kívánt erőforrás tulajdonosi engedélyekkel rendelkező fiókkal jelentkezzen be.
2. Az erőforrás panelen kattintson a **hozzáférés** ikon ![](media/azure-stack-manage-permissions/image1.png).
3. Az a **felhasználók** panelen kattintson a **szerepkörök**.
4. Az a **szerepkörök** panelen kattintson a **Hozzáadás** hozzáférés biztosítása az univerzális csoport az Active Directory-csoporthoz.

## <a name="next-steps"></a>További lépések
[Azure Stack-bérlő hozzáadása](azure-stack-add-new-user-aad.md)

