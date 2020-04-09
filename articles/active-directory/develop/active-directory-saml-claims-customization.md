---
title: Az Azure AD alkalmazás SAML tokenjogcímeinek testreszabása
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan szabhatja testre az AZURE AD-ben a vállalati alkalmazások SAML-jogkivonatában kiadott jogcímeket.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 87a9632ec2433b8698e3ae3761ba733aa6bc63a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885684"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz

Ma az Azure Active Directory (Azure AD) támogatja az egyszeri bejelentkezést (SSO) a legtöbb nagyvállalati alkalmazással, beleértve az Azure AD alkalmazásgalériába előre integrált alkalmazásokat, valamint az egyéni alkalmazásokat is. Amikor egy felhasználó hitelesíti magát egy alkalmazás az Azure AD-n keresztül az SAML 2.0 protokoll használatával, az Azure AD küld egy jogkivonatot az alkalmazásnak (HTTP POST-on keresztül). Ezután az alkalmazás érvényesíti és használja a jogkivonatot a felhasználó bejelentkezéséhez, ahelyett, hogy felhasználónevet és jelszót kérne. Ezek az SAML-tokenek *jogcímeknek*nevezett információkat tartalmaznak a felhasználóról.

A *jogcím* olyan információ, amelyet az identitásszolgáltató a felhasználóról állít fel az adott felhasználó számára általuk kiadott jogkivonaton belül. Az [SAML tokenben](https://en.wikipedia.org/wiki/SAML_2.0)ezeket az adatokat általában az SAML attribútumutasítás tartalmazza. A felhasználó egyedi azonosítója általában az SAML-tulajdonosban jelenik meg, amelyet névazonosítónak is neveznek.

Alapértelmezés szerint az Azure AD kiad egy SAML-jogkivonatot az alkalmazásnak, amely a `NameIdentifier` felhasználó felhasználónevének (más néven a felhasználó egyszerű neve) értékével rendelkező jogcímet tartalmaz az Azure AD-ben, amely egyedileg azonosíthatja a felhasználót. Az SAML-jogkivonat további jogcímeket is tartalmaz, amelyek tartalmazzák a felhasználó e-mail címét, utónevét és vezetéknevét.

Az alkalmazás SAML-jogkivonatában kiadott jogcímek megtekintéséhez vagy szerkesztéséhez nyissa meg az alkalmazást az Azure Portalon. Ezután nyissa meg a **Felhasználói attribútumok & jogcímek** szakaszt.

![Nyissa meg a Felhasználói attribútumok & jogcímek szakaszt az Azure Portalon](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Két oka lehet annak, hogy miért kell az SAML-jogkivonatban kiadott jogcímeket szerkesztenie:

* Az alkalmazás `NameIdentifier` megköveteli, hogy a vagy NameID-jogcím nem az Azure AD-ben tárolt felhasználónév (vagy egyszerű felhasználónév) kell lennie.
* Az alkalmazás írásra írt, hogy szükség van egy másik jogcím URI-k vagy jogcímértékek.

## <a name="editing-nameid"></a>Névazonosító szerkesztése

A NameID (névazonosító érték) szerkesztése:

1. Nyissa meg a **Névazonosító** értéklapját.
1. Jelölje ki azt az attribútumot vagy átalakítást, amelyet alkalmazni szeretne az attribútumra. Tetszés szerint megadhatja azt a formátumot, amelyet a NameID jogcímnek tartalmaznia kell.

   ![A NameID (névazonosító) érték szerkesztése](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID formátum

Ha az SAML-kérelem tartalmazza a NameIDPolicy elemet egy adott formátummal, majd az Azure AD tiszteletben tartja a formátumot a kérelemben.

Ha az SAML-kérelem nem tartalmaz egy elemet NameIDPolicy, majd az Azure AD kiadja a NameID a megadott formátumban. Ha nincs megadva formátum, az Azure AD a kiválasztott jogcímforráshoz társított alapértelmezett forrásformátumot fogja használni.

A **Névazonosító formátumának kiválasztása** legördülő menüben az alábbi lehetőségek közül választhat.

| NameID formátum | Leírás |
|---------------|-------------|
| **Alapértelmezett** | Az Azure AD az alapértelmezett forrásformátumot fogja használni. |
| **Állandó** | Az Azure AD az állandó nevetadja meg formátumot. |
| **E-mail cím** | Az Azure AD az EmailAddress-t használja NameID formátumként. |
| **Meghatározatlan** | Az Azure AD névazonosító formátumként meghatározatlan formátumot fog használni. |
| **Windows tartományminősítési név** | Az Azure AD a WindowsDomainQualifiedName-t használja NameID formátumként. |

Átmeneti NameID is támogatott, de nem érhető el a legördülő menüben, és nem konfigurálható az Azure oldalán. A NameIDPolicy attribútumról az [Egyszeri bejelentkezéssaml protokoll](single-sign-on-saml-protocol.md)című témakörben olvashat bővebben.

### <a name="attributes"></a>Attribútumok

Válassza ki a `NameIdentifier` kívánt forrást a (vagy NameID) jogcímhez. Az alábbi lehetőségek közül választhat.

| Név | Leírás |
|------|-------------|
| E-mail | A felhasználó e-mail címe |
| userprincipalName (felhasználóneve) | A felhasználó egyszerű felhasználóneve (UPN) |
| helyszíni samaccount | A helyszíni Azure AD-ből szinkronizált SAM-fiók név |
| tárgyiobjektum | A felhasználó objektumtárgyai az Azure AD-ben |
| alkalmazott | A felhasználó alkalmazottazonosítója |
| Címtárbővítmények | A [helyszíni Active Directoryból az Azure AD Connect Sync használatával szinkronizált](../hybrid/how-to-connect-sync-feature-directory-extensions.md) címtárbővítmények |
| Extension attribútumok 1-15 | Az Azure AD-séma bővítéséhez használt helyszíni bővítményattribútumok |

További információ: [3. táblázat: Érvényes azonosítóértékek forrásonként](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Az Azure AD-ben definiált jogcímekhez bármilyen állandó (statikus) értéket is hozzárendelhet. Kérjük, kövesse az alábbi lépéseket, hogy hozzá egy állandó érték:

1. Az [Azure Portalon](https://portal.azure.com/)a **Felhasználói attribútumok & jogcímek** szakaszban kattintson a **Szerkesztés** ikonra a jogcímek szerkesztéséhez.

1. Kattintson a szükséges jogcímre, amelyet módosítani szeretne.

1. Írja be az idézőjelek nélküli állandó értéket a **Forrás attribútumba** a szervezet nek megfelelően, majd kattintson a **Mentés gombra.**

    ![Nyissa meg a Felhasználói attribútumok & jogcímek szakaszt az Azure Portalon](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Az állandó érték az alábbiak szerint jelenik meg.

    ![Nyissa meg a Felhasználói attribútumok & jogcímek szakaszt az Azure Portalon](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Különleges igények - átalakítások

Használhatja a jogcímátalakítások függvények is.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartományutótagot az e-mail címről vagy az egyszerű felhasználónévről. Ez csak az átadott felhasználónév első részét bontja ki (például "joe_smith" helyett joe_smith@contoso.com). |
| **Csatlakozás()** | Attribútumot egyesít ellenőrzött tartománnyal. Ha a kiválasztott felhasználói azonosító értéknek van tartománya, akkor kibontja a felhasználónevet a kijelölt ellenőrzött tartomány hozzáfűzéséhez. Ha például az e-mailt (joe_smith@contoso.com) választja ki felhasználói azonosító értékként, joe_smith@contoso.onmicrosoft.comés contoso.onmicrosoft.com jelöli meg ellenőrzött tartományként, az a . |
| **ToLower()** | A kijelölt attribútum karaktereit kisbetűkké alakítja. |
| **ToUpper()** | A kijelölt attribútum karaktereit nagybetűkké alakítja. |

## <a name="adding-application-specific-claims"></a>Alkalmazásspecifikus jogcímek hozzáadása

Alkalmazásspecifikus jogcímek hozzáadása:

1. A **Felhasználói attribútumok & jogcímek**csoportban válassza az **Új jogcím hozzáadása** lehetőséget a Felhasználói **jogcímek kezelése** lap megnyitásához.
1. Adja meg a jogcímek **nevét.** Az érték nek nem feltétlenül kell követnie az URI-mintát az SAML-specifikáció szerint. Ha URI-mintára van szüksége, azt a Névtér mezőbe **helyezheti.**
1. Válassza ki azt a **forrást,** amelyben a jogcím beolvassa az értékét. Kiválaszthat egy felhasználói attribútumot a forrásattribútum legördülő legördülő legördülő részéből, vagy alkalmazhat egy átalakítást a felhasználói attribútumra, mielőtt jogcímként bocsátana ki.

### <a name="claim-transformations"></a>Jogcímátalakítások

Átalakítás alkalmazása felhasználói attribútumra:

1. A **Jogcím kezelése csoportban**válassza az *Átalakítás* jogcímforrásként lehetőséget az **Átalakítás kezelése** lap megnyitásához.
2. Válassza ki a függvényt az átalakítási legördülő menüből. A kiválasztott függvénytől függően paramétereket és állandó értéket kell megadnia az átalakítás során. A rendelkezésre álló funkciókkal kapcsolatos további információkért tekintse meg az alábbi táblázatot.
3. Ha több átalakítást szeretne alkalmazni, kattintson az **Átalakítás hozzáadása gombra.** Egy jogcímre legfeljebb két átalakítást alkalmazhat. Például először kibonthatja a e-mail előtagot. `user.mail` Ezután, hogy a húr nagybetűs.

   ![A NameID (névazonosító) érték szerkesztése](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

A jogcímek átalakításához a következő függvények használhatók.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartományutótagot az e-mail címről vagy az egyszerű felhasználónévről. Ez csak az átadott felhasználónév első részét bontja ki (például "joe_smith" helyett joe_smith@contoso.com). |
| **Csatlakozás()** | Új értéket hoz létre két attribútum összekapcsolásával. Szükség esetén a két attribútum közötti elválasztót is használhatja. A NameID jogcím átalakítása esetén az illesztés ellenőrzött tartományra korlátozódik. Ha a kiválasztott felhasználói azonosító értéknek van tartománya, akkor kibontja a felhasználónevet a kijelölt ellenőrzött tartomány hozzáfűzéséhez. Ha például az e-mailt (joe_smith@contoso.com) választja ki felhasználói azonosító értékként, joe_smith@contoso.onmicrosoft.comés contoso.onmicrosoft.com jelöli meg ellenőrzött tartományként, az a . |
| **ToLower()** | A kijelölt attribútum karaktereit kisbetűkké alakítja. |
| **ToUpper()** | A kijelölt attribútum karaktereit nagybetűkké alakítja. |
| **Tartalmaz()** | Attribútumot vagy állandót ad ki, ha a bemenet megegyezik a megadott értékkel. Ellenkező esetben megadhat egy másik kimenetet, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kiállítani, amelyben az érték a felhasználó e-mail címe, ha az tartalmazza a "@contoso.comtartományt, ellenkező esetben az egyszerű felhasználónevet szeretné kiadni. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter(bemenet)*: user.email<br/>*Érték*:@contoso.com" "<br/>2. paraméter (kimenet): user.email<br/>3. paraméter (kimenet, ha nincs egyezés): user.userprincipalname |
| **EndWith()** | Attribútumot vagy állandót ad ki, ha a bemenet a megadott értékkel végződik. Ellenkező esetben megadhat egy másik kimenetet, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kiállítani, amelyben az érték a felhasználó alkalmazotti azonosítója, ha az alkalmazottazonosító "000" végződéssel végződik, ellenkező esetben egy bővítményattribútumot szeretne kiadni. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter(bemenet)*: user.employeeid<br/>*Érték*: "000"<br/>2. paraméter (kimenet): user.employeeid<br/>3. paraméter (kimenet, ha nincs egyezés): user.extensionattribute1 |
| **StartWith()** | Attribútumot vagy állandót ad ki, ha a bemenet a megadott értékkel kezdődik. Ellenkező esetben megadhat egy másik kimenetet, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kiállítani, amelyben az érték a felhasználó alkalmazotti azonosítója, ha az ország/régió "US" értékkel kezdődik, ellenkező esetben egy bővítményattribútumot szeretne kiadni. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter(bemenet)*: user.country<br/>*Érték*: "US"<br/>2. paraméter (kimenet): user.employeeid<br/>3. paraméter (kimenet, ha nincs egyezés): user.extensionattribute1 |
| **Extract() - Egyeztetés után** | A megadott értéknek megfelelő részkarakterláncértékét adja eredményül.<br/>Ha például a bemeneti érték "Finance_BSimon", az egyező érték "Finance_", akkor a jogcím kimenete "BSimon". |
| **Extract() - Egyeztetés előtt** | A részkarakterláncot adja vissza, amíg meg nem egyezik a megadott értékkel.<br/>Ha például a bemeneti érték "BSimon_US", az egyező érték "_US", akkor a jogcím kimenete "BSimon". |
| **Kivonat() - az egyeztetés között** | A részkarakterláncot adja vissza, amíg meg nem egyezik a megadott értékkel.<br/>Ha például a bemeneti érték "Finance_BSimon_US", az első egyező érték "Finance_", a második egyező érték "_US", akkor a jogcím kimenete "BSimon". |
| **ExtractAlpha() - előtag** | A karakterlánc betűrendes részét adja eredményül.<br/>Ha például a bemenetértéke "BSimon_123", akkor a "BSimon" értéket adja vissza. |
| **ExtractAlpha() - utótag** | A karakterlánc alfabetikus részét adja eredményül.<br/>Ha például a bemenet értéke "123_Simon", akkor "Simon" értéket ad vissza. |
| **ExtractNumeric() - előtag** | A karakterlánc előtag numerikus részét adja eredményül.<br/>Ha például a bemenetértéke "123_BSimon", akkor a "123" értéket adja vissza. |
| **ExtractNumeric() - utótag** | A karakterlánc numerikus hányadrészét adja eredményül.<br/>Ha például a bemenetértéke "BSimon_123", akkor a "123" értéket adja vissza. |
| **IfEmpty()** | Attribútumot vagy állandót ad ki, ha a bemenet null vagy üres.<br/>Ha például egy extensionattributeban tárolt attribútumot szeretne kiadni, ha egy adott felhasználó alkalmazotti azonosítója üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1. paraméter(bemenet): user.employeeid<br/>2. paraméter (kimenet): user.extensionattribute1<br/>3. paraméter (kimenet, ha nincs egyezés): user.employeeid |
| **IfNotEmpty()** | Attribútumot vagy állandót ad ki, ha a bemenet nem null értékű vagy üres.<br/>Ha például egy extensionattributeban tárolt attribútumot szeretne kiadni, ha egy adott felhasználó alkalmazotti azonosítója nem üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1. paraméter(bemenet): user.employeeid<br/>2. paraméter (kimenet): user.extensionattribute1 |

Ha további átalakításokra van szüksége, küldje el ötlete az [Azure AD visszajelzési fórumában](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) a *SaaS-alkalmazás* kategória alatt.

## <a name="emitting-claims-based-on-conditions"></a>A feltételeken alapuló követelések kibocsátása

A jogcím forrását a felhasználó típusa és a felhasználó csoportjának csoportja alapján adhatja meg. 

A felhasználó típusa a következő lehet:
- **Bármely**: Minden felhasználó hozzáférhet az alkalmazáshoz.
- **Tagok**: A bérlő natív tagja
- **Minden vendég:** A felhasználó egy külső szervezettől, Azure AD-vel vagy anélkül kerül át.
- **AAD-vendégek:** A vendégfelhasználó egy másik szervezethez tartozik az Azure AD használatával.
- **Külső vendégek:** A vendégfelhasználó olyan külső szervezethez tartozik, amely nem rendelkezik Azure AD-vel.


Az egyik forgatókönyv, ahol ez hasznos, ha a jogcím forrása eltér a vendég és egy alkalmazott egy alkalmazás hoz való hozzáférés. Előfordulhat, hogy meg szeretné adni, hogy ha a felhasználó alkalmazott, a NameID a user.email-ből származik, de ha a felhasználó vendég, akkor a NameID a user.extensionattribute1 forrásból származik.

Jogcímfeltétel hozzáadása:

1. A **Jogcím kezelése területen**bontsa ki a jogcímfeltételeket.
2. Válassza ki a felhasználó típusát.
3. Válassza ki azokat a csoportokat, amelyekhez a felhasználónak tartoznia kell. Egy adott alkalmazás összes jogcímén legfeljebb 10 egyedi csoportot választhat ki. 
4. Válassza ki azt a **forrást,** amelyben a jogcím beolvassa az értékét. Kiválaszthat egy felhasználói attribútumot a forrásattribútum legördülő legördülő legördülő részéből, vagy alkalmazhat egy átalakítást a felhasználói attribútumra, mielőtt jogcímként bocsátana ki.

A feltételek hozzáadásának sorrendje fontos. Az Azure AD kiértékeli a feltételeket fentről lefelé annak eldöntéséhez, hogy melyik értéket kell kiállítani a jogcímben. 

Például Brita Simon egy vendégfelhasználó a Contoso bérlő. Ő tartozik egy másik szervezet, amely szintén használja az Azure AD. A Fabrikam-alkalmazás alábbi konfigurációja miatt, amikor a Brita megpróbál bejelentkezni a Fabrikam-ba, az Azure AD a következő feltételeket értékeli ki.

Először is, az Azure AD ellenőrzi, `All guests`hogy Brita felhasználói típusa. Mivel ez igaz, akkor az Azure AD hozzárendeli a jogcím forrását. `user.extensionattribute1` Másodszor, az Azure AD ellenőrzi, hogy `AAD guests`A Brita felhasználói típusa , mivel ez is igaz, akkor az Azure AD hozzárendeli a jogcím `user.mail`forrását. Végül a követelést a `user.email` Brita értékével bocsátják ki.

![Jogcímek feltételes konfigurációja](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>További lépések

* [Alkalmazáskezelés az Azure AD-ben](../manage-apps/what-is-application-management.md)
* [Egyszeri bejelentkezési alkalmazások konfigurálása, amelyek nem szerepelnek az Azure AD alkalmazásgyűjteményében](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML-alapú egyszeri bejelentkezés – problémamegoldás](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
