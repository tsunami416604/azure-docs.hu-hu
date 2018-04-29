---
title: 'Az Azure Active Directory tartományi szolgáltatások: A riasztások hibaelhárítása |} Microsoft Docs'
description: Riasztások hibaelhárítása az Azure AD tartományi szolgáltatásokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 0c8fc2551f529fbff647d3400144fa2a9600bbd9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD tartományi szolgáltatások - riasztások hibaelhárítása
A cikkben hibaelhárítási útmutatók a felügyelt tartományok indításakor a riasztásokhoz.


Válassza ki a hibaelhárítási lépéseket, amelyek megfelelnek az ID vagy az üzenet a riasztásban.

| **Riasztás azonosítója** | **Riasztási üzenet** | **Megoldás** |
| --- | --- | :--- |
| AADDS001 | *A felügyelt tartomány számára engedélyezve van a biztonságos LDAP az interneten keresztül. Azonban 636-os port elérését nem zárolva van a hálózati biztonsági csoportok használatával. Ezt tehetik közzé a felhasználói fiókok a felügyelt tartományra, a jelszó találgatásos támadásokkal szemben.* | [Helytelen biztonságos LDAP-konfiguráció](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *A felügyelt tartományok társított Azure AD-címtár törölve lett. A felügyelt tartomány már nem támogatott konfiguráció. A Microsoft nem figyelése, kezeléséhez, javítás és a felügyelt tartományok szinkronizálása.* | [Hiányzó könyvtár](#aadds100-missing-directory) |
| AADDS101 | *Azure AD tartományi szolgáltatások az Azure AD B2C-címtár nem engedélyezhető.* | [Az Azure AD B2C fut. Ebben a könyvtárban](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Az Azure AD tartományi szolgáltatások megfelelő működéséhez szükséges egyszerű szolgáltatás az Azure AD-címtár törölve lett. Ez a konfiguráció hatással van a Microsoft képes figyeléséhez, kezeléséhez, a javítás, és a felügyelt tartományok szinkronizálása.* | [Hiányzó egyszerű szolgáltatásnév](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *Az IP-címtartományt, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat a virtuális hálózat van egy nyilvános IP-címtartományban. Azure AD tartományi szolgáltatások engedélyezni kell a privát IP-címtartománnyal rendelkező virtuális hálózatban. Ez a konfiguráció hatással van a Microsoft figyeléséhez, kezeléséhez, a javítás lehetőségét, és a felügyelt tartományok szinkronizálása.* | [A nyilvános IP-címtartományban címe](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft nem tudja elérni a tartományvezérlőket, a felügyelt tartomány. Ez akkor fordulhat elő, ha egy hálózati biztonsági csoport (NSG) van konfigurálva. a virtuális hálózati blokkok hozzáférési a felügyelt tartományra. Egy másik lehetséges oka-e a felhasználó által megadott útvonal, hogy blokkolja az internetről érkező bejövő forgalmat.* | [Hálózati hiba](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *Az alkalmazás azonosítójával "d87dcbc6-a371-462e-88e3-28ad15ec4e64" a szolgáltatás egyszerű lett törölve, és majd újra létrehozza. Az újbóli mögött inkonzisztens engedélyek hagyja el a felügyelt tartományok kiszolgálásához szükséges az Azure AD tartományi szolgáltatások erőforrásait. A felügyelt tartományra jelszavak szinkronizálása az hatással lehet.* | [A jelszó-szinkronizálás alkalmazás elavultak](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *A felügyelt tartományra volt utoljára szinkronizálva az Azure ad-val [dátum]. Felhasználók nem tudnak bejelentkezni a felügyelt tartományra, vagy a csoporttagságot nem lehet az Azure AD Szinkronizáló.* | [Szinkronizálás ideje még nem történt.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *A felügyelt tartományra legutolsó biztonsági mentése [dátum].* | [A biztonsági mentés még nem került sor egy kis idő](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *A felügyelt tartományra biztonságos LDAP tanúsítványának érvényessége XX.* | [Biztonságos LDAP tanúsítvány lejár](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *A felügyelt tartomány fel van függesztve, mert a tartományhoz tartozó Azure-előfizetés nem aktív.* | [Felfüggesztés miatt letiltott előfizetésbe](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *A felügyelt tartományra fel van függesztve, mert egy érvénytelen konfigurációja. A szolgáltatás nem tudta kezelni, javítást, vagy a tartományvezérlők, a felügyelt tartományok frissítése hosszú ideig.* | [Egy érvénytelen konfiguráció miatt felfüggesztése](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100: Hiányzó könyvtár
**Figyelmeztető üzenet:**

*A felügyelt tartományok társított Azure AD-címtár törölve lett. A felügyelt tartomány már nem támogatott konfiguráció. A Microsoft nem figyelése, kezeléséhez, javítás és a felügyelt tartományok szinkronizálása.*

**Megoldás:**

Ez a hiba általában okozza az Azure-előfizetéshez helytelenül áthelyezése egy új Azure AD-címtárral, és törli a régi továbbra is társítva van az Azure AD tartományi szolgáltatásokat Azure AD-címtárban.

Ez a hiba nem állítható helyre. Megoldásához kell [törölje a meglévő felügyelt tartományok](active-directory-ds-disable-aadds.md) és új címtárcsoportok hozza létre újra. Ha a hiba törlése, lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport [támogatásához](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Az Azure AD B2C fut. Ebben a könyvtárban
**Figyelmeztető üzenet:**

*Azure AD tartományi szolgáltatások az Azure AD B2C-címtár nem engedélyezhető.*

**Megoldás:**

>[!NOTE]
>Ahhoz, hogy továbbra is használhatja az Azure AD tartományi szolgáltatásokat, létre kell hoznia egy nem Azure AD B2C-címtárat az Azure AD tartományi szolgáltatások betűtípusainak.

A szolgáltatás visszaállításához kövesse az alábbi lépéseket:

1. [Törölje a felügyelt tartományok](active-directory-ds-disable-aadds.md) a meglévő Azure Active Directoryból.
2. Hozzon létre egy új könyvtárat, amely nincs az Azure AD B2C-címtár.
3. Kövesse a [bevezetés](active-directory-ds-getting-started.md) útmutató egy felügyelt tartomány újbóli létrehozásához.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: A nyilvános IP-címtartomány van cím

**Figyelmeztető üzenet:**

*Az IP-címtartományt, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat a virtuális hálózat van egy nyilvános IP-címtartományban. Azure AD tartományi szolgáltatások engedélyezni kell a privát IP-címtartománnyal rendelkező virtuális hálózatban. Ez a konfiguráció hatással van a Microsoft figyeléséhez, kezeléséhez, a javítás lehetőségét, és a felügyelt tartományok szinkronizálása.*

**Megoldás:**

> [!NOTE]
> A probléma megoldására törölnie kell a meglévő felügyelt tartományok, majd hozza létre újból a magánhálózati IP-címtartománnyal rendelkező virtuális hálózatban. A folyamat be nem őket.

Megkezdése előtt olvassa el a **privát IP v4 cím címtér** szakasz [Ez a cikk](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

A virtuális hálózaton belüli gépek előfordulhat, hogy kéréseket küld az Azure-erőforrást az azonos IP-címtartományt, az alhálózat konfigurálva. Azonban mivel a virtuális hálózati ebben a tartományban van konfigurálva, ezeket a kérelmeket továbbítja a virtuális hálózaton belül, és a nem érte el a kívánt webes erőforrások. Ez a konfiguráció az Azure AD tartományi szolgáltatások kiszámíthatatlan hibákat eredményezhet.

**Ha Ön a tulajdonosa az IP-címtartományt az interneten, amely a virtuális hálózat van konfigurálva, a riasztás figyelmen kívül hagyható. Azonban nem véglegesíthető a Azure AD tartományi szolgáltatások számára a [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] Ezzel a konfigurációval, mivel azt kiszámíthatatlan hibákat okozhat.**


1. [Törölje a felügyelt tartományok](active-directory-ds-disable-aadds.md) a könyvtárból.
2. Javítsa ki az IP-címtartományt az alhálózat
  1. Keresse meg a [oldalon virtuális hálózatok, az Azure-portálon](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Válassza ki a virtuális hálózatot alkalmazással tervezi használni az Azure AD tartományi szolgáltatásokhoz.
  3. Kattintson a **Címtéren** a beállítások
  4. A címtartomány frissítse a meglévő címtartomány kattintva és a Szerkesztés, vagy egy újabb címtartomány felvétele. Győződjön meg arról az új címtartomány privát IP-címtartományban. Mentse a módosításokat.
  5. Kattintson a **alhálózatok** a bal oldali navigációs sáv.
  6. Kattintson a táblázat módosításához az alhálózaton.
  7. A címtartomány frissítése, és mentse a módosításokat.
3. Hajtsa végre a [az első lépések használatával Azure AD tartományi szolgáltatások útmutató](active-directory-ds-getting-started.md) újbóli létrehozásához hajtsa végre a felügyelt tartományok. Győződjön meg arról, hogy egy privát IP-címtartománnyal rendelkező virtuális hálózatot választ.
4. Tartományhoz való csatlakozást a virtuális gépeket az új tartományba, hajtsa végre a [Ez az útmutató](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Annak érdekében, hogy a riasztás fel lett oldva, a tartomány állapotának ellenőrzése két órában.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Szinkronizálás nem fejeződött be egy kis idő

**Figyelmeztető üzenet:**

*A felügyelt tartományra volt utoljára szinkronizálva az Azure ad-val [dátum]. Felhasználók nem tudnak bejelentkezni a felügyelt tartományra, vagy a csoporttagságot nem lehet az Azure AD Szinkronizáló.*

**Megoldás:**

[Ellenőrizze a tartomány állapotát](active-directory-ds-check-health.md) a riasztásokhoz, amely a felügyelt tartomány konfigurációs problémákat jelezhet. Egyes esetekben a konfigurációjával kapcsolatos problémákat tilthatja le a Microsoft képes szinkronizálni a felügyelt tartományok. Minden riasztás feloldása, várjon, amíg képes két óra és ellenőrzés biztonsági megjelenítéséhez, ha a szinkronizálás befejeződött.


## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Biztonsági másolat nem vette egy kis idő

**Figyelmeztető üzenet:**

*A felügyelt tartományra legutolsó biztonsági mentése [dátum].*

**Megoldás:**

[Ellenőrizze a tartomány állapotát](active-directory-ds-check-health.md) a riasztásokhoz, amely a felügyelt tartomány konfigurációs problémákat jelezhet. Egyes esetekben a konfigurációjával kapcsolatos problémákat tilthatja le a Microsoft képes szinkronizálni a felügyelt tartományok. Minden riasztás feloldása, várjon, amíg képes két óra és ellenőrzés biztonsági megjelenítéséhez, ha a szinkronizálás befejeződött.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Letiltott előfizetésbe miatt felfüggesztése

**Figyelmeztető üzenet:**

*A felügyelt tartomány fel van függesztve, mert a tartományhoz tartozó Azure-előfizetés nem aktív.*

**Megoldás:**

A szolgáltatás visszaállítása [újítsa meg az Azure-előfizetéshez](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) a felügyelt tartományok társított.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Egy érvénytelen konfiguráció miatt felfüggesztése

**Figyelmeztető üzenet:**

*A felügyelt tartományra fel van függesztve, mert egy érvénytelen konfigurációja. A szolgáltatás nem tudta kezelni, javítást, vagy a tartományvezérlők, a felügyelt tartományok frissítése hosszú ideig.*

**Megoldás:**

[Ellenőrizze a tartomány állapotát](active-directory-ds-check-health.md) a riasztásokhoz, amely a felügyelt tartomány konfigurációs problémákat jelezhet. Ha ezek a riasztások bármelyikét oldhatja, tegye meg. Után forduljon a támogatási szolgálathoz kívánja újból engedélyezni az előfizetését.

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
