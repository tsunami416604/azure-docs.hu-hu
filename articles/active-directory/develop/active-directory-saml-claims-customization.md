---
title: Az Azure ad-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása |} A Microsoft Docs
description: Ismerje meg az Azure AD-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: celested
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6fe74852824c10d24729f785e5e33a17b793161
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878570"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: Vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása

Még ma Azure Active Directory (Azure AD) támogatja az egyszeri bejelentkezés (SSO) a legtöbb vállalati alkalmazásokkal, beleértve az Azure AD-alkalmazásgyűjtemény, valamint a egyéni alkalmazásokat előre integrált alkalmazások. Amikor egy felhasználó hitelesíti magát egy alkalmazást az SAML 2.0 protokoll használatával az Azure AD-n keresztül, az Azure AD egy tokent az alkalmazást (egy HTTP POST) küld. És ezt követően az alkalmazás ellenőrzi és használja a jogkivonatot a felhasználó nem kér a felhasználónévvel és jelszóval bejelentkezni. Ezek a SAML-jogkivonatok tartalmazzák a felhasználó, más néven adatokra *jogcímek*.

A *jogcím* arról, hogy identitásszolgáltatót egy felhasználó adatainak bocsátanak ki, hogy a felhasználó számára a tokenen belül van. A [SAML-jogkivonat](https://en.wikipedia.org/wiki/SAML_2.0), ezek az adatok általában az SAML-attribútum utasítás szerepel. Az SAML-tulajdonos alkalmazásnév-azonosító néven a általában jelzi a felhasználó egyedi azonosítója.

Alapértelmezés szerint az Azure AD kibocsát egy SAML-jogkivonat, amely tartalmazza az alkalmazás egy `NameIdentifier` jogcím értéke az a felhasználó felhasználónevét (más néven az egyszerű felhasználónév), az Azure ad-ben, amely a felhasználó egyedi azonosítására alkalmas. Az SAML-jogkivonat a felhasználó e-mail címét, Utónév és Vezetéknév tartalmazó további jogcímek is tartalmaz.

Megtekinteni vagy szerkeszteni a jogcímek, az alkalmazás számára kibocsátott SAML-jogkivonatban, nyissa meg az alkalmazás az Azure Portalon. Nyissa meg a **felhasználói attribútumok & jogcímek** szakaszban.

![Felhasználói attribútumok & jogcímek szakasz](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Előfordulhat, hogy miért a SAML-jogkivonatban kiadott jogcímek szerkesztése kell két lehetséges oka van:

* Az alkalmazás megköveteli a `NameIdentifier` vagy a NameID jogcím-verziójáétól eltérő Azure AD-ben tárolt felhasználónevet (vagy egyszerű felhasználónév).
* Az alkalmazás eltérő szabályzatkészletet jogcím URI-k vagy a jogcímértékek lett írva.

## <a name="editing-nameid"></a>NameID szerkesztése

A NameID (név azonosító érték) szerkesztése:

1. Nyissa meg a **azonosító értékét nevet** lapot.
1. Válassza ki az attribútum vagy az attribútum a alkalmazni kívánt átalakítást. Szükség esetén a kívánt azt szeretné, hogy a NameID jogcím formátumot is megadhat.

   ![A NameID (névazonosító) értékének szerkesztése](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-formátum

Ha az SAML-kérelmet tartalmaz egy meghatározott formátumnak NameIDPolicy elemmel, majd az Azure AD betartja a formátumot a kérésben.

Ha az SAML-kérelmet a NameIDPolicy nem tartalmaz egy elemet, majd az Azure AD állít ki a következő formátumban adja meg, hogy a NameID. Ha nincs formátum van megadva, az Azure AD fog használni a kiválasztott jogcímforrás társított alapértelmezett adatforrás-formátum.

Az a **válasszon azonosító formátuma** legördülő listában, az alábbi lehetőségek közül választhat.

| NameID-formátum | Leírás |
|---------------|-------------|
| **Alapértelmezett** | Az Azure AD fog használni az alapértelmezett adatforrás-formátum. |
| **Tartós** | Az Azure AD állandó használja, a NameID-formátum. |
| **E-mail cím** | Az Azure AD EmailAddress használja, a NameID-formátum. |
| **Nincs megadva** | Az Azure AD nem meghatározott használja, a NameID-formátum. |
| **Átmeneti** | Az Azure AD átmeneti használja, a NameID-formátum. |

A NameIDPolicy attribútum kapcsolatos további információkért lásd: [egyszeri bejelentkezéses SAML-protokoll](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribútumok

Válassza ki a kívánt forrása a `NameIdentifier` (vagy NameID) jogcím. Az alábbi lehetőségek közül választhat.

| Name (Név) | Leírás |
|------|-------------|
| E-mail | A felhasználó e-mail címe |
| userprincipalName | Egyszerű felhasználónév (UPN) a felhasználó |
| onpremisessamaccount | SAM-fiók neve, amely a helyszínről az Azure AD szinkronizálása megtörtént |
| objektumazonosító | az Azure ad-ben a felhasználó objektumazonosítója |
| EmployeeID | a felhasználót az EmployeeID |
| Címtárbővítmények | Címtárbővítmények [az Azure AD Connect szinkronizálási szolgáltatás használata a helyszíni Active Directoryból szinkronizált](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 1 – 15. Bővítményattribútumok | A helyszíni kiterjesztési attribútumot használja az Azure AD-séma kiterjesztése |

További információ: [3. táblázat: Érvényes azonosító értéket, forrás](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Speciális jogcím - átalakítás

A jogcímek átalakítások funkciók is használható.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | A tartományi utótag eltávolítja az e-mail-cím vagy a felhasználó egyszerű neve. Ez a felhasználónév átadott keresztül csak az első részt kinyeri (például "joe_smith" helyett joe_smith@contoso.com). |
| **JOIN()** | Az attribútum egy ellenőrzött tartomány csatlakozik. Ha a kiválasztott felhasználói azonosító értékét egy tartománnyal rendelkezik, azt fogja bontsa ki a hozzáfűzni a kiválasztott ellenőrzött tartomány felhasználónév. Ha például az e-mailt választja (joe_smith@contoso.com), a felhasználói azonosító értékét, és válassza contoso.onmicrosoft.com ellenőrzött tartományának részeként, emiatt joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konvertálja a karaktereket a kijelölt attribútum kisbetűs karaktert. |
| **ToUpper()** | Konvertálja a karaktereket a kijelölt attribútum nagybetűket. |

## <a name="adding-application-specific-claims"></a>Alkalmazás-specifikus jogcímek hozzáadása

Alkalmazás-specifikus jogcímek hozzáadása:

1. A **felhasználói attribútumok & jogcímek**válassza **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** lap.
1. Adja meg a **neve** a jogcímek. Az érték nem szigorúan csak egy URI mintát követik, a SAML-specifikáció szerint szükséges. Ha egy URI-minta, akkor lehet helyezni, amely a **Namespace** mező.
1. Válassza ki a **forrás** hol fog a jogcím az értékét a vizualizációhoz. Válassza ki a felhasználói attribútum a forrás-attribútum legördülő listából, vagy egy átalakítás alkalmazni a felhasználói attribútum jogcímként kibocsátó azt megelőzően.

### <a name="application-specific-claims---transformations"></a>Alkalmazás-specifikus jogcím - átalakítás

A jogcímek átalakítások funkciók is használható.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | A tartományi utótag eltávolítja az e-mail-cím vagy a felhasználó egyszerű neve. Ez a felhasználónév átadott keresztül csak az első részt kinyeri (például "joe_smith" helyett joe_smith@contoso.com). |
| **JOIN()** | Létrehoz egy új értéket a Közösséghez való csatlakozás után két attribútum. Ha szeretné a két attribútum közötti elválasztó is használhatja. |
| **ToLower()** | Konvertálja a karaktereket a kijelölt attribútum kisbetűs karaktert. |
| **ToUpper()** | Konvertálja a karaktereket a kijelölt attribútum nagybetűket. |
| **Contains()** | Ha a bemeneti megegyezik a megadott értéket jelenít meg egy attribútum vagy konstans. Ellenkező esetben egy másik kimeneti megadhat fel, ha nem egyezik meg.<br/>Például, ha szeretné gridre bocsáthatja ki egy jogcímet, ahol az érték a felhasználó e-mail címét, ha a tartomány tartalmaz "@contoso.com", ellenkező esetben a felhasználó egyszerű neve kimeneti szeretné. Ehhez a következő értékek állíthatók be:<br/>*A paraméter 1(input)*: user.email<br/>*Érték*: "@contoso.com"<br/>(Kimenet) 2. paraméter: user.email<br/>(Ha nem egyezik meg a kimenet) 3. paraméter: user.userprincipalname |
| **EndWith()** | Az attribútum vagy állandó jelenít meg, ha a bemeneti végződik-e a megadott értéket. Ellenkező esetben egy másik kimeneti megadhat fel, ha nem egyezik meg.<br/>Például szeretné gridre bocsáthatja ki egy jogcímet, ahol az érték a felhasználó employeeid, ha az employeeid "000" végződik, ellenkező esetben szeretné-e a kimenetben bővítményattribútum. Ehhez a következő értékek állíthatók be:<br/>*A paraméter 1(input)*: user.employeeid<br/>*Érték*: "000"<br/>(Kimenet) 2. paraméter: user.employeeid<br/>(Ha nem egyezik meg a kimenet) 3. paraméter: user.extensionattribute1 |
| **StartWith()** | Ha a bemeneti kezdődik-e a megadott értéket jelenít meg egy attribútum vagy állandó. Ellenkező esetben egy másik kimeneti megadhat fel, ha nem egyezik meg.<br/>Például ha azt szeretné, ahol az érték a felhasználó employeeid Ha "US" kezdődik az ország jogcím kibocsátható, ellenkező esetben érdemes bővítményattribútum kimeneti. Ehhez a következő értékek állíthatók be:<br/>*A paraméter 1(input)*: felhasználó.ország<br/>*Érték*: "US"<br/>(Kimenet) 2. paraméter: user.employeeid<br/>(Ha nem egyezik meg a kimenet) 3. paraméter: user.extensionattribute1 |
| **Extract() - megfelelő után** | A karakterláncrészt adja vissza, miután a kulcs megegyezik-e a megadott értéket.<br/>Például ha a bemeneti érték "Finance_BSimon", a megfelelő értéke "Finance_", akkor az a jogcím-kimenet "BSimon". |
| **Extract() - egyeztetése előtt** | A karakterláncrészt adja vissza, amíg meg nem találja a megadott értéket.<br/>Például ha a bemeneti érték "BSimon_US", a megfelelő értéke "_US", akkor az a jogcím-kimenet "BSimon". |
| **Extract() - kapcsolódó között** | A karakterláncrészt adja vissza, amíg meg nem találja a megadott értéket.<br/>Például ha a bemeneti érték "Finance_BSimon_US", az első egyező értéke "Finance_", a második egyező érték "_US", akkor a kimenete a jogcím "BSimon". |
| **ExtractAlpha() - Prefix** | A karakterlánc a előtag betűrend szerinti rendezés részét adja vissza.<br/>Például ha a bemeneti érték "BSimon_123", majd visszaküldi "BSimon". |
| **ExtractAlpha() - Suffix** | A karakterlánc utótagja betűrend szerinti rendezés részét adja vissza.<br/>Például ha a bemeneti érték "123_Simon", majd visszaküldi "BSimon". |
| **ExtractNumeric() - előtag** | A karakterlánc a előtag numerikus részét adja vissza.<br/>Például ha a bemeneti érték "123_BSimon", majd visszaküldi "123". |
| **ExtractNumeric() - utótag** | A karakterlánc numerikus utótagot részét adja vissza.<br/>Például ha a bemeneti érték "BSimon_123", majd visszaküldi "123". |
| **IfEmpty()** | Ha a bemeneti adat null értékű vagy üres jelenít meg egy attribútum vagy konstans.<br/>Például, ha azt szeretné, a kimenetben egy extensionattribute tárolva, ha az adott felhasználó számára az employeeid üres attribútum. Ehhez a következő értékek állíthatók be:<br/>A paraméter 1(input): user.employeeid<br/>(Kimenet) 2. paraméter: user.extensionattribute1<br/>(Ha nem egyezik meg a kimenet) 3. paraméter: user.employeeid |
| **IfNotEmpty()** | Ha a bemenet nem null értékű vagy üres jelenít meg egy attribútum vagy konstans.<br/>Például, ha azt szeretné, a kimenetben egy attribútum egy extensionattribute tárolva, ha az adott felhasználó számára az employeeid nem üres. Ehhez a következő értékek állíthatók be:<br/>A paraméter 1(input): user.employeeid<br/>(Kimenet) 2. paraméter: user.extensionattribute1 |

További átalakítások van szüksége, ha beküldi egy ötletét, az a [az Azure AD Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) alatt a *SaaS-alkalmazás* kategória.

## <a name="next-steps"></a>További lépések

* [Alkalmazások kezelése az Azure ad-ben](../manage-apps/what-is-application-management.md)
* [Az alkalmazásokat, amelyek nem szerepelnek az Azure AD alkalmazáskatalógusában egyszeri bejelentkezés konfigurálása](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML-alapú egyszeri bejelentkezés hibaelhárítása](howto-v1-debug-saml-sso-issues.md)
