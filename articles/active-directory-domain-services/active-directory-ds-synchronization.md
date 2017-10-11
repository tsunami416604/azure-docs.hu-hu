---
title: "Azure Active Directory tartományi szolgáltatások: A felügyelt tartományok szinkronizálás |} Microsoft Docs"
description: "Az Azure Active Directory tartományi szolgáltatások által felügyelt tartományokhoz szinkronizációja ismertetése"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 0c9a9a56e1489ee91fcc332beeef36cdc9c93dc1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Szinkronizálás az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
A következő ábra bemutatja, hogyan szinkronizálási működik az Azure AD tartományi szolgáltatások felügyelt tartományok.

![Az Azure AD tartományi szolgáltatásokra szinkronizált topológiákkal](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Szinkronizálás a helyszíni címtárból az Azure AD-bérlő
Az Azure AD Connect szinkronizálási szolgáltatás felhasználói fiókok szinkronizálása, csoporttagságok használatával, és a hitelesítő adatok csak az Azure AD-bérlő. Attribútumok a felhasználói fiókok például az egyszerű Felhasználónevet és a helyszíni biztonsági azonosító (security Identifier azonosítót) szinkronizálja a rendszer. Ha az Azure AD tartományi szolgáltatásokat használja, az NTLM és Kerberos hitelesítéshez szükséges a hagyományos hitelesítő kivonatokat is szinkronizálódnak az Azure AD-bérlő.

Késleltetve visszaírt állítja be, ha az Azure AD-címtár változásokat szinkronizálódnak a helyszíni Active Directoryban. Például, ha módosítja a jelszavát, az Azure AD önkiszolgáló jelszó-módosítási funkciókat használ, a módosított jelszó frissül a helyszíni Active Directory-tartománynak.

> [!NOTE]
> Mindig az Azure AD Connect legújabb verziójának segítségével ellenőrizze, hogy az összes ismert hibákat tartalmaz.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Az Azure AD-bérlő szinkronizálását a felügyelt tartományra
Felhasználói fiókok, csoporttagságot és hitelesítő kivonatokat alapján szinkronizálja az Azure AD-bérlő az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. A szinkronizálási folyamat be nem automatikus. Nem kell konfigurálni, figyelheti vagy a szinkronizálási folyamat kezeléséhez. A könyvtár egyszeri kezdeti szinkronizálás befejezése után, általában a felügyelt tartományok megjelennek az Azure ad-ben végzett módosítások körülbelül 20 percet vesz igénybe. A szinkronizálás időköze jelszómódosítások vonatkozik, vagy attribútumok az Azure ad-ben végrehajtott módosításokat.

A szinkronizálási folyamat akkor is egy-way/egyirányú jellegűek. A felügyelt tartományok írásvédett nagymértékben kivételével minden egyéni szervezeti egységek hoz létre. Ezért nem módosíthatja felhasználói attribútumok, a felhasználói jelszavakat és a csoporttagságot a felügyelt tartományon belül. Ennek eredményeképpen nincs nincs fordított szinkronizálás módosításait a felügyelt tartomány vissza az Azure AD-bérlő.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Egy helyszíni Többerdős környezetben való szinkronizálásra
Számos szervezet rendelkezik több fiókerdővel álló elég bonyolult a helyszíni identitás-infrastruktúra. Az Azure AD Connect szinkronizálási felhasználók, csoportok és hitelesítő kivonatokat a többerdős környezetben, az Azure AD-bérlő támogatja.

Ezzel szemben az Azure AD-bérlő egy sokkal egyszerűbb, és strukturálatlan névtér. Ahhoz, hogy a felhasználók számára az Azure AD által védett alkalmazások megbízható hozzáférése, UPN-ütközések feloldása különböző erdőkben találhatók a felhasználói fiókok között. Az Azure AD tartományi szolgáltatások által kezelt tartomány aránylik zárja be az Azure AD-bérlő hasonlóságot. Ezért a felügyelt tartományok látható egy egyszerű Szervezetiegység-struktúrája. Összes felhasználók és csoportok a "AADDC felhasználók" tárolóban, függetlenül a helyszíni tartományban vagy erdőben, ahol azok lettek szinkronizálva a tárolják. Előfordulhat, hogy konfigurálta a hierarchikus OU helyszíni struktúra. A felügyelt tartományok azonban továbbra is rendelkezik egy egyszerű strukturálatlan Szervezetiegység-struktúrájától.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Kizárások - mi nincs szinkronizálva a felügyelt tartományra
A következő objektumok és attribútumok szinkronizálása nem Azure AD-bérlőn vagy a felügyelt tartományok:

* **Kizárt attribútumok:** dönthet, ha szeretne kizárni a bizonyos attribútumok szinkronizálása az Azure AD-bérlő az Azure AD Connect használatával a helyszíni tartományból. Ezek az attribútumok kizárt nem érhetők el a kezelt tartományban.
* **Csoportházirendek:** konfigurálva abban a tartományban, a helyszíni csoportházirendek nincsenek szinkronizálva. a felügyelt tartományra.
* **SYSVOL-megosztás:** hasonlóan a tartalmát a helyi tartomány, a SYSVOL-megosztás nincsenek szinkronizálva. a felügyelt tartományra.
* **Számítógép-objektumok:** számítógép-objektumok esetében a helyi tartományhoz csatlakozó számítógépek nincsenek szinkronizálva a felügyelt tartományra. Ezeket a számítógépeket a felügyelt tartományok bizalmi kapcsolattal rendelkezik, és nem csak a helyi tartományhoz tartozik. A felügyelt tartományok, a számítógép-objektumok csak olyan számítógépeken, explicit módon tartomány-csatlakozott a felügyelt tartományra található.
* **A felhasználók és csoportok SidHistory attribútumok:** az elsődleges felhasználója és elsődleges csoport biztonsági azonosítói a helyi tartomány szinkronizálva van a felügyelt tartományra. Azonban a felhasználók és csoportok meglévő SidHistory attribútumok nincsenek szinkronizálva a helyszíni tartományból a felügyelt tartományra.
* **Szervezeti egység (OU) struktúrák:** a helyszíni tartományi megadott szervezeti egységek ne szinkronizáljon a felügyelt tartományra. A felügyelt tartományok nincsenek két beépített szervezeti egységekhez. Alapértelmezés szerint a felügyelt tartományok rendelkezik egy egyszerű Szervezetiegység-struktúrája. De ki is [hozzon létre egy egyéni szervezeti Egységet a felügyelt tartományok](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Hogyan megadott attribútumok szinkronizálva a felügyelt tartományra
A következő táblázat néhány általános attribútumokkal rendelkeznek, és ismerteti, hogyan akkor a rendszer szinkronizálja a felügyelt tartományra.

| A felügyelt tartományok attribútum | Forrás | Megjegyzések |
|:--- |:--- |:--- |
| EGYSZERŰ FELHASZNÁLÓNÉV |Felhasználó UPN attribútum az Azure AD-bérlőben |A felügyelt tartományok való szinkronizálása az Azure AD-bérlő az UPN-attribútumot. Ezért a legmegbízhatóbb módszer az, hogy jelentkezzen be a felügyelt tartományok használja az egyszerű felhasználónév. |
| sAMAccountName |Felhasználó mailNickname az Azure AD-bérlő attribútumnak, vagy automatikusan generált |A SAMAccountName attribútum a mailNickname attribútumot az Azure AD-bérlő származik. Több felhasználói fiók azonos mailNickname attribútuma van, a SAMAccountName, automatikusan generált. Ha a felhasználó mailNickname vagy UPN előtagja hosszabb 20 karakternél, a SAMAccountName jön létre automatikusan a 20 karakter lehet SAMAccountName attribútumok kielégítéséhez. |
| Jelszavak |Az Azure AD-bérlő felhasználói jelszó |Az Azure AD-bérlő (más néven kiegészítő hitelesítő adatok) NTLM vagy Kerberos hitelesítéshez szükséges hitelesítő kivonatokat vannak szinkronizálva. Ha az Azure AD-bérlő a szinkronizált bérlők, ezeket a hitelesítő adatokat a helyszíni tartományból forrása. |
| Elsődleges felhasználó/csoport biztonsági azonosítója |Automatikusan létrehozott |A felhasználó vagy csoport fiókok elsődleges biztonsági azonosítója jön létre automatikusan a kezelt tartományban. Ez az attribútum nem egyezik az elsődleges felhasználó/csoport SID az objektum a helyszíni Active Directory-tartománynak. Ez az eltérés az oka, hogy a felügyelt tartományra névtérrel rendelkező különböző SID mint a helyszíni tartományban. |
| A felhasználók és csoportok SID-előzmények |A helyszíni elsődleges felhasználói és csoportos biztonsági azonosítója |A SidHistory attribútum a felhasználók és csoportok a kezelt tartományban a megfelelő elsődleges felhasználó vagy csoport SID a helyszíni tartományi egyezik van beállítva. Ez a funkció segítségével könnyebben növekedési-és-shift a helyszíni alkalmazások a felügyelt tartományra, mert nem kell újra-hozzáférés-vezérlési lista erőforrások. |

> [!NOTE]
> **Jelentkezzen be a felügyelt tartományra az UPN-formátum:** a SAMAccountName attribútum lehet, hogy az egyes felhasználói fiókok a felügyelt tartományok az automatikusan generált. Ha több felhasználó található a azonos mailNickname attribútum, vagy a felhasználók rendelkeznek-e túlságosan hosszú UPN-előtagok, ezek a felhasználók számára a SAMAccountName lehet automatikusan generált. Ezért a SAMAccountName (például CONTOSO100\joeuser) formátuma nem mindig jelentkezzen be a tartomány megbízható módot. Automatikusan létrehozott SAMAccountName a felhasználói UPN előtag eltérhet. Az UPN-formátumban (például "joeuser@contoso100.com") megbízhatóan bejelentkezni a felügyelt tartományra.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>A felhasználói fiókok címtárattribútum-leképezésben
A következő táblázat bemutatja, hogyan adott attribútumok a felhasználói objektumok az Azure AD-bérlő a megfelelő attribútumokat a felügyelt tartományok lettek szinkronizálva.

| Az Azure AD-bérlő felhasználói attribútum | A felügyelt tartományok felhasználói attribútum |
|:--- |:--- |
| AccountEnabled |userAccountControl (beállítása vagy a bit ACCOUNT_DISABLED törlése) |
| city |l csomag |
| Ország |CO |
| Szervezeti egység |Szervezeti egység |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Beosztás |Cím |
| mail |mail |
| mailNickname |Az msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (néha lehet automatikusan generált) |
| Mobileszköz |Mobileszköz |
| objektumazonosító |Az msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (beállítása vagy a bit DONT_EXPIRE_PASSWORD törlése) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Irányítószám |Irányítószám |
| preferredLanguage |preferredLanguage |
| state |St |
| StreetAddress |StreetAddress |
| Vezetéknév |sorozatszám |
| TelephoneNumber |TelephoneNumber |
| UserPrincipalName |UserPrincipalName |

### <a name="attribute-mapping-for-groups"></a>A csoportok címtárattribútum-leképezésben
A következő táblázat bemutatja, hogyan adott attribútumok csoport az Azure AD-bérlő objektumok lettek szinkronizálva a felügyelt tartományok megfelelő attribútumokat.

| Az Azure AD-bérlőben attribútum | A felügyelt tartományok csoport attribútum |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (néha lehet automatikusan generált) |
| mail |mail |
| mailNickname |Az msDS-AzureADMailNickname |
| objektumazonosító |Az msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| SecurityEnabled |GroupType |

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Az Azure AD-bérlő a felügyelt tartomány nem szinkronizált objektumok
Ez a cikk az előző szakaszban leírtak nincs a felügyelt tartományok vissza az Azure AD-bérlő a Nincs szinkronizálás. Előfordulhat, hogy [hozzon létre egy egyéni szervezeti egységet (OU)](active-directory-ds-admin-guide-create-ou.md) a kezelt tartományban. Létrehozhat további, az egyéb szervezeti egységek, felhasználók, csoportok vagy ezeket egyéni szervezeti egységek belül szolgáltatásfiókok. Nincs egyéni szervezeti egységek belül létrehozott objektumok lettek szinkronizálva vissza az Azure AD-bérlő. Ezek az objektumok csak a felügyelt tartományon belüli használatra érhetők el. Ezért ezek az objektumok ne legyenek láthatók, Azure AD Graph API-t, az Azure AD PowerShell-parancsmagok használatával, vagy használja az Azure AD felügyeleti felhasználói Felületét.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Szolgáltatások – Azure AD tartományi szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyvek - Azure AD tartományi szolgáltatások](active-directory-ds-scenarios.md)
* [Azure AD tartományi szolgáltatások hálózati szempontjai](active-directory-ds-networking.md)
* [Ismerkedés az Azure AD tartományi szolgáltatások](active-directory-ds-getting-started.md)
