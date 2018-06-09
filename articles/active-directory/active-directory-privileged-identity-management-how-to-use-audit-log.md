---
title: A napló használata az Azure AD Privileged Identity Management |} Microsoft Docs
description: 'Útmutató: az auditnapló használata az Azure Privileged Identity Management bővítmény.'
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 05a70874367d578beee1dc605510f9370b6abd42
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35232992"
---
# <a name="using-the-audit-log-in-pim"></a>A PIM a napló használatával
A Privileged Identity Management (PIM) napló segítségével tekintse meg a felhasználó-hozzárendeléseket és aktiválások egy adott időtartamon belül. Ha meg szeretné tekinteni a teljes naplózási előzmények a bérlői rendszergazda, a végfelhasználó és a szinkronizálási tevékenység, beleértve a tevékenység használhatja a [Azure Active Directory hozzáférési és használati jelentések.](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>Keresse meg a napló
Az a [Azure-portálon](https://portal.azure.com) irányítópultot, válassza ki a **Azure AD Privileged Identity Management** alkalmazást. Ott, hozzáférni a biztonsági naplóba kattintva **kiemelt szerepköröket kezelése** > **naplózási előzmények** a PIM irányítópulton.

## <a name="the-audit-log-graph"></a>Az ellenőrzési napló diagramhoz
A napló segítségével tekintse meg az összes aktiválás, napi maximális aktiválások és napi átlagos aktiválások vonaldiagramon.  Az adatok szerepkör szűrhető Ha több szerepkör van a naplózási előzmények.

Használja a **idő**, **művelet**, és **szerepkör** gombokra kattintva rendezheti a naplót.

## <a name="the-audit-log-list"></a>A vizsgálati naplók listája
A vizsgálati naplók listája oszlopai a következők:

* **Kérelmező** – a felhasználóknak, akik a szerepkör aktiválása vagy módosítsa a kért.  Ha az érték "Azure rendszer", ellenőrizze a Azure naplózási további információt.
* **Felhasználói** -aktiválásával vagy az adott szerepkörhöz hozzárendelt felhasználó.
* **Szerepkör** -a szerepkörhöz hozzárendelt, vagy a felhasználó által aktivált.
* **A művelet** – a kérelmező által végrehajtott műveleteket. Ez magában foglalhatja hozzárendelés, helyekhez, aktiválás vagy deaktiválás.
* **Idő** – Ha a művelet történt.
* **Mintafelismerési** -szöveg az aktiválás során az az oka mező megadása esetén megjelenik itt.
* **Lejárati** – csak akkor érvényes, a szerepkörök aktiválásához.

## <a name="filter-the-audit-log"></a>A napló szűrése
A gombra kattintva megjelenik a napló információkat szűrheti is a **szűrő** gombra.  A **frissítés diagram (paraméterek) panelen** jelenik meg.

Miután beállította a szűrőket, kattintson a **frissítés** a naplóban szereplő adatok szűrésére.  Ha az adatok nem jelennek meg azonnal, frissítse az oldalt.

### <a name="change-the-date-range"></a>Módosítás dátumtartományban
Használja a **Ma**, **múlt héten**, **elmúlt hónap**, vagy **egyéni** gombokkal módosíthatja az időtartományt a biztonsági napló.

Ha úgy dönt, a **egyéni** gomb, Ön által megadott egy **a** dátummezője és egy **való** dátum mezőben adja meg a napló dátumtartomány.  HH/nn/éééé formátumban adja meg a dátumok, vagy kattintson a a **naptár** ikonra, és válassza ki a dátum alapján.

### <a name="change-the-roles-included-in-the-log"></a>A naplóban szereplő szerepkörök módosítása
Ellenőrizze, vagy törölje a jelet a **szerepkör** vagy kizárja a a naplóból szerepkörönként jelölőnégyzetét.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

