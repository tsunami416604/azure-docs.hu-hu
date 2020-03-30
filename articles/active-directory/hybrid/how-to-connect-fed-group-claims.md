---
title: Alkalmazások csoportjogcímbeállításainak konfigurálása az Azure Active Directoryval | Microsoft dokumentumok
description: A csoportjogcímek Azure AD-vel való használatra történő konfigurálásáról.
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
ms.openlocfilehash: b8708aec1137836516852135412c4c7cec2feba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408402"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Alkalmazások csoportjogcímének konfigurálása az Azure Active Directoryval (nyilvános előzetes verzió)

Az Azure Active Directory a felhasználók csoporttagsági adatait az alkalmazásokon belüli használatra a jogkivonatokban biztosíthatja.  Két fő minta támogatott:

- Az Azure Active Directory objektumazonosító (OID) attribútuma által azonosított csoportok (általánosan elérhető)
- Az Active Directory (AD) szinkronizált csoportjainak és felhasználóinak sAMAccountName vagy GroupSID attribútumai által azonosított csoportok (Nyilvános előzetes verzió)

> [!IMPORTANT]
> Az előzetes verzió funkcióval kapcsolatban számos kikötést kell figyelembe venni:
>
>- A helyszíni rendszerből szinkronizált sAMAccountName és biztonsági azonosítóattribútumok (SID) használatának támogatása lehetővé teszi a meglévő alkalmazások áthelyezését az AD FS-ből és más identitásszolgáltatóktól. Az Azure AD-ben kezelt csoportok nem tartalmazzák a jogcímek emléséhez szükséges attribútumokat.
>- Nagyobb szervezetekben a felhasználók által tagként működő csoportok száma meghaladhatja azt a korlátot, amelyet az Azure Active Directory hozzáad egy jogkivonathoz. 150 csoport egy SAML-tokenhez és 200 egy JWT.150 groups for a SAML token, and 200 for a JWT. Ez előre nem látható eredményekhez vezethet. Ha a felhasználók nagy számú csoporttagsággal rendelkeznek, azt javasoljuk, hogy használja a jogcímekben kibocsátott csoportok korlátozását az alkalmazás megfelelő csoportjaira.  
>- Az új alkalmazás fejlesztés, vagy azokban az esetekben, ahol az alkalmazás konfigurálható, és ahol beágyazott csoport támogatás nem szükséges, azt javasoljuk, hogy az alkalmazáson belüli engedélyezés alkalmazáson belüli szerepkörök önnemcsoportok on alapul.  Ez korlátozza a jogkivonatba való bejutáshoz szükséges információk mennyiségét, biztonságosabb, és elválasztja a felhasználói hozzárendelést az alkalmazás konfigurációjától.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Az AD FS-ből és más identitásszolgáltatókból áttelepített alkalmazások csoportos jogcímének csoportosítása

Az AD FS szolgáltatással való hitelesítésre konfigurált számos alkalmazás a Windows AD csoport attribútumai formájában a csoporttagsági adatokra támaszkodik.   Ezek az attribútumok a tartománynév minősítésű sAMAccountName csoport vagy a Windows csoport biztonsági azonosítója (GroupSID).  Amikor az alkalmazás összevan egyítve az AD FS, AD FS használja a TokenGroups függvény a felhasználó csoporttagságok beolvasása.

Az AD FS-ből áthelyezett alkalmazásoknak azonos formátumú jogcímekre van szüksége. Csoport- és szerepkörjogcímek bocsáthatók ki az Azure Active Directoryból, amely a tartomány minősített sAMAccountName vagy az Active Directoryból szinkronizált GroupSID-et tartalmazza, nem pedig a csoport Azure Active Directory-objektumazonosítóját.

A csoportjogcímek támogatott formátumai a következők:

- **Azure Active Directory-csoport objectid** (elérhető az összes csoport)
- **sAMAccountName** (Az Active Directoryból szinkronizált csoportok számára érhető el)
- **NetbiosDomain\sAMAccountName** (az Active Directoryból szinkronizált csoportok számára érhető el)
- **DNSDomainName\sAMAccountName** (az Active Directoryból szinkronizált csoportok számára érhető el)
- **Helyszíni csoport biztonsági azonosítója** (az Active Directoryból szinkronizált csoportok számára érhető el)

> [!NOTE]
> Az sAMAccountName és a Helyszíni csoport SID-attribútumai csak az Active Directoryból szinkronizált csoportobjektumokon érhetők el.   Nem érhetők el az Azure Active Directoryban vagy az Office365-ben létrehozott csoportokon.   Az Azure Active Directoryban konfigurált alkalmazások a helyszíni csoportattribútumok szinkronizálására csak szinkronizált csoportokhoz jutnak el.

## <a name="options-for-applications-to-consume-group-information"></a>A csoportadatok felhasználására szolgáló alkalmazások lehetőségei

Az alkalmazások meghívhatják az MS Graph csoportok végpontját a hitelesített felhasználó csoportadatainak beszerzéséhez. Ez a hívás biztosítja, hogy a felhasználó által tagként lévő összes csoport akkor is elérhető legyen, ha nagy számú csoport érintett.  A csoportszámbavétel ezután független a jogkivonat méretkorlátaitól.

Azonban ha egy meglévő alkalmazás azt várja, hogy a csoport adatait jogcímeken keresztül, az Azure Active Directory konfigurálható számos különböző jogcímformátumok.  Vegye figyelembe a következő lehetőségeket:

- Ha a csoporttagságot alkalmazáson on-in-alkalmazás engedélyezési célokra használja, célszerű a Csoport objectid használatát használni. A csoport objectid nem módosítható és egyedi az Azure Active Directoryban, és minden csoport számára elérhető.
- Ha a helyszíni sAMAccountName csoportot használja az engedélyezéshez, használjon tartományminősített neveket;  Kisebb az esélye, hogy a nevek összecsapnak. az sAMAccountName lehet, hogy egy Active Directory-tartományban egyedi, de ha egynél több Active Directory-tartomány van szinkronizálva egy Azure Active Directory-bérlővel, akkor egynél több csoport is rendelkezhet ugyanazzal a névvel.
- Fontolja meg [az alkalmazásszerepkörök](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) használatát a csoporttagság és az alkalmazás közötti átirányítási réteg biztosításához.   Az alkalmazás ezután belső engedélyezési döntéseket hoz a jogkivonatban lévő szerepkör-kagylók alapján.
- Ha az alkalmazás úgy van beállítva, hogy az Active Directoryból szinkronizált csoportattribútumokat kapjon, és egy csoport nem tartalmazza ezeket az attribútumokat, akkor nem fog szerepelni a jogcímekben.
- A jogcímek csoportjogcímek beágyazott csoportokat tartalmaznak, kivéve, ha a csoportjogcímek az alkalmazáshoz rendelt csoportokra való korlátozásának beállításával vannak.  Ha egy felhasználó a B csoport tagja, a B csoport pedig az A csoport tagja, akkor a csoport felhasználói jogcímeinek tartalmaznia kell a GroupA-t és a B csoportot is. Ha egy szervezet felhasználói nagy számú csoporttagsággal rendelkeznek, a jogkivonatban felsorolt csoportok száma nőhet a jogkivonat mérete.  Az Azure Active Directory korlátozza a tokenben kibocsátandó csoportok számát 150 SAML-állítások, és 200 JWT.  Ha egy felhasználó nagyobb számú csoport tagja, a csoportok kimaradnak, és helyette a Graph végpontra mutató hivatkozás tapad a csoportadatok megszerzéséhez.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Az Active Directoryból szinkronizált csoportattribútumok használatának előfeltételei

A csoporttagsági jogcímek bármely csoport számára kiadhatók, ha az ObjectId formátumot használja. A csoportjogcímek használata a csoport ObjectId-tól eltérő formátumban, a csoportokat szinkronizálni kell az Active Directoryból az Azure AD Connect használatával.

Az Azure Active Directory konfigurálása két lépésből áll, hogy csoportneveket defektje az Active Directory-csoportoknak.

1. **Csoportnevek szinkronizálása az Active Directoryból** Mielőtt az Azure Active Directory képes kibocsátni a csoportneveket vagy a helyszíni csoport SID-csoport csoport- vagy szerepkörjogcímekben, a szükséges attribútumokat szinkronizálni kell az Active Directoryból.  Az Azure AD Connect 1.2.70-es vagy újabb verzióját kell futtatnia.   Az Azure AD Connect 1.2.70-nél korábbi verziói szinkronizálják a csoportobjektumokat az Active Directoryból, de nem tartalmazzák a szükséges csoportnév-attribútumokat.  Frissítés az aktuális verzióra.

2. **Konfigurálja az alkalmazásregisztrációt az Azure Active Directoryban úgy, hogy az tartalmazza a csoportjogcímeket a jogkivonatokban** A csoportjogcímek konfigurálhatók a portál Vállalati alkalmazások szakaszában, vagy az alkalmazásregisztrációk szakaszban az alkalmazásjegyzék használatával.  A csoportjogcímek konfigurálásához az alkalmazásjegyzékben lásd alább a "Konfigurálása az Azure Active Directory alkalmazásregisztráció a csoport attribútumok" című részt.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Csoportjogcímek hozzáadása az SAML-alkalmazások jogkivonataihoz egyszeri szolgáltatás konfigurációjával

Egy katalógus vagy nem katalógus SAML alkalmazás csoportjogcímének konfigurálásához nyissa meg a **Vállalati alkalmazások**alkalmazást, kattintson a listában az alkalmazásra, válassza az **Egyszeri bejelentkezés konfiguráció lehetőséget,** majd válassza **a Felhasználói attribútumok & jogcímek**lehetőséget.

Kattintson a **Csoportjogcím hozzáadása lehetőségre**  

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

A választógombokkal választhatja ki, hogy mely csoportok at kell szerepeltetni a tokenben.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Kiválasztás | Leírás |
|----------|-------------|
| **Minden csoport** | Biztonsági csoportokat, terjesztési listákat és szerepköröket bocsát ki.  |
| **Biztonsági csoportok** | Biztonsági csoportokat bocsát ki, amelyeknek a felhasználó tagja a csoportjogcímben |
| **Címtárbeli szerepkörök** | Ha a felhasználó címtárszerepköröket kap, akkor azok "wids" jogcímként kerülnek kibocsátásra (a csoportjogcím nem kerül kibocsátásra) |
| **Az alkalmazáshoz rendelt csoportok** | Csak azalkalmazáshoz kifejezetten hozzárendelt csoportokat bocsátki, és a felhasználó tagja a |

Ha például a felhasználó által tagként lévő összes biztonsági csoportot szeretné kicsinálni, válassza a Biztonsági csoportok lehetőséget.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Az Azure AD objektumazonosítók helyett az Active Directoryból szinkronizált Active Directory-attribútumok használatával történő csoportok kibocsátásához válassza ki a kívánt formátumot a legördülő menüből. A jogcímek csak az Active Directoryból szinkronizált csoportok lesznek benne.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Ha csak az alkalmazáshoz rendelt csoportokat szeretné kijelölni, válassza **az alkalmazáshoz rendelt csoportok lehetőséget.**

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Az alkalmazáshoz rendelt csoportok szerepelni fognak a jogkivonatban.  A többi csoport, amelynek a felhasználó tagja, ki lesz maradhagyva.  Ezzel a beállítással beágyazott csoportok nem szerepelnek, és a felhasználónak az alkalmazáshoz rendelt csoport közvetlen tagjának kell lennie.

Az alkalmazáshoz rendelt csoportok módosításához jelölje ki az alkalmazást a **Vállalati alkalmazások** listából, majd kattintson az alkalmazás bal oldali navigációs menüjének Felhasználók **és csoportok** parancsára.

Lásd a [dokumentum: Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) a csoportos hozzárendelés ek alkalmazáshoz való kezelésének részleteiért.

### <a name="advanced-options"></a>Speciális beállítások

A csoportjogcímek kibocsátásának módját a Speciális beállítások csoport beállításai módosíthatják.

A csoportjogcím nevének testreszabása: Ha be van jelölve, a csoportjogcímekhez más jogcímtípus adható meg.   Írja be a jogcím típusát a Név mezőbe és a jogcím nem kötelező névterét a névtér mezőbe.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Egyes alkalmazások megkövetelik, hogy a csoporttagsági adatok megjelenjenek a "szerepkör" jogcímben. A "Szerepkörjogcímek megjelenítése" jelölőnégyzet bejelölésével szükség lehet szerepkörként a felhasználó csoportjainak kibocsáthatók.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Ha a csoportadatok szerepkörként történő kibocsátására van lehetőség, csak csoportok jelennek meg a szerepkörjogcímjogban.  A felhasználó által hozzárendelt alkalmazásszerepkörök nem jelennek meg a szerepkörjogcímhelyen.

### <a name="edit-the-group-claims-configuration"></a>A csoportjogcímkonfiguráció szerkesztése

Miután egy csoportjogcím-konfigurációt hozzáadtak a Felhasználói attribútumok & jogcímek konfigurációjához, a csoportjogcím hozzáadásának lehetősége szürkén jelenik meg.  A csoportjogcím konfigurációjának módosításához kattintson a csoportjogcímre a **További jogcímek** listában.

![jogcímek felhasználói felülete](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Az Azure AD alkalmazás regisztrációjának konfigurálása csoportattribútumokhoz

A csoportjogcímek az [alkalmazásjegyzék](../../active-directory/develop/reference-app-manifest.md) [Választható jogcímek](../../active-directory/develop/active-directory-optional-claims.md) szakaszában is konfigurálhatók.

1. A portálon ->Azure Active Directory -> alkalmazásregisztrációk->alkalmazás->manifest

2. Csoporttagsági jogcímek engedélyezése a groupMembershipClaim módosításával

Az érvényes értékek a következők:

| Kiválasztás | Leírás |
|----------|-------------|
| **"Minden"** | Biztonsági csoportokat, terjesztési listákat és szerepköröket bocsát ki |
| **"Biztonsági csoport"** | Biztonsági csoportokat bocsát ki, amelyeknek a felhasználó tagja a csoportjogcímben |
| **"DirectoryRole** | Ha a felhasználó címtárszerepköröket kap, akkor azok "wids" jogcímként kerülnek kibocsátásra (a csoportjogcím nem kerül kibocsátásra) |
| **"Alkalmazáscsoport** | Csak azalkalmazáshoz kifejezetten hozzárendelt csoportokat bocsátki, és a felhasználó tagja a |

   Példa:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Alapértelmezés szerint a csoport objectid-jai megjelennek a csoport jogcímértékében.  Ha módosítani szeretné a jogcímértékét, hogy az a helyszíni csoport attribútumait tartalmazza, vagy hogy a jogcímtípusát szerepkörre módosítsa, használja az OptionalClaims konfigurációt az alábbiak szerint:

3. Állítsa be a csoportnév konfigurációjának választható jogcímeit.

   Ha azt szeretné, hogy a jogkivonatban lévő csoportok tartalmazzák a helyszíni AD-csoport attribútumait, adja meg, hogy melyik jogkivonat típusára kell alkalmazni a választható jogcímeket a választható jogcímek szakaszban.  Több tokentípus is felsorolható:

   - idToken az OIDC-azonosító tokenhez
   - accessToken az OAuth/OIDC hozzáférési jogkivonathoz
   - Saml2Token SAML tokenekhez.

   > [!NOTE]
   > A Saml2Token típus az SAML1.1 és az SAML2.0 formátumú tokenekre egyaránt vonatkozik.

   Minden megfelelő jogkivonat-típus, módosítsa a csoportok jogcím a manifest OptionalClaims szakasz ának használatára. Az OptionalClaims séma a következő:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Választható jogcímséma | Érték |
   |----------|-------------|
   | **név:** | "Csoportoknak" kell lenniük. |
   | **Forrás:** | Nem használt. Null kihagyása vagy megadása |
   | **Alapvető:** | Nem használt. Hamis kihagyás vagy megadása |
   | **továbbitulajdonságok:** | A további tulajdonságok listája.  Érvényes opciók: "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   A további tulajdonságokban csak egy "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" szükséges.  Ha egynél több van jelen, az elsőt használja a rendszer, és a többi figyelmen kívül hagyja.

   Egyes alkalmazások csoportadatokat igényelnek a szerepkörjogcímben szereplő felhasználóról.  Ha a jogcímtípusát csoportjogcímről szerepkörjogcímre szeretné módosítani, adja hozzá a "emit_as_roles" értéket a további tulajdonságokhoz.  A csoportértékek a szerepkörjogcímleg kerülnek kibocsátásra.

   > [!NOTE]
   > Ha a "emit_as_roles" kifejezést használja, a felhasználó hozzárendelt alkalmazásszerepkörei nem jelennek meg a szerepkörjogcímhelyen

### <a name="examples"></a>Példák

Csoportok at bocsátanak ki csoportnévként oauth hozzáférési tokenekben dnsDomainName\SAMAccountName formátumban

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

A netbiosDomain\samAccountName formátumban visszaküldendő csoportnevek kibocsátása az SAML és OIDC id tokenekben szereplő szerepkörjogcímjogként:

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

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)

[Szerepkörjogcímek konfigurálása](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
