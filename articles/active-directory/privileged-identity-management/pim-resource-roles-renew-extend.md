---
title: Azure erőforrás-szerepkörök hozzárendeléseinek kiterjesztése vagy megújítása a PIM-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan bővítheti vagy újíthatja meg az Azure-erőforrások szerepkör-hozzárendeléseit Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eeba148945f7aa52dd32edc0fec4c45e2ab0748
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804130"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Azure-erőforrás szerepkör-hozzárendeléseinek kiterjesztése vagy megújítása a PIM-ben

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) új vezérlőket vezet be az Azure-erőforrások hozzáférési és hozzárendelési életciklusának kezeléséhez. A rendszergazdák a kezdő és a befejező dátum-idő tulajdonságokkal is hozzárendelhetők a tagsághoz. A hozzárendelés befejezésének megközelítése esetén a PIM e-mail-értesítéseket küld az érintett felhasználóknak vagy csoportoknak. E-mail-értesítéseket is küld az erőforrás rendszergazdái számára a megfelelő hozzáférés fenntartásának biztosítása érdekében. Előfordulhat, hogy a hozzárendelések megújítva maradnak, és a lejárt állapotban maradhatnak, akár 30 napig, még akkor is, ha a hozzáférés nincs kiterjesztve.

## <a name="who-can-extend-and-renew"></a>Ki bővíthető és újítható meg?

Csak az erőforrás rendszergazdája bővítheti vagy újíthatja meg a szerepkör-hozzárendeléseket. Az érintett tag kérheti a hamarosan lejáró szerepkörök kibővítését, és a már lejárt szerepkörök megújítását kéri.

## <a name="when-are-notifications-sent"></a>Mikor kell elküldeni az értesítéseket?

A PIM e-mailes értesítéseket küld a rendszergazdáknak és a szerepkörök érintett tagjainak, amelyek 14 napon belül lejárnak, és egy nappal a lejárat előtt. Egy további e-mailt küld, ha egy hozzárendelés hivatalosan lejár. 

A rendszergazdák értesítést kapnak, ha egy vagy több lejáró vagy lejárt szerepkör-kérelem érkezik a kiterjesztéshez vagy megújításhoz. Ha egy adott rendszergazda feloldja a kérést, az összes többi rendszergazda értesítést kap a feloldási döntésről (jóváhagyott vagy megtagadott). Ezután a kérelmező tag értesítést kap a döntésről. 

## <a name="extend-role-assignments"></a>Szerepkör-hozzárendelések kiterjesztése

A következő lépések ismertetik a szerepkör-hozzárendelések kiterjesztésének vagy megújításának kérelmezési, megoldási vagy felügyeleti folyamatát. 

### <a name="member-extend"></a>Tagbővítés

A szerepkör-hozzárendelés tagjai az erőforrások **saját szerepkörök** lapján és a PIM-portál legfelső szintű **saját szerepkörök** lapján a **jogosult** vagy az **aktív** lapról is kiterjeszthetik a szerepkör-hozzárendelések lejáratát. A tagok kérhetik a következő 14 napban lejáró jogosult és aktív (hozzárendelt) szerepkörök kiterjesztését.

![Azure-erőforrások – saját szerepkörök lap, amely egy műveleti oszloppal rendelkező jogosult szerepkörök listáját tartalmazza](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Ha a hozzárendelés befejezési dátuma és időpontja 14 napon belül van, a **kibővíteni** kívánt gomb aktív hivatkozás lesz a felhasználói felületen. A következő példában feltételezzük, hogy az aktuális dátum március 27.

![Művelet oszlop az aktiválásra vagy a kiterjesztésre mutató hivatkozásokkal](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

A szerepkör-hozzárendelés kiterjesztésének igényléséhez válassza a **kiterjesztés** lehetőséget a kérelem űrlapjának megnyitásához.

![Szerepkör-hozzárendelési ablaktábla kiterjesztése egy ok mezővel](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Az eredeti hozzárendeléssel kapcsolatos információk megtekintéséhez bontsa ki a **hozzárendelés részletei**elemet. Adja meg a bővítmény kérelmének okát, majd válassza a **kiterjesztés**lehetőséget.

>[!Note]
>Azt javasoljuk, hogy a bővítmény miért szükséges, és hogy mennyi ideig kell megadni a bővítményt (ha rendelkezik ezekkel az információkkal).

![Szerepkör-hozzárendelési ablaktábla kiterjesztése a hozzárendelés részleteivel kibontva](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Az erőforrás-rendszergazdák pillanatok alatt megkapnak egy e-mailes értesítést, amely azt kéri, hogy tekintsék át a bővítményre vonatkozó kérést. Ha egy kiterjesztésre irányuló kérelmet már elküldtek, a Azure Portal elején egy bejelentési értesítés jelenik meg, amely ismerteti a hibát.

![Értesítés arról, hogy már van függőben lévő szerepkör-hozzárendelési bővítmény](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

A bal oldali ablaktábla **függőben lévő kérelmek** lapján megtekintheti a kérelem állapotát, vagy megszakíthatja azt.

![Azure-erőforrások – függőben lévő kérelmek oldal, amely felsorolja a kért függőben lévő kéréseket és a megszakított hivatkozást](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Rendszergazda jóváhagyása

Ha egy tag egy szerepkör-hozzárendelés kiterjesztésére vonatkozó kérelmet küld, az erőforrás-rendszergazdák egy e-mail-értesítést kapnak, amely tartalmazza az eredeti hozzárendelés részleteit és a kérelem okát. Az értesítés közvetlen hivatkozást tartalmaz a rendszergazda jóváhagyására vagy elutasítására vonatkozó kérésre. 

Amellett, hogy az e-mail-hivatkozás követése mellett a rendszergazdák jóváhagyják vagy megtagadják a kéréseket, a PIM felügyeleti portálon, majd a bal oldali ablaktáblán a **kérések jóváhagyása** lehetőség választásával.

![Azure-erőforrások – a kérelmek jóváhagyása lap felsorolja a kérelmeket és a jóváhagyásra vagy elutasításra mutató hivatkozásokat](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Ha egy rendszergazda kiválasztja a **jóváhagyás** vagy a **Megtagadás**lehetőséget, a rendszer megjeleníti a kérés részleteit, valamint egy mezőt, amely a naplók indoklását adja meg.

![Szerepkör-hozzárendelési kérelem jóváhagyása a kérelmező okának, a hozzárendelés típusa, a kezdési idő, a Befejezés időpontja és az ok alapján](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

A szerepkör-hozzárendelés kiterjesztésére vonatkozó kérelem jóváhagyásakor az erőforrás-rendszergazdák választhatnak új kezdő dátumot, záró dátumot és hozzárendelési típust. Szükség lehet a hozzárendelés típusának módosítására, ha a rendszergazda korlátozott hozzáférést szeretne biztosítani egy adott feladat végrehajtásához (például egy nap). Ebben a példában a rendszergazda módosíthatja a hozzárendelést a **jogosultról** **aktívra**. Ez azt jelenti, hogy hozzáférést biztosíthatnak a kérelmezőnek anélkül, hogy aktiválni kellene őket.

### <a name="admin-extend"></a>Rendszergazdai kiterjesztés

Ha egy szerepkör tagja elfelejti vagy nem tud szerepkör-tagsági bővítményt kérni, a rendszergazda kiterjesztheti a tag nevében a hozzárendelést. A szerepkör-tagság felügyeleti bővítményei nem igényelnek jóváhagyást, de a szerepkör kiterjesztése után az összes többi rendszergazdának értesítést küld a rendszer.

A szerepkör tagságának kibővítéséhez keresse meg a PIM erőforrás-szerepkör vagy tag nézetét. Keresse meg azt a tagot, amelyhez bővítmény szükséges. Ezután válassza a **kiterjesztés** elemet a művelet oszlopban.

![Azure-erőforrások – tagok lap a kiterjeszthető hivatkozásokkal rendelkező jogosult szerepkörök listázása](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Szerepkör-hozzárendelések megújítása

Habár a bővítmény igénylésének folyamata elméletileg hasonló, a lejárt szerepkör-hozzárendelés megújításának folyamata eltérő. A következő lépések segítségével a tagok és a rendszergazdák szükség esetén megújítják a lejárt szerepkörökhöz való hozzáférést.

### <a name="member-renew"></a>Tag megújítása

Azok a tagok, akik már nem férnek hozzá az erőforrásokhoz, akár 30 napig is hozzáférhetnek a lejárt hozzárendelési előzményekhez. Ehhez keresse meg a **saját szerepköröket** a bal oldali ablaktáblán, majd válassza ki a **lejárt szerepkörök** lapot az Azure Resource roles szakaszban.

![Szerepkörök lap – lejárt szerepkörök lap](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A szerepkörök listája a **jogosult szerepkörök**alapértelmezett értékeit jeleníti meg. Használja a legördülő menüt a jogosult és az aktív hozzárendelt szerepkörök közötti váltáshoz.

A listában szereplő bármelyik szerepkör-hozzárendelés megújításának kéréséhez válassza ki a **megújítási** műveletet. Ezután adja meg a kérelem okát. Hasznos lehet időtartamot biztosítani az erőforrás-rendszergazda által a jóváhagyáshoz vagy elutasításhoz szükséges további környezeten kívül.

![A szerepkör-hozzárendelés ablaktábla megújítása az OK mezőben](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

A kérés elküldése után az erőforrás-rendszergazdák értesítést kapnak egy függőben lévő kérelemről a szerepkör-hozzárendelés megújításához.

### <a name="admin-approves"></a>A rendszergazda jóváhagyja

Az erőforrás-rendszergazdák hozzáférhetnek a megújítási kérelemhez az e-mail-értesítésben szereplő hivatkozáson keresztül, vagy a Azure Portal és a bal oldali ablaktáblán a **kérések jóváhagyása** lehetőség kiválasztásával.

![Azure-erőforrások – a kérelmek jóváhagyása lap felsorolja a kérelmeket és a jóváhagyásra vagy elutasításra mutató hivatkozásokat](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Ha egy rendszergazda kiválasztja a **jóváhagyás** vagy a **Megtagadás**lehetőséget, a rendszer a kérés részleteit jeleníti meg egy mezővel együtt, amely indoklást biztosít a naplók számára.

![Szerepkör-hozzárendelési kérelem jóváhagyása a kérelmező okának, a hozzárendelés típusa, a kezdési idő, a Befejezés időpontja és az ok alapján](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

A szerepkör-hozzárendelés megújítására vonatkozó kérelem jóváhagyásakor az erőforrás-rendszergazdáknak új kezdési dátumot, záró dátumot és hozzárendelési típust kell megadniuk. 

### <a name="admin-renew"></a>Rendszergazdai megújítás

Az erőforrás-rendszergazdák egy erőforrás bal oldali navigációs menüjének **tagok** lapján megújítják a lejárt szerepkör-hozzárendeléseket. Egy erőforrás-szerepkör **lejárt** szerepkörök lapján is megújítják a lejárt szerepkör-hozzárendeléseket.

Az összes lejárt szerepkör-hozzárendelés listájának megtekintéséhez a **tagok** képernyőn válassza a **lejárt szerepkörök**elemet.

![Azure-erőforrások – a tagok lap a megújításra mutató hivatkozásokkal rendelkező lejárt szerepkörök listázása](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás-szerepkörökre vonatkozó kérelmek jóváhagyása vagy elutasítása a PIM-ben](pim-resource-roles-approval-workflow.md)
- [Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben](pim-resource-roles-configure-role-settings.md)
