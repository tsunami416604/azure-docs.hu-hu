---
title: Privileged Identity Management az Azure-erőforrások - kiterjesztése, és szerepkörök megújítása |} Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan bővítheti és újítsa meg az Azure erőforrás-szerepkörök PIM erőforrások.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management - szerepkör erőforrás - kiterjesztése vagy megújítása

Az Azure-erőforrások PIM vezet be az új szabályozza a hozzáférést és a hozzárendelés életciklusa Azure-erőforrások kezeléséhez. A rendszergazdák rendelhet tagság kezdő és záró dátum idejű tulajdonságainak használata. Amikor megközelíti a hozzárendelés célból, PIM e-mail értesítéseket küld az érintett tagok (Ez lehet egy felhasználó vagy csoport), és a rendszergazdák számára, az erőforrás biztosítják-megfelelő hozzáféréssel. Abban az esetben, ha hozzáférést tétlenség miatt nincs kibővítve, a hozzárendelések megújíthatja és is látható maradjon, lejárt állapotban akár 30 napig.

## <a name="who-can-extend-and-renew"></a>Kik kiterjesztése és megújításához?

Az erőforrás csak rendszergazdák kiterjeszthetik vagy szerepkör-hozzárendelések megújításához. Az érintett tag kérheti kiterjesztése szerepkörök lejár, és szerepkörök kérelem megújítási már lejárt.

## <a name="when-are-notifications-sent"></a>Amikor értesítések a küldenek?

PIM értesítő e-mailek küld a rendszergazdák és az érintett tagokat a szerepkörök lejárta előtt egy nappal 14 napon belül lejár. Egy további e-mailt küld, amikor hozzárendelés hivatalosan lejárt. 

Rendszergazdák értesítést kapjon, amikor egy lejáró vagy lejárt szerepkör tagjának vagy megújítása igényel. Egy adott rendszergazda megoldja a kérelmet, ha az összes rendszergazda értesítést kap a feloldási döntési (jóváhagyása és elutasítása), és taggal értesítést kap a döntést. 

## <a name="extend-role-assignments"></a>Szerepkör-hozzárendelések kiterjesztése

Az alábbi lépéseket helyzeteit vázolják fel, a lépések és a felhasználói felület kért, megoldása vagy egy kiterjesztés vagy a szerepkör-hozzárendelés megújítása felügyelete részt. 

### <a name="member-extend"></a>Tag kiterjesztése

Szerepkör-hozzárendelés tagjai kérhet terjessze ki a szerepkör-hozzárendelések lejáró közvetlenül a "Jogosult" vagy "Aktív" fülre, a "Saját szerepkörök" lapon erőforrás és a legfelső szintű a szerepkörök a PIM portál. Tagok kérhetnek kiterjesztése jogosult és az aktív (hozzárendelt) szerepkörök, a következő 14 nap múlva lejár.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Amikor a hozzárendelés záró dátum-idő 14 napon belül, a "Bővítése" gombra lesz aktív hivatkozások a felhasználói felületen. Az alábbi példában fel, hogy az aktuális dátum: március 27.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Igényelni ehhez a szerepkörhöz kiterjesztése hozzárendelés kattintson a "Bővítése" az űrlap megnyitásához.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Bontsa ki a "hozzárendelés részletei" az eredeti hozzárendelés adatainak megtekintésére. Adja meg a bővítmény kérés okát, majd kattintson a "Bővítése".

>[!Note]
>Azt javasoljuk, hogy a részletes, miért szükség a bővítményt, és mennyi ideig a bővítmény kell (amennyiben ismert).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

Pár perc múlva, az erőforrás-rendszergazdák azok tekintse át a bővítmény kérelem kérő e-mailben értesítést fog kapni. Már elküldte a kérelmet, ha egy bejelentési értesítést az Azure portál, amely ismerteti, hogy a hiba a lap tetején fog megjelenni.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Látogasson el a "függőben lévő kérelmek" fülre a bal oldali navigációs menü állapotának megtekintéséhez, vagy megszakítja a kérést.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Rendszergazda jóváhagyása

Tag kérést küld egy szerepkör-hozzárendelés kiterjesztése, amikor a erőforrás-rendszergazdák az eredeti hozzárendelésének és az OK, a kérelmező adja részleteit tartalmazó e-mailben értesítést kapni. Az értesítés a kérelmet jóváhagyja vagy elutasítja a rendszergazda egy közvetlen hivatkozást tartalmaz. 

A rendszergazdák mellett a következő hivatkozásra az e-mailben, jóváhagyhatja vagy megtagadja a hozzáférést a PIM felügyeleti portál megnyitásával, majd válassza a "Jóváhagyás kérelmek" a bal oldali navigációs menü kérelmek.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Amikor a rendszergazda jóváhagyása és a letiltott alkalmazások itt az a kérelmek részleteit számára jelennek meg mező mellett adja meg a felügyeleti naplók indokát.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Ha a szerepkör-hozzárendelés kiterjeszteni kérelem jóváhagyását, erőforrás rendszergazdák kiválaszthatják, új kezdő és záró dátum-idő és a hozzárendelés-típus. Hozzárendelés típusa lehet szükség, ha a rendszergazda korlátozott hozzáférést biztosít egy adott feladat (például egy nap) végezze el. Ebben a példában a rendszergazda módosíthatja a hozzárendelés jogosult az aktív, a kérelmező hozzáférést biztosító anélkül, hogy azok aktiválásához.

### <a name="admin-extend"></a>Felügyeleti kiterjesztése

Abban az esetben egy szerepkör tagjának elfelejti, vagy nem tudja befejezni a szerepkör tagsági bővítmény rendszergazda meghosszabbíthatja nevében tag értékadás. Szerepköri tagság a felügyeleti bővítményei nem jóváhagyás megkövetelése, de a rendszer értesítéseket küld, amely a szerepkör létrehozása után más rendszergazdákat.

Egy szerepkör kiterjeszteni tagsági PIM a erőforrás szerepkör vagy a tag nézet megnyitása. Található a kiterjesztés igénylő, majd kattintson a "Bővítése" művelet oszlopában.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Újítsa meg a szerepkör-hozzárendelések

Fogalmilag hasonló, amíg a folyamat egy lejárt szerepkör-hozzárendelés megújításához eltér bővítmény igénylő tagok és a rendszergazdák számára. Az alábbi tagokat és a rendszergazdák lépéseket követve megújíthatják a hozzáférés a lejárt szerepkörökhöz szükség esetén.

### <a name="member-renew"></a>Tag megújítása

Már nem tud hozzáférni az erőforrásokhoz tagjai férhetnek hozzá akár 30 napig lejárt hozzárendelési előzményei saját szerepkörök navigáljon a bal oldali navigációs a PIM, majd válassza a "Szerepkörök lejárt" fülre az Azure-erőforrás szerepkörök szakaszban.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Az alapértelmezett érték a jogosult hozzárendelések látható szerepkörök listája. A legördülő jogosult közötti váltáshoz használja, és aktív szerepkört.

A listában válassza ki a "Megújítási" műveletet a szerepkör-hozzárendelések bármelyikéhez kéri, és adja meg a kérés okát. Akkor célszerű mellett minden további környezetre, amely segítségére lesz az erőforrás rendszergazda úgy dönt, hogy jóváhagyásához vagy elutasításához időtartamot.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

A kérelem elküldése után erőforrás-Rendszergazdák szerepkör-hozzárendelés megújításához függőben lévő kérelem értesítést kap.

### <a name="admin-approves"></a>Rendszergazda jóváhagyása

Erőforrás-rendszergazdák érhetik el a tanúsítványmegújítási kérelmet a hivatkozásra az e-mail értesítés vagy PIM elérése a Auzre portálról, majd válassza a "Jóváhagyás kérelmek" bal oldali navigációs menüjében.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Amikor a rendszergazda jóváhagyása és a letiltott alkalmazások itt az a kérelmek részleteit számára jelennek meg mező mellett adja meg a felügyeleti naplók indokát.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Szerepkör-hozzárendelés megújításának kéréséhez jóváhagyásakor erőforrás rendszergazdák adjon meg egy új kezdő és záró dátum-idő és a hozzárendelés típusa. 

### <a name="admin-renew"></a>Újítsa meg a rendszergazda

Erőforrás-rendszergazdák megújíthatják a tagok lapon, a bal oldali navigációs menü erőforrás, vagy lejárt szerepkör-hozzárendelések erőforrás szerepkör lejárt szerepkörök fülre.

A tagok képernyő lejárt szerepkörök összes lejárt szerepkör-hozzárendelések listájának megtekintéséhez jelölje ki.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>További lépések

[Jóváhagyás aktiválásához szükséges](pim-resource-roles-approval-workflow.md)

[A szerepkör aktiválása](pim-resource-roles-use-the-audit-log.md)


