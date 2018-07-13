---
title: Az Azure AD Privileged Identity Management adatvédelmi varázsló
description: Az első alkalommal használja az Azure Active Directory Privileged Identity Management bővítmény megjelenik egy biztonsági varázsló. Ez a cikk ismerteti a lépéseket, a varázsló használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 6d600afea67d2703c796b403b3e509383f7171a5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590288"
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management biztonsági varázslója használatával 
Ha Ön az első, aki a szervezet az Azure Privileged Identity Management (PIM) futtatása, megjelenik egy varázsló segítségével. A varázsló segítségével megismerheti a biztonsági kockázatokat az emelt szintű identitások és a PIM használata a kockázatok csökkentése érdekében. Nem kell módosításokat a varázsló a meglévő szerepkör-hozzárendelés, ha később szeretne.

## <a name="what-to-expect"></a>Mi várható?
A szervezet elindul, miközben a PIM használatával, mielőtt állandó-e az összes szerepkör-hozzárendelések: a felhasználók mindig vannak ezek a szerepkörök akkor is, ha jelenleg nem szükségesek a jogosultságait.  A varázsló az első lépés bemutatja, magas szintű jogosultságokat igénylő szerepkörök listáját, és hogy hány felhasználó van éppen ezeket a szerepköröket. Hogy részletesebben is megtekintheti a felhasználók olvashat bővebben, ha egy adott szerepkörhöz vagy nem több ismeri.

A varázsló második lépése lehetővé teszi a rendszergazda szerepkör-hozzárendelések módosítása.  

> [!WARNING]
> Fontos, hogy legalább egy globális rendszergazda, és a egy szervezeti fiókkal (nem Microsoft-fiók) több kiemelt szerepkörű rendszergazda. Ha csak egy kiemelt szerepkörű rendszergazda, a szervezet nem lesz képes a PIM kezelése, ha a fiókot törölték.
> Emellett folyamatosan állandó, ha egy felhasználó Microsoft-fiókkal (fiókkal jelentkezzen be a Microsoft szolgáltatásaihoz, például a Skype-hoz és az Outlook.com-ot használnak) szerepkör-hozzárendelések. Ha azt tervezi, többtényezős hitelesítés megkövetelése az aktiváláshoz ahhoz a szerepkörhöz tartozó, hogy a felhasználó lesz zárolva.
> 
> 

Miután elvégezte a módosításokat, a varázsló már nem jelennek meg. A következő alkalommal, amikor Ön vagy egy másik kiemelt szerepkörű rendszergazda a PIM használatát tervezi, látni fogja a PIM-irányítópulton.  

* Ha szeretne hozzáadni vagy felhasználók eltávolítása a szerepkörök vagy-hozzárendelések módosítása az állandó jogosulttá, további információk: [hozzáadása vagy egy felhasználói szerepkör eltávolítása](pim-how-to-add-role-to-user.md).
* Ha szeretné a PIM kezelése hozzáférést biztosít a több felhasználó, további információk: [a PIM kezelése hozzáférés biztosítása](pim-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

