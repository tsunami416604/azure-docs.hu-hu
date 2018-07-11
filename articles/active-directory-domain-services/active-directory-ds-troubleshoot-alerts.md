---
title: 'Az Azure Active Directory Domain Services: A riasztások hibaelhárítása |} A Microsoft Docs'
description: Riasztások hibaelhárítása az Azure AD tartományi szolgáltatásokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 360c6c98227e52f0540b00ef136888d3d143b9fb
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951074"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Az Azure AD Domain Services – riasztások hibaelhárítása
A cikk ismerteti a hibaelhárítási útmutatók tapasztalhat a felügyelt tartományra a riasztás.


Válassza ki a hibaelhárítási lépéseket, amelyek megfelelnek a azonosítója vagy az üzenet a riasztásban.

| **Riasztás azonosítója** | **Riasztási üzenet** | **Felbontás** |
| --- | --- | :--- |
| AADDS001 | *A felügyelt tartomány Secure LDAP interneten keresztül engedélyezve van. 636-os porthoz való hozzáférés le a hálózati biztonsági csoport használatával azonban nincs zárolva. Ez előfordulhat, hogy teszik elérhetővé a felhasználói fiókok jelszavát találgatásos támadások, a felügyelt tartományon.* | [Helytelen biztonságos LDAP-konfiguráció](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *Előfordulhat, hogy a felügyelt tartományhoz társított Azure AD-címtárat törölték. A felügyelt tartomány már nem támogatott konfiguráció. A Microsoft nem figyelése, kezelése, javítani és szinkronizálni a felügyelt tartományt.* | [Hiányzó könyvtár](#aadds100-missing-directory) |
| AADDS101 | *Az Azure AD Domain Services nem engedélyezhető az Azure AD B2C-címtár.* | [Ebben a címtárban az Azure AD B2C fut.](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Az Azure AD Domain Services megfelelő működéséhez szükséges szolgáltatásnevet az Azure AD-címtár törölve lett. Ez a konfiguráció hatással van a Microsoft képes figyelése, kezelése, patch, és szinkronizálni a felügyelt tartományt.* | [Hiányzik a szolgáltatásnév](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *Az IP-címtartományt, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat a virtuális hálózathoz van egy nyilvános IP-címtartományt. Az Azure AD Domain Services egy magánhálózati IP-címtartományt a virtuális hálózaton engedélyezni kell. Ez a konfiguráció hatással van a Microsoft képes figyelése, kezelése, patch és szinkronizálása a felügyelt tartományra.* | [Cím szerepel egy nyilvános IP-címtartomány](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft nem tudja elérni a tartományvezérlőket, a felügyelt tartományhoz. Ez akkor fordulhat elő, ha a virtuális hálózati blokkolja a hozzáférést a felügyelt tartományhoz konfigurált hálózati biztonsági csoport (NSG). Egy másik lehetséges oka van-e egy felhasználó által megadott útvonalat a blokkolja az internetről bejövő forgalmat.* | [Hálózati hiba](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *A szolgáltatásnév "d87dcbc6-a371-462e-88e3-28ad15ec4e64" Alkalmazásazonosítóval rendelkező lett törölve, és majd újra létrehozza. Az újbóli hagy hátra inkonzisztens engedélyeket a szükséges szolgáltatást a felügyelt tartományhoz az Azure AD tartományi szolgáltatások erőforrásait. A felügyelt tartományra a jelszó-szinkronizálás is hatással lehet.* | [A jelszó-szinkronizálás alkalmazás nem naprakész](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *A felügyelt tartomány volt utoljára szinkronizálva az Azure ad-vel [Date]. Előfordulhat, hogy a felhasználók nem jelentkezhetnek be a felügyelt tartományon, vagy a csoporttagságot nem lehet az Azure ad-vel szinkronizált.* | [Szinkronizálás egy ideje még nem történt.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *A felügyelt tartomány legutolsó biztonsági mentése [Date].* | [Még nem került sor a biztonsági mentést, egy ideje](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *A felügyelt tartomány secure LDAP-tanúsítványt lejár: [date]].* | [Biztonságos LDAP-tanúsítvány hamarosan lejár](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *A felügyelt tartomány fel van függesztve, mert a tartományhoz tartozó Azure-előfizetés nem aktív.* | [Miatt letiltott előfizetés felfüggesztése](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *A felügyelt tartomány fel van függesztve, egy érvénytelen konfiguráció miatt. A szolgáltatás nem volt képes kezelni, patch, vagy a felügyelt tartományok tartományvezérlőinek frissítése hosszú ideje.* | [Egy érvénytelen konfiguráció miatt felfüggesztése](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100: Hiányzó könyvtár
**Riasztás jelenik meg:**

*Előfordulhat, hogy a felügyelt tartományhoz társított Azure AD-címtárat törölték. A felügyelt tartomány már nem támogatott konfiguráció. A Microsoft nem figyelése, kezelése, javítani és szinkronizálni a felügyelt tartományt.*

**Megoldás:**

Ez a hiba általában okozza helytelenül áthelyezése az Azure-előfizetéshez egy új Azure AD-címtár és a régi törlése Azure AD-címtár, amely az Azure AD tartományi szolgáltatások továbbra is társítva van.

Ez a hiba nem állítható helyre. Háríthatja el kell [törölje a meglévő felügyelt tartományt](active-directory-ds-disable-aadds.md) és hozza létre újra az új címtárban. Ha problémába ütközik törlése, lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [támogatás](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Az Azure AD B2C fut-e ebben a címtárban
**Riasztás jelenik meg:**

*Az Azure AD Domain Services nem engedélyezhető az Azure AD B2C-címtár.*

**Megoldás:**

>[!NOTE]
>Annak érdekében, hogy továbbra is használhatja az Azure AD tartományi szolgáltatásokat, újra létre kell hoznia egy nem Azure AD B2C-címtár az Azure AD Domain Services-példányában.

A szolgáltatás visszaállításához kövesse az alábbi lépéseket:

1. [A felügyelt tartomány törlése](active-directory-ds-disable-aadds.md) a meglévő Azure AD-címtárát.
2. Hozzon létre egy új könyvtárat, amely nem az Azure AD B2C-címtár.
3. Kövesse a [bevezetés](active-directory-ds-getting-started.md) útmutató újra létre kell hozni egy felügyelt tartományon.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Egy nyilvános IP-címtartomány van címe

**Riasztás jelenik meg:**

*Az IP-címtartományt, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat a virtuális hálózathoz van egy nyilvános IP-címtartományt. Az Azure AD Domain Services egy magánhálózati IP-címtartományt a virtuális hálózaton engedélyezni kell. Ez a konfiguráció hatással van a Microsoft képes figyelése, kezelése, patch és szinkronizálása a felügyelt tartományra.*

**Megoldás:**

> [!NOTE]
> Probléma megoldásához törölje a meglévő felügyelt tartományt, és hozza létre újból a magánhálózati IP-címtartományt a virtuális hálózaton. Ez a folyamat zavart okozó lesz.

Mielőtt elkezdené, olvassa el a **privát IP v4-Címterület** szakasz [Ez a cikk](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

A virtuális hálózaton belüli gépek előfordulhat, hogy kéréseket küld az Azure-erőforrást az IP-címtartományból, amelyek úgy vannak konfigurálva az alhálózat. Mivel ez a tartomány a virtuális hálózaton van beállítva, ezeket a kérelmeket a virtuális hálózaton belül lesz irányítva és azonban nem fogják tudni elérni az importálni kívánt webes erőforrások. Ez a konfiguráció az Azure AD tartományi szolgáltatásokkal kiszámíthatatlan hibákat eredményezhet.

**Ha Ön a tulajdonosa az IP-címtartományt az interneten, hogy a virtuális hálózaton van beállítva, ez a riasztás figyelmen kívül hagyható. Azonban az Azure AD Domain Services nem lehet véglegesíteni kell a [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] Ezzel a konfigurációval, mivel kiszámíthatatlan hibákat eredményezhet.**


1. [A felügyelt tartomány törlése](active-directory-ds-disable-aadds.md) a címtárból.
2. Javítsa ki az IP-címtartományt az alhálózat
  1. Keresse meg a [virtuális hálózatok lapján az Azure Portalon](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Válassza ki a virtuális hálózatot szeretne használni az Azure AD tartományi szolgáltatásokhoz.
  3. Kattintson a **címtér** beállításaiban
  4. A címtartomány frissítse a meglévő címtartomány kattintva és a Szerkesztés, vagy egy további címtartományt hozzáadása. Ellenőrizze, hogy az új címtartományt a magánhálózati IP-címtartományt. Mentse a módosításokat.
  5. Kattintson a **alhálózatok** a bal oldali navigációs menüben.
  6. Kattintson a táblázat a szerkeszteni kívánt alhálózaton található.
  7. Frissítse a címtartományt, és mentse a módosításokat.
3. Hajtsa végre a [az első lépések használatával az Azure AD Domain Services útmutató](active-directory-ds-getting-started.md) újra létrehozhatja a felügyelt tartományra. Győződjön meg arról, hogy válasszon ki egy magánhálózati IP-címtartományt a virtuális hálózat.
4. Tartományhoz való csatlakozás a virtuális gépek az új tartományba, hajtsa végre a [Ez az útmutató](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Annak érdekében, hogy a riasztás fel lett oldva, ellenőrizze a tartomány állapotának két órán belül.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Szinkronizálása nem befejeződött egy ideje

**Riasztás jelenik meg:**

*A felügyelt tartomány volt utoljára szinkronizálva az Azure ad-vel [Date]. Előfordulhat, hogy a felhasználók nem jelentkezhetnek be a felügyelt tartományon, vagy a csoporttagságot nem lehet az Azure ad-vel szinkronizált.*

**Megoldás:**

[Ellenőrizze a tartomány állapotának](active-directory-ds-check-health.md) riasztás, amely a felügyelt tartomány konfigurációjában kapcsolatos problémákat jelezhet. Egyes esetekben a konfigurációval kapcsolatos problémák blokkolhatja a Microsoft képes szinkronizálni a felügyelt tartományt. Ha tudja feloldani a riasztásokat, várjon két óra és ellenőrzés biztonsági megtekintheti, ha a szinkronizálás befejeződött.

Az alábbiakban néhány gyakori okáról, miért leállítja a szinkronizálás felügyelt tartományokban:
- Hálózati kapcsolat le van tiltva, a felügyelt tartományon. A hálózati problémák ellenőrzése kapcsolatos további információkért olvassa el hogyan való [hálózati biztonsági csoportok hibaelhárítása](active-directory-ds-troubleshoot-nsg.md) és [hálózati követelmények az Azure AD tartományi szolgáltatásokhoz](active-directory-ds-networking.md).
-  A jelszó-szinkronizálás soha ne állítson be vagy befejeződött. A jelszó-szinkronizálás beállításához, olvassa el a [Ez a cikk](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Biztonsági másolat nem foglalt egy ideje

**Riasztás jelenik meg:**

*A felügyelt tartomány legutolsó biztonsági mentése [Date].*

**Megoldás:**

[Ellenőrizze a tartomány állapotának](active-directory-ds-check-health.md) riasztás, amely a felügyelt tartomány konfigurációjában kapcsolatos problémákat jelezhet. Egyes esetekben a konfigurációval kapcsolatos problémák blokkolhatja a Microsoft képes szinkronizálni a felügyelt tartományt. Ha tudja feloldani a riasztásokat, várjon két óra és ellenőrzés biztonsági megtekintheti, ha a szinkronizálás befejeződött.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Letiltott előfizetés miatt felfüggesztése

**Riasztás jelenik meg:**

*A felügyelt tartomány fel van függesztve, mert a tartományhoz tartozó Azure-előfizetés nem aktív.*

**Megoldás:**

> [!WARNING]
> A felügyelt tartomány hosszabb időt is fel van függesztve, ha kimerítik a törlése folyamatban van. Célszerű a lehető leggyorsabban oldja meg a felfüggesztés. További, a Microsoft [Ez a cikk](active-directory-ds-suspension.md).

A szolgáltatás visszaállítása [újítsa meg az Azure-előfizetés](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) a felügyelt tartományhoz társított.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Egy érvénytelen konfiguráció miatt felfüggesztése

**Riasztás jelenik meg:**

*A felügyelt tartomány fel van függesztve, egy érvénytelen konfiguráció miatt. A szolgáltatás nem volt képes kezelni, patch, vagy a felügyelt tartományok tartományvezérlőinek frissítése hosszú ideje.*

**Megoldás:**

> [!WARNING]
> A felügyelt tartomány hosszabb időt is fel van függesztve, ha kimerítik a törlése folyamatban van. Célszerű a lehető leggyorsabban oldja meg a felfüggesztés. További, a Microsoft [Ez a cikk](active-directory-ds-suspension.md).

[Ellenőrizze a tartomány állapotának](active-directory-ds-check-health.md) riasztás, amely a felügyelt tartomány konfigurációjában kapcsolatos problémákat jelezhet. Ha bármelyik ezek a riasztások megoldása, tegye meg. Miután kérje a támogatási újraaktiválhatja előfizetését.


## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
