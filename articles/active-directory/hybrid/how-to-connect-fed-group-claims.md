---
title: Csoportos jogcímek konfigurálása a Azure Active Directorykal rendelkező alkalmazásokhoz | Microsoft Docs
description: Információk a csoportos jogcímek konfigurálásáról az Azure AD-vel való használatra.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170487"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Azure Active Directory (nyilvános előzetes verzió) alkalmazásokhoz tartozó jogcímek konfigurálása

A Azure Active Directory az alkalmazásokon belüli használatra a felhasználók csoport tagsági adatait is megadhatja a jogkivonatokban.  Két fő mintázat támogatott:

- A Azure Active Directory objektumazonosító (OID) attribútummal azonosított csoportok (általánosan elérhető)
- Active Directory (AD) szinkronizált csoportok és felhasználók (nyilvános előzetes verzió) sAMAccountName vagy GroupSID attribútumai által azonosított csoportok

> [!IMPORTANT]
> Az előzetes verzió funkcióinak megjegyzései többek között a következők:
>
>- A helyszínen szinkronizált sAMAccountName-és biztonsági azonosítók (SID-ek) használatának támogatása lehetővé teszi a meglévő alkalmazások áthelyezését AD FS és más identitás-szolgáltatókból. Az Azure AD-ben felügyelt csoportok nem tartalmazzák a jogcímek kibocsátásához szükséges attribútumokat.
>- Nagyobb szervezeteknél a felhasználók azon csoportjai száma, amelyeknek a felhasználó tagja, túllépheti azt a korlátot, amelyet a Azure Active Directory felvesz a tokenbe. 150 csoport egy SAML-tokenhez és egy JWT 200. Ez előre nem látható eredményekhez vezethet. Ha ez egy lehetséges probléma, javasoljuk a tesztelést, és ha szükséges, várjon, amíg az alkalmazáshoz tartozó csoportokra korlátozni szeretné a jogcímeket.  
>- Az új alkalmazások fejlesztéséhez, illetve azokban az esetekben, amikor az alkalmazás konfigurálható, és ahol nincs szükség beágyazott csoport támogatására, javasoljuk, hogy az alkalmazáson belüli engedélyezés a csoportok helyett az alkalmazás szerepkörein alapuljon.  Ez korlátozza a tokenbe bekerülő információk mennyiségét, biztonságosabb, és elkülöníti a felhasználói hozzárendelést az alkalmazás konfigurációjától.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>A AD FS és más identitás-szolgáltatótól áttelepítésre kerülő alkalmazások jogcímeinek csoportosítása

Számos olyan alkalmazás, amely a AD FS való hitelesítésre van konfigurálva, a csoporttagság-információkra támaszkodik a Windows AD Group-attribútumok formájában.   Ezek az attribútumok a csoport sAMAccountName, amelyek a tartománynevek vagy a Windows-csoport biztonsági azonosítója (GroupSID) alapján lehetnek minősítettek.  Ha az alkalmazás összevonása AD FS, a AD FS a TokenGroups függvény használatával kéri le a felhasználó csoporttagságait.

Ahhoz, hogy az alkalmazás megkapja az AD FStól kapott jogkivonatot, a csoport-és szerepkör-jogcímek nem a csoport Azure Active Directory objectID, hanem a tartomány minősített sAMAccountName is tartalmazhatnak.

A csoportos jogcímek által támogatott formátumok a következők:

- **Azure Active Directory csoport ObjectId** (Minden csoport számára elérhető)
- **sAMAccountName** (Elérhető a Active Directoryról szinkronizált csoportok számára)
- **NetbiosDomain\sAMAccountName** (Elérhető a Active Directoryról szinkronizált csoportok számára)
- **DNSDomainName\sAMAccountName** (Elérhető a Active Directoryról szinkronizált csoportok számára)
- Helyszíni **csoport biztonsági azonosítója** (Elérhető a Active Directoryról szinkronizált csoportok számára)

> [!NOTE]
> a sAMAccountName és a helyszíni SID attribútumok csak a Active Directory szinkronizált csoport objektumain érhetők el.   Azure Active Directory vagy Office 365 létrehozott csoportokban nem érhetők el.   A Azure Active Directoryban konfigurált alkalmazások szinkronizált helyszíni csoportok attribútumai csak szinkronizált csoportok számára válnak elérhetővé.

## <a name="options-for-applications-to-consume-group-information"></a>Beállítások a csoport adatainak felhasználásához

A csoportok adatainak beszerzésének egyik módja a Graph groups végpont meghívása, hogy a csoporttagság beolvasható legyen a hitelesített felhasználó számára. Ez a hívás biztosítja, hogy a felhasználó összes csoportja elérhető legyen, még akkor is, ha nagy számú csoport vesz részt, és az alkalmazásnak minden olyan csoportot fel kell sorolnia, amely a felhasználó tagja.  A csoport enumerálása ezután független a tokenek méretének korlátaitól.

Ha azonban egy meglévő alkalmazás már a kapott jogcímek jogcímei alapján a csoport adatait is felhasználja, Azure Active Directory számos különböző jogcím-beállítással konfigurálható az alkalmazás igényeinek megfelelően.  vegye figyelembe a következő lehetőségeket:

- Ha csoporttagság használata az alkalmazáson belüli hitelesítéshez, érdemes a csoport ObjectID használni, amely nem módosítható és egyedi Azure Active Directory és minden csoport számára elérhető.
- Ha a helyszíni csoport sAMAccountName használja az engedélyezéshez, használjon tartományneveket a tartomány minősített neveivel.  a felmerülő helyzetekben a nevek ütköznek. a saját sAMAccountName egyedi lehet egy Active Directory tartományon belül, de ha egynél több Active Directory tartomány van szinkronizálva egy Azure Active Directory Bérlővel, több csoport is rendelkezhet ugyanazzal a névvel.
- Vegye fontolóra az [alkalmazási szerepkörök](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) használatát, hogy a csoporttagság és az alkalmazás közötti átirányítási réteget biztosítson.   Az alkalmazás ezután belső engedélyezési döntéseket tesz a tokenben lévő szerepkör-kagylók alapján.
- Ha az alkalmazás úgy van konfigurálva, hogy lekérje a Active Directory szinkronizált csoport-attribútumokat, és a csoport nem tartalmazza azokat az attribútumokat, amelyeket a jogcímek nem tartalmaznak.
- A jogkivonatokban lévő jogcímek beágyazott csoportokat tartalmaznak.   Ha a felhasználó a GroupB tagja, és a GroupB a Groupa tagja, akkor a felhasználóhoz tartozó jogcímek a Groupe és a GroupB is tartalmazzák. A beágyazott csoportok és a nagyszámú csoporttagság nagy mennyiségű csoporttal rendelkező felhasználók számára a tokenek méretének növelése érdekében a tokenben felsorolt csoportok száma növelhető.   Azure Active Directory korlátozza azoknak a csoportoknak a számát, amelyeket a rendszer 150 tokenben bocsát ki az SAML-kijelentésekhez, és 200 a JWT, hogy megakadályozza a tokenek túl nagy mennyiségű megszerzését.  Ha a felhasználó nagyobb számú csoport tagja a korlátnál, a csoportok kikerülnek a diagram végpontra mutató hivatkozással a csoport adatainak beszerzéséhez.

> A Active Directoryról szinkronizált csoport attribútumainak használatának előfeltételei:   A csoportokat Azure AD Connect használatával kell szinkronizálni Active Directory.

A Azure Active Directory konfigurálásának két lépése van Active Directory csoportoknak.

1. **Csoportok nevének szinkronizálása Active Directory** Mielőtt Azure Active Directory a csoport-vagy szerepkör-jogcímek csoportjai számára a csoportok nevét vagy a helyszíni csoport biztonsági azonosítóját, a szükséges attribútumokat szinkronizálni kell a Active Directory.  Azure AD Connect 1.2.70 vagy újabb verziót kell futtatnia.   A 1.2.70 verzió előtt Azure AD Connect szinkronizálja a csoport objektumait Active Directoryból, de alapértelmezés szerint nem tartalmazza a szükséges csoportnév-attribútumokat.  Frissítsen az aktuális verzióra.

2. **Az alkalmazás regisztrációjának konfigurálása a Azure Active Directoryban a jogkivonatokban lévő csoportos jogcímek belefoglalásához** A csoportos jogcímek a portál vállalati alkalmazások szakaszában konfigurálhatók a katalógus vagy a nem katalógus SAML SSO-alkalmazáshoz, vagy az alkalmazás-jegyzékfájl használatával az alkalmazás-regisztráció szakaszban.  Ha a csoport jogcímeit az alkalmazás jegyzékfájljában szeretné konfigurálni, tekintse meg az alábbi "Azure Active Directory alkalmazás-regisztráció konfigurálása a csoport attribútumaihoz" című szakaszt.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>SAML-alkalmazásokhoz tartozó jogcímek konfigurálása SSO-konfiguráció használatával

Ha egy katalógus vagy nem katalógus SAML-alkalmazáshoz állít be jogcímeket, nyissa meg a vállalati alkalmazásokat, kattintson az alkalmazásra a listában, majd válassza az egyszeri bejelentkezés konfigurálása lehetőséget.

Jelölje be a "jogkivonat által visszaadott csoportok" elem melletti Szerkesztés ikont.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

A választógombok használatával kiválaszthatja, hogy mely csoportok szerepeljenek a jogkivonatban

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Választás | Leírás |
|----------|-------------|
| **Minden csoport** | Biztonsági csoportokat és terjesztési listát bocsát ki.   Azt is eredményezi, hogy a felhasználó hozzá van rendelve egy "wids" jogcímhez, és minden olyan alkalmazási szerepkört, amelyet a felhasználó hozzárendelt a szerepkör-jogcímben való kibocsátáshoz. |
| **Biztonsági csoportok** | Biztonsági csoportokat bocsát ki, a felhasználó tagja a groups jogcímnek |
| **Terjesztési listák** | Kibocsátja a terjesztési listát, amely a felhasználó tagja |
| **Címtárbeli szerepkörök** | Ha a felhasználó olyan címtárbeli szerepköröket kap, amelyeket a rendszer "wids" jogcímként bocsát ki (a csoportok jogcím nem lesz kibocsátva) |

Például az összes olyan biztonsági csoport kibocsátásához, amely tagja a felhasználónak, válassza a biztonsági csoportok lehetőséget.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Ha az Azure AD objectIDs helyett Active Directory szinkronizált Active Directory attribútumokkal szeretne csoportokat kibocsátani, válassza ki a kívánt formátumot a legördülő listából.  Ez helyettesíti a jogcímek objektum-AZONOSÍTÓját a csoportok nevét tartalmazó karakterlánc-értékekkel.   A jogcímek között csak a Active Directory szinkronizált csoportok lesznek felszámítva.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>Speciális beállítások

A csoportos jogcímek kibocsátása a speciális beállítások területen található beállításokkal módosítható

Szabja testre a csoportjogcím nevét:  Ha be van jelölve, egy másik jogcím-típus adható meg a csoportok jogcímeihez.   A névtér mezőben adja meg a jogcím típusát a név mezőben, valamint a jogcím opcionális névterét.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Egyes alkalmazásokban a csoporttagság információit kell megjeleníteni a "szerepkör" jogcímben. A felhasználó csoportjait szerepkörökként is kibocsáthatja a "szerepkör-jogcímek kibocsátása" jelölőnégyzet bejelölésével.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Ha a csoportok adatai szerepkörként való kibocsátásának lehetősége van használatban, csak a csoportok jelennek meg a szerepkör-jogcímben.  A felhasználó által hozzárendelt összes alkalmazás-szerepkör nem jelenik meg a szerepkör-jogcímben.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Az Azure AD-alkalmazás regisztrációjának konfigurálása a csoport attribútumaihoz

A csoportos jogcímeket az [alkalmazás jegyzékfájljának](../../active-directory/develop/reference-app-manifest.md) [opcionális jogcímek](../../active-directory/develop/active-directory-optional-claims.md) szakaszában is lehet konfigurálni.

1. A portálon – > Azure Active Directory – > alkalmazás regisztrációi – > válassza az alkalmazás – > jegyzékfájlt

2. Csoporttagság-jogcímek engedélyezése a groupMembershipClaim módosításával

   Az érvényes értékek a következők:

   - Összes
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Példa:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Alapértelmezés szerint a csoport ObjectIDs a rendszer a csoportos jogcím értékét fogja kiállítani.  Ha módosítani szeretné a jogcím értékét, hogy a tartalmazza a helyszíni csoport attribútumait, vagy módosítani szeretné a jogcím típusát a szerepkörre, használja a OptionalClaims-konfigurációt az alábbiak szerint:

3. Adja meg a csoport neve konfigurációjának választható jogcímeit.

   Ha azt szeretné, hogy a tokenben lévő csoportok tartalmazzák a helyszíni AD-csoport attribútumait a választható jogcímek szakaszban, adja meg, hogy melyik jogkivonat-típust kívánja alkalmazni, a kért választható jogcím nevét és a kívánt további tulajdonságokat.  Több jogkivonat-típus is szerepelhet:

   - a OIDC azonosító token idToken
   - a OAuth/OIDC hozzáférési token accessToken
   - SAML-tokenek Saml2Token.

   > [!NOTE]
   > A Saml2Token típusa SAML 1.1 és SAML 2.0 formátumú jogkivonatokra is vonatkozik.

   Minden releváns jogkivonat-típus esetében módosítsa a csoportok jogcímet a jegyzékfájl OptionalClaims szakaszának használatára. A OptionalClaims séma a következő:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Választható jogcímek sémája | Value |
   |----------|-------------|
   | **név:** | "Groups" értéknek kell lennie |
   | **forrás** | Nincs használatban. Kihagyás vagy a Null érték meghatározása |
   | **alapvető** | Nincs használatban. Kihagyás vagy a hamis meghatározása |
   | **additionalProperties:** | További tulajdonságok listája.  Az érvényes beállítások a következők: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   A additionalProperties csak a "sam_account_name", a "dns_domain_and_sam_account_name", a "netbios_domain_and_sam_account_name" egyike szükséges.  Ha egynél több van jelen, az első használatban van, és minden más figyelmen kívül lesz hagyva.

   Egyes alkalmazások a szerepkör-jogcímben szereplő felhasználóra vonatkozó csoportos adatokat igényelnek.  Ha módosítani szeretné a jogcím típusát egy csoport jogcímen egy szerepkör-jogcímre, adja hozzá a "emit_as_roles" elemet a további tulajdonságokhoz.  A csoport értékeit a rendszer a szerepkör-jogcímben fogja kiállítani.

   > [!NOTE]
   > Ha a "emit_as_roles" minden olyan alkalmazási szerepkört használ, amelyhez a felhasználó hozzá van rendelve, nem jelenik meg a szerepkör-jogcímben.

### <a name="examples"></a>Példák

Csoportok kibocsátása csoport neveként a OAuth hozzáférési jogkivonatokban dnsDomainName\SAMAccountName formátumban

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Az SAML-és OIDC-azonosító jogkivonatokban a netbiosDomain\samAccountName formátumban visszaadott csoportok nevének kibocsátása:

```json
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
