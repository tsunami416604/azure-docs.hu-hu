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
ms.date: 07/16/2018
ms.author: ergreenl
ms.openlocfilehash: 5f350bacdc21ec4e5077c93ecc9d97f2fe6c39a5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090909"
---
# <a name="suspended-domains"></a>Felfüggesztett tartományok
Ha az Azure AD Domain Services nem tudja teljesíteni a felügyelt tartomány egy hosszú ideig, felfüggesztett állapotba helyezni, a felügyelt tartományhoz. Ez a cikk elmagyarázza miért felügyelt tartományok fel vannak függesztve, a felfüggesztés hossza és a felfüggesztett tartomány javítása.


## <a name="states-your-managed-domain-can-be-in"></a>A felügyelt tartomány állapotok lehet

![Felfüggesztett tartomány ütemterv](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

A fenti ábra bemutatja, hogyan tartomány fel van függesztve, mennyi ideig lesz felfüggesztve, és végső soron a felügyelt tartomány törlése. Az alábbi szakaszok az okokat, miért lehet felfüggeszteni a tartományhoz, és hogyan újraaktiválására egy felügyelt tartományon.

### <a name="running-state"></a>"Fut" állapotú
A felügyelt tartomány, amely megfelelően konfigurált és működő rendszeresen a **futó** állapota.

**Mi várható:**
* A Microsoft is képes rendszeresen a felügyelt tartomány állapotának monitorozásához.
* A felügyelt tartományhoz tartozó tartományvezérlőket javítani, és rendszeresen frissülnek.
* Az Azure Active Directoryból származó rendszeresen a felügyelt tartomány szinkronizálva.
* Rendszeres biztonsági mentést készít a felügyelt tartományok.


### <a name="needs-attention-state"></a>"Figyelmet igényel" állapota
A felügyelt tartomány a **figyelmet** állapotba, ha egy vagy több probléma van szükség egy rendszergazda intézkedését. A felügyelt tartomány állapotának lapján megjelennek ebben az állapotban lévő egy vagy több riasztást. Például ha konfigurálta a korlátozó NSG-t a virtuális hálózat, a Microsoft lehet nem lehet frissíteni, és figyelheti a felügyelt tartományra. Ez a konfiguráció érvénytelen keletkező riasztás eredményez, és a felügyelt tartományra a "Figyelmet" állapotba helyezi.

Minden riasztás megoldási lépések készletével rendelkezik. Egyes riasztásokban átmenetiek, és lekérése automatikusan feloldásra a szolgáltatás által. Néhány egyéb riasztások a riasztás megfelelő megoldási lépései utasításokat követve oldhatja meg. Bizonyos kritikus riasztások megszüntetéséhez szüksége, forduljon a Microsoft ügyfélszolgálatához.

További információkért lásd: [egy felügyelt tartományon riasztások hibaelhárítása](active-directory-ds-troubleshoot-alerts.md).

**Mi várható:**

Bizonyos esetekben (például, ha egy érvénytelen a hálózati konfigurációt) a felügyelt tartomány a tartományvezérlő nem érhető el. Ezért a Microsoft nem garantálja a felügyelt tartomány figyelt, javítva, frissíteni vagy készíteni rendszeresen ebben az állapotban.

* A felügyelt tartomány nem megfelelő állapotban van, és folyamatos állapot lehet a megfigyelésre, amíg a riasztás fel lett oldva.
* A felügyelt tartományhoz tartozó tartományvezérlőket nem lehet javítani, vagy frissítése.
* Az Azure Active Directoryból módosítások nem lesznek szinkronizálva a felügyelt tartományra.
* A felügyelt tartományhoz tartozó biztonsági másolatok lehet tenni, ha lehetséges.
* A felügyelt tartomány érintő riasztások megoldása, ha lehetséges, a "Fut" állapotba állíthatja vissza a felügyelt tartomány lehet.
* Kritikus riasztások aktiválódnak a konfigurációs problémákat, ahol a Microsoft nem tudja elérni a tartományvezérlőket. Ha az ilyen riasztások nem oldódnak 15 napon belül, a felügyelt tartomány "Felfüggesztett" állapotban fog kerülni.


### <a name="suspended-state"></a>"Felfüggesztett" állapotban
Felügyelt tartomány helyezi a **felfüggesztett** állapot a következő okok miatt:
* Egy vagy több kritikus riasztások 15 nap során nem szervizelt. Ezt okozhatja egy hibás, hogy blokkolja az Azure AD tartományi szolgáltatások által igényelt erőforrások való hozzáférést.
    * Például, ha a felügyelt tartomány rendelkezik riasztás [AADDS104: hálózati hiba](active-directory-ds-troubleshoot-nsg.md) több mint 15 napja feloldatlan.
* Az Azure-előfizetéshez egy számlázási probléma, vagy ha az Azure-előfizetés lejárt.

A felügyelt tartományok fel vannak függesztve, amikor a Microsoft nem tudja kezelni, figyelése, vagy a biztonsági másolatot készíteni a tartományt a folyamatos.

**Mi várható:**
* A felügyelt tartományhoz tartozó tartományvezérlőket Szolgáltatáskulcs vannak, és nem érhetők el a virtuális hálózaton belül.
* (Ha engedélyezve van) az interneten keresztül a felügyelt tartomány Secure LDAP-hozzáférését nem működik.
* Látni fogja a felügyelt tartományba, a naplózás tartományhoz való hitelesítése sikertelen csatlakoztatott virtuális gépeket, csatlakozás keresztül LDAP/LDAPS stb.
* A felügyelt tartományhoz tartozó biztonsági másolatok már nem kerül.
* El kell hárítania a riasztást okozó a felügyelt tartomány "Felfüggesztett" állapotban legyen, és forduljon az ügyfélszolgálathoz.
* Támogatási csak akkor, ha van meglévő biztonsági mentésből, amely 30 napon belül visszaállíthatja a felügyelt tartomány lehet.


### <a name="deleted-state"></a>"Törölt" állapot
A felügyelt tartomány, amely a "Felfüggesztett" állapotban marad, a 15 napon keresztül **törölt**.

**Mi várható:**
* Minden erőforráshoz és a felügyelt tartományhoz tartozó biztonsági másolatokat a rendszer törli.
* A felügyelt tartomány nem lehet visszaállítani, és létre kell hoznia egy új felügyelt tartomány használatához Azure AD tartományi szolgáltatásokat.
* Akkor nem kell fizetnie a felügyelt tartományhoz.


## <a name="what-happens-when-a-managed-domain-is-suspended"></a>Mi történik, ha egy felügyelt tartomány fel van függesztve?
Tartomány fel van függesztve, amikor az Azure AD Domain Services leállítja, és megszüntetéséhez látja el az a felügyelt tartományhoz tartozó tartományvezérlőket. Ennek eredményeképpen már nem készít biztonsági másolatokat, a felhasználók nem tudnak bejelentkezni a tartományba, és leállítja a szinkronizálást az Azure AD.

A felügyelt tartomány csak 15 nap legfeljebb felfüggesztett állapotban marad. A megfelelő időben történő helyreállítás biztosítása érdekében ajánlott a felfüggesztés minél hamarabb cím.


## <a name="how-do-i-know-if-my-managed-domain-is-suspended"></a>Honnan tudhatom meg, ha a felügyelt tartomány fel van függesztve?
Megjelenik egy [riasztás](active-directory-ds-troubleshoot-alerts.md) a az Azure AD tartományi szolgáltatások Health-oldal az Azure Portalon, amely a tartomány felfüggesztve deklarálja. Emellett a tartomány állapota "Felfüggesztett" jeleníti meg.


## <a name="how-do-i-restore-a-suspended-domain"></a>Hogyan állíthatok vissza egy felfüggesztett tartományt?
A "Felfüggesztett" állapotban lévő tartomány visszaállításához kövesse az alábbi lépéseket:

1. Keresse meg a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon
2. Kattintson a kívánt újraaktiválására tartományon
3. A bal oldali navigációs sávján kattintson **állapota**
4. Kattintson a riasztásra. A riasztás azonosítója lesz AADDS503 vagy AADDS504, felfüggesztése okától függően.
5. Kattintson a a feloldási hivatkozásra a riasztásban, és kövesse a lépéseket a riasztás feloldása.

A tartomány csak a legutóbbi biztonsági mentés napját állítható vissza. A dátum a legutóbbi biztonsági mentését a felügyelt tartomány állapotának lapján jelenik meg. Történt változásainak után a legutóbbi biztonsági mentés nem állítható vissza. A felügyelt tartományhoz tartozó biztonsági másolatok legfeljebb 30 napig tárolódnak. 30 napnál régebbi biztonsági másolatok törlődnek.


## <a name="deleting-domains"></a>Tartomány törlése
A tartomány fel van függesztve, több mint 15 napon keresztül, ha az Azure AD Domain Services törlése miatt tétlen és a tartományi szolgáltatás megakadályozhatják a felügyelt tartományhoz. Már nem díjköteles az Azure AD tartományi szolgáltatásokhoz. Ezen a ponton nem állítsa vissza a felügyelt tartományra, és létre kell hoznia azt.


## <a name="next-steps"></a>További lépések
- [Riasztások a felügyelt tartományon](active-directory-ds-troubleshoot-alerts.md)
- [További információ az Azure AD tartományi szolgáltatások](active-directory-ds-overview.md)
- [Lépjen kapcsolatba a termékcsoport](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
