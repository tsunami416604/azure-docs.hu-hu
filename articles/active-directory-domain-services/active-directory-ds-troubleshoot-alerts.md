---
title: 'Az Azure Active Directory tartományi szolgáltatások: Riasztások hibaelhárítása |} A Microsoft Docs'
description: Riasztások hibaelhárítása az Azure AD tartományi szolgáltatásokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 492b15bddad598d65c15c48f04d3148c41cd3c7e
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817529"
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
| AADDS106 | *Az a felügyelt tartományhoz társított Azure-előfizetés törölve lett.  Az Azure AD tartományi szolgáltatások továbbra is megfelelően működik-e az aktív előfizetést igényel.* | [Azure-előfizetés nem található](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Az a felügyelt tartományhoz társított Azure-előfizetés nem lesz aktív.  Az Azure AD tartományi szolgáltatások továbbra is megfelelően működik-e az aktív előfizetést igényel.* | [Azure-előfizetés le van tiltva.](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Az Azure AD tartományi szolgáltatások által használt előfizetés át lett helyezve egy másik könyvtárra. Az Azure AD Domain Services rendelkeznie kell aktív előfizetés megfelelően működik-e ugyanabban a címtárban.* | [Az előfizetés áthelyezése könyvtárak](#aadds108-subscription-moved-directories) |
| AADDS109 | *A felügyelt tartomány használt erőforrás törölve lett. Azure AD Domain Services megfelelő működéséhez szükség van ehhez az erőforráshoz.* | [Erőforrás törölve lett](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *Az Azure AD Domain Services telepítésre kijelölt alhálózat megtelt, és nincs hely a további tartományvezérlő kell létrehozni.* | [Alhálózat megtelt.](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | * Egyszerű szolgáltatás, amely az Azure AD tartományi szolgáltatásokat használ a tartomány nem jogosult az Azure-előfizetés erőforrásainak kezelése. A szolgáltatásnévnek kell a szolgáltatás a felügyelt tartományra a jogosultságokat. * | Egyszerű szolgáltatás nem engedélyezett |
| AADDS112 | *Azonosítottuk, hogy a tartomány a virtuális hálózat alhálózatának előfordulhat, hogy nincs elegendő IP-címet. Az Azure AD Domain Services kell legalább két rendelkezésre álló IP-címet a engedélyezve van az alhálózaton belül. Javasoljuk, hogy legalább 3 – 5 tartalék IP-címek az alhálózaton belül. Ez azért fordulhatott elő, ha más virtuális gépek vannak telepítve az alhálózatban, így kimerítsék a számos elérhető IP-címek vagy az alhálózaton elérhető IP-címek számának korlátozása.* | [Nincs elegendő IP-címek](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Az Azure AD tartományi szolgáltatások által használt erőforrásokat egy nem várt állapotot észlelt, és nem állítható helyre.* | [Erőforrások helyreállíthatatlan](#aadds113-resources-are-unrecoverable) |
| AADDS114 | * Az Azure AD tartományi szolgáltatások üzembe helyezéshez kiválasztott alhálózatban nem érvényes, és nem használható. * | [Érvénytelen alhálózat](#aadds114-subnet-invalid) |
| AADDS115 | *Egy vagy több, a hálózat által használt erőforrások a felügyelt tartomány nem működik a módon célhatóköre zárolva van.* | [Zárolt erőforrások](#aadds115-resources-are-locked) |
| AADDS116 | *Egy vagy több, a felügyelt tartomány által használt erőforrások nem működik a szabályzat restriction(s) miatt.* | [Erőforrások használhatatlanná válnak.](#aadds116-resources-are-unusable) |
| AADDS500 | *A felügyelt tartomány volt utoljára szinkronizálva az Azure ad-vel [Date]. Előfordulhat, hogy a felhasználók nem jelentkezhetnek be a felügyelt tartományon, vagy a csoporttagságot nem lehet az Azure ad-vel szinkronizált.* | [Szinkronizálás egy ideje még nem történt.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *A felügyelt tartomány legutolsó biztonsági mentése [Date].* | [Még nem került sor a biztonsági mentést, egy ideje](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *A felügyelt tartomány secure LDAP-tanúsítványt [date]-én jár le.* | [Biztonságos LDAP-tanúsítvány hamarosan lejár](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *A felügyelt tartomány fel van függesztve, mert a tartományhoz tartozó Azure-előfizetés nem aktív.* | [Miatt letiltott előfizetés felfüggesztése](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *A felügyelt tartomány fel van függesztve, egy érvénytelen konfiguráció miatt. A szolgáltatás nem volt képes kezelni, patch, vagy a felügyelt tartományok tartományvezérlőinek frissítése hosszú ideje.* | [Egy érvénytelen konfiguráció miatt felfüggesztése](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Hiányzó könyvtár
**Riasztás jelenik meg:**

*Előfordulhat, hogy a felügyelt tartományhoz társított Azure AD-címtárat törölték. A felügyelt tartomány már nem támogatott konfiguráció. A Microsoft nem figyelése, kezelése, javítani és szinkronizálni a felügyelt tartományt.*

**Megoldás:**

Ez a hiba általában okozza helytelenül áthelyezése az Azure-előfizetéshez egy új Azure AD-címtár és a régi törlése Azure AD-címtár, amely az Azure AD tartományi szolgáltatások továbbra is társítva van.

Ez a hiba nem állítható helyre. Háríthatja el kell [törölje a meglévő felügyelt tartományt](active-directory-ds-disable-aadds.md) és hozza létre újra az új címtárban. Ha problémába ütközik törlése, lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [támogatás](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Az Azure AD B2C fut ebben a címtárban
**Riasztás jelenik meg:**

*Az Azure AD Domain Services nem engedélyezhető az Azure AD B2C-címtár.*

**Megoldás:**

>[!NOTE]
>Annak érdekében, hogy továbbra is használhatja az Azure AD tartományi szolgáltatásokat, újra létre kell hoznia egy nem Azure AD B2C-címtár az Azure AD Domain Services-példányában.

A szolgáltatás visszaállításához kövesse az alábbi lépéseket:

1. [A felügyelt tartomány törlése](active-directory-ds-disable-aadds.md) a meglévő Azure AD-címtárát.
2. Hozzon létre egy új könyvtárat, amely nem az Azure AD B2C-címtár.
3. Kövesse a [bevezetés](active-directory-ds-getting-started.md) útmutató újra létre kell hozni egy felügyelt tartományon.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Cím szerepel egy nyilvános IP-címtartomány

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

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Az Azure-előfizetés nem található

**Riasztás jelenik meg:**

*Az a felügyelt tartományhoz társított Azure-előfizetés törölve lett.  Az Azure AD tartományi szolgáltatások továbbra is megfelelően működik-e az aktív előfizetést igényel.*

**Megoldás:**

Az Azure AD Domain Services függvény-előfizetést igényel, és nem helyezhető át másik előfizetésbe. Az Azure-előfizetést, amely a felügyelt tartományhoz társított lett törölve lett, mert szüksége lesz újra létre kell hozni egy Azure-előfizetés és Azure AD tartományi szolgáltatásokat.

1. Azure-előfizetés létrehozása
2. [A felügyelt tartomány törlése](active-directory-ds-disable-aadds.md) a meglévő Azure AD-címtárát.
3. Kövesse a [bevezetés](active-directory-ds-getting-started.md) útmutató újra létre kell hozni egy felügyelt tartományon.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Az Azure-előfizetés le van tiltva

**Riasztás jelenik meg:**

*Az a felügyelt tartományhoz társított Azure-előfizetés nem lesz aktív.  Az Azure AD tartományi szolgáltatások továbbra is megfelelően működik-e az aktív előfizetést igényel.*

**Megoldás:**


1. [Az Azure-előfizetés megújítása](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Az előfizetés megújítása esetén az Azure AD Domain Services egy értesítést fog kapni az Azure és engedélyezze újra a felügyelt tartományra.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Az előfizetés áthelyezése könyvtárak

**Riasztás jelenik meg:**

*Az Azure AD tartományi szolgáltatások által használt előfizetés át lett helyezve egy másik könyvtárra. Az Azure AD Domain Services rendelkeznie kell aktív előfizetés megfelelően működik-e ugyanabban a címtárban.*

**Megoldás:**

Vagy áthelyezheti az előfizetéshez társított vissza az előző címtár az Azure AD tartományi szolgáltatásokat, vagy szüksége [a felügyelt tartomány törlése](active-directory-ds-disable-aadds.md) a létező címtárból, és hozza létre újra a kiválasztott könyvtár (vagy egy új előfizetést, vagy módosítsa a könyvtárat az Azure AD Domain Services-példány van).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: A felügyelt tartományához tartozó erőforrások nem találhatók

**Riasztás jelenik meg:**

*A felügyelt tartomány használt erőforrás törölve lett. Azure AD Domain Services megfelelő működéséhez szükség van ehhez az erőforráshoz.*

**Megoldás:**

Az Azure AD Domain Services adott az erőforrásokat hozza létre annak érdekében, hogy a megfelelő működéshez üzembe helyezése során többek között a nyilvános IP-címek, a hálózati adapterek és a egy terheléselosztó. A megnevezett bármelyikét törlődnek, ha ez egy nem támogatott állapotban kell lennie a felügyelt tartomány okoz, és megakadályozza, hogy a tartomány felügyelete alatt áll. Ez a riasztás amikor valakivel, aki képes szerkeszteni az Azure AD Domain Services-erőforrások törli egy szükséges erőforrás található. Hogyan lehet visszaállítani a felügyelt tartomány lépései.

1.  Keresse meg az Azure AD tartományi szolgáltatások health-oldal
  1.    Utazás a [Azure AD tartományi szolgáltatások lap](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) az Azure Portalon.
  2.    A bal oldali navigációs sávján kattintson **állapota**
2.  Ellenőrizze, hogy ha a riasztás-e a kevesebb mint 4 órája jött létre
  1.    Az oldalt, kattintson a riasztás azonosítójú **AADDS109**
  2.    A riasztás számára, amikor először található időbélyeg fog rendelkezni. Ha az időbélyeg kevesebb mint 4 órája, esetén megvan az esélye, hogy az Azure AD tartományi szolgáltatások újra létrehozhatja a törölt erőforráscsoport.
3.  Ha a riasztás több mint 4 órája jött létre, a felügyelt tartomány helyreállíthatatlan állapotban van. Törölnie kell, majd hozza létre újra az Azure AD tartományi szolgáltatásokat.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: Az a felügyelt tartományhoz társított alhálózat megtelt.

**Riasztás jelenik meg:**

*Az Azure AD Domain Services telepítésre kijelölt alhálózat megtelt, és nincs hely a további tartományvezérlő kell létrehozni.*

**Megoldás:**

Ez a hiba nem állítható helyre. Háríthatja el kell [törölje a meglévő felügyelt tartományt](active-directory-ds-disable-aadds.md) és [hozza létre újra a felügyelt tartományhoz](active-directory-ds-getting-started.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Egyszerű szolgáltatás nem engedélyezett

**Riasztás jelenik meg:**

*A tartomány használ az Azure AD Domain Services egyszerű szolgáltatás nem jogosult az Azure-előfizetés erőforrásainak kezelése. A szolgáltatásnévnek kell a szolgáltatás a felügyelt tartományra a jogosultságokat.*

**Megoldás:**

Our service principals need access to be able to manage and create resources on your managed domain. Valaki megtagadta a szolgáltatásnév hozzáférhessen, és most már nem tudja erőforrások kezeléséhez. Kövesse a szolgáltatásnévvel való hozzáférést.

1. További információ [RBAC vezérlő és az Azure Portalon alkalmazásokhoz való hozzáférés biztosításának módja](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Tekintse át a hozzáférést, amely a szolgáltatásnév azonosítójú ```abba844e-bc0e-44b0-947a-dc74e5d09022``` és a egy korábbi időpontban megtagadta a hozzáférést.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Nincs elegendő IP-cím a felügyelt tartományban

**Riasztás jelenik meg:**

*Azonosítottuk, hogy a tartomány a virtuális hálózat alhálózatának előfordulhat, hogy nincs elegendő IP-címet. Az Azure AD Domain Services kell legalább két rendelkezésre álló IP-címet a engedélyezve van az alhálózaton belül. Javasoljuk, hogy legalább 3 – 5 tartalék IP-címek az alhálózaton belül. Ez azért fordulhatott elő, ha más virtuális gépek vannak telepítve az alhálózatban, így kimerítsék a számos elérhető IP-címek vagy az alhálózaton elérhető IP-címek számának korlátozása.*

**Megoldás:**

1. A felügyelt tartomány bérlőjéből törölni.
2. Javítsa ki az IP-címtartományt az alhálózat
  1. Keresse meg a [virtuális hálózatok lapján az Azure Portalon](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Válassza ki a virtuális hálózatot szeretne használni az Azure AD tartományi szolgáltatásokhoz.
  3. Kattintson a **címtér** beállításaiban
  4. A címtartomány frissítse a meglévő címtartomány kattintva és a Szerkesztés, vagy egy további címtartományt hozzáadása. Mentse a módosításokat.
  5. Kattintson a **alhálózatok** a bal oldali navigációs menüben.
  6. Kattintson a táblázat a szerkeszteni kívánt alhálózaton található.
  7. Frissítse a címtartományt, és mentse a módosításokat.
3. Hajtsa végre a [az első lépések használatával az Azure AD Domain Services útmutató](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) újra létrehozhatja a felügyelt tartományra. Győződjön meg arról, hogy válasszon ki egy magánhálózati IP-címtartományt a virtuális hálózat.
4. Tartományhoz való csatlakozás a virtuális gépek az új tartományba, hajtsa végre a [Ez az útmutató](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. A tartomány állapotának ellenőrzése annak biztosítása érdekében, hogy végrehajtotta a lépéseket megfelelően két órán belül.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Erőforrások helyreállíthatatlan

**Riasztás jelenik meg:**

*Az Azure AD tartományi szolgáltatások által használt erőforrásokat egy nem várt állapotot észlelt, és nem állítható helyre.*

**Megoldás:**

Ez a hiba nem állítható helyre. Háríthatja el kell [törölje a meglévő felügyelt tartományt](active-directory-ds-disable-aadds.md) és [hozza létre újra a felügyelt tartomány](active-directory-ds-getting-started.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Érvénytelen alhálózat

**Riasztás jelenik meg:**

*Az Azure AD tartományi szolgáltatások üzembe helyezéshez kiválasztott alhálózatban nem érvényes, és nem használható.*

**Megoldás:**

Ez a hiba nem állítható helyre. Háríthatja el kell [törölje a meglévő felügyelt tartományt](active-directory-ds-disable-aadds.md) és [hozza létre újra a felügyelt tartomány](active-directory-ds-getting-started.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Zárolt erőforrások

**Riasztás jelenik meg:**

*Egy vagy több, a hálózat által használt erőforrások a felügyelt tartomány nem működik a módon célhatóköre zárolva van.*

**Megoldás:**

1.  Tekintse át a Resource Manager-művelet jelentkezik be a hálózati erőforrások (Ez adjon info melyik zárolására módosítása miatt).
2.  Távolítsa el a zárolások az erőforrásokon, úgy, hogy az Azure AD Domain Services egyszerű szolgáltatás működhet rajtuk.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Erőforrások használhatatlanná válnak.

**Riasztás jelenik meg:**

*Egy vagy több, a felügyelt tartomány által használt erőforrások nem működik a szabályzat restriction(s) miatt.*

**Megoldás:**

1.  Tekintse át a Resource Manager-műveletet a hálózati erőforrások, a felügyelt tartományhoz tartozó bejelentkezik.
2.  Gyengíthetik a szabályzatok korlátozásai az erőforrásokon, úgy, hogy az AAD-DS egyszerű szolgáltatás működhet rajtuk.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Szinkronizálás egy ideje nem fejeződött be

**Riasztás jelenik meg:**

*A felügyelt tartomány volt utoljára szinkronizálva az Azure ad-vel [Date]. Előfordulhat, hogy a felhasználók nem jelentkezhetnek be a felügyelt tartományon, vagy a csoporttagságot nem lehet az Azure ad-vel szinkronizált.*

**Megoldás:**

[Ellenőrizze a tartomány állapotának](active-directory-ds-check-health.md) riasztás, amely a felügyelt tartomány konfigurációjában kapcsolatos problémákat jelezhet. Egyes esetekben a konfigurációval kapcsolatos problémák blokkolhatja a Microsoft képes szinkronizálni a felügyelt tartományt. Ha tudja feloldani a riasztásokat, várjon két óra és ellenőrzés biztonsági megtekintheti, ha a szinkronizálás befejeződött.

Az alábbiakban néhány gyakori okáról, miért leállítja a szinkronizálás felügyelt tartományokban:
- Hálózati kapcsolat le van tiltva, a felügyelt tartományon. A hálózati problémák ellenőrzése kapcsolatos további információkért olvassa el hogyan való [hálózati biztonsági csoportok hibaelhárítása](active-directory-ds-troubleshoot-nsg.md) és [hálózati követelmények az Azure AD tartományi szolgáltatásokhoz](active-directory-ds-networking.md).
-  A jelszó-szinkronizálás soha ne állítson be vagy befejeződött. A jelszó-szinkronizálás beállításához, olvassa el a [Ez a cikk](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Biztonsági másolat nem került sor egy ideje

**Riasztás jelenik meg:**

*A felügyelt tartomány legutolsó biztonsági mentése [Date].*

**Megoldás:**

[Ellenőrizze a tartomány állapotának](active-directory-ds-check-health.md) riasztás, amely a felügyelt tartomány konfigurációjában kapcsolatos problémákat jelezhet. Egyes esetekben a konfigurációval kapcsolatos problémák blokkolhatja a Microsoft képes biztonsági mentésre a felügyelt tartományra. Ha tudja feloldani a riasztásokat, várjon két óra és ellenőrzése biztonsági megtekintheti, ha a biztonsági mentés befejeződött.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Miatt letiltott előfizetés felfüggesztése

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
