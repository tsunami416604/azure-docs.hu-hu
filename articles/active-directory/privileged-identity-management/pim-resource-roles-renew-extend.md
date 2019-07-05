---
title: Meghosszabbítása vagy megújítása az Azure-erőforrás szerepkör-hozzárendeléseket a PIM - Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, hogyan bővítheti, vagy az Azure AD Privileged Identity Management (PIM) az Azure-erőforrás szerepkör-hozzárendelések megújításához.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 147b1714c88fd93a3098ecf7a28164a227af29de
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476305"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Meghosszabbítása vagy megújítása az Azure-erőforrás szerepkör-hozzárendelések az PIM-ben

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vezet be új vezérlőket az Azure-erőforrások hozzáférés és a hozzárendelés életciklusának kezelésére. A rendszergazdák a kezdő és záró dátum-idő tulajdonságokkal tagsági rendelhet hozzá. Amikor megközelíti a hozzárendelés vége, a PIM küld az érintett felhasználók vagy csoportok e-mail-értesítéseket. A rendszergazdák az erőforrás kell biztosítják, hogy megfelelő hozzáférési e-mail-értesítéseket is küld. Hozzárendelések meg lehet újítani, és látható, akár 30 napig lejárt állapotban maradnak, még akkor is, ha nincs kibővítve a hozzáférés.

## <a name="who-can-extend-and-renew"></a>Akik bővítheti és megújítását?

Csak a rendszergazdák az erőforrás kiterjeszthetik vagy szerepkör-hozzárendelések megújításához. Az érintett tag kérhetnek készül, hogy hamarosan lejár, és már lejárt szerepkörök megújítása kérelem szerepkört kiterjesztését.

## <a name="when-are-notifications-sent"></a>Ha vannak elküldött értesítések?

A PIM e-mail-értesítéseket küld rendszergazdái és szerepköröket, amelyek 14 napon belül, egy nappal a lejárat előtt lejár érintett tagjai. Hozzárendelés hivatalosan lejártakor egy további e-mailt küld. 

A rendszergazdák értesítéseket kaphat, amikor egy lejáró vagy lejárt a szerepkör meghosszabbítása vagy megújítása kérelmeket. Egy adott rendszergazda megoldja a kérést, ha a megoldás döntés (jóváhagyása és elutasítása) összes többi rendszergazda értesítést kap. Majd a kérést küldő tagot a döntés értesítést kap. 

## <a name="extend-role-assignments"></a>Szerepkör-hozzárendelések kiterjesztése

Az alábbi lépéseket vázoltuk kér, feloldása vagy -kiterjesztés vagy a szerepkör-hozzárendelés megújítására felügyel. 

### <a name="member-extend"></a>Tag kiterjesztése

Szerepkör-hozzárendelés tagjai lejáró közvetlenül a szerepkör-hozzárendelések kiterjesztheti a **jogosult** vagy **aktív** lapján a **saját szerepkörök** lap egy adott erőforrás- és legfelső szintjén **Saját szerepkörök** a PIM-portál. A tagok kérhetnek kiterjesztése elérhető és aktív (hozzárendelt) szerepkörök, amelyek 14 napon belül lejár.

![Azure-erőforrások – saját szerepkörök oldalon egy művelet oszlop listaelem jogosult szerepkörök](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Ha a hozzárendelés záró dátum és időpont van 14 napon belül, a gombra kattintva **kiterjesztése** válik, az aktív kapcsolat a felhasználói felületen. A következő példában az aktuális dátum későbbi, március 27 feltételezik.

![A művelet oszlop aktiválás vagy kiterjesztett hivatkozásokkal](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Hosszabbítsa meg a szerepkör-hozzárendelés, jelölje be **kiterjesztése** az űrlap megnyitásához.

![Szerepkör-hozzárendelési ablaktáblán egy OK be kiterjesztése](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Az eredeti hozzárendelés kapcsolatos információk megtekintéséhez bontsa ki a **hozzárendelés részletei**. Adja meg a bővítmény kérés okát, és válassza ki **kiterjesztése**.

>[!Note]
>Azt javasoljuk, hogy miért szükség a bővítményt, és mennyi ideig a bővítmény számára ajánlott hozzáférést adni (Ha rendelkezik ezekkel az információkkal) részleteit.

![Szerepkör-hozzárendelési ablaktáblán kiterjesztése a kibontott hozzárendelés részletei](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Csupán néhány másodpercet erőforrás-rendszergazdák kap egy e-mailben értesítést kér, akkor tekintse át a bővítmény kérelem. Ha egy kiterjesztésére vonatkozó kérelem már megtörtént, egy bejelentési értesítést, amely elmagyarázza a hibát az Azure portal tetején jelenik meg.

![Értesítés tájékoztatja, hogy nincs már egy meglévő függőben lévő szerepkör-hozzárendelés bővítmény](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Nyissa meg a **függőben lévő kérések** lapra a bal oldali panelen, a kérelem állapotának megtekintéséhez, vagy törölje azt.

![Azure-erőforrások – függőben lévő kérelmeket tartalmazó lapot minden függőben lévő kért és a Mégse gombra mutató hivatkozást listázása](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Rendszergazda jóváhagyhatja

Amikor egy tagot ad meg egy szerepkör-hozzárendelés kiterjesztésére vonatkozó kérelem, erőforrás-rendszergazda is megkapja az eredeti hozzárendelési és a kérés okát részleteit tartalmazó e-mail értesítés. Az értesítés a kérelmet jóváhagyja vagy elutasítja a rendszergazda egy közvetlen hivatkozást tartalmaz. 

A hivatkozásra az e-mailt a következő használatán rendszergazdák jóváhagyhatják vagy megtagadhatják a kérelmek a portál és a kiválasztja a PIM-Adminisztráció **kérések jóváhagyása** a bal oldali panelen.

![Azure-erőforrások – jóváhagyása kérelmek lapon listaelem kérelmek és hagyja jóvá vagy utasítsa mutató hivatkozások](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Ha a rendszergazda itt **jóváhagyás** vagy **Megtagadás**, a kérelem részletei jelennek meg, és a egy mezőt a naplókhoz a bejelentkezésétől indokolniuk.

![A kérelmezői OK, hozzárendelés-típus, szerepkör-hozzárendelési kérelem jóváhagyása, idő, befejezési időpontja és OK indítása](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ha egy szerepkör-hozzárendelés kiterjesztésére vonatkozó kérelem jóváhagyása, erőforrás-rendszergazdák kiválaszthatják, egy új kezdő dátum, a záró dátum és a hozzárendelés-típus. Hozzárendelés-típus módosítása szükséges, ha a rendszergazda szeretne korlátozottan hozzáférhetővé, hogy egy adott feladat (egy nap, a példában) lehet. Ebben a példában a rendszergazda módosíthatja a hozzárendelés a **jogosult** való **aktív**. Ez azt jelenti, de nem aktiválja a kérelmezőnek hozzáférést is nyújtanak.

### <a name="admin-extend"></a>Rendszergazdai kiterjesztés

Ha szerepkör tagja elfelejti, vagy nem tudja szerepkör tagsága hosszabbítsa, egy rendszergazda kiterjesztheti a tag nevében hozzárendelést. Felügyeleti szerepkör tagsága bővítmények nem igényel jóváhagyási, de értesítést kap minden más rendszergazdák után a szerepkör ki van terjesztve.

Egy szerepkör tagságát kiterjeszteni, keresse meg az erőforrás-szerepkör vagy a tag a PIM nézet. A bővítmény igénylő tag található. Válassza ki **kiterjesztése** művelet oszlopában.

![Azure-erőforrások – a tagok lapon listaelem jogosult szerepkörök kiterjesztése mutató hivatkozásokkal](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Szerepkör-hozzárendelések megújításához

A folyamat egy bővítmény kérelmezési tárolókéhoz hasonló, amíg a folyamat egy lejárt szerepkör-hozzárendelés megújítására eltér. Az alábbi lépéseket követve, tagok és rendszergazdák megújíthatják lejárt szerepkörök szükség esetén a hozzáférést.

### <a name="member-renew"></a>Tag megújítása

Akár 30 napon lejárt hozzárendelési előzményei tagjai, akik már nem tud hozzáférni az erőforrásokhoz férhet hozzá. Ehhez a megkeresését **saját szerepkörök** a bal oldali panelen, és válassza ki a a **lejárt szerepkörök** lap az Azure-erőforrás szerepkörök szakaszban.

![Saját szerepkörök lap – lejárt szerepkörök lap](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Az alapértelmezett érték látható szerepkörök listájának **jogosult szerepkörök**. A legördülő menü használatával jogosult és aktív hozzárendelt szerepkörök között.

A szerepkör-hozzárendeléseket a lista bármelyik kéri, válassza ki a **megújítása** művelet. Adja meg a kérés okát. Hasznos lehet, adja meg az időtartamot minden további környezetre, amely segít a erőforrás rendszergazda úgy dönt, hogy jóváhagyása vagy elutasítása mellett.

![Szerepkör hozzárendelése paneljéről, amelyen látható a OK box megújítása](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Miután elküldte a kérelmet, erőforrás-rendszergazdák a szerepkör-hozzárendelés megújítására vonatkozó, függő kérelem értesítést kap.

### <a name="admin-approves"></a>Rendszergazdai jóváhagyás

Erőforrás-rendszergazdák férhessenek hozzá a hivatkozásra az e-mail-értesítés vagy a PIM elérése az Azure Portalról, és válassza a tanúsítványmegújítási kérelem **kérések jóváhagyása** a bal oldali ablaktáblán.

![Azure-erőforrások – jóváhagyása kérelmek lapon listaelem kérelmek és hagyja jóvá vagy utasítsa mutató hivatkozások](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Ha a rendszergazda itt **jóváhagyás** vagy **Megtagadás**, a kérelem részleteit biztosít indoklásának a vizsgálati naplók és a egy mezőben látható.

![A kérelmezői OK, hozzárendelés-típus, szerepkör-hozzárendelési kérelem jóváhagyása, idő, befejezési időpontja és OK indítása](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Egy szerepkör-hozzárendelés megújítására vonatkozó kérelem jóváhagyásakor erőforrás rendszergazdák adjon meg egy új kezdő dátum, a záró dátum és a hozzárendelés-típus. 

### <a name="admin-renew"></a>Rendszergazdai megújítás

Erőforrás-rendszergazdák származó lejárt szerepkör-hozzárendelések megújíthatják a **tagok** lapra a bal oldali navigációs menü erőforrás. Azokat a lejárt szerepkör-hozzárendelések is megújíthatják a **lejárt** erőforrás szerepkör szerepkörök lapjáról.

Az összes megtekintése lejárt a szerepkör-hozzárendeléseket, a **tagok** képernyőn válassza ki **lejárt szerepkörök**.

![Azure-erőforrások – lejárt szerepkörök megújítása mutató hivatkozásokkal listázása a tagok lap](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>További lépések

- [Hagyja jóvá vagy utasítsa a PIM az Azure-erőforrások szerepköreihez tartozó kérelmek](pim-resource-roles-approval-workflow.md)
- [A PIM Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
