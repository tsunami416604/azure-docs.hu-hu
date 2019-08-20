---
title: 'Azure Active Directory Domain Services: Riasztások hibakeresése | Microsoft Docs'
description: Azure AD Domain Services riasztások hibáinak megoldása
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 5a8f3401de0dc452193efbcf79aef87a19aed081
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617090"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services – riasztások hibakeresése
Ez a cikk hibaelhárítási útmutatókat tartalmaz a felügyelt tartományon esetlegesen felmerülő riasztásokhoz.


Válassza ki a riasztásban szereplő AZONOSÍTÓhoz vagy üzenethez tartozó hibaelhárítási lépéseket.

| **Riasztás azonosítója** | **Riasztási üzenet** | **Felbontás** |
| --- | --- | :--- |
| AADDS001 | *Az interneten keresztüli Secure LDAP engedélyezve van a felügyelt tartományhoz. Az 636-as porthoz való hozzáférés azonban nem áll le hálózati biztonsági csoport használatával. A felhasználók a felügyelt tartomány felhasználói fiókjait felhasználhatják az elkövetett jelszavakkal kapcsolatos támadásokra.* | [Helytelen a biztonságos LDAP-konfiguráció](alert-ldaps.md) |
| AADDS100 | *Lehet, hogy törölték a felügyelt tartományhoz tartozó Azure AD-címtárat. A felügyelt tartomány már nem támogatott konfigurációban van. A Microsoft nem tudja figyelni, kezelni, javítani és szinkronizálni a felügyelt tartományt.* | [Hiányzó könyvtár](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services nem engedélyezhető Azure AD B2C könyvtárban.* | [Azure AD B2C fut ebben a könyvtárban](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Az Azure AD-címtárból törölte a Azure AD Domain Services megfelelő működéséhez szükséges egyszerű szolgáltatást. Ez a konfiguráció befolyásolja a Microsoft képességét a felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.* | [Hiányzó szolgáltatásnév](alert-service-principal.md) |
| AADDS103 | *Annak a virtuális hálózatnak az IP-címtartomány, amelyben engedélyezte a Azure AD Domain Services nyilvános IP-tartományban van. A Azure AD Domain Servicest engedélyezni kell egy magánhálózati IP-címtartományt használó virtuális hálózaton. Ez a konfiguráció befolyásolja a Microsoft képességét a felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.* | [A cím nyilvános IP-tartományban van](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft nem tudja elérni a tartományvezérlőket ehhez a felügyelt tartományhoz. Ez akkor fordulhat elő, ha a virtuális hálózaton konfigurált hálózati biztonsági csoport (NSG) blokkolja a hozzáférést a felügyelt tartományhoz. Egy másik lehetséges ok, ha van egy felhasználó által megadott útvonal, amely blokkolja az internetről érkező bejövő forgalmat.* | [Hálózati hiba](alert-nsg.md) |
| AADDS105 | *A "d87dcbc6-a371-462e-88e3-28ad15ec4e64" AZONOSÍTÓJÚ szolgáltatásnevet törölték, majd újból létrehozták. A szabadidő a felügyelt tartomány kiszolgálásához szükséges Azure AD Domain Services erőforrásokon inkonzisztens engedélyek mögött hagy. A felügyelt tartomány jelszavainak szinkronizálása hatással lehet.* | [A jelszó-szinkronizálási alkalmazás elavult](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *A felügyelt tartományhoz tartozó Azure-előfizetést törölték.  Azure AD Domain Services működésének folytatásához aktív előfizetésre van szükség.* | [Az Azure-előfizetés nem található](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *A felügyelt tartományhoz társított Azure-előfizetés nem aktív.  Azure AD Domain Services működésének folytatásához aktív előfizetésre van szükség.* | [Az Azure-előfizetés le van tiltva](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *Az Azure AD Domain Services által használt előfizetés át lett helyezve egy másik könyvtárba. Azure AD Domain Services a megfelelő működéséhez aktív előfizetéssel kell rendelkeznie ugyanabban a címtárban.* | [Előfizetés áthelyezett könyvtárai](#aadds108-subscription-moved-directories) |
| AADDS109 | *A felügyelt tartományhoz használt erőforrás törölve lett. Ez az erőforrás szükséges ahhoz, hogy a Azure AD Domain Services megfelelően működjön.* | [Egy erőforrás törölve](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *A Azure AD Domain Services telepítéséhez kiválasztott alhálózat megtelt, és nem rendelkezik a létrehozandó további tartományvezérlőhöz szükséges hellyel.* | [Az alhálózat megtelt](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Egy egyszerű szolgáltatásnév, amelyet Azure AD Domain Services a tartomány kiszolgálására használ, nincs jogosultsága az Azure-előfizetésben lévő erőforrások kezelésére. Az egyszerű szolgáltatásnak engedélyt kell nyernie a felügyelt tartomány kiszolgálásához.* | [Egyszerű szolgáltatásnév](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Megállapítottuk, hogy a tartomány virtuális hálózatának alhálózata nem rendelkezhet elegendő IP-címmel. Azure AD Domain Services az alhálózaton belül legalább két elérhető IP-címet kell használnia, a alkalmazásban engedélyezve van. Javasoljuk, hogy az alhálózaton belül legalább 3-5 tartalék IP-címet kapjon. Ez akkor fordulhat elő, ha az alhálózaton belül más virtuális gépek is üzembe vannak helyezve, így a rendelkezésre álló IP-címek száma kiesik, vagy ha az alhálózat rendelkezésre álló IP-címeinek száma korlátozást tartalmaz.* | [Nincs elég IP-cím](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Az Azure AD Domain Services által használt erőforrások váratlan állapotban észlelhetők, és nem állíthatók helyre.* | [Az erőforrások nem állíthatók helyre](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *A Azure AD Domain Services telepítéséhez kiválasztott alhálózat érvénytelen, és nem használható.* | [Érvénytelen alhálózat](#aadds114-subnet-invalid) |
| AADDS115 | *A felügyelt tartomány által használt hálózati erőforrások közül egy vagy több nem használható, mert a célként megadott hatókör zárolva van.* | [Az erőforrások zárolva vannak](#aadds115-resources-are-locked) |
| AADDS116 | *A felügyelt tartomány által használt hálózati erőforrások közül egy vagy több nem hajtható végre a házirend-korlátozás (ok) miatt.* | [Az erőforrások használhatatlanok](#aadds116-resources-are-unusable) |
| AADDS500 | *A felügyelt tartományt utoljára szinkronizálták az Azure AD-vel [date]. Előfordulhat, hogy a felhasználók nem tudnak bejelentkezni a felügyelt tartományba, vagy a csoporttagságok nem szinkronizálhatók az Azure AD-vel.* | [A szinkronizálás még nem történt meg](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *A felügyelt tartomány utolsó biztonsági mentése a következő időpontban: [date].* | [Egy darabig nem történt biztonsági mentés.](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *A felügyelt tartomány biztonságos LDAP-tanúsítványa a következő időpontban lejár: [date].* | [Biztonságos LDAP-tanúsítvány lejárata](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *A felügyelt tartomány fel van függesztve, mert a tartományhoz társított Azure-előfizetés nem aktív.* | [Felfüggesztés letiltott előfizetés miatt](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *A felügyelt tartomány Érvénytelen konfiguráció miatt fel van függesztve. A szolgáltatás hosszú ideje nem tudta kezelni, javítani vagy frissíteni a felügyelt tartomány tartományvezérlőit.* | [Felfüggesztés Érvénytelen konfiguráció miatt](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Hiányzó könyvtár
**Riasztási üzenet:**

*Lehet, hogy törölték a felügyelt tartományhoz tartozó Azure AD-címtárat. A felügyelt tartomány már nem támogatott konfigurációban van. A Microsoft nem tudja figyelni, kezelni, javítani és szinkronizálni a felügyelt tartományt.*

**Megoldás:**

Ezt a hibát általában az okozza, hogy az Azure-előfizetést helytelenül helyezi át egy új Azure AD-címtárba, és törli a régi Azure AD-könyvtárat, amely még társítva van Azure AD Domain Serviceshoz.

Ez a hiba nem állítható helyre. A megoldáshoz törölnie kell [a meglévő felügyelt tartományt](delete-aadds.md) , majd újra létre kell hoznia az új címtárban. Ha nem sikerül a törlés, forduljon a Azure Active Directory Domain Services terméktámogatási csapatához. [](contact-us.md)

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C fut ebben a könyvtárban
**Riasztási üzenet:**

*Azure AD Domain Services nem engedélyezhető Azure AD B2C könyvtárban.*

**Megoldás:**

>[!NOTE]
>A Azure AD Domain Services használatának folytatásához újra létre kell hoznia a Azure AD Domain Services példányt egy nem Azure AD B2C könyvtárban.

A szolgáltatás visszaállításához kövesse az alábbi lépéseket:

1. [Törölje a felügyelt tartományt](delete-aadds.md) a meglévő Azure ad-címtárból.
2. Hozzon létre egy olyan új könyvtárat, amely nem Azure AD B2C könyvtár.
3. A felügyelt tartomány újbóli létrehozásához kövesse az [első lépések](tutorial-create-instance.md) útmutatót.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: A cím nyilvános IP-tartományban van

**Riasztási üzenet:**

*Annak a virtuális hálózatnak az IP-címtartomány, amelyben engedélyezte a Azure AD Domain Services nyilvános IP-tartományban van. A Azure AD Domain Servicest engedélyezni kell egy magánhálózati IP-címtartományt használó virtuális hálózaton. Ez a konfiguráció befolyásolja a Microsoft képességét a felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.*

**Megoldás:**

> [!NOTE]
> A probléma megoldásához törölnie kell a meglévő felügyelt tartományt, majd újra létre kell hoznia egy magánhálózati IP-címtartományt tartalmazó virtuális hálózaton. Ez a folyamat zavaró.

Mielőtt elkezdené, olvassa el a [jelen cikk](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces) **magánhálózati IP v4-címtartomány** című szakaszát.

A virtuális hálózaton belül a gépek olyan Azure-erőforrásokhoz is csatlakozhatnak, amelyek az alhálózathoz konfigurált azonos IP-címtartomány alatt találhatók. Mivel azonban a virtuális hálózat ehhez a tartományhoz van konfigurálva, a rendszer a virtuális hálózaton belül továbbítja a kéréseket, és nem éri el a kívánt webes erőforrásokat. Ez a konfiguráció előre nem látható hibákat eredményezhet Azure AD Domain Services.

**Ha a virtuális hálózatban konfigurált IP-címtartomány tulajdonosa, akkor ez a riasztás figyelmen kívül hagyható. A Azure AD Domain Services azonban nem tudja véglegesíteni az [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)-t] Ezzel a konfigurációval, mert kiszámíthatatlan hibákat eredményezhet.**


1. [Törölje a felügyelt tartományt](delete-aadds.md) a címtárból.
2. Az alhálózat IP-címtartomány javítása
   1. Navigáljon a [Azure Portal Virtual Networks (virtuális hálózatok](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)) lapjára.
   2. Válassza ki a Azure AD Domain Services használni kívánt virtuális hálózatot.
   3. Kattintson a **címtartomány** elemre a beállítások területen.
   4. A címtartomány frissítéséhez kattintson a meglévő címtartomány elemre, és szerkessze, vagy adjon hozzá egy további címtartományt. Győződjön meg arról, hogy az új címtartomány egy magánhálózati IP-tartományban van. Mentse a módosításokat.
   5. Kattintson az alhálózatok elemre a bal oldali navigációs sávon.
   6. Kattintson arra az alhálózatra, amelyet szerkeszteni szeretne a táblában.
   7. Frissítse a címtartományt, és mentse a módosításokat.
3. A felügyelt tartomány újbóli létrehozásához kövesse [az Első lépések Azure ad Domain Services útmutató segítségével](tutorial-create-instance.md) . Győződjön meg arról, hogy egy magánhálózati IP-címtartományt használó virtuális hálózatot választ.
4. Ha tartományhoz szeretné csatlakoztatni a virtuális gépeket az új tartományhoz, kövesse [ezt az útmutatót](join-windows-vm.md).
8. A riasztás feloldása érdekében két órán belül ellenőrizze a tartomány állapotát.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Az Azure-előfizetés nem található

**Riasztási üzenet:**

*A felügyelt tartományhoz tartozó Azure-előfizetést törölték.  Azure AD Domain Services működésének folytatásához aktív előfizetésre van szükség.*

**Megoldás:**

Azure AD Domain Services működéséhez előfizetésre van szükség, és nem helyezhető át másik előfizetésbe. Mivel a felügyelt tartományhoz társított Azure-előfizetés törölve lett, újra létre kell hoznia egy Azure-előfizetést és Azure AD Domain Services.

1. Azure-előfizetés létrehozása
2. [Törölje a felügyelt tartományt](delete-aadds.md) a meglévő Azure ad-címtárból.
3. A felügyelt tartomány újbóli létrehozásához kövesse az [első lépések](tutorial-create-instance.md) útmutatót.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Az Azure-előfizetés le van tiltva

**Riasztási üzenet:**

*A felügyelt tartományhoz társított Azure-előfizetés nem aktív.  Azure AD Domain Services működésének folytatásához aktív előfizetésre van szükség.*

**Megoldás:**


1. [Újítsa meg az Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)-előfizetését.
2. Az Előfizetés megújítása után Azure AD Domain Services értesítést kap az Azure-tól a felügyelt tartomány újbóli engedélyezéséhez.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Előfizetés áthelyezett könyvtárai

**Riasztási üzenet:**

*Az Azure AD Domain Services által használt előfizetés át lett helyezve egy másik könyvtárba. Azure AD Domain Services a megfelelő működéséhez aktív előfizetéssel kell rendelkeznie ugyanabban a címtárban.*

**Megoldás:**

Áthelyezheti a Azure AD Domain Serviceshoz társított előfizetést az előző könyvtárba, vagy törölnie kell a [felügyelt tartományt](delete-aadds.md) a meglévő címtárból, és újra létre kell hoznia a kiválasztott könyvtárban (akár egy új előfizetéssel, akár módosítsa azt a könyvtárat, amelyben a Azure AD Domain Services példánya található).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: A felügyelt tartomány erőforrásai nem találhatók

**Riasztási üzenet:**

*A felügyelt tartományhoz használt erőforrás törölve lett. Ez az erőforrás szükséges ahhoz, hogy a Azure AD Domain Services megfelelően működjön.*

**Megoldás:**

A Azure AD Domain Services a megfelelő működéshez, például a nyilvános IP-címekhez, a hálózati adapterekhez és a Load Balancerhez való telepítéskor adott erőforrásokat hoz létre. Ha az elnevezett elemek bármelyike törölve lett, a felügyelt tartomány nem támogatott állapotba kerül, és megakadályozza, hogy a tartomány felügyelhető legyen. Ez a riasztás akkor található, ha a Azure AD Domain Services-erőforrások szerkesztésére képes személy törli a szükséges erőforrásokat. A következő lépések ismertetik, hogyan állíthatja vissza a felügyelt tartományt.

1. Navigáljon a Azure AD Domain Services Health (állapot) lapra
   1.    Utazás a Azure Portal [Azure ad Domain Services lapjára](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) .
   2.    A bal oldali navigációs sávon kattintson az **állapot** elemre.
2. Ellenőrizze, hogy a riasztás kevesebb-e, mint 4 órája
   1.    Az állapot lapon kattintson a **AADDS109** azonosítóval rendelkező riasztásra
   2.    A riasztás időbélyegzővel fog rendelkezni, amikor először megtalálták. Ha az időbélyeg kevesebb, mint 4 órával ezelőtt, fennáll az esélye annak, hogy Azure AD Domain Services újra létre tudja hozni a törölt erőforrást.
3. Ha a riasztás 4 óránál régebbi, a felügyelt tartomány helyreállíthatatlan állapotban van. Azure AD Domain Services törlése és újbóli létrehozása szükséges.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A felügyelt tartományhoz társított alhálózat megtelt

**Riasztási üzenet:**

*A Azure AD Domain Services telepítéséhez kiválasztott alhálózat megtelt, és nem rendelkezik a létrehozandó további tartományvezérlőhöz szükséges hellyel.*

**Megoldás:**

Ez a hiba nem állítható helyre. A megoldáshoz törölnie kell [a meglévő felügyelt tartományt](delete-aadds.md) , és [újból létre kell hoznia a felügyelt tartományt](tutorial-create-instance.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Egyszerű szolgáltatásnév

**Riasztási üzenet:**

*Egy egyszerű szolgáltatásnév, amelyet Azure AD Domain Services a tartomány kiszolgálására használ, nincs jogosultsága az Azure-előfizetésben lévő erőforrások kezelésére. Az egyszerű szolgáltatásnak engedélyt kell nyernie a felügyelt tartomány kiszolgálásához.*

**Megoldás:**

A szolgáltatás résztvevői számára hozzáférést kell biztosítani a felügyelt tartomány erőforrásainak kezeléséhez és létrehozásához. Valaki megtagadta a szolgáltatásnév elérését, és most már nem tudja kezelni az erőforrásokat. A szolgáltatáshoz való hozzáférés engedélyezéséhez kövesse az alábbi lépéseket:.

1. További információ a [RBAC-szabályozásról és az alkalmazásokhoz való hozzáférés engedélyezéséről a Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Tekintse át az egyszerű szolgáltatásnév hozzáférését az azonosítóval ```abba844e-bc0e-44b0-947a-dc74e5d09022``` , és adjon meg egy korábbi időpontban megtagadott hozzáférést.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Nincs elegendő IP-cím a felügyelt tartományban

**Riasztási üzenet:**

*Megállapítottuk, hogy a tartomány virtuális hálózatának alhálózata nem rendelkezhet elegendő IP-címmel. Azure AD Domain Services az alhálózaton belül legalább két elérhető IP-címet kell használnia, a alkalmazásban engedélyezve van. Javasoljuk, hogy az alhálózaton belül legalább 3-5 tartalék IP-címet kapjon. Ez akkor fordulhat elő, ha az alhálózaton belül más virtuális gépek is üzembe vannak helyezve, így a rendelkezésre álló IP-címek száma kiesik, vagy ha az alhálózat rendelkezésre álló IP-címeinek száma korlátozást tartalmaz.*

**Megoldás:**

1. Törölje a felügyelt tartományt a bérlőből.
2. Az alhálózat IP-címtartomány javítása
   1. Navigáljon a [Azure Portal Virtual Networks (virtuális hálózatok](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)) lapjára.
   2. Válassza ki a Azure AD Domain Services használni kívánt virtuális hálózatot.
   3. Kattintson a **címtartomány** elemre a beállítások területen.
   4. A címtartomány frissítéséhez kattintson a meglévő címtartomány elemre, és szerkessze, vagy adjon hozzá egy további címtartományt. Mentse a módosításokat.
   5. Kattintson az alhálózatok elemre a bal oldali navigációs sávon.
   6. Kattintson arra az alhálózatra, amelyet szerkeszteni szeretne a táblában.
   7. Frissítse a címtartományt, és mentse a módosításokat.
3. A felügyelt tartomány újbóli létrehozásához kövesse [az Első lépések Azure ad Domain Services útmutató segítségével](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) . Győződjön meg arról, hogy egy magánhálózati IP-címtartományt használó virtuális hálózatot választ.
4. Ha tartományhoz szeretné csatlakoztatni a virtuális gépeket az új tartományhoz, kövesse [ezt az útmutatót](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Ellenőrizze a tartomány állapotát két órán belül, és győződjön meg arról, hogy megfelelően végrehajtotta-e a lépéseket.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Az erőforrások nem állíthatók helyre

**Riasztási üzenet:**

*Az Azure AD Domain Services által használt erőforrások váratlan állapotban észlelhetők, és nem állíthatók helyre.*

**Megoldás:**

Ez a hiba nem állítható helyre. A megoldáshoz törölnie kell [a meglévő felügyelt tartományt](delete-aadds.md) , majd [újra létre kell hoznia a felügyelt tartományt](tutorial-create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Érvénytelen alhálózat

**Riasztási üzenet:**

*A Azure AD Domain Services telepítéséhez kiválasztott alhálózat érvénytelen, és nem használható.*

**Megoldás:**

Ez a hiba nem állítható helyre. A megoldáshoz törölnie kell [a meglévő felügyelt tartományt](delete-aadds.md) , majd [újra létre kell hoznia a felügyelt tartományt](tutorial-create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Az erőforrások zárolva vannak

**Riasztási üzenet:**

*A felügyelt tartomány által használt hálózati erőforrások közül egy vagy több nem használható, mert a célként megadott hatókör zárolva van.*

**Megoldás:**

1.  Tekintse át a Resource Manager műveleti naplóit a hálózati erőforrásokon (ez adja meg azokat az információkat, amelyeken a zárolás megakadályozza a módosítást).
2.  Távolítsa el az erőforrások zárolásait úgy, hogy a Azure AD Domain Services egyszerű szolgáltatásnév működjenek rajtuk.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Az erőforrások használhatatlanok

**Riasztási üzenet:**

*A felügyelt tartomány által használt hálózati erőforrások közül egy vagy több nem hajtható végre a házirend-korlátozás (ok) miatt.*

**Megoldás:**

1.  Tekintse át a felügyelt tartomány hálózati erőforrásain található Resource Manager műveleti naplókat.
2.  Gyengítheti az erőforrásokra vonatkozó házirend-korlátozásokat, hogy a HRE-DS szolgáltatás résztvevői is működjenek rajtuk.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A szinkronizálás egy darabig nem fejeződött be

**Riasztási üzenet:**

*A felügyelt tartományt utoljára szinkronizálták az Azure AD-vel [date]. Előfordulhat, hogy a felhasználók nem tudnak bejelentkezni a felügyelt tartományba, vagy a csoporttagságok nem szinkronizálhatók az Azure AD-vel.*

**Megoldás:**

Győződjön meg arról, hogy [a tartomány állapota](check-health.md) olyan riasztásokat tartalmaz, amelyek a felügyelt tartomány konfigurációjában esetlegesen felmerülő problémákra utalnak. Időnként a konfigurációval kapcsolatos problémák letilthatják a Microsoft számára a felügyelt tartomány szinkronizálásának képességét. Ha bármilyen riasztást képes feloldani, várjon két órát, és térjen vissza, és ellenőrizze, hogy befejeződött-e a szinkronizálás.

Íme néhány gyakori ok, amiért a szinkronizálás leáll a felügyelt tartományokban:
- A hálózati kapcsolatok blokkolva vannak a felügyelt tartományon. Ha többet szeretne megtudni a hálózat problémáinak ellenőrzéséről, olvassa el a [hálózati biztonsági csoportok](alert-nsg.md) és [a Azure ad Domain Services hálózati követelményeinek](network-considerations.md)elhárítása című témakört.
-  A jelszó-szinkronizálás nem lett beállítva vagy nem fejeződött be. A jelszó-szinkronizálás beállításához olvassa el [ezt a cikket](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Egy darabig nem történt biztonsági mentés.

**Riasztási üzenet:**

*A felügyelt tartomány utolsó biztonsági mentése a következő időpontban: [date].*

**Megoldás:**

Győződjön meg arról, hogy [a tartomány állapota](check-health.md) olyan riasztásokat tartalmaz, amelyek a felügyelt tartomány konfigurációjában esetlegesen felmerülő problémákra utalnak. Időnként a konfigurációval kapcsolatos problémák letilthatják a Microsoft számára a felügyelt tartomány biztonsági mentésének lehetőségét. Ha bármilyen riasztást képes feloldani, várjon két órát, és térjen vissza, és ellenőrizze, hogy befejeződött-e a biztonsági mentés.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Felfüggesztés letiltott előfizetés miatt

**Riasztási üzenet:**

*A felügyelt tartomány fel van függesztve, mert a tartományhoz társított Azure-előfizetés nem aktív.*

**Megoldás:**

> [!WARNING]
> Ha a felügyelt tartomány hosszabb ideig fel van függesztve, a törlés veszélye fennáll. A lehető leggyorsabban a felfüggesztést kell kezelni. További információért olvassa el [ezt a cikket](suspension.md).

A szolgáltatás visszaállításához [újítsa meg a](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) felügyelt tartományhoz tartozó Azure-előfizetést.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Felfüggesztés Érvénytelen konfiguráció miatt

**Riasztási üzenet:**

*A felügyelt tartomány Érvénytelen konfiguráció miatt fel van függesztve. A szolgáltatás hosszú ideje nem tudta kezelni, javítani vagy frissíteni a felügyelt tartomány tartományvezérlőit.*

**Megoldás:**

> [!WARNING]
> Ha a felügyelt tartomány hosszabb ideig fel van függesztve, a törlés veszélye fennáll. A lehető leggyorsabban a felfüggesztést kell kezelni. További információért olvassa el [ezt a cikket](suspension.md).

Győződjön meg arról, hogy [a tartomány állapota](check-health.md) olyan riasztásokat tartalmaz, amelyek a felügyelt tartomány konfigurációjában esetlegesen felmerülő problémákra utalnak. Ezen riasztások bármelyikének feloldásához tegye a következőt:. Ezután forduljon az ügyfélszolgálathoz az előfizetés újbóli engedélyezéséhez.


## <a name="contact-us"></a>Kapcsolat
A [visszajelzések megosztásához és](contact-us.md)a támogatáshoz forduljon a Azure Active Directory Domain Services termék csapatához.
