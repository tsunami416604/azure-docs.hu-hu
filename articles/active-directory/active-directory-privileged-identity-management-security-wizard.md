---
title: Az Azure AD Privileged Identity Management adatvédelmi varázslója
description: Az Azure Active Directory Privileged Identity Management bővítmény, az első használatakor választhat egy biztonsági varázslót. Ez a cikk a varázsló lépéseit ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: cb08e5143814db29a7bec8c46226f07a26e51d25
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233020"
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management biztonsági varázslóval 
Ha Ön az első személy a szervezeténél Azure Privileged Identity Management (PIM) futtatásához, választhat egy varázslót. A varázsló segít megérteni a biztonsági kockázatok kiemelt jogosultságú identitások és a PIM használata a kockázatok csökkentése érdekében. Nem kell módosítaniuk a varázslóban a meglévő szerepkör-hozzárendelések, ha később szeretné végrehajtani.

## <a name="what-to-expect"></a>Mi várható?
PIM segítségével a szervezet megkezdése előtt minden szerepkör-hozzárendeléseket a rendszer állandó: a felhasználók mindig vannak ezek a szerepkörök akkor is, ha a jogosultságait jelenleg nincs szükségük.  Az első lépés a varázsló mutatja magas szintű jogosultságokat igénylő szerepkörök listáját és a felhasználók jelenleg ezeket a szerepköröket. További információt felhasználók egy adott szerep megtekintheti, vagy több nem ismeri.

A varázsló második lépése lehetővé teszi rendszergazdai szerepkör-hozzárendelések módosítása.  

> [!WARNING]
> Fontos, hogy rendelkezik legalább egy globális rendszergazda, és több kiemelt szerepkörű rendszergazda egy olyan szervezeti fiókkal (nem Microsoft-fiókkal). Ha csak egy kiemelt szerepkörű rendszergazda, a szervezet csak akkor tudja kezelni a PIM, ha, hogy a fiókot törölték.
> Azt is vegye állandó, ha egy felhasználó Microsoft-fiókkal (fiókkal bejelentkezni a Microsoft-szolgáltatásokat, mint a Skype és Outlook.com-ot használnak) szerepkör-hozzárendelések. Ha azt tervezi, az adott szerepkörhöz tartozó aktiválást megkövetelő, hogy a felhasználó lesz zárolva.
> 
> 

Módosításokat hajtott végre, miután a varázsló már nem jelennek meg. A következő használatakor, vagy egy másik kiemelt szerepkörű rendszergazda PIM, látni fogja a PIM irányítópult.  

* Ha azt szeretné, adja hozzá vagy felhasználók eltávolítása a szerepkörök vagy-hozzárendelések módosítása az állandó támogatható, további információ: [hozzáadása vagy eltávolítása a felhasználói szerepkör](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
* Ha azt szeretné, a további felhasználók hozzáférésének kezelése a PIM, további információ: [hogyan kezelhetik a PIM hozzáférést](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

