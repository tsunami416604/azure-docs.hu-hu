---
title: "Az Azure Active Directory tartományi szolgáltatások: A riasztások hibaelhárítása |} Microsoft Docs"
description: "Riasztások hibaelhárítása az Azure AD tartományi szolgáltatásokhoz"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: ergreenl
ms.openlocfilehash: b2e0edf3588f3b1db5f4b6641019be1ded9cb50e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD tartományi szolgáltatások - riasztások hibaelhárítása
A cikkben hibaelhárítási útmutatók a felügyelt tartományok indításakor a riasztásokhoz.


Válasszon ki a megfelelő hibaelhárítási lépéseket vagy riasztás azonosítója, illetve üzenet megjelenne.

| **Riasztás azonosítója** | **Riasztási üzenet** | **Megoldás** |
| --- | --- | :--- |
| AADDS001 | *A felügyelt tartomány számára engedélyezve van a biztonságos LDAP az interneten keresztül. Azonban 636-os port elérését nem zárolva van a hálózati biztonsági csoportok használatával. Ezt tehetik közzé a felhasználói fiókok a felügyelt tartományra, a jelszó találgatásos támadásokkal szemben.* | [Helytelen biztonságos LDAP-konfiguráció](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *A felügyelt tartományok társított Azure AD-címtár törölve lett. A felügyelt tartomány már nem támogatott konfiguráció. A Microsoft nem figyelése, kezeléséhez, javítás és a felügyelt tartományok szinkronizálása.* | [Hiányzó könyvtár](#aadds100-missing-directory) |
| AADDS101 | *Azure AD tartományi szolgáltatások az Azure AD B2C-címtár nem engedélyezhető.* | [Az Azure AD B2C fut. Ebben a könyvtárban](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Az Azure AD tartományi szolgáltatások megfelelő működéséhez szükséges egyszerű szolgáltatás az Azure AD-címtár törölve lett. Ez a konfiguráció hatással van a Microsoft képes figyeléséhez, kezeléséhez, a javítás, és a felügyelt tartományok szinkronizálása.* | [Hiányzó egyszerű szolgáltatásnév](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *Az IP-címtartományt, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat a virtuális hálózat van egy nyilvános IP-címtartományban. Azure AD tartományi szolgáltatások engedélyezni kell a privát IP-címtartománnyal rendelkező virtuális hálózatban. Ez a konfiguráció hatással van a Microsoft figyeléséhez, kezeléséhez, a javítás lehetőségét, és a felügyelt tartományok szinkronizálása.* | [A nyilvános IP-címtartományban címe](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft nem tudja elérni a tartományvezérlőket, a felügyelt tartomány. Ez akkor fordulhat elő, ha egy hálózati biztonsági csoport (NSG) van konfigurálva. a virtuális hálózati blokkok hozzáférési a felügyelt tartományra. Egy másik lehetséges oka-e a felhasználó által megadott útvonal, hogy blokkolja az internetről érkező bejövő forgalmat.* | [Hálózati hiba](active-directory-ds-troubleshoot-nsg.md) |

## <a name="aadds100-missing-directory"></a>AADDS100: Hiányzó könyvtár
**Figyelmeztető üzenet:**

*A felügyelt tartományok társított Azure AD-címtár törölve lett. A felügyelt tartomány már nem támogatott konfiguráció. A Microsoft nem figyelése, kezeléséhez, javítás és a felügyelt tartományok szinkronizálása.*

**Szervizelés:**

Ez a hiba általában okozza az Azure-előfizetéshez helytelenül áthelyezése egy új Azure AD-címtárral, és törli a régi továbbra is társítva van az Azure AD tartományi szolgáltatásokat Azure AD-címtárban.

Ez a hiba nem állítható helyre. Megoldásához kell [törölje a meglévő felügyelt tartományok](active-directory-ds-disable-aadds.md) és új címtárcsoportok hozza létre újra. Ha a hiba törlése, lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport [támogatásához](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Az Azure AD B2C fut. Ebben a könyvtárban
**Figyelmeztető üzenet:**

*Azure AD tartományi szolgáltatások az Azure AD B2C-címtár nem engedélyezhető.*

**Szervizelés:**

>[!NOTE]
>Ahhoz, hogy továbbra is használhatja az Azure AD tartományi szolgáltatásokat, létre kell hoznia egy nem Azure AD B2C-címtárat az Azure AD tartományi szolgáltatások betűtípusainak.

A szolgáltatás visszaállításához kövesse az alábbi lépéseket:

1. [Törölje a felügyelt tartományok](active-directory-ds-disable-aadds.md) a meglévő Azure Active Directoryból.
2. Hozzon létre egy új könyvtárat, amely nincs az Azure AD B2C-címtár.
3. Kövesse a [bevezetés](active-directory-ds-getting-started.md) útmutató egy felügyelt tartomány újbóli létrehozásához.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: A nyilvános IP-címtartomány van cím

**Figyelmeztető üzenet:**

*Az IP-címtartományt, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat a virtuális hálózat van egy nyilvános IP-címtartományban. Azure AD tartományi szolgáltatások engedélyezni kell a privát IP-címtartománnyal rendelkező virtuális hálózatban. Ez a konfiguráció hatással van a Microsoft figyeléséhez, kezeléséhez, a javítás lehetőségét, és a felügyelt tartományok szinkronizálása.*

**Szervizelés:**

> [!NOTE]
> A probléma megoldására törölnie kell a meglévő felügyelt tartományok, majd hozza létre újból a magánhálózati IP-címtartománnyal rendelkező virtuális hálózatban. A folyamat be nem őket.

Megkezdése előtt olvassa el a **privát IP v4 cím címtér** szakasz [Ez a cikk](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

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
8. Ellenőrizze a tartomány állapotát annak érdekében, hogy végrehajtotta a megfelelően két órában.


## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
