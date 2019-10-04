---
title: SAML-token jogcímek testreszabása vállalati alkalmazásokhoz az Azure AD-ben | Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre az SAML-jogkivonat által az Azure AD-ben közzétett vállalati alkalmazásokhoz kiadott jogcímeket.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e7681afe3f5361b17670312c8391349c650a89d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936777"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: Az SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz

Napjainkban a Azure Active Directory (Azure AD) támogatja az egyszeri bejelentkezést (SSO) a legtöbb nagyvállalati alkalmazással, beleértve az Azure AD-katalógusban és az egyéni alkalmazásokban előre integrált alkalmazásokat is. Amikor egy felhasználó az SAML 2,0 protokollal hitelesíti az alkalmazást az Azure AD-n keresztül, az Azure AD tokent küld az alkalmazásnak (HTTP-POSTon keresztül). Ezután az alkalmazás érvényesíti és használja a jogkivonatot a felhasználó bejelentkezésére a Felhasználónév és a jelszó kérése helyett. Ezek az SAML-tokenek a *jogcímek*által ismert felhasználóra vonatkozó adatokat tartalmaznak.

A *jogcím* olyan információ, amelyet az identitás-szolgáltató az adott felhasználóhoz tartozó jogkivonatban lévő felhasználóval kapcsolatos. Az [SAML-tokenben](https://en.wikipedia.org/wiki/SAML_2.0)ezeket az adatok JELLEMZŐEN az SAML-attribútum utasításában találhatók. A felhasználó egyedi AZONOSÍTÓját általában az SAML-tulajdonos jelöli, más néven a name Identifier.

Alapértelmezés szerint az Azure AD egy SAML-jogkivonatot bocsát ki az alkalmazásnak, amely tartalmaz egy `NameIdentifier` jogcímet, amelynek a felhasználó felhasználóneve (más néven az egyszerű felhasználónév) értéke az Azure AD-ben, amely egyedileg azonosíthatja a felhasználót. Az SAML-jogkivonat a felhasználó e-mail-címét, utónevét és vezetéknevét tartalmazó további jogcímeket is tartalmaz.

Az SAML-jogkivonatban kiadott jogcímek megtekintéséhez vagy módosításához nyissa meg az alkalmazást Azure Portalban. Ezután nyissa meg a **felhasználói attribútumok & jogcímek** szakaszt.

![Nyissa meg a felhasználói attribútumok & jogcímek szakaszt a Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Az SAML-jogkivonatban kiadott jogcímek szerkesztésének két lehetséges oka lehet:

* Az alkalmazáshoz a `NameIdentifier` vagy a NameID jogcímnek kell lennie, mint az Azure AD-ben tárolt Felhasználónév (vagy egyszerű felhasználónév).
* Az alkalmazás úgy lett írva, hogy egy másik jogcím-URI-t vagy jogcím-értéket igényel.

## <a name="editing-nameid"></a>NameID szerkesztése

A NameID (név azonosító érték) szerkesztése:

1. Nyissa meg a **név-azonosító érték** lapot.
1. Válassza ki azt az attribútumot vagy átalakítást, amelyre alkalmazni kívánja az attribútumot. Megadhatja azt a formátumot is, amelyre a NameID jogcím vonatkozik.

   ![A NameID (Name Identifier) értékének szerkesztése](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID formátuma

Ha az SAML-kérelem megadott formátumú NameIDPolicy tartalmaz, az Azure AD tiszteletben tartja a kérelem formátumát.

Ha az SAML-kérelem nem tartalmaz NameIDPolicy-elemet, az Azure AD a megadott formátumban adja ki a NameID. Ha nincs megadva a formátum, az Azure AD a jogcím forrásához társított alapértelmezett formátumot fogja használni.

A **név-azonosító formátum** legördülő listából választhatja ki az alábbi lehetőségek egyikét.

| NameID formátuma | Leírás |
|---------------|-------------|
| **Alapértelmezett** | Az Azure AD az alapértelmezett forrás formátumot fogja használni. |
| **Állandó** | Az Azure AD a NameID formátumot használja állandóként. |
| **E-mail cím** | Az Azure AD az EmailAddress formátumot fogja használni NameID formátumban. |
| **Meghatározatlan** | Az Azure AD nem megadott NameID formátumot használ. |

További információ a NameIDPolicy attribútumról: [egyszeri bejelentkezéses SAML protokoll](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attribútumok

Válassza ki a `NameIdentifier` (vagy NameID) jogcím kívánt forrását. A következő lehetőségek közül választhat.

| Name (Név) | Leírás |
|------|-------------|
| Email | A felhasználó e-mail-címe |
| userprincipalName | A felhasználó egyszerű felhasználóneve (UPN) |
| onpremisessamaccount | A helyszíni Azure AD-ből szinkronizált SAM-fiók neve |
| oid | a felhasználó ObjectId az Azure AD-ben |
| EmployeeID | a felhasználó Alkalmazottkód |
| Címtárkiterjesztések | A helyszíni [Active Directory szinkronizált címtárszolgáltatás-bővítmények Azure ad Connect Sync használatával](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Bővítmény attribútumai 1-15 | Az Azure AD-séma kibővítéséhez használt helyszíni bővítmény attribútumai |

További információ: [Table 3: Érvényes azonosító értékek: Source @ no__t-0.

### <a name="special-claims---transformations"></a>Speciális jogcímek – átalakítások

Használhatja a jogcím-átalakítási funkciókat is.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartományi utótagot az e-mail-címről vagy az egyszerű felhasználónévből. Ez csak a Felhasználónév első részét (például "joe_smith") adja át a joe_smith@contoso.com helyett. |
| **Csatlakozás ()** | Egy attribútumot ellenőrzött tartománnyal társít. Ha a kiválasztott felhasználóazonosító-érték tartományhoz tartozik, a rendszer kibontja a felhasználónevet a kiválasztott ellenőrzött tartomány hozzáfűzéséhez. Ha például az e-mailt (joe_smith@contoso.com) adja meg a felhasználói azonosító értékként, és a contoso.onmicrosoft.com-t ellenőrzött tartományként választja, akkor ez joe_smith@contoso.onmicrosoft.com értéket eredményez. |
| **ToLower()** | A kijelölt attribútum karaktereit kisbetűs karakterekké alakítja. |
| **ToUpper()** | A kijelölt attribútum karaktereit nagybetűvé alakítja. |

## <a name="adding-application-specific-claims"></a>Alkalmazás-specifikus jogcímek hozzáadása

Alkalmazás-specifikus jogcímek hozzáadása:

1. A **felhasználói attribútumok & jogcímek**területen válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** lap megnyitásához.
1. Adja meg a jogcímek **nevét** . Az értéknek nem feltétlenül kell követnie egy URI-mintát az SAML-specifikáció alapján. Ha URI-mintázatra van szüksége, azt a **névtér** mezőbe helyezheti.
1. Válassza ki azt a **forrást** , ahol a jogcím le fogja kérdezni az értékét. Kiválaszthat egy felhasználói attribútumot a forrás attribútum legördülő menüből, vagy alkalmazhat egy átalakítást a felhasználói attribútumra, mielőtt jogcímet kibocsátja jogcímként.

### <a name="application-specific-claims---transformations"></a>Alkalmazásspecifikus jogcímek – átalakítások

Használhatja a jogcím-átalakítási funkciókat is.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | Eltávolítja a tartományi utótagot az e-mail-címről vagy az egyszerű felhasználónévből. Ez csak a Felhasználónév első részét (például "joe_smith") adja át a joe_smith@contoso.com helyett. |
| **Csatlakozás ()** | Létrehoz egy új értéket két attribútum összekapcsolásával. Igény szerint elválasztót is használhat a két attribútum között. |
| **ToLower()** | A kijelölt attribútum karaktereit kisbetűs karakterekké alakítja. |
| **ToUpper()** | A kijelölt attribútum karaktereit nagybetűvé alakítja. |
| **Tartalmazza ()** | Attribútumot vagy állandót ad eredményül, ha a bemenet megfelel a megadott értéknek. Ellenkező esetben további kimenetet is megadhat, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kibocsátani, amelyben az érték a felhasználó e-mail-címe, ha a "@contoso.com" tartományt tartalmazza, ellenkező esetben az egyszerű felhasználónevet is ki szeretné állítani. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (bemenet)* : user. e-mail<br/>*Érték*: "@contoso.com"<br/>2\. paraméter (kimenet): user. e-mail<br/>3\. paraméter (kimenet, ha nincs egyezés): user. userPrincipalName |
| **EndWith()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet a megadott értékkel végződik. Ellenkező esetben további kimenetet is megadhat, ha nincs egyezés.<br/>Ha például olyan jogcímet szeretne kibocsátani, amelyben az érték a felhasználó Alkalmazottkód értéke, ha az Alkalmazottkód "000" végződéssel végződik, ellenkező esetben egy Extension attribútumot szeretne kiállítani. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (bemenet)* : user. Alkalmazottkód<br/>*Érték*: 000<br/>2\. paraméter (kimenet): user. Alkalmazottkód<br/>3\. paraméter (kimenet, ha nincs egyezés): user. extensionAttribute1 |
| **StartWith()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet a megadott értékkel kezdődik. Ellenkező esetben további kimenetet is megadhat, ha nincs egyezés.<br/>Ha például egy olyan jogcímet szeretne kibocsátani, amelyben az érték a felhasználó Alkalmazottkód értéke, ha az ország/régió az "USA"-val kezdődik, ellenkező esetben kimeneti attribútumot szeretne kiállítani. Ehhez a következő értékeket kell konfigurálnia:<br/>*1. paraméter (bemenet)* : felhasználó. ország<br/>*Érték*: USA<br/>2\. paraméter (kimenet): user. Alkalmazottkód<br/>3\. paraméter (kimenet, ha nincs egyezés): user. extensionAttribute1 |
| **Kinyerés () – a megfeleltetés után** | Azt az alsztringet adja vissza, amely a megadott értéknek felel meg.<br/>Ha például a bemeneti érték "Finance_BSimon", a megfelelő érték "Finance_", akkor a jogcím kimenete "BSimon". |
| **Kinyerés () – a megfeleltetés előtt** | Az alsztringet adja vissza, amíg meg nem felel a megadott értéknek.<br/>Ha például a bemeneti érték "BSimon_US", a megfelelő érték "_US", akkor a jogcím kimenete "BSimon". |
| **Kinyerés () – megfeleltetések között** | Az alsztringet adja vissza, amíg meg nem felel a megadott értéknek.<br/>Ha például a bemeneti érték "Finance_BSimon_US", az első egyező érték "Finance_", a második egyező érték "_US", akkor a jogcím kimenete "BSimon". |
| **ExtractAlpha() - Prefix** | A karakterlánc ábécé részét adja vissza.<br/>Ha például a bemeneti érték "BSimon_123", akkor a "BSimon" értéket adja vissza. |
| **ExtractAlpha() - Suffix** | A karakterlánc ábécé részét képező utótagot adja vissza.<br/>Ha például a bemeneti érték "123_Simon", akkor a "Simon" értéket adja vissza. |
| **ExtractNumeric () – előtag** | A sztring numerikus részét adja vissza.<br/>Ha például a bemeneti érték "123_BSimon", a "123" értéket adja vissza. |
| **ExtractNumeric () – utótag** | A karakterlánc numerikus részét adja vissza.<br/>Ha például a bemeneti érték "BSimon_123", a "123" értéket adja vissza. |
| **IfEmpty()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet null értékű vagy üres.<br/>Ha például egy extensionattribute tárolt attribútumot szeretne kiállítani, ha egy adott felhasználó Alkalmazottkód értéke üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1\. paraméter (bemenet): user. Alkalmazottkód<br/>2\. paraméter (kimenet): user. extensionAttribute1<br/>3\. paraméter (kimenet, ha nincs egyezés): user. Alkalmazottkód |
| **IfNotEmpty()** | Attribútumot vagy állandó értéket ad eredményül, ha a bemenet nem null vagy üres.<br/>Ha például egy extensionattribute tárolt attribútumot szeretne kiállítani, ha az adott felhasználóhoz tartozó Alkalmazottkód nem üres. Ehhez a következő értékeket kell konfigurálnia:<br/>1\. paraméter (bemenet): user. Alkalmazottkód<br/>2\. paraméter (kimenet): user. extensionAttribute1 |

Ha további átalakításokra van szüksége, küldje el ötletét az [Azure ad visszajelzési fórumában](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) az *SaaS-alkalmazás* kategóriában.

## <a name="next-steps"></a>További lépések

* [Alkalmazások kezelése az Azure AD-ben](../manage-apps/what-is-application-management.md)
* [Egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem szerepelnek az Azure AD-alkalmazás-katalógusban](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML-alapú egyszeri bejelentkezés – problémamegoldás](howto-v1-debug-saml-sso-issues.md)
