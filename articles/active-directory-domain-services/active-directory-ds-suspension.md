---
title: 'Az Azure Active Directory tartományi szolgáltatások: Felfüggesztve tartományok |} A Microsoft Docs'
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
ms.openlocfilehash: 99896b0f618151cd368a21c4a212b7d8e28b7a6f
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126093"
---
# <a name="suspended-domains"></a>Felfüggesztett tartományok
Ha az Azure AD Domain Services nem tudja teljesíteni a felügyelt tartomány egy hosszú ideig, felfüggesztett állapotba helyezni, a felügyelt tartományhoz. Ez a cikk azt ismerteti, miért felügyelt tartományok fel vannak függesztve, és a egy felfüggesztett tartomány javítása.


## <a name="states-your-managed-domain-can-be-in"></a>A felügyelt tartomány állapotok lehet

![Felfüggesztett tartomány ütemterv](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

A fenti ábra ismerteti a lehetséges állapotok az Azure AD tartományi szolgáltatásokkal felügyelt tartományban lehet.

### <a name="running-state"></a>"Fut" állapotú
A felügyelt tartomány, amely megfelelően konfigurált és működő rendszeresen a **futó** állapota.

**Mi várható:**
* A Microsoft rendszeresen is a felügyelt tartomány állapotának monitorozásához.
* A felügyelt tartományhoz tartozó tartományvezérlőket javítani, és rendszeresen frissülnek.
* Az Azure Active Directoryból származó rendszeresen a felügyelt tartomány szinkronizálva.
* Rendszeres biztonsági mentést készít a felügyelt tartományok.


### <a name="needs-attention-state"></a>"Figyelmet igényel" állapota
A felügyelt tartomány a **figyelmet** állapotba, ha egy vagy több probléma van szükség egy rendszergazda intézkedését. A felügyelt tartomány állapotának lapján megjelennek ebben az állapotban lévő egy vagy több riasztást. Például ha konfigurálta a korlátozó NSG-t a virtuális hálózat, a Microsoft lehet nem lehet frissíteni, és figyelheti a felügyelt tartományra. Ez a konfiguráció érvénytelen keletkező riasztás eredményez, és a felügyelt tartományra a "Figyelmet" állapotba helyezi.

Minden riasztás megoldási lépések készletével rendelkezik. Egyes riasztásokban átmenetiek, és lekérése automatikusan feloldásra a szolgáltatás által. Néhány egyéb riasztások a riasztás megfelelő megoldási lépései utasításokat követve oldhatja meg. Bizonyos kritikus riasztások megszüntetéséhez szüksége, forduljon a Microsoft ügyfélszolgálatához.

További információkért lásd: [egy felügyelt tartományon riasztások hibaelhárítása](active-directory-ds-troubleshoot-alerts.md).

**Mi várható:**

Bizonyos esetekben (például, ha egy érvénytelen a hálózati konfigurációt) a felügyelt tartomány a tartományvezérlő nem érhető el. A Microsoft nem garantálja a felügyelt tartomány van figyelni, javítva, frissített vagy készíteni rendszeresen ebben az állapotban.

* A felügyelt tartomány nem megfelelő állapotban van, és folyamatos állapotfigyelés előfordulhat, hogy leállítja, amíg a riasztás fel lett oldva.
* A felügyelt tartományhoz tartozó tartományvezérlőket nem lehet javítani, vagy frissítése.
* Az Azure Active Directoryból módosítások nem lesznek szinkronizálva a felügyelt tartományra.
* A felügyelt tartományhoz tartozó biztonsági másolatok lehet tenni, ha lehetséges.
* A felügyelt tartomány érintő riasztások megoldása, ha lehetséges, a "Fut" állapotba állíthatja vissza a felügyelt tartomány lehet.
* Kritikus riasztások aktiválódnak a konfigurációs problémákat, ahol a Microsoft nem tudja elérni a tartományvezérlőket. Ha az ilyen riasztások nem szűnik meg, 15 napon belül, a felügyelt tartomány "Felfüggesztett" állapotban fog kerülni.


### <a name="suspended-state"></a>"Felfüggesztett" állapotban
Felügyelt tartomány helyezi a **felfüggesztett** állapot a következő okok miatt:
* Egy vagy több kritikus riasztások még nem lett feloldva 15 napon keresztül. Kritikus riasztások okozhatja egy hibás, hogy blokkolja az Azure AD tartományi szolgáltatások által igényelt erőforrások való hozzáférést.
    * Például, ha a felügyelt tartomány rendelkezik riasztás [AADDS104: hálózati hiba](active-directory-ds-troubleshoot-nsg.md) több mint 15 napja feloldatlan.
* Egy számlázási probléma az Azure-előfizetésében, vagy az Azure-előfizetés lejárt.

A felügyelt tartományok fel vannak függesztve, amikor a Microsoft nem tudja kezelni, figyelése, javítása vagy folyamatosan a tartomány biztonsági mentési.

**Mi várható:**
* A felügyelt tartományhoz tartozó tartományvezérlőket Szolgáltatáskulcs vannak, és nem érhető el a virtuális hálózaton belül.
* (Ha engedélyezve van) az interneten keresztül a felügyelt tartomány Secure LDAP-hozzáférését nem működik.
* Megfigyelheti, hogy a felügyelt tartományon való hitelesítéséhez, tartományhoz csatlakoztatott virtuális gépek naplózása és LDAP/LDAPS keresztül kapcsolódó hibák.
* A felügyelt tartományhoz tartozó biztonsági másolatok már nem kerül.
* Az Azure AD szinkronizálása leáll.
* Oldja meg a riasztást okozó a felügyelt tartomány "Felfüggesztett" állapotban legyen, és forduljon az ügyfélszolgálathoz.
* Támogatási előfordulhat, hogy a felügyelt tartomány visszaállítani, csak akkor, ha létezik egy biztonsági másolatot, amely 30 napon belül.

A felügyelt tartományra csak marad a felfüggesztett állapotban 15 napon keresztül. A felügyelt tartomány szeretné használni, a Microsoft azt javasolja, kritikus riasztások azonnal megoldani.


### <a name="deleted-state"></a>"Törölt" állapot
A felügyelt tartomány, amely a "Felfüggesztett" állapotban marad, a 15 napon keresztül **törölt**.

**Mi várható:**
* Minden erőforráshoz és a felügyelt tartományhoz tartozó biztonsági másolatokat a rendszer törli.
* Állítsa vissza a felügyelt tartomány lehet, és létre kell hoznia egy új felügyelt tartomány használatához Azure AD tartományi szolgáltatásokat.
* Ha törli, Ön a felügyelt tartományhoz tartozó nem számlázzuk.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Hogyan tudta, hogy ha a felügyelt tartomány fel van függesztve?
Megjelenik egy [riasztás](active-directory-ds-troubleshoot-alerts.md) a az Azure AD tartományi szolgáltatások Health-oldal az Azure Portalon, amely a tartomány felfüggesztve deklarálja. A tartomány állapota is látható "Felfüggesztett".


## <a name="restore-a-suspended-domain"></a>A felfüggesztett tartomány visszaállítása
A "Felfüggesztett" állapotban lévő tartomány visszaállításához kövesse az alábbi lépéseket:

1. Keresse meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon
2. Kattintson a felügyelt tartományon.
3. A bal oldali navigációs sávján kattintson **egészségügyi**.
4. Kattintson a riasztásra. A riasztás azonosítója lesz AADDS503 vagy AADDS504, felfüggesztése okától függően.
5. Kattintson a a feloldási hivatkozásra a riasztásban, és kövesse a lépéseket a riasztás feloldása.

A felügyelt tartományra napjától a legutóbbi biztonsági mentés csak állítható vissza. A dátum a legutóbbi biztonsági mentését a felügyelt tartomány állapotának lapján jelenik meg. Történt változásainak után a legutóbbi biztonsági mentés nem állítható vissza. A felügyelt tartományhoz tartozó biztonsági másolatok legfeljebb 30 napig tárolódnak. 30 napnál régebbi biztonsági másolatok törlődnek.


## <a name="next-steps"></a>További lépések
- [Riasztások a felügyelt tartományon](active-directory-ds-troubleshoot-alerts.md)
- [További információ az Azure AD tartományi szolgáltatások](active-directory-ds-overview.md)
- [Lépjen kapcsolatba a termékcsoport](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
