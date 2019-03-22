---
title: 'Az Azure Active Directory tartományi szolgáltatások: Szinkronizálás felügyelt tartományokban |} A Microsoft Docs'
description: Megismerheti a szinkronizálást az Azure Active Directory Domain Services által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: b5544be37015368173f3f8eb39288b7a97ae11c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58084997"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Szinkronizálás az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
A következő ábra szemlélteti a szinkronizálás működése az Azure AD Domain Services felügyelt tartomány.

![Szinkronizálás az Azure AD tartományi szolgáltatásokban](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Szinkronizálás a helyszíni címtárból az Azure AD-bérlővel
Azure AD Connect szinkronizálása a helyi felhasználói fiókok szinkronizálása, csoporttagságok szolgál, és a hitelesítő kivonatok az Azure AD-bérlővel. A felhasználói attribútumok például az egyszerű felhasználónév-fiókok és a helyszíni biztonsági azonosító (security identifier) szinkronizált. Ha Azure AD tartományi szolgáltatásokat használja, az NTLM és Kerberos hitelesítéshez szükséges tanúsítványkivonatok örökölt is szinkronizálódnak az Azure AD-bérlővel.

Késleltetve visszaírt állít be, ha az Azure AD-címtár bekövetkezett változások vannak szinkronizálva a helyszíni Active Directoryban. Például, ha módosítja a jelszavát, az Azure AD önkiszolgáló jelszó-felügyelettel, a módosított jelszó frissül a helyszíni AD-tartományhoz.

> [!NOTE]
> Ellenőrizze, hogy az összes ismert hibájának mindig használja az Azure AD Connect legújabb verzióját.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Az Azure AD-bérlőből a felügyelt tartományhoz való szinkronizálás
Felhasználói fiókok, a csoporttagságokat és a hitelesítési adatok kivonatait az Azure AD-bérlő a rendszer szinkronizálja az Azure AD tartományi szolgáltatásokkal felügyelt tartományban. Ez a szinkronizálási folyamat automatikusan sor kerül. Nem kell konfigurálnia, figyelése, vagy a szinkronizálási folyamat kezeléséhez. Kezdeti szinkronizálás, néhány nap alatt az Azure AD-címtár objektumok számától függően eltarthat néhány órát. Kezdeti szinkronizálás befejeződése után frissíteni kell a felügyelt tartományhoz az Azure ad-ben végrehajtott módosítások körülbelül 20-30 percet vesz igénybe. A szinkronizálás időköze vonatkozik a jelszó módosítására, vagy ha attribútumok az Azure AD-ben végrehajtott módosításokat.

A szinkronizálási folyamat akkor is egy-way/egyirányú jellegűek. A felügyelt tartományra csak nagymértékben olvasható kivételével minden egyéni szervezeti egységek hoz létre. Ezért a felhasználói attribútumok, felhasználói jelszavakat vagy a felügyelt tartományon belüli csoporttagságok, nem végezhet módosításokat. Ennek eredményeképpen nem létezik a módosításokat a felügyelt tartományból az Azure AD-bérlő nincs fordított szinkronizálás.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Egy helyszíni Többerdős környezetben való szinkronizálás
Számos szervezet rendelkezik több fiókerdővel álló viszonylag összetett helyszíni identitás-infrastruktúrát. Az Azure AD Connect támogatja a felhasználók, csoportok és a hitelesítési adatok kivonatait Többerdős környezetben, az Azure AD-bérlővel való szinkronizálását.

Ezzel szemben az Azure AD-bérlő egy sokkal egyszerűbb, és egybesimított névtér. Ahhoz, hogy a felhasználók megbízhatóan hozzáférhessenek az Azure AD által védett alkalmazások, egyszerű felhasználónév ütközések feloldásához különböző erdőkben találhatók a felhasználói fiókok között. Az Azure AD tartományi szolgáltatásokkal felügyelt tartományban vándorlási zárja be az Azure AD-bérlővel nagyon hasonló. Egybesimított Szervezetiegység-struktúrára jelenik meg a felügyelt tartományra. Az összes felhasználói fiókokat és csoportokat a "AADDC felhasználók" tárolót, annak ellenére, hogy a különböző helyszíni tartományok és erdők szinkronizált tárolja. Előfordulhat, hogy konfigurálta a hierarchikus OU helyszíni struktúra. A felügyelt tartomány még egy egyszerű, egybesimított Szervezetiegység-struktúrára.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>A kizárások – Mi a felügyelt tartomány nincs szinkronizálva
A következő objektumok vagy attribútumok nincsenek szinkronizálva az Azure AD-bérlővel, vagy a felügyelt tartomány:

* **Kizárt attribútumai:** Előfordulhat, hogy ki szeretne zárni bizonyos attribútumok az Azure AD-bérlő szinkronizálja a helyszíni tartomány Azure AD Connect használatával. Ezek az attribútumok kizárt nem érhető el a felügyelt tartományra.
* **Csoportházirendek:** A felügyelt tartományra a helyszíni tartomány konfigurált csoportházirendek nincsenek szinkronizálva.
* **SYSVOL-megosztás:** Hasonlóképpen a Sysvol-megosztás a helyszíni tartomány tartalmát nincsenek szinkronizálva a felügyelt tartományra.
* **Számítógép-objektumok:** Számítógép-objektumokat a helyszíni tartományhoz csatlakoztatott számítógépek esetében a rendszer nem szinkronizálja a felügyelt tartományra. Ezeket a számítógépeket a felügyelt tartomány bizalmi kapcsolattal rendelkezik, és nem csak a helyi tartományhoz tartoznak. A felügyelt tartományban lévő számítógép-objektumok csak a számítógépeken, explicit módon tartomány-csatlakozott a felügyelt tartományhoz találja.
* **Felhasználók és csoportok SidHistory attribútumai:** Az elsődleges felhasználói elsődleges csoportos biztonsági azonosítókkal való létrehozásához a helyszíni tartomány szinkronizálja a felügyelt tartományhoz. Azonban a felhasználók és csoportok meglévő SidHistory attribútumok nincsenek szinkronizálva a helyszíni tartomány a felügyelt tartományra.
* **Szervezeti egység (OU) struktúrák:** Ne szinkronizáljon a felügyelt tartományra a helyszíni tartomány megadott szervezeti egységek. A felügyelt tartomány két beépített szervezeti egységek találhatók. Alapértelmezés szerint a felügyelt tartomány rendelkezik egy egybesimított Szervezetiegység-struktúrára. Dönthet úgy, hogy azonban [a felügyelt tartományhoz hozzon létre egy egyéni szervezeti](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Hogyan adott attribútumok legyenek szinkronizálva a felügyelt tartomány
A következő táblázat néhány gyakori attribútumok és ismerteti, hogyan akkor a rendszer szinkronizálja a felügyelt tartományra.

| A felügyelt tartomány attribútum | Forrás | Megjegyzések |
|:--- |:--- |:--- |
| Egyszerű felhasználónév |A felhasználó UPN attribútum az Azure AD-bérlőben |Az egyszerű felhasználónév attribútum az Azure AD-bérlőből szinkronizálása a felügyelt tartományra van. Ezért jelentkezzen be a felügyelt tartományra a legmegbízhatóbb módszer az UPN-használ. |
| SAMAccountName |Felhasználó mailNickname attribútum az Azure AD-bérlővel, vagy automatikusan generált |A SAMAccountName attribútum forrása a mailNickname attribútum az Azure AD-bérlőben. Ha több felhasználói fiók rendelkezik az azonos mailNickname attribútum, a SAMAccountName, automatikusan létrehozott. Ha a felhasználó mailNickname vagy UPN-előtagja hosszabb 20 karakternél, a SAMAccountName jön létre automatikusan SAMAccountName attribútumok a 20 karakterszámot kielégítéséhez. |
| Jelszavak |Az Azure AD-bérlőből a felhasználó jelszavát |(Más néven kiegészítő hitelesítő adatok) az NTLM vagy Kerberos-hitelesítéshez szükséges hitelesítő kivonatokat a rendszer szinkronizálja a az Azure AD-bérlőjében. Ha az Azure AD-bérlő szinkronizált bérlők, a ezeket a hitelesítő adatokat a helyszíni tartomány forrásai. |
| Elsődleges felhasználó/csoport biztonsági azonosítója |Auto-generated |Elsődleges biztonsági azonosítója a felhasználói és csoportfiókok jön létre automatikusan a felügyelt tartományban. Ez az attribútum nem egyezik az elsődleges felhasználói és csoportos biztonsági azonosítója, az objektum a helyszíni AD-tartományhoz. Ez az eltérés oka, hogy a felügyelt tartomány eltérő SID-névtérrel rendelkező, mint a helyszíni tartománnyal. |
| Felhasználók és csoportok SID-előzmények |A helyszíni elsődleges felhasználói és csoportos biztonsági azonosítója |A felügyelt tartományban lévő felhasználók és csoportok SidHistory attribútumát értéke egyezik meg a megfelelő elsődleges felhasználó vagy csoport SID a helyszíni tartományban. Ez a funkció segítségével könnyebben lift-and-shift a felügyelt tartományra a helyszíni alkalmazások, mivel nem kell újra-ACL-erőforrásokat. |

> [!NOTE]
> **Jelentkezzen be a felügyelt tartományra a egyszerű felhasználónév formátuma:** A SAMAccountName attribútumot lehet néhány felhasználói fiók a felügyelt tartomány számára automatikusan létrehozott. Ha több felhasználó rendelkezik ugyanazon a mailNickname attribútum, vagy a felhasználók rendelkeznek az UPN-előtagok túl hosszú, ezek a felhasználók a SAMAccountName lehet automatikusan létrehozott. Ezért a SAMAccountName formátum (például CONTOSO100\joeuser) nincs mindig megbízhatóan bejelentkezni a tartományba. Felhasználók a SAMAccountName automatikusan generált UPN előtag eltérhet. Az egyszerű felhasználónév formátumban (például "joeuser@contoso100.com") megbízhatóan bejelentkezni a felügyelt tartományhoz.

### <a name="attribute-mapping-for-user-accounts"></a>A felhasználói fiókokhoz attribútumleképezés
Az alábbi táblázat mutatja be, hogyan adott attribútumok az Azure AD-bérlő objektumokat a rendszer szinkronizálja a felügyelt tartományhoz kapcsolódó attribútumok felhasználó.

| Az Azure AD-bérlőben a felhasználói attribútum | A felügyelt tartomány felhasználói attribútum |
|:--- |:--- |
| accountEnabled |userAccountControl (állítja be, vagy törli a ACCOUNT_DISABLED bites) |
| city |l |
| Ország |CO |
| Szervezeti egység |Szervezeti egység |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Beosztás |cím |
| levelezés |levelezés |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (néha lehet automatikusan létrehozott) |
| mobil |mobil |
| objektumazonosító |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |tartományvezérlőjére |
| passwordPolicies |userAccountControl (állítja be, vagy törli a DONT_EXPIRE_PASSWORD bites) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Irányítószám |Irányítószám |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Vezetéknév |sorozatszám |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>A csoportok attribútumleképezés
Az alábbi táblázat mutatja be, hogyan adott attribútumok az Azure AD-bérlő objektumokat a rendszer szinkronizálja a felügyelt tartományhoz kapcsolódó attribútumok csoport.

| Az Azure AD-bérlőben attribútum | A felügyelt tartomány csoport attribútum |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (néha lehet automatikusan létrehozott) |
| levelezés |levelezés |
| mailNickname |msDS-AzureADMailNickname |
| objektumazonosító |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |tartományvezérlőjére |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Jelszó Jelszókivonat szinkronizálása és biztonsági szempontok
Ha engedélyezi az Azure AD tartományi szolgáltatásokat, az Azure AD-címtár állít elő, és tárolja a jelszókivonatokat az NTLM és Kerberos-kompatibilis formátumban. 

A meglévő felhőalapú felhasználói fiókok Azure AD soha nem tárolja a titkosítatlan szöveges jelszót, mivel ezek a kivonatok nem automatikusan létrejön. Ezért a Microsoft megköveteli [alaphelyzetbe állítása és módosítása a jelszavukat felhőfelhasználók](active-directory-ds-getting-started-password-sync.md) ahhoz, hogy azok a jelszókivonatok jön létre, és az Azure AD-ben tárolt. Az Azure AD tartományi szolgáltatások engedélyezése után az Azure AD-ben létrehozott felhőalapú felhasználói fiókok a jelszókivonatokat jönnek létre és tárolja az NTLM és Kerberos kompatibilis formátumban. 

A felhasználói fiókok szinkronizálása a helyszíni AD-bA az Azure AD Connect-szinkronizálás, kell [konfigurálása az Azure AD Connect szinkronizálja a jelszókivonatokat az NTLM és Kerberos kompatibilis formátumokban](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Az NTLM és Kerberos kompatibilis jelszókivonatokat mindig tárolódnak a titkosított módon az Azure ad-ben. Ezek a kivonatok úgy, hogy csak az Azure AD tartományi szolgáltatásokhoz férhet hozzá a visszafejtési kulcs titkosított. Nincs más szolgáltatás vagy az Azure AD-összetevő nem a visszafejtési kulcsok a hozzáférést. A titkosítási kulcsokat a egyedi-Azure AD-bérlővel. Az Azure AD Domain Services, a tartományvezérlőket ahhoz a felügyelt tartományra a jelszókivonatok szinkronizálása. Ezek a jelszavak kivonatait tárolásának és védelmének tartományvezérlőkön ezek hogyan jelszavak tárolásának és védelmének a Windows Server AD tartományvezérlőkön hasonló. Ezek a felügyelt tartományvezérlőkön a lemezek inaktív titkosítása.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objektumok, amelyek nincsenek szinkronizálva az Azure AD-bérlővel a felügyelt tartományból
Ez a cikk egy előző szakaszban leírt nincs nem szinkronizálása a felügyelt tartományból térjen vissza az Azure AD-bérlő. Dönthet úgy, hogy [hozzon létre egy egyéni szervezeti egység (OU)](active-directory-ds-admin-guide-create-ou.md) a felügyelt tartományban. Létrehozhat további, más szervezeti egységek, felhasználók, csoportok vagy szolgáltatásfiókok belül ezeket egyéni szervezeti egységekhez. Az egyéni szervezeti egységek belül létrehozott objektumok egyike vannak szinkronizálva az Azure AD-bérlővel. Ezek az objektumok csak a felügyelt tartományon belüli használatra érhetők el. Ezért ezek az objektumok nem láthatók az Azure AD PowerShell-parancsmagok, az Azure AD Graph API-t, vagy pedig az Azure AD felügyeleti felhasználói Felületét.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Szolgáltatások – Azure AD tartományi szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyv – Azure AD tartományi szolgáltatások](active-directory-ds-scenarios.md)
* [Hálózati megfontolások az Azure AD tartományi szolgáltatások](active-directory-ds-networking.md)
* [Az Azure AD Domain Services használatának első lépései](active-directory-ds-getting-started.md)
