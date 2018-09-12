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
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: ab61e1f892f46ad36df741b7a72afcfcbaa0ed87
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44376935"
---
# <a name="manage-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés kezelése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack egy felhasználó lehet egy olvasó, tulajdonos vagy közreműködő egy előfizetés, erőforráscsoport vagy szolgáltatás minden egyes példányánál. Például a felhasználó előfordulhat, hogy olvasó engedélye arra, hogy egy előfizetés, de a virtuális gép hét tulajdonosi engedélyekkel rendelkeznek.

 - Olvasó: A felhasználó, aki mindent megtekinthet, de nem módosíthatja.
 - Közreműködői: Felhasználói kivételével mindent felügyelhetnek erőforrásokhoz való hozzáférést.
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

