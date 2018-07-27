---
title: 'Az Azure Active Directory Domain Services: Felfüggesztve tartományok |} A Microsoft Docs'
description: Felügyelt tartomány felfüggesztése és törlése
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 93e93f3cfa72fff744ada8d5109ae30a619c84b0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264732"
---
# <a name="suspended-domains"></a>Felfüggesztett tartományok
Ha az Azure AD Domain Services nem tudja teljesíteni a felügyelt tartomány egy hosszú ideig, felfüggesztett állapotba helyezi a felügyelt tartományhoz. Ez a cikk azt ismerteti, miért felügyelt tartományok fel vannak függesztve, és a egy felfüggesztett tartomány javítása.


## <a name="states-your-managed-domain-can-be-in"></a>A felügyelt tartomány állapotok lehet

![Felfüggesztett tartomány ütemterv](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

A fenti ábra ismerteti a lehetséges állapotok az Azure AD tartományi szolgáltatásokkal felügyelt tartományban lehet.

### <a name="running-state"></a>"Fut" állapotban
A felügyelt tartomány, amely megfelelően konfigurált és működő rendszeresen a **futó** állapota.

**Mi történik**
* A Microsoft rendszeresen is a felügyelt tartomány állapotának monitorozásához.
* A felügyelt tartományhoz tartozó tartományvezérlőket javítani, és rendszeresen frissülnek.
* Az Azure Active Directoryból származó rendszeresen a felügyelt tartomány szinkronizálva.
* Rendszeres biztonsági mentést készít a felügyelt tartományok.


### <a name="needs-attention-state"></a>"Figyelmet igényel" állapota
A felügyelt tartomány a **figyelmet** állapotba, ha egy vagy több problémát kell-e egy rendszergazda intézkedését. A felügyelt tartomány állapotának lapján az ebben az állapotban lévő egy vagy több riasztást sorolja fel. 

Például ha konfigurálta a korlátozó NSG-t a virtuális hálózat, a Microsoft nem módosíthatja, és figyelheti a felügyelt tartományra. Ez a konfiguráció érvénytelen riasztást, amely a felügyelt tartományra a "Figyelmet" állapotba helyezi.

Minden riasztás megoldási lépések készletével rendelkezik. Egyes riasztásokban átmenetiek, és lekérése a szolgáltatás által automatikusan megoldva. Néhány egyéb riasztások a riasztás megfelelő megoldási lépései utasításokat követve oldhatja meg. Bizonyos kritikus riasztások kell beolvasni a megoldást a Microsoft ügyfélszolgálatától.

További információkért lásd: [egy felügyelt tartományon riasztások hibaelhárítása](active-directory-ds-troubleshoot-alerts.md).

**Mi történik**

Bizonyos esetekben (például, ha egy érvénytelen a hálózati konfigurációt) a felügyelt tartomány a tartományvezérlő nem érhető el lehet. Ha a felügyelt tartományra a "Figyelmet" állapotban van, a Microsoft nem garantálja, hogy, fog kell figyelni, javítva, frissített vagy készül rendszeres időközönként.

* A felügyelt tartomány nem megfelelő állapotban van, és folyamatos állapotfigyelés a riasztás kijavításáig leáll.
* A felügyelt tartományhoz tartozó tartományvezérlőket nem lehet javítani vagy frissítése.
* Az Azure Active Directoryból származó előfordulhat, hogy nem lesznek szinkronizálva a felügyelt tartományra.
* A felügyelt tartományhoz tartozó biztonsági másolatok kerülhet sor, ha lehetséges.
* Ha, oldja meg a riasztásokat, amelyek negatív hatással vannak a felügyelt tartományra, lehet, azt a "Fut" állapotba állíthatja vissza.
* Kritikus riasztások aktiválódnak a konfigurációs problémákat, ahol a Microsoft nem tudja elérni a tartományvezérlőket. Ha az ilyen riasztások nem szűnik meg, 15 napon belül, a felügyelt tartomány helyezni, a "Felfüggesztett" állapotban.


### <a name="the-suspended-state"></a>A "Felfüggesztve" állapota
Felügyelt tartomány helyezi a **felfüggesztett** állapot a következő okok miatt:

* Egy vagy több kritikus riasztások még nem lett feloldva 15 napon keresztül. Kritikus riasztások okozhatja egy hibás, hogy blokkolja az erőforrásokat, amelyek szükségesek az Azure AD tartományi szolgáltatásokhoz való hozzáférést.
    * Ha például a riasztás [AADDS104: hálózati hiba](active-directory-ds-troubleshoot-nsg.md) a felügyelt tartomány a több mint 15 napon keresztül feloldatlan.
* Egy számlázási probléma az Azure-előfizetésében, vagy az Azure-előfizetés lejárt.

Ha a Microsoft nem tudja kezelni, figyelése, javítása vagy biztonsági mentése a tartomány folyamatosan felügyelt tartományok fel vannak függesztve.

**Mi történik**
* A felügyelt tartományhoz tartozó tartományvezérlőket Szolgáltatáskulcs vannak, és nem érhető el a virtuális hálózaton belül.
* (Ha engedélyezve van) az interneten keresztül a felügyelt tartomány Secure LDAP-hozzáférését nem működik.
* Megfigyelheti, hogy a felügyelt tartományon való hitelesítéséhez, virtuális gépek a tartományhoz való bejelentkezés vagy LDAP/LDAPS keresztül kapcsolódó hibák.
* A felügyelt tartományhoz tartozó biztonsági másolatok már nem kerül.
* Az Azure AD szinkronizálása leáll.

A riasztás feloldásához jelenítse meg, miután a felügyelt tartomány "Felfüggesztve" állapota hiányzóra. Ezután szüksége, forduljon az ügyfélszolgálathoz.
Támogatási előfordulhat, hogy állítsa vissza a felügyelt tartományhoz, de csak akkor, ha egy biztonsági másolatot, amely 30 napon belül létezik.

A felügyelt tartományra csak marad a felfüggesztett állapotban 15 napon keresztül. A felügyelt tartomány szeretné használni, a Microsoft azt javasolja, kritikus riasztások azonnali megoldása.


### <a name="deleted-state"></a>"Törölt" állapot
A felügyelt tartomány, amely a "Felfüggesztett" állapotban marad, a 15 napon keresztül **törölt**.

**Mi történik**
* Minden erőforráshoz és a felügyelt tartományhoz tartozó biztonsági másolatokat a rendszer törli.
* Állítsa vissza a felügyelt tartomány lehet, és létre kell hoznia egy új felügyelt tartomány használatához Azure AD tartományi szolgáltatásokat.
* Törlés után nem használatáért a felügyelt tartományhoz.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hogyan tudta, hogy ha a felügyelt tartomány fel van függesztve?
Megjelenik egy [riasztás](active-directory-ds-troubleshoot-alerts.md) deklarálja, hogy a tartomány fel van függesztve, az Azure Portalon az Azure AD tartományi szolgáltatások állapotának oldalon. A tartomány állapota is látható "Felfüggesztett".


## <a name="restore-a-suspended-domain"></a>A felfüggesztett tartomány visszaállítása
Állítsa vissza egy tartományhoz, amely a "Felfüggesztett" állapotban van, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon.
2. Válassza ki a felügyelt tartományhoz.
3. A bal oldali panelen válassza ki a **egészségügyi**.
4. Válassza ki a riasztást. A riasztás azonosítója lesz AADDS503 vagy AADDS504, felfüggesztése okától függően.
5. Válassza ki a megadott feloldási hivatkozásra a riasztásban. Ezután kövesse a lépéseket a riasztás feloldásához.

A felügyelt tartományra csak állítható vissza a legutóbbi biztonsági mentését a dátumhoz. A dátum a legutóbbi biztonsági mentését a felügyelt tartomány állapotának lapján jelenik meg. Történt változásainak után a legutóbbi biztonsági mentés nem állítható vissza. A felügyelt tartományhoz tartozó biztonsági másolatok legfeljebb 30 napig tárolódnak. 30 napnál régebbi biztonsági másolatok törlődnek.


## <a name="next-steps"></a>További lépések
- [A felügyelt tartományhoz tartozó riasztások](active-directory-ds-troubleshoot-alerts.md)
- [További információ az Azure AD tartományi szolgáltatások](active-directory-ds-overview.md)
- [Lépjen kapcsolatba a termékcsoport](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
