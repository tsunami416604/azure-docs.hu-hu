---
title: Ajánlott eljárások a Microsoft Identity platformhoz | Azure
description: Ismerje meg az ajánlott eljárásokat, ajánlásokat és általános áttekintéseket a Microsoft Identity platformmal való integráció során.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1ce33e90cab1c5172cbf14470f24345d0446b0de
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200286"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft Identity platform – ajánlott eljárások és javaslatok

Ez a cikk az ajánlott eljárásokat, javaslatokat és általános áttekintéseket ismerteti a Microsoft Identity platformmal való integráció során.  Ez az ellenőrzőlista kiváló minőségű és biztonságos integrációt biztosít. Rendszeresen tekintse át a listát, és győződjön meg róla, hogy az alkalmazás az Identity platformmal való integrációjának minőségét és biztonságát tartja karban. Az ellenőrzőlista nem alkalmas a teljes alkalmazás áttekintésére. Az ellenőrzőlista tartalma változhat, ahogy javítunk a platformon.

Ha most ismerkedik az első lépésekkel, tekintse meg a [Microsoft Identity platform dokumentációját](index.yml) , ahol megismerheti a hitelesítési alapismereteket, az alkalmazás forgatókönyveit a Microsoft Identity platformon, és így tovább.

Az alábbi ellenőrzőlista használatával biztosíthatja, hogy az alkalmazás hatékonyan legyen integrálva a [Microsoft Identity platformmal](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Alapvető beállítások

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Olvassa el és Ismerje meg a [Microsoft Platform szabályzatait](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Győződjön meg arról, hogy az alkalmazás megfelel a felhasználók és a platform elleni védelemhez tervezett feltételeknek. |

## <a name="ownership"></a>Tulajdon

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Győződjön meg arról, hogy az alkalmazások regisztrálásához és kezeléséhez használt fiókhoz tartozó információk naprakészek. |

## <a name="branding"></a>Branding

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tartsa be az [alkalmazások védjegyezési irányelveit](howto-add-branding-in-azure-ad-apps.md). |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Adjon meg egy értelmes nevet és emblémát az alkalmazáshoz. Ezek az információk az [alkalmazás jóváhagyására vonatkozó kérdésben](application-consent-experience.md)jelennek meg. Győződjön meg arról, hogy a név és az embléma megfelel a vállalatának vagy termékének, hogy a felhasználók tájékozott döntéseket hozhassanak. Győződjön meg arról, hogy nem sérti a védjegyeket. |

## <a name="privacy"></a>Adatvédelem

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Adja meg az alkalmazás használati feltételeire és adatvédelmi nyilatkozatára mutató hivatkozásokat. |

## <a name="security"></a>Biztonság

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az átirányítási URI-k kezelése: <ul><li>Őrizze meg az összes átirányítási URI tulajdonosát, és tartsa naprakészen a DNS-rekordokat.</li><li>Ne használjon helyettesítő karaktereket (*) az URI-k között.</li><li>Webalkalmazások esetén győződjön meg arról, hogy az összes URI biztonságos és titkosított (például https-sémák használatával).</li><li>Nyilvános ügyfelek esetén használjon platform-specifikus átirányítási URI-ket, ha vannak ilyenek (főleg az iOS és az Android esetében). Ellenkező esetben használja a nagy mennyiségű véletlenszerű átirányítási URI-t, hogy elkerülje az ütközéseket, amikor visszahívja az alkalmazást.</li><li>Ha az alkalmazást egy elkülönített webügynök használja, akkor a https://login.microsoftonline.com/common/oauth2/nativeclient használatát is használhatja.</li><li>Tekintse át és vágja fel az összes fel nem használt vagy szükségtelen átirányítási URI-t a szokásos módon.</li></ul> |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az alkalmazás regisztrálva van egy címtárban, csökkentse és manuálisan figyelje az alkalmazás-regisztrációs tulajdonosok listáját. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha explicit módon nem szükséges, ne engedélyezze a [OAuth2 implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) támogatását. [Itt](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)tájékozódhat az érvényes forgatókönyvről. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | A Felhasználónév/jelszó túllépése. Ne használja az [erőforrás-tulajdonosi jelszó hitelesítő adatait (ROPC)](v2-oauth-ropc.md), amely közvetlenül kezeli a felhasználók jelszavát. Ennek a folyamatnak magas fokú megbízhatóságra és felhasználói expozícióra van szüksége, és csak akkor használható, ha más, biztonságosabb, a folyamatok nem használhatók. Erre a folyamatra továbbra is szükség van bizonyos helyzetekben (például DevOps), de ügyeljen arra, hogy a használatával korlátozásokat fog alkalmazni az alkalmazására.  A modernebb megközelítésekhez olvassa el a [hitelesítési folyamatokat és az alkalmazás forgatókönyveit](authentication-flows-app-scenarios.md).|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | A bizalmas alkalmazás hitelesítő adatainak védelme és kezelése webalkalmazásokhoz, webes API-khoz és Daemon-alkalmazásokhoz. [Tanúsítvány hitelesítő adatainak](active-directory-certificate-credentials.md)használata, nem a jelszó hitelesítő adatai (az ügyfél titkos kulcsa). Ha jelszó-hitelesítő adatokat kell használnia, ne állítsa be manuálisan. Ne tárolja a hitelesítő adatokat a kódban vagy a konfigurációban, és soha ne engedélyezze őket emberek általi kezelésre. Ha lehetséges, használjon [felügyelt identitásokat az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) vagy [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) a hitelesítő adatok tárolásához és rendszeres elforgatásához. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Győződjön meg arról, hogy az alkalmazás a legkevésbé megfelelő jogosultságokat kéri. Csak olyan engedélyeket kérjen, amelyeket az alkalmazásnak teljesen szüksége van, és csak akkor, amikor szüksége van rájuk. Ismerje meg a különböző [típusú engedélyeket](v1-permissions-and-consent.md#types-of-permissions). Csak szükség esetén használja az alkalmazás engedélyeit; Ha lehetséges, használjon delegált engedélyeket. Az Microsoft Graph engedélyek teljes listájáért tekintse meg ezt az [engedélyeket](https://docs.microsoft.com/graph/permissions-reference)ismertető témakört. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha a Microsoft Identity platform használatával biztosít API-t, alaposan gondolja át, hogy milyen engedélyekkel kell rendelkeznie. Gondolja át, hogy mi a megfelelő részletesség a megoldáshoz, és hogy a jogosultság (ok) rendszergazdai hozzájárulást igényel. Az engedélyezési döntések meghozatala előtt keresse meg a várt engedélyeket a bejövő jogkivonatokban. |

## <a name="implementation"></a>Megvalósítás

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Használjon modern hitelesítési megoldásokat (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) a felhasználók biztonságos bejelentkezéséhez. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) |  Ne program közvetlenül olyan protokollok ellen, mint a OAuth 2,0 és az Open ID. Ehelyett használja a [Microsoft hitelesítési függvénytárát (MSAL)](msal-overview.md). A MSAL-kódtárak biztonságos módon csomagolják a biztonsági protokollokat egy könnyen használható könyvtárban, és beépített támogatást biztosít a [feltételes hozzáférési](/azure/active-directory/conditional-access/overview) forgatókönyvek, az eszközre érvényes [egyszeri bejelentkezés (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)és a beépített jogkivonat-gyorsítótárazás támogatásához. További információkért tekintse meg a Microsoft által támogatott [ügyféloldali kódtárak](reference-v2-libraries.md#microsoft-supported-client-libraries) és a [köztes kódtárak](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) listáját, valamint a [kompatibilis, harmadik féltől származó ügyféloldali kódtárak](reference-v2-libraries.md#compatible-client-libraries)listáját.<br/><br/>Ha meg kell adnia a hitelesítési protokollok kódját, olyan módszertant kell követnie, mint például a [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Ügyeljen arra, hogy az egyes protokollokra vonatkozó szabványok részletes leírásában a biztonsági szempontokat kell figyelembe venni.|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) |  Meglévő alkalmazások migrálása a [Azure Active Directory Authentication Library (ADAL)](active-directory-authentication-libraries.md) szolgáltatásból a [Microsoft hitelesítési könyvtárába](msal-overview.md). A MSAL a Microsoft legújabb Identity platform-megoldása, és a ADAL előnyben részesített. A szolgáltatás .NET, JavaScript, Android, iOS, macOS rendszeren érhető el, és a Python és a Java nyilvános előzetes verziójában is elérhető. További információ: [ADAL.net](msal-net-migration.md), [ADAL. js](msal-compare-msal-js-and-adal-js.md), [ADAL.net és iOS Broker-](msal-net-migration-ios-broker.md) alkalmazások áttelepítése.|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) |  A Mobile apps esetében minden platformot az alkalmazás regisztrációs felületének használatával konfigurálhat. Ahhoz, hogy az alkalmazás kihasználja az egyszeri bejelentkezés Microsoft Authenticator vagy a Microsoft Céges portál előnyeit, az alkalmazásnak "közvetítő átirányítási URI-t" kell beállítania. Ez lehetővé teszi a Microsoft számára, hogy a hitelesítés után visszaadja a vezérlést az alkalmazásnak. Az egyes platformok konfigurálásakor az alkalmazás regisztrációs felülete végigvezeti Önt a folyamaton. Egy működő példa letöltéséhez használja a rövid útmutatót. IOS rendszeren használja a Brokers és a System Webview lehetőséget, amikor csak lehetséges.|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) |  A Web Apps vagy a webes API-k esetében fiókon belül egy jogkivonat-gyorsítótárat kell megőrizni.  A webalkalmazások esetében a jogkivonat-gyorsítótárat a fiók AZONOSÍTÓjának kell megadnia.  Webes API-k esetében a fióknak az API meghívásához használt jogkivonat kivonatával kell megjelennie. A MSAL.NET egyéni jogkivonat-gyorsítótárazási szerializálást biztosít a .NET-keretrendszer és a .NET Core alplatformok számára. A biztonság és a teljesítmény érdekében javasoljuk, hogy felhasználónként egy gyorsítótárat szerializáljon. További információ: a jogkivonat- [gyorsítótár szerializálásának](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)áttekintése.|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az alkalmazás által igényelt adatai [Microsoft Graphon](https://developer.microsoft.com/graph)keresztül érhetők el, akkor az egyes API-k helyett az Microsoft Graph-végponton kell megadnia az ehhez az adatkezeléshez szükséges engedélyeket. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) |Ne tekintse meg a hozzáférési jogkivonat értékét, vagy próbálja meg ügyfélként elemezni.  Megváltoztathatják az értékeket, a formátumokat, vagy a figyelmeztetés nélkül is titkosítva lesznek – mindig használja a id_token, ha az ügyfélnek meg kell tanulnia valamit a felhasználóval kapcsolatban, vagy meg kell hívnia Microsoft Graph.  Csak a webes API-knak kell elemezni a hozzáférési jogkivonatokat (mivel ezek a formátum és a titkosítási kulcsok beállítása). |

## <a name="end-user-experience"></a>Végfelhasználói élmény

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ismerje meg [a hozzájárulási élményt](application-consent-experience.md) , és konfigurálja az alkalmazás beleegyezési kérésének részleteit, hogy a végfelhasználók és a rendszergazdák elegendő információval rendelkezzenek az alkalmazás megbízhatóságának megállapításához. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Csökkentse azt az időtartamot, ameddig a felhasználónak be kell írnia a bejelentkezési hitelesítő adatokat, miközben az alkalmazás használata közben csendes hitelesítést (csendes jogkivonat-beszerzést) kell megadnia az interaktív folyamatok előtt. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Minden bejelentkezéshez ne használja a "prompt = beleegyezés" kifejezést. Csak a prompt = beleegyezés használata, ha úgy döntött, hogy további engedélyek megadását kell kérnie (például ha módosította az alkalmazás szükséges engedélyeit). |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha szükséges, bővítse alkalmazását felhasználói adataival. A [Microsoft Graph API](https://developer.microsoft.com/graph) használatával egyszerűen elvégezhető a megoldás. A [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) eszköz, amely segítséget nyújt az első lépésekhez. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Regisztrálja az alkalmazás által igényelt engedélyek teljes készletét, hogy a rendszergazdák könnyen megadhatják a hozzájárulásukat a Bérlőnek. Futtassa a [növekményes beleegyezést](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) a futtatáskor, hogy a felhasználók tisztában legyenek azzal, hogy az alkalmazás Miért kér olyan engedélyeket, amelyek az első indításkor érinthetik vagy megzavarják a felhasználókat. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Hozzon létre [tiszta egyszeri kijelentkezési élményt](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Ez egy adatvédelmi és biztonsági követelmény, és jó felhasználói élményt tesz lehetővé. |

## <a name="testing"></a>Tesztelés

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tesztelje azokat a [feltételes hozzáférési házirendeket](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , amelyek befolyásolhatják a felhasználók számára az alkalmazás használatának lehetőségét. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tesztelje alkalmazását az összes olyan lehetséges fiókkal, amelyet támogatni kíván (például munkahelyi vagy iskolai fiókok, személyes Microsoft-fiókok, alárendelt fiókok és szuverén fiókok). |

## <a name="additional-resources"></a>További források

Részletes információk a 2.0-s verzióról:

* [Microsoft Identity platform (v 2.0 – áttekintés)](v2-overview.md)
* [Microsoft Identity platform-protokollok – dokumentáció](active-directory-v2-protocols.md)
* [Hozzáférési jogkivonatok referenciája](access-tokens.md)
* [Azonosító jogkivonatok referenciája](id-tokens.md)
* [A hitelesítési kódtárak referenciája](reference-v2-libraries.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
