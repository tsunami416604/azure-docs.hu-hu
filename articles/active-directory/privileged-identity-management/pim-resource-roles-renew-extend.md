---
title: Az Azure-erőforrás-szerepkör-hozzárendelések megújítása a PIM-ben – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan bővítheti vagy újíthatja meg az Azure-erőforrás-szerepkör-hozzárendeléseket az Azure AD kiemelt identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022916"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Azure-erőforrásszerepkör-hozzárendelések bővítése vagy megújítása a kiemelt identitáskezelésben

Az Azure Active Directory (Azure AD) privilegizált identitáskezelése (PIM) vezérlőket biztosít az Azure-erőforrások hozzáférési és hozzárendelési életciklusának kezeléséhez. A rendszergazdák a kezdő és a záró dátum-idő tulajdonságok használatával rendelhetnek szerepköröket. Amikor a hozzárendelés vége közeledik, a Kiemelt identitáskezelés e-mail értesítéseket küld az érintett felhasználóknak vagy csoportoknak. E-mailes értesítéseket is küld az erőforrás rendszergazdáinak a megfelelő hozzáférés fenntartásának biztosítása érdekében. A hozzárendelések megújíthatók, és lejárt állapotban legfeljebb 30 napig láthatók maradnak, még akkor is, ha a hozzáférés nem hosszabbodik meg.

## <a name="who-can-extend-and-renew"></a>Ki hosszabbíthatja meg és újíthatja meg?

Csak az erőforrás rendszergazdái terjeszthetik ki vagy újíthatják meg a szerepkör-hozzárendeléseket. Az érintett felhasználó vagy csoport kérheti a hamarosan lejáró szerepkörök kiterjesztését, és kérheti a már lejárt szerepkörök megújítását.

## <a name="when-are-notifications-sent"></a>Mikor lesz nekik az értesítések?

A kiemelt identitáskezelés e-mailértesítéseket küld a rendszergazdáknak és az érintett felhasználóknak vagy szerepkörcsoportoknak, amelyek 14 napon belül és egy nappal a lejárat előtt lejárnak. További e-mailt küld, ha egy megbízás hivatalosan lejár.

A rendszergazdák értesítést kapnak, ha egy felhasználó vagy csoport lejáró vagy lejárt szerepkör-kérelmeket rendel a kiterjesztésre vagy a megújításra. Amikor egy adott rendszergazda megoldja a kérést, az összes többi rendszergazdát értesíti a (jóváhagyott vagy elutasított) megoldásról. Ezután a kérelmező felhasználó vagy csoport értesítést kap a döntésről.

## <a name="extend-role-assignments"></a>Szerepkör-hozzárendelések bővítése

A következő lépések ismertetik a szerepkör-hozzárendelés kiterjesztésének vagy megújításának kérelmezésének, feloldásának vagy felügyeletének folyamatát.

### <a name="self-extend-expiring-assignments"></a>Önkiterjesztő lejáró hozzárendelések

A szerepkörhöz rendelt felhasználók vagy csoportok kiterjeszthetik a lejáró szerepkör-hozzárendeléseket közvetlenül az erőforrás **Saját szerepkörök** lapjának **Jogosult** vagy **Aktív** lapjáról, valamint a Kiemelt identitáskezelési portál **"Szerepkörök** kezelése" felső szintjéről. A felhasználók vagy csoportok kérhetik a következő 14 napban lejáró jogosult és aktív (hozzárendelt) szerepkörök kiterjesztését.

![Azure-erőforrások – A szerepkörök lapja a jogosult szerepköröket listázta egy Művelet oszlopban](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Ha a hozzárendelés befejezési dátumideje 14 napon belül van, a **Kiterjesztés** gomb aktív hivatkozássá válik a felhasználói felületen. A következő példában tegyük fel, hogy az aktuális dátum március 27.

![Műveletoszlop az Aktiválás vagy a Kiterjesztés hivatkozással](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

A szerepkör-hozzárendelés meghosszabbításának kérelmezéséhez válassza a **Kiterjesztés** lehetőséget a kéreleműrlap megnyitásához.

![Szerepkör-hozzárendelés ablaktábla kiterjesztése okmezővel](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Az eredeti hozzárendelésadatainak megtekintéséhez bontsa ki a **Hozzárendelés részletei csomópontot.** Adja meg a bővítménykérés okát, majd kattintson a **Kiterjesztés gombra.**

>[!NOTE]
>Javasoljuk, hogy a részleteket, hogy miért a kiterjesztés szükséges, és mennyi ideig kell adni a kiterjesztést (ha ezt az információt).

![Szerepkör-hozzárendelés ablaktábla kiterjesztése a hozzárendelés részleteivel kibontva](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Pillanatok on-mailben az erőforrás-rendszergazdák e-mailben értesítést kapnak, amelyben kérik, hogy tekintse át a bővítménykérelmet. Ha a kiterjesztési kérelem már el lett küldve, egy Azure-értesítés jelenik meg a portálon.

![Értesítés arról, hogy már létezik függőben lévő szerepkör-hozzárendelési bővítmény](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Lépjen a **Függőben lévő kérelmek** lapra a kérés állapotának megtekintéséhez vagy visszavonásához.

![Azure-erőforrások – Függőben lévő kérelmek lap, amely felsorolja a függőben lévő kért eket és a Mégse hivatkozást](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Rendszergazda által jóváhagyott bővítmény

Amikor egy felhasználó vagy csoport egy szerepkör-hozzárendelés meghosszabbítására irányuló kérelmet küld, az erőforrás-rendszergazdák e-mailben értesítést kapnak, amely tartalmazza az eredeti hozzárendelés részleteit és a kérelem okát. Az értesítés közvetlen hivatkozást tartalmaz a rendszergazda jóváhagyására vagy elutasítására irányuló kérésre.

Az e-mailből származó hivatkozás követésén kívül a rendszergazdák jóváhagyhatják vagy megtagadhatják a kérelmeket a Kiemelt identitáskezelési felügyeleti portálon, és a bal oldali ablaktáblában a **Kérések jóváhagyása** lehetőséget választva.

![Azure-erőforrások – Kérelmek listaelemezési kérelmeinek és jóváhagyására vagy elutasítására vonatkozó hivatkozások jóváhagyása vagy megtagadása](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Ha a rendszergazda a **Jóváhagyás** vagy **a Megtagadás**lehetőséget választja, a rendszer a kérelem részleteit, valamint a naplónaplók üzleti indoklását szolgáló mezőt mutatja be.

![Szerepkör-hozzárendelési kérelem jóváhagyása a kérelmező okával, hozzárendeléstípussal, kezdési idővel, befejezési idővel és okokkal](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

A szerepkör-hozzárendelés kiterjesztésére irányuló kérelem jóváhagyásakor az erőforrás-rendszergazdák új kezdési dátumot, befejezési dátumot és hozzárendeléstípust választhatnak. A hozzárendelés típusának módosítására akkor lehet szükség, ha a rendszergazda korlátozott hozzáférést szeretne biztosítani egy adott feladat elvégzéséhez (például egy nap). Ebben a példában a rendszergazda módosíthatja a hozzárendelést **Jogosultról** **Aktívra.** Ez azt jelenti, hogy anélkül biztosíthatnak hozzáférést a kérelmezőhöz, hogy aktiválniuk kellene őket.

### <a name="admin-initiated-extension"></a>Rendszergazda által kezdeményezett bővítmény

Ha egy szerepkörhöz rendelt felhasználó nem kér bővítményt a szerepkör-hozzárendeléshez, a rendszergazda meghosszabbíthatja a hozzárendelést a felhasználó nevében. A szerepkör-hozzárendelés felügyeleti bővítményei nem igényelnek jóváhagyást, de a szerepkör bővítése után az összes többi rendszergazdának értesítést küldenek.

Szerepkör-hozzárendelés bővítéséhez keresse meg az erőforrás-szerepkör vagy hozzárendelés nézetét a Kiemelt identitáskezelés ben. Keresse meg a kiterjesztést igénylő hozzárendelést. Ezután válassza a **Műveletoszlop Kiterjesztés** lehetőséget.

![Azure-erőforrások – a jogosult szerepköröket tartalmazó lap hozzárendelései a kiterjesztendő hivatkozásokkal](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Szerepkör-hozzárendelések megújítása

Bár fogalmilag hasonló a bővítmény igénylésének folyamatához, a lejárt szerepkör-hozzárendelés megújításának folyamata eltérő. A következő lépések segítségével a hozzárendelések és a rendszergazdák szükség esetén megújíthatják a lejárt szerepkörökhöz való hozzáférést.

### <a name="self-renew"></a>Önmegújítás

Azok a felhasználók, akik már nem férnek hozzá az erőforrásokhoz, legfeljebb 30 nap lejárt hozzárendelési előzményeket érhetnek el. Ehhez keresse meg a **Saját szerepkörök** a bal oldali ablaktáblában, és válassza a **Lejárt szerepkörök** lapot az Azure-erőforrás-szerepkörök szakaszban.

![Saját szerepkörök lap – Lejárt szerepkörök lap](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A szerepkörök listája alapértelmezés szerint **a Jogosult szerepkörök elemre mutat.** A legördülő menüsegítségével válthat a Jogosult és az Aktív hozzárendelt szerepkörök között.

Ha a listában szereplő szerepkör-hozzárendelések bármelyikének megújítását szeretné kérni, válassza a **Megújítás** műveletet. Ezután adja meg a kérés okát. Hasznos lehet egy időtartamot megadni a további környezeten vagy üzleti indokláson kívül, amely segíthet az erőforrás-rendszergazdának úgy dönteni, hogy jóváhagyja vagy elutasítja.

![Szerepkör-hozzárendelés megújítása ablaktábla az Ok mezővel](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

A kérelem elküldése után az erőforrás-rendszergazdák értesítést kapnak a szerepkör-hozzárendelés megújítására irányuló függőben lévő kérésről.

### <a name="admin-approves"></a>Rendszergazda jóváhagyja

Az erőforrás-rendszergazdák hozzáférhetnek a megújítási kérelemhez az e-mail értesítésben található hivatkozásról, vagy az Azure Portalon a Kiemelt identitáskezelés elérésével, és a **kérelmek jóváhagyása** a bal oldali ablaktáblából kiválasztásával.

![Azure-erőforrások – Kérelmek listaelemezési kérelmeinek és jóváhagyására vagy elutasítására vonatkozó hivatkozások jóváhagyása vagy megtagadása](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Ha a rendszergazda a **Jóváhagyás** vagy a Megtagadás lehetőséget **választja,** a kérelem részletei egy mezővel együtt jelennek meg, amely a naplónaplók üzleti indoklását biztosítja.

![Szerepkör-hozzárendelési kérelem jóváhagyása a kérelmező okával, hozzárendeléstípussal, kezdési idővel, befejezési idővel és okokkal](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

A szerepkör-hozzárendelés megújítására irányuló kérelem jóváhagyásakor az erőforrás-rendszergazdáknak új kezdési dátumot, befejezési dátumot és hozzárendeléstípust kell megadniuk.

### <a name="admin-renew"></a>Rendszergazdamegújítása

Az erőforrás-rendszergazdák megújíthatják a lejárt szerepkör-hozzárendeléseket az erőforrás bal oldali navigációs menüjének **Tagok** lapjáról. Megújíthatják a lejárt szerepkör-hozzárendeléseket is egy erőforrásszerepkör **Lejárt** szerepköre lapjáról.

Az összes lejárt szerepkör-hozzárendelés listájának megtekintéséhez a **Tagok** képernyőn válassza a **Lejárt szerepkörök**lehetőséget.

![Azure-források – A tagok lapban lejárt szerepkörök et sorolnak fel a megújításra mutató hivatkozásokkal](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>További lépések

- [Azure-erőforrás-szerepkörök kéréseinek jóváhagyása vagy megtagadása a kiemelt identitáskezelésben](pim-resource-roles-approval-workflow.md)
- [Az Azure-erőforrásszerepkör-beállítások konfigurálása a Kiemelt identitáskezelés ben](pim-resource-roles-configure-role-settings.md)
