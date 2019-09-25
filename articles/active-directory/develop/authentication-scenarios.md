---
title: Hitelesítés a Microsoft Identity platformon | Azure
description: Ismerkedjen meg a Microsoft Identity platform hitelesítésével, az alkalmazás modelljével, az API-val, a kiépítés és a Microsoft Identity platform által támogatott leggyakoribb hitelesítési forgatókönyvekkel.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/23/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5214fc26d299c6abb72ed6cd448728903e78f
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272542"
---
# <a name="what-is-authentication"></a>Mi a hitelesítés?

A *hitelesítési* valamely fél érvényes hitelesítő adatainak bekérése, amely egy identitás- és hozzáférés-vezérléshez használható rendszerbiztonsági tag létrehozását eredményezi. Egyszerűbb megfogalmazásban a hitelesítés az a folyamat, amelynek során a felhasználó bebizonyítja, hogy az, akinek vallja magát. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni.

Az *engedélyezés* egy műveletre jogosító engedély megadása egy hitelesített rendszerbiztonsági tag számára. Meghatározza az elérhető adatok körét és az azokon végrehajtható műveleteket. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni.

A Microsoft Identity platform leegyszerűsíti az alkalmazások fejlesztőinek hitelesítését azáltal, hogy szolgáltatásként szolgáltatja a hitelesítést, és támogatja az iparági szabványnak megfelelő protokollokat, például a OAuth 2,0 és az OpenID Connect szolgáltatást, valamint a különböző platformokhoz készült nyílt forráskódú kódtárakat segít a kódolás gyors megkezdésében.

A Microsoft Identity platform programozási modellje két fő felhasználási esettel rendelkezik:

* Az OAuth 2.0 engedélyezési folyamat – amikor az erőforrás tulajdonosa adja meg az engedélyt az ügyfélalkalmazás számára, hogy az elérhesse az erőforrás-tulajdonos erőforrásait.
* Az erőforrások ügyfél általi elérése – ezt az erőforrás-kiszolgáló valósítja meg a hozzáférési jogkivonatokban foglalt jogcímértékek alapján, amelyek a vonatkozó hozzáférés-vezérlési döntések meghozatalát irányítják.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>A hitelesítés alapjai a Microsoft Identity platformon

Vegyük a legalapvetőbb forgatókönyvet, amelyben az identitásokat alkalmazni kell: a felhasználónak a böngészőben hitelesítenie kell magát egy webalkalmazásban. Az alábbi ábra ezt a forgatókönyvet mutatja be:

![Webalkalmazásba való bejelentkezés áttekintése](./media/authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Az ábrán lévő összetevőkről a következőket érdemes tudni:

* A Microsoft Identity platform az identitás-szolgáltató. Az identitásszolgáltató feladata a szervezet címtárában lévő felhasználók és alkalmazások identitásának ellenőrzése, majd a felhasználók és alkalmazások sikeres hitelesítését követően a biztonsági jogkivonatok kiadása.
* Azure Active Directory (Azure AD) regisztrálni kell egy olyan alkalmazást, amely szeretné kiszervezni a Microsoft Identity platform hitelesítését. Az Azure AD regisztrálja és egyedileg azonosítja az alkalmazást a címtárban.
* A fejlesztők használhatják a nyílt forráskódú Microsoft Identity platform hitelesítési kódtárait, hogy a protokoll részletes adatainak felügyeletével megkönnyítsék a hitelesítést. További információ: Microsoft Identity platform 2.0-s [hitelesítési kódtárak](reference-v2-libraries.md) és 1.0-s verziójú [hitelesítési kódtárak](active-directory-authentication-libraries.md).
* A felhasználó hitelesítését követően az alkalmazásnak ellenőriznie kell a felhasználó biztonsági jogkivonatát, hogy megbizonyosodhasson róla a hitelesítés sikeréről. A rövid útmutatók, oktatóanyagok és a különböző nyelvekhez és keretrendszerekhez elérhető kódminták mutatják be, hogy az alkalmazásnak mit kell tennie.
  * Az alkalmazások gyors felépítésével és a jogkivonatok beszerzését és frissítését, a felhasználók bejelentkeztetését és a felhasználóadatok megjelenítését végző funkcionalitás hozzáadásával kapcsolatban lásd a dokumentáció **Rövid útmutatók** szakaszát.
  * A hitelesítéssel kapcsolatos legfontosabb fejlesztői feladatok elvégzéséhez, például a hozzáférési jogkivonatok beszerzéséhez és azok Microsoft Graph API és egyéb API-k hívásaiban való használatához, a hagyományos böngészőalapú alkalmazásokon belüli, OpenID Connect használatával végzett Microsoft-bejelentkezések megvalósításához, valamint hasonló feladatokhoz használható részletes, forgatókönyv-alapú eljárásokat a dokumentáció **Oktatóanyagok** szakaszában találja.
  * A kódminták letöltéséhez látogasson el a [GitHubra](https://github.com/Azure-Samples?q=active-directory).
* A hitelesítési folyamathoz használt kérés-válasz folyamatot az alkalmazott hitelesítési protokoll határozza meg, például az OAuth 2.0, az OpenID Connect, a WS-Federation vagy az SAML 2.0. A protokollokkal kapcsolatos további információkért tekintse meg a dokumentációjának **fogalmak > hitelesítési protokoll** című szakaszát.

A fenti példaforgatókönyvben az alkalmazásokat az alábbi két szerepkör szerint osztályozhatjuk:

* Olyan alkalmazások, amelyeknek biztonságosan kell hozzáférniük az erőforrásokhoz
* Olyan alkalmazások, amelyek az erőforrások szerepét töltik be

### <a name="how-each-flow-emits-tokens-and-codes"></a>Az egyes folyamatok tokeneket és kódokat bocsátanak ki

Az ügyfél felépítésének módjától függően a Microsoft Identity platform által támogatott hitelesítési folyamatok közül egy (vagy több) is használható.  Ezek a folyamatok különféle tokeneket hozhatnak létre (id_tokens, frissítési tokeneket, hozzáférési jogkivonatokat), valamint engedélyezési kódokat, és különböző jogkivonatokat igényelnek a működésük érdekében. Ez a diagram egy áttekintést nyújt:

|Folyamat | Igényel | id_token | hozzáférési jogkivonat | jogkivonat frissítése | engedélyezési kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamatábrája](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicit folyamat](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC folyamat](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Jogkivonat-beváltások frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | jogkivonat frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md) | hozzáférési jogkivonat| x| x| x| |
|[Eszköz kódjának folyamata](v2-oauth2-device-code.md) | | x| x| x| |
|[Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazás)| | |

**Megjegyzések**:

Az implicit módban kiállított tokenek hossza korlátozott, mert az URL-cím (ahol `response_mode` `query` a vagy `fragment`a) a böngészőbe kerül vissza.  Egyes böngészőkben korlátozva van a böngészőablakban elhelyezhető URL-cím mérete, és a művelet nem hajtható végre, ha túl hosszú.  Így ezek a jogkivonatok nem rendelkeznek `groups` vagy `wids` jogcímek. 


Most, hogy áttekinti az alapismereteket, olvassa el a következőt: az Identity app Model és az API megismerése, a kiépítés működése a Microsoft Identity platformon, valamint a Microsoft Identity platform által támogatott általános forgatókönyvekre mutató hivatkozások.

## <a name="application-model"></a>Alkalmazásmodell

A Microsoft Identity platform a következő két fő funkció megvalósítására szolgáló, adott modellt használó alkalmazásokat jeleníti meg:

* **Az alkalmazás azonosítása a támogatott hitelesítési protokollnak megfelelően** – Ez az összes azonosító, URL-cím, titkos kulcs és a hitelesítés alkalmával szükséges összes vonatkozó információ számba vételét jelenti. Itt a Microsoft Identity platform:

    * Tárolja a futásidejű hitelesítés támogatásához szükséges összes adatot.
    * Tárolja a szükséges adatokat annak eldöntéséhez, hogy az alkalmazásoknak milyen erőforrásokat kellhet elérniük, és az adott kéréseket teljesíteni kell-e, illetve milyen körülmények között kell teljesíteni.
    * Biztosítja az infrastruktúrát az alkalmazásregisztráció megvalósításához az alkalmazásfejlesztő bérlőjén és a többi Azure AD-bérlőn.

* **Kezelheti a felhasználói hozzájárulásukat a jogkivonat-kérelmek ideje alatt, és megkönnyítheti az alkalmazások dinamikus kihelyezését a bérlők között** – itt, a Microsoft Identity platformon:

    * Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
    * Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

A Microsoft Identity platformban az **Application Object** egy alkalmazást ír le absztrakt entitásként. A fejlesztők alkalmazásokkal dolgoznak. A központi telepítés ideje alatt a Microsoft Identity platform egy adott alkalmazás-objektumot használ tervrajzként egy **egyszerű szolgáltatásnév**létrehozásához, amely egy alkalmazás konkrét példányát jelöli a címtárban vagy a bérlőn belül. Ez a szolgáltatásnév határozza meg, hogy az alkalmazás ténylegesen milyen tevékenységeket hajthat végre az adott célcímtárban, mely erőforrásokat érheti el, és így tovább. A Microsoft Identity platform egy egyszerű szolgáltatásnevet hoz létre egy alkalmazás-objektumból a **beleegyező**módon.

Az alábbi ábrán egy egyszerűsített Microsoft Identity platform kiépítési folyamata látható.  Ebben az esetben két bérlő létezik (A és B), ahol a bérlő az alkalmazás tulajdonosa, a B bérlő pedig az alkalmazást egy egyszerű szolgáltatásnév használatával hozza létre.  

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. A B bérlő felhasználója megpróbál bejelentkezni az alkalmazásba, az engedélyezési végpont jogkivonatot kér az alkalmazáshoz.
1. A felhasználói hitelesítő adatok beszerzése és ellenőrzése hitelesítéssel történik.
1. A rendszer megkéri a felhasználót, hogy adjon hozzáférést az alkalmazásnak a B bérlőhöz való hozzáféréshez.
1. A Microsoft Identity platform az A bérlő Application objektumát használja tervként egy egyszerű szolgáltatásnév létrehozásához a B bérlőben
1. A felhasználó megkapja az igényelt jogkivonatot

A folyamat tetszőleges mennyiségben ismételhető a többi bérlőre (C, D és így tovább) vonatkozóan. Az A bérlő megőrzi az alkalmazás tervét (Application Object). A többi bérlőn, amelyekre az alkalmazás hozzájárulást kapott, továbbra is a felhasználók és a rendszergazdák határozhatják meg az egyedi bérlőkön lévő megfelelő szolgáltatásnév-objektumokon keresztül, hogy az alkalmazás milyen tevékenységeket hajthat végre. További információ: [alkalmazás-és szolgáltatásnév-objektumok a Microsoft Identity platformon](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Jogcímek a Microsoft Identity platform biztonsági jogkivonatokban

A Microsoft Identity platform által kiadott biztonsági jogkivonatok (hozzáférési és azonosító jogkivonatok) jogcímeket vagy a hitelesített tulajdonossal kapcsolatos információkat tartalmaznak. Az alkalmazások a jogcímeket különféle műveletekhez használhatják:

* A jogkivonat érvényesítése
* Az alany címtárbérlőjének azonosítása
* A felhasználó adatainak megjelenítése
* Az alany engedélyezésének meghatározása

Az egyes biztonsági jogkivonatokban lévő jogcímek a jogkivonat típusától, a felhasználó hitelesítéséhez használt hitelesítő adatoktól és az alkalmazás konfigurációjától függenek.

A Microsoft Identity platform által kibocsátott egyes típusú jogcímek rövid leírását az alábbi táblázat tartalmazza. Részletesebb információkért tekintse meg a Microsoft Identity platform által kiadott [hozzáférési jogkivonatokat](access-tokens.md) és [azonosító jogkivonatokat](id-tokens.md) .

| Igénylés | Leírás |
| --- | --- |
| Alkalmazásazonosító | A jogkivonatot használó alkalmazást azonosítja. |
| Célközönség | A fogadó erőforrást azonosítja, amelynek a jogkivonat szól. |
| Alkalmazáshitelesítési környezet osztályhivatkozása | Az ügyfél hitelesítési módját adja meg (nyilvános vagy bizalmas ügyfél). |
| Hitelesítési időpont | A hitelesítés megtörténtének dátumát és időpontját rögzíti. |
| Hitelesítési módszer | A jogkivonatalany hitelesítésének módját adja meg (jelszó, tanúsítvány stb.). |
| Utónév | A felhasználó Azure AD-ben beállított utónevét adja meg. |
| Csoportok | Az olyan Azure AD-csoportok objektumazonosítóit adja meg, amelyeknek az adott felhasználó tagja. |
| Identitásszolgáltató | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. |
| Kibocsátás időpontja | A jogkivonat kibocsátásának időpontját rögzíti, amely alapján általában a jogkivonat frissességét szokás elbírálni. |
| Kiállító | A jogkivonatot kiadó biztonsági jegykiadó szolgáltatást, valamint az Azure AD-bérlőt azonosítja. |
| Vezetéknév | A felhasználó Azure AD-ben beállított vezetéknevét adja meg. |
| Name (Név) | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. |
| Objektumazonosító | Az alany Azure AD-beli megváltoztathatatlan egyedi azonosítóját adja meg. |
| Szerepkörök | A felhasználónak kiosztott Azure AD-beli alkalmazás-szerepkörök rövid neveit adja meg. |
| Scope | Az ügyfélalkalmazás számára megadott engedélyeket jelzi. |
| Tárgy | Azt a szolgáltatásnevet adja meg, amelynek az adatait a jogkivonat hitelesíti. |
| Bérlőazonosító | A jogkivonatot kibocsátó címtárbérlő megváltoztathatatlan egyedi azonosítóját adja meg. |
| Jogkivonat élettartama | A jogkivonat érvényességi idejét határozza meg. |
| Felhasználó egyszerű neve | Az alany egyszerű felhasználónevét adja meg. |
| Version | A jogkivonat verziószámát adja meg. |

## <a name="next-steps"></a>További lépések

* Tudnivalók a [Microsoft Identity platform által támogatott alkalmazások típusairól és forgatókönyvekről](app-types.md)
