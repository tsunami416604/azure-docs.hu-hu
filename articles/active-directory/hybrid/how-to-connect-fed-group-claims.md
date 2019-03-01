---
title: Alkalmazások csoport-jogcímek konfigurálása az Azure Active Directory |} A Microsoft Docs
description: Információk az Azure AD-csoport jogcímeket konfigurálása.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 8e4f9e76baf07e6ea2cb4cccb63ed0a9add5d767
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57012048"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Alkalmazások csoport-jogcímek konfigurálása az Azure Active Directoryval (nyilvános előzetes verzió)

Az Azure Active Directory biztosíthat a felhasználók csoporttagsági információit a jogkivonatokban alkalmazásaiban.  Két fő minták támogatottak:

- Az Azure Active Directory-objektumazonosító (OID) (általánosan elérhető) által azonosított csoportok
- Csoportok SAMAccountName vagy GroupSID által azonosított Active Directory (AD) szinkronizált csoportok és felhasználók (nyilvános előzetes verzió)

>[!Note]
>Használja a neveket és a helyszíni biztonsági azonosítók (SID) támogatása lehetővé teszi, hogy az AD FS már meglévő alkalmazások áthelyezését.    Az Azure AD-ban kezelt csoportok nem gridre bocsáthatja ki ezeket a jogcímeket szükséges attribútumokat tartalmazza.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-idps"></a>Csoport jogcímek az AD FS és az egyéb identitásszolgáltató-ről alkalmazások

Számos alkalmazás, amely az AD FS-sel a hitelesítést a csoporttagsági információkat AD Windows csoportattribútumok formájában támaszkodnak.   Ezek az attribútumok a SAMAccountName, esetleg által-tartománynevet vagy a Windows-csoport biztonsági azonosítója.  Ha az alkalmazás össze van vonva az AD FS-sel, az AD FS a felhasználó csoporttagságát beolvasni a TokenGroups függvényt használja.

Az alkalmazás kapna, az AD FS jogkivonat megfelelően, a előfordulhat, hogy a csoport és a szerepkör jogcím kibocsátott a tartományt tartalmazó minősített, a csoport az Azure Active Directory-objektumazonosító helyett az SAMAccountName.

Csoport jogcímek a támogatott formátumok a következők:

- **Az Azure Active Directory GroupObjectId** (az összes csoport érhető el)
- **SAMAccountName** (az Active Directoryból szinkronizált csoportok számára érhető el)
- **NetbiosDomain\samAccountName** (az Active Directoryból szinkronizált csoportok számára érhető el)
- **DNSDomainName\samAccountName** (az Active Directoryból szinkronizált csoportok számára érhető el)

>[!NOTE]
>SAMAccountName és OnPremisesGroupSID attribútumok az Active Directoryból szinkronizált objektumok csak érhetők el.   Nem érhetők el az Azure Active Directory vagy Office 365-öt létrehozott csoportok.   A helyi csoport attribútumok az alkalmazások csak a szinkronizált csoportokhoz megkapja őket.

## <a name="options-for-applications-to-consume-group-information"></a>Alkalmazások felhasználhatják őket a csoportinformációk lehetőségei

Egy csoport információkhoz juthat az alkalmazások módja annak érdekében, hogy a hitelesített felhasználó csoporttagságát beolvasni a Graph-csoportok végpont meghívására. Ez a hívás biztosítja, hogy minden az egy felhasználó csoportjának tagja esetén érhetők el még nincsenek érintett csoportok nagy számú, és az alkalmazásnak kell a felhasználó tagja összes csoport enumerálása.  Csoport enumerálása majd nem függ a token fájlméretre vonatkozó korlátozások.

Azonban ha már meglévő alkalmazás vár fogyasztanak csoport információkat kap a jogkivonat jogcímeiben keresztül, az Azure Active Directory konfigurálható számos különböző jogcímek lehetőséget az alkalmazás igényeinek.  Vegye figyelembe a következő beállításokat:

- Csoporttagság használata egy alkalmazás engedélyezési célra (e csoport tagságát lekérte a token vagy gráf), ezért célszerű az a csoport ObjectID, amely nem módosítható és egyedi az Azure Active Directory és az összes csoport érhető el .
- Ha a csoport SAMAccountName használata a hitelesítéshez, használja a minősített tartománynevek;  rendelkezik kevésbé eredő helyzetekben esélyét voltak nevek csomópontjánál. SAMAccountName saját egyedi Active Directory-tartományban lévő, de egynél több Active Directory-tartomány az Azure Active Directory-bérlő szinkronizálva van-e egynél több azonos nevű csoport lehetőséget.
- Fontolja meg [alkalmazás-szerepkörök](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) a csoporttagságot és az alkalmazás között kiosztótábla réteget biztosít.   Az alkalmazás ezután teszi szerepkör vájókagyló a jogkivonat alapján belső felhasználását engedélyezési döntésekhez.
- Ha az alkalmazás az Active Directoryból szinkronizált attribútumok csoport beolvasására konfigurált, és a egy csoport nem tartalmazhat. Ezek az attribútumok nem fog szerepelni a jogcímeket.
- A jogkivonatok csoportjogcímek közé tartozik a beágyazott csoportokat.   Ha egy felhasználó tagja GroupB és GroupB GroupA tagja, majd a csoport jogcímek a felhasználó tartalmazni fogja a GroupA és GroupB. A szervezetek számára a beágyazott csoportok gyakori használati és a felhasználói csoporttagságok nagy számú a jogkivonatban szereplő csoportok száma növelhető a jogkivonat mérete.   Az Azure Active Directory a SAML helyességi feltételek 150 és 200 a JWT jogkivonatot eredményez, csoportok száma korlátozza.

>Az Active Directoryból szinkronizált csoport attribútumok használatának előfeltételei:   A csoportok szinkronizálni kell az Active Directoryból az Azure AD Connect használatával.

Gridre bocsáthatja ki az Active Directory-csoportok a csoport nevét az Azure Active Directory konfigurálása két lépésből áll.

1. **Csoport neve az Active Directory szinkronizálása** előtt az Azure Active Directory kibocsátható a csoport nevét, vagy a csoport vagy szerepkör CSOPORTBIZTONSÁGI jogcímeken a helyi csoport, a szükséges attribútumokkal kell szinkronizálni az Active Directory.  Az Azure AD Connect 1.2.70 verzióját kell futtatnia vagy újabb.   Korábbi 1.2.70 az Azure AD Connect th objektumok az Active Directoryból szinkronizálva lesznek, de nem tartalmazza a szükséges csoport nevük alapértelmezés szerint.  Frissítenie kell az aktuális verzióra.

2. **Az alkalmazásregisztráció konfigurálása az Azure Active Directory-csoport jogcímeket tartalmaznak a jogkivonatokban** csoportjogcímek lehet vagy a vállalati alkalmazások szakaszban a portál egy katalógusban vagy a katalógusban nem szereplő SAML SSO alkalmazáshoz konfigurált vagy Az alkalmazásjegyzék használja az alkalmazást az Alkalmazásregisztrációk szakaszban.  Csoport jogcímek konfigurálása az application manifest itt talál: "Az Azure Active Directory Alkalmazásregisztráció csoport attribútumok konfigurálása" alatt.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>Csoport jogcímek, az alkalmazások SAML SSO-konfiguráció beállítása

Egy katalógusban vagy a katalógusban nem szereplő SAML alkalmazáshoz csoportjogcímek konfigurálásához nyissa meg a vállalati alkalmazások, az alkalmazásra a listában kattintson, és válassza az egyszeri bejelentkezés konfigurálását.

Kattintson a Szerkesztés ikonra, "A csoportok jogkivonatot adott vissza" mellett

![a jogcímek felhasználói felület](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Válassza ki, mely csoportok szerepelnie kell a jogkivonatot a választógombok segítségével

![a jogcímek felhasználói felület](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Kiválasztás | Leírás |
|----------|-------------|
| **Minden csoport** | Bocsát ki a biztonsági csoportokat és terjesztési sorolja fel.   Címtárbeli szerepkörök a felhasználó hozzá van rendelve egy "wids" jogcímet bocsátja ki, és bármilyen alkalmazás-szerepkörök, a felhasználó hozzá van rendelve a szerepkörök jogcímet bocsátja ki is okoz. |
| **Biztonsági csoportok** | Bocsát ki a felhasználó tagja a csoportok jogcímek biztonsági csoportok |
| **Terjesztési lista** | A felhasználó tagja terjesztési listák bocsát ki |
| **Címtárbeli szerepkört** | Ha a felhasználó hozzá van rendelve a címtárbeli szerepkört azok vannak kibocsátott, a "wids" jogcím (jogcím nem bocsátja ki csoportokat) |

Jelölje be például a felhasználó a tagja, biztonsági csoportok kibocsátható, biztonsági csoportok

![a jogcímek felhasználói felület](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

### <a name="advanced-options"></a>Speciális beállítások

A csoportjogcímek vannak kibocsátva módon módosíthatják a beállításokat a Speciális beállítások

Testre szabhatja a csoport jogcímszabály neve:  Ha a kiválasztott, egy másik jogcímtípushoz csoportjogcímek adható meg.   Adja meg a nevét és opcionális névtér a jogcím típusa a jogcím a névtér mező.

![a jogcímek felhasználói felület](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Csoportok az Active Directory segítségével kibocsátható attribútumok helyett az Azure AD objectid azonosítójának a "Visszaadandó csoportok nevei helyett azonosítók" jelölőnégyzetet, majd a legördülő listából válassza ki a formátumot.  Az Objektumazonosító, a jogcímeket, cseréli csoport nevét tartalmazó karakterlánc-értékeket.   A jogcímek csak az Active Directoryból szinkronizált csoportok fog szerepelni.

![a jogcímek felhasználói felület](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Egyes alkalmazásoknak a csoporttagsági információkat a "szerepkör" jogcím jelennek meg. A felhasználói csoportok szerepkörökként "Kibocsátható egy szerepkör jogcím-csoportok" négyzet bejelölésével opcionálisan küldik.  

![a jogcímek felhasználói felület](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

>[!NOTE]
>A csoport adatai szerepkörökként kibocsátható beállítást használja, ha csak a csoportok megjelennek a szerepkör jogcím.  Bármely alkalmazás-szerepkörök, a felhasználó hozzá van rendelve a szerepkör jogcím nem jelenik.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Az Azure AD alkalmazás regisztrálása csoport attribútumok konfigurálása  

Csoportjogcímek is megadható az a [választható jogcímek](../../active-directory/develop/active-directory-optional-claims.md) szakaszában a [Application Manifest](../../active-directory/develop/reference-app-manifest.md).

 1. A portálon az Azure Active Directory -> Alkalmazás -> -> Válassza ki a regisztrációk alkalmazás-jegyzékfájl >

 2. A groupMembershipClaim módosításával csoport tagsági jogcímek engedélyezése
 
    Az érvényes értékek a következők:

    - "All"
    - "SecurityGroup"
    - "DistributionList"
    - "DirectoryRole"

    Példa:

    ```
    "groupMembershipClaims": "SecurityGroup"
    ```
 
    Csoport objectid azonosítójának fog bocsátja ki a csoport alapértelmezés szerint a jogcím értéke.  A helyi csoport attribútumait tartalmazza, vagy módosítsa a jogcímtípus szerepkör jogcím értéke módosításához használja a következő OptionalClaims konfiguráció:

 3. Állítsa be a csoport konfigurációja nem kötelező jogcímekben.

    Ha szeretné a jogkivonat csoportok a helyszíni AD-csoport attribútumok az opcionális jogcímek szakaszban adja meg, melyik typ tokenu választható jogcímet kell alkalmazni, a kért választható jogcím és minden egyéb tulajdonságot, kívánt nevét tartalmazza.  Több tokentípusokat is listázva lehet:

    - idToken a OIDC azonosító jogkivonat
    - az OAuth/OIDC jogkivonat a hozzáférési tokent
    - Az SAML-jogkivonatok Saml2Token.

    >[!NOTE]
    >SAML1.1 és a SAML2.0 formátumú jogkivonatokat vonatkozik a Saml2Token típusa  

    Minden releváns token típusának módosítása a jogcímet a csoportok a jegyzékfájlban OptionalClaims területén. A OptionalClaims séma a következőképpen történik:

 ```
 {
    "name": "groups",
    "source": null,
    "essential": false,
    "additionalProperties": []
 }
 ```

 | Nem kötelező jogcímek séma | Érték |
 |----------|-------------|
 | **név:** | "Csoportok" kell lennie. |
 | **Forrás:** | Nincs használatban. Nincs megadva vagy null értéket adjon meg |
 | **alapvető:** | Nincs használatban. Nincs megadva, vagy adja meg a hamis |
 | **additionalProperties:** | További tulajdonságainak listája.  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

 In additionalProperties only one of "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name” are required.  Ha egynél több található, az első, és a többi figyelmen kívül hagyja.

 Egyes alkalmazásoknak a felhasználó a szerepkör jogcím csoport adatait.  Ha módosítani szeretné a szerepkör jogcím egy csoportból jogcímet a jogcím típusa, adja hozzá a további tulajdonságok "emit_as_roles".  A csoport értékeket fogja a szerepkör jogcím bocsátja ki.

 >[!NOTE]
 >"Emit_as_roles" használata egyetlen alkalmazás-szerepkör konfigurálva, hogy a felhasználó lesz hozzárendelve a szerepkör jogcím nem jelennek meg

### <a name="examples"></a>Példák

Csoportok kibocsátható, az OAuth hozzáférési tokenek dnsDomainName\SAMAccountName formátumban nevei

```
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Kibocsátható csoportnevek netbiosDomain\samAccountName formátumban kell visszaadni, SAML és OIDC azonosító-jogkivonatokat a jogcím a szerepkörök:

```
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>További lépések

[Mi az a hibrid identitás?](whatis-hybrid-identity.md)