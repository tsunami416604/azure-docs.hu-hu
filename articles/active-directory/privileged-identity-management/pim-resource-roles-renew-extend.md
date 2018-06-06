---
title: Kiterjesztése, és tekintse át a szerepkörök az Azure-erőforrások Privileged Identity Management használatával |} Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan bővítheti és újítsa meg az Azure erőforrás-szerepkörök PIM erőforrások.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f152b2dd690976b31a34046954440dc848e5952c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699529"
---
# <a name="extend-and-review-roles-in-azure-resources-by-using-privileged-identity-management"></a>Kiterjesztése, és tekintse át a szerepkörök az Azure-erőforrások Privileged Identity Management használatával

Privileged Identity Management (PIM) az Azure-erőforrások vezet be az új szabályozza a hozzáférést és a hozzárendelés életciklusa Azure-erőforrások kezeléséhez. A rendszergazdák rendelhet tagság kezdő és záró dátum idejű tulajdonságainak használata. Amikor megközelíti a hozzárendelés célból, PIM e-mail értesítéseket küld az érintett felhasználók vagy csoportok. A rendszergazdák az erőforrás kell biztosítják, hogy megfelelő hozzáférési e-mail értesítést is küld. Hozzárendelések előfordulhat, hogy meg kell újítani, és látható akár 30 napig lejárt állapotban maradnak, még akkor is, ha nincs kibővítve a hozzáférés.

## <a name="who-can-extend-and-renew"></a>Kik kiterjesztése és megújításához?

Az erőforrás csak rendszergazdák kiterjeszthetik vagy szerepkör-hozzárendelések megújításához. Az érintett tag kiterjesztése, amely lejár, és a szerepköröket, amelyek már lejárt kérnie szerepkörök kérhet.

## <a name="when-are-notifications-sent"></a>Amikor értesítések a küldenek?

PIM értesítő e-mailek küld a rendszergazdák és az érintett tagok szerepkörök lejárta előtt egy nappal 14 napon belül lejár. Hozzárendelés hivatalosan lejártakor további e-mailt küld. 

Rendszergazdák értesítést kapjon, amikor egy lejáró vagy lejárt szerepkör tagjának vagy megújítása igényel. Egy adott rendszergazda megoldja a kérelmet, ha más rendszergazdák számára a feloldási döntési (elfogadhat vagy elutasíthat) értesítést kap. Majd a kérést küldő tagot értesítést kap a döntést. 

## <a name="extend-role-assignments"></a>Szerepkör-hozzárendelések kiterjesztése

Az alábbi lépéseket vázoltuk kér, megoldása vagy egy bővítmény vagy szerepkör-hozzárendelés megújítása felügyel. 

### <a name="member-extend"></a>Tagbővítés

Szerepkör-hozzárendelés tagjai kiterjesztheti lejáró szerepkör-hozzárendelések közvetlenül a **jogosult** vagy **Active** lapján a **a szerepkörök** erőforrás és a legfelső szintű lap **a szerepkörök** a PIM portál lapján. Tagok kérhetnek kiterjesztése jogosult és az aktív (hozzárendelt) szerepkörök, a következő 14 nap múlva lejár.

![Szerepkörök kiterjesztése](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

A hozzárendelés záró dátum-idő esetén 14 napon belül, a gombra kattintva **bővítése** válik a felhasználói felületen az aktív kapcsolat. A következő példában fel, hogy az aktuális dátum: március 27.

![A "Bővítése" gombra](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Egy bővítmény a szerepkör-hozzárendelés kérte, jelölje be **bővítése** az űrlap megnyitásához.

![Nyissa meg az űrlap](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Az eredeti hozzárendelés adatainak megtekintésére, bontsa ki a **hozzárendelés részletei**. Adja meg a bővítmény kérés okát, majd válassza ki **bővítése**.

>[!Note]
>Azt javasoljuk, hogy a részletes, miért szükség a bővítményt, és mennyi ideig a teljesíteni kell (ha ezek az információk).

![Szerepkör-hozzárendelés kiterjesztése](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

Pár perc múlva erőforrás-rendszergazdák kap egy e-mail értesítést kér, akkor tekintse át a bővítmény kérelem. Ha már elküldte a kérelmet, egy bejelentési értesítést az Azure portál, amely ismerteti, hogy a hiba tetején jelenik meg.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Lépjen a **függőben lévő kérelmek** lapon a bal oldali ablaktáblán, a kérelem állapotának megtekintéséhez, vagy megszakítja a műveletet.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Rendszergazda jóváhagyása

Tag kérést küld egy szerepkör-hozzárendelés kiterjesztése, amikor a erőforrás-rendszergazdák az eredeti hozzárendelést és a kérés okát részleteit tartalmazó e-mailben értesítést kapni. Az értesítés a kérelmet jóváhagyja vagy elutasítja a rendszergazda egy közvetlen hivatkozást tartalmaz. 

Mellett az e-mailben a hivatkozást követve, a rendszergazdák jóváhagyása vagy megtagadja a hozzáférést a PIM felügyeleti portál és a választ a kérelmek **kérelmek jóváhagyása** a bal oldali ablaktáblán.

![Képernyőkép a hiba](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Ha a rendszergazda itt **jóváhagyás** vagy **Megtagadás**, a kérelem részletes adatait látható, adja meg a felügyeleti naplók indokát mező mellett.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Ha a szerepkör-hozzárendelés kiterjeszteni kérelem jóváhagyását, erőforrás-rendszergazdák kiválaszthatják, egy új kezdési dátuma, a befejezési dátum és a hozzárendelés-típus. Hozzárendelés-típus módosítása lehet szükséges, ha a rendszergazda szeretne végrehajtani egy adott feladatot (egy nap, a példában), korlátozott hozzáférést biztosítanak. Ebben a példában a rendszergazda módosíthatja a hozzárendelés **jogosult** való **aktív**. Ez azt jelenti, hogy azok hozzáférést biztosíthat a kérelmező anélkül, hogy azok aktiválásához.

### <a name="admin-extend"></a>Rendszergazdai kiterjesztés

Ha elfelejti szerepkör tagja, vagy nem tudja befejezni a szerepkör tagsági bővítmény, a rendszergazda kiterjesztheti a tag nevében értékadás. Szerepköri tagság a felügyeleti bővítményei nem jóváhagyás megkövetelése, de a rendszer értesítéseket küld más rendszergazdák után a szerepkör megtörtént-e.

A szerepköri tagság kiterjesztéséhez tallózással keresse meg az erőforrás-szerepkör vagy a tag a PIM nézetben. A bővítmény igénylő tag található. Válassza ki **bővítése** művelet oszlopában.

![A szerepköri tagság kiterjesztése](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Újítsa meg a szerepkör-hozzárendelések

A folyamatot a kért bővítmény fogalmilag hasonló, amíg a folyamat egy lejárt szerepkör-hozzárendelés megújítása nem azonos. Az alábbi lépéseket követve, tagok és a rendszergazdák megújíthatják lejárt szerepkörökhöz szükség esetén a hozzáférést.

### <a name="member-renew"></a>Tag megújítása

Akár 30 napig lejárt hozzárendelési előzményei már nem tud hozzáférni az erőforrásokhoz tagjai férhetnek hozzá. Ehhez az szükséges, hogy tallózással **saját szerepkörök** a bal oldali ablaktáblán, majd válassza ki a **szerepkörök lejárt** lap az Azure-erőforrás szerepkörök szakaszában.

![A "Szerepkörök lejárt" lap](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Az alapértelmezett érték látható szerepkörök listája **jogosult szerepkörök**. A legördülő menü segítségével jogosult és a felelős szerepkörök között.

A szerepkör-hozzárendelések a listában szereplő bármely kéri, válassza ki a **megújítási** művelet. Adja meg a kérés okát. Akkor célszerű mellett minden további környezetre, amely segít a erőforrás rendszergazda úgy dönt, hogy jóváhagyásához vagy elutasításához időtartamot.

![Újítsa meg a szerepkör-hozzárendelés](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Miután elküldte a kérést, erőforrás-Rendszergazdák szerepkör-hozzárendelés megújításához függőben lévő kérelem értesítést kap.

### <a name="admin-approves"></a>Rendszergazda jóváhagyása

Erőforrás-rendszergazdák érhetik el a tanúsítványmegújítási kérelmet a hivatkozás vagy PIM elérése az Azure-portálon, majd válassza az e-mail értesítés **kérelmek jóváhagyása** a bal oldali ablaktáblán.

![Kérések jóváhagyása](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Ha a rendszergazda itt **jóváhagyás** vagy **Megtagadás**, a kérelem részletes adatait arra, hogy a felügyeleti naplók indokát mező mellett látható.

![Hagyja jóvá a szerepkör-hozzárendelés](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Szerepkör-hozzárendelés megújításának kéréséhez jóváhagyásakor erőforrás rendszergazdák adjon meg egy új kezdési dátuma, a befejezési dátum és a hozzárendelés-típus. 

### <a name="admin-renew"></a>Rendszergazdai megújítás

Erőforrás-rendszergazdák megújíthatják a lejárt szerepkör-hozzárendelések a **tagok** a bal oldali navigációs menü erőforrás fülre. Azokat a lejárt szerepkör-hozzárendelések belül is megújíthatják a **lejárt** szerepkörök lapon olyan erőforrás-szerepkörhöz.

Megtekintheti az összes szerepkör-hozzárendelések, lejárt ezen a **tagok** képernyőn válassza ki **szerepkörök lejárt**.

![Lejárt szerepkörök](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>További lépések

[Jóváhagyás aktiválásához szükséges](pim-resource-roles-approval-workflow.md)

[A szerepkör aktiválása](pim-resource-roles-use-the-audit-log.md)


