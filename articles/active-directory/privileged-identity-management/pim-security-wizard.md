---
title: Adatvédelmi varázsló az PIM-ben – Azure |} A Microsoft Docs
description: Az Azure AD Privileged Identity Management (PIM) első használatakor megjelenő adatvédelmi varázsló ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 178a4c5e978075f2a59b22a1cccf462138527964
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189079"
---
# <a name="security-wizard-in-pim"></a>A PIM biztonsági varázsló
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

- [A PIM használatának megkezdése](pim-getting-started.md)
- [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Hozzáférés biztosítása más rendszergazdák számára, miközben a PIM kezelése](pim-how-to-give-access-to-pim.md)
