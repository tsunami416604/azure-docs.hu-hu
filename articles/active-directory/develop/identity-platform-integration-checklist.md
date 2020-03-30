---
title: Gyakorlati tanácsok a Microsoft identitáskezelési platformjához | Azure
description: Ismerje meg a gyakorlati tanácsok, javaslatok és közös felügyeleteket a Microsoft identitáskezelési platformmal való integráció során.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050502"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>A Microsoft identity platform gal kapcsolatos gyakorlati tanácsai és javaslatai

Ez a cikk a Microsoft identitáskezelési platformmal való integráció során ismerteti az ajánlott eljárásokat, javaslatokat és közös felügyeleteket.  Ez az ellenőrzőlista kiváló minőségű és biztonságos integrációt eredményez. Tekintse át ezt a listát rendszeresen, hogy az alkalmazás és az identitásplatform közötti integráció minősége és biztonsága megmaradjon. Az ellenőrzőlista nem a teljes alkalmazás áttekintésére szolgál. Az ellenőrzőlista tartalma változhat, ahogy fejlesztjük a platformot.

Ha még csak most ismerkedik, tekintse meg a [Microsoft identity platform dokumentációját,](index.yml) amely ből megtudhatja, hogy miként lehet tudni a hitelesítési alapismeretekről, az alkalmazásforgatókönyvekről a Microsoft identity platformon és egyebeket.

Az alábbi ellenőrzőlista segítségével biztosíthatja, hogy alkalmazása hatékonyan integrálva legyen a [Microsoft identitásplatformmal.](https://docs.microsoft.com/azure/active-directory/develop/)

## <a name="basics"></a>Alapvető beállítások

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Olvassa el és ismerje meg a [Microsoft platformszabályzatát.](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409) Győződjön meg arról, hogy az alkalmazás betartja a feltételeket vázolt, mivel azok célja, hogy megvédje a felhasználók és a platform. |

## <a name="ownership"></a>Tulajdonjog

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Győződjön meg arról, hogy az alkalmazások regisztrálásához és kezeléséhez használt fiókhoz társított adatok naprakészek. |

## <a name="branding"></a>Védjegyezés

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tartsa be az [alkalmazásokra vonatkozó márkajelzési irányelveket.](howto-add-branding-in-azure-ad-apps.md) |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Adjon meg értelmes nevet és emblémát az alkalmazáshoz. Ez az információ megjelenik az [alkalmazás hozzájárulási parancsában.](application-consent-experience.md) Győződjön meg arról, hogy a neve és az emblémája a vállalatot/terméket reprezentálja, hogy a felhasználók megalapozott döntéseket hozhassanak. Győződjön meg arról, hogy nem sért meg semmilyen védjegyet. |

## <a name="privacy"></a>Adatvédelem

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az alkalmazás szolgáltatási feltételeire és adatvédelmi nyilatkozatára mutató hivatkozások at adhat meg. |

## <a name="security"></a>Biztonság

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az átirányítási URI-k kezelése: <ul><li>Őrizze meg az átirányítási URI-k tulajdonjogát, és tartsa naprakészen a DNS-rekordokat.</li><li>Ne használjon helyettesítő karaktereket (*) az URI-kban.</li><li>A webalkalmazások esetében győződjön meg arról, hogy minden URI-k biztonságosak és titkosítottak (például https sémák használatával).</li><li>Nyilvános ügyfelek esetén platformspecifikus átirányítási URI-kat használjon, ha vannak ilyenek (főleg iOS és Android rendszerre). Ellenkező esetben nagy mennyiségű véletlenszerűséggel rendelkező átirányítási URI-k at használjon az ütközések megelőzéséhez, amikor visszahívja az alkalmazást.</li><li>Ha az alkalmazást egy elszigetelt webügynöktől használja, használhatja a használatát. `https://login.microsoftonline.com/common/oauth2/nativeclient`</li><li>Rendszeresen tekintse át és vágja le az összes fel nem használt vagy szükségtelen átirányítási URI-t.</li></ul> |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az alkalmazás regisztrálva van egy címtárban, minimalizálja és manuálisan figyelje az alkalmazásregisztrációs tulajdonosok listáját. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az [OAuth2 implicit támogatási folyamat](v2-oauth2-implicit-grant-flow.md) támogatása, kivéve, ha kifejezetten szükséges. Az érvényes forgatókönyvről [itt](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)olvashat. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Lépjen túl a felhasználónévn/jelszón. Ne használja [az erőforrás-tulajdonos i jelszó hitelesítő adatfolyamát (ROPC),](v2-oauth-ropc.md)amely közvetlenül kezeli a felhasználók jelszavait. Ez a folyamat nagyfokú megbízhatóságot és felhasználói expozíciót igényel, és csak akkor használható, ha más, biztonságosabb, folyamatok nem használhatók. Ez a folyamat továbbra is szükség van bizonyos esetekben (például a DevOps), de vigyázz, hogy annak használata korlátozza az alkalmazást.  A modernebb megközelítésekért olvassa el [a Hitelesítési folyamatok és alkalmazásforgatókönyvek című szöveget.](authentication-flows-app-scenarios.md)|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Védje és kezelje a bizalmas alkalmazáshitelesítő adatokat a webalkalmazások, webes API-k és démonalkalmazások számára. [Tanúsítványhitelesítő adatokat](active-directory-certificate-credentials.md)használjon, ne jelszóhitelesítő adatokat (ügyféltitkokat). Ha jelszóhitelesítő adatokat kell használnia, ne állítsa be manuálisan. Ne tárolja a hitelesítő adatokat kódvagy konfiguráció, és soha nem teszi lehetővé számukra, hogy kezeli az emberek. Ha lehetséges, használja [felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) vagy [az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) tárolására és rendszeresen forgatni a hitelesítő adatokat. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Győződjön meg arról, hogy az alkalmazás a legkevesebb jogosultsági jogosultsági engedélyeket kéri. Csak olyan engedélyeket kérjen, amelyekre az alkalmazásnak feltétlenül szüksége van, és csak akkor, ha szüksége van rájuk. Ismerje meg a különböző [típusú engedélyeket.](v2-permissions-and-consent.md#permission-types) Csak akkor használjon alkalmazásengedélyeket, ha szükséges; lehetőség szerint delegált engedélyeket használjon. A Microsoft Graph-engedélyek teljes listáját ebben az [engedélyhivatkozásban tetszés szerint kaphat.](https://docs.microsoft.com/graph/permissions-reference) |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha egy API-t a Microsoft identitásplatform használatával biztonságossá, alaposan gondolja át az engedélyeket, amelyeket elérhetővé kell tennie. Fontolja meg, mi a megfelelő részletességgel a megoldás, és mely engedélyek (ek) igényel rendszergazdai jóváhagyást. Mielőtt bármilyen engedélyezési döntést hozna, ellenőrizze a várt engedélyeket a bejövő jogkivonatokban. |

## <a name="implementation"></a>Megvalósítás

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | A felhasználók biztonságos bejelentkezéséhez használjon modern hitelesítési megoldásokat (OAuth 2.0, [OpenID Connect).](v2-protocols-oidc.md) |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) |  Ne programozzon közvetlenül olyan protokollok ellen, mint az OAuth 2.0 és a Open ID. Ehelyett használja ki a [Microsoft hitelesítési könyvtárat (MSAL).](msal-overview.md) Az MSAL-kódtárak biztonságosan csomagolják a biztonsági protokollokat egy könnyen használható könyvtárban, és beépített támogatást kap a [feltételes hozzáféréses](/azure/active-directory/conditional-access/overview) forgatókönyvekhez, az eszköz szintű [egyszeri bejelentkezéshez (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)és a beépített jogkivonat-gyorsítótárazási támogatáshoz. További információt a Microsoft által támogatott [ügyfélkódtárak](reference-v2-libraries.md#microsoft-supported-client-libraries) és [köztes szoftvertárak,](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) valamint a [kompatibilis, külső ügyféltárak listájában](reference-v2-libraries.md#compatible-client-libraries)talál.<br/><br/>Ha a hitelesítési protokollokhoz be kell adnia a kódot, olyan módszert kell követnie, mint például a [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Fordítson különös figyelmet az egyes protokollok szabványspecifikációiban található biztonsági szempontokra.|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) |  Telepítse át a meglévő alkalmazásokat [az Azure Active Directory hitelesítési könyvtárából (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) a Microsoft [hitelesítési könyvtárába.](msal-overview.md) Az MSAL a Microsoft legújabb identitásplatform-megoldása, és az ADAL előnyben részesül. Elérhető a .NET, JavaScript, Android, iOS, macOS és szintén nyilvános előzetes python és Java. További információ [a ADAL.NET](msal-net-migration.md), [ADAL.js,](msal-compare-msal-js-and-adal-js.md) [valamint ADAL.NET és iOS-brókeralkalmazások](msal-net-migration-ios-broker.md) áttelepítéséről.|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) |  Mobilalkalmazások esetén konfigurálja az egyes platformokat az alkalmazásregisztrációs élmény használatával. Annak érdekében, hogy az alkalmazás kihasználhassa a Microsoft Authenticator vagy a Microsoft Company Portal előnyeit az egyszeri bejelentkezéshez, az alkalmazásnak szüksége van egy "közvetítő átirányítási URI"-ra konfigurálva. Ez lehetővé teszi a Microsoft számára, hogy a hitelesítés után visszaadja az alkalmazás vezérlését. Az egyes platformok konfigurálásakor az alkalmazásregisztrációs élmény végigvezeti a folyamaton. A rövid útmutató segítségével töltsön le egy működő példát. IOS rendszeren amikor csak lehetséges, használja a brókereket és a rendszer webnézetét.|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) |  A webes alkalmazásokban vagy webes API-kban fiókonként egy jogkivonat-gyorsítótárat tarthat meg.  A webalkalmazások esetében a jogkivonat-gyorsítótárat a fiókazonosítónak kell megkell keyd-nek lennie.  Webes API-k esetén a fiókot az API-k hívásához használt jogkivonat kivonatának kell megadnia. MSAL.NET egyéni tokengyorsítótár-szerializálást biztosít a . Biztonsági és teljesítménybeli okokból javasoljuk, hogy felhasználónként egy gyorsítótárat szerializáljanak. További információ a [tokengyorsítótár szerializálásáról.](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az alkalmazás által igényelt adatok a [Microsoft Graph](https://developer.microsoft.com/graph)on keresztül érhetők el, kérjen engedélyeket ezekhez az adatokhoz a Microsoft Graph-végpont használatával, nem pedig az egyes API-k használatával. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) |Ne tekintse meg a hozzáférési jogkivonat értékét, és ne próbálja meg ügyfélként elemezni.  Módosíthatják az értékeket, formátumokat, vagy akár figyelmeztetés nélkül titkosíthatók is – mindig a id_token használja, ha az ügyfélnek meg kell tanulnia valamit a felhasználóról, vagy hívja a Microsoft Graph-ot.  Csak a webes API-k elemeznek hozzáférési jogkivonatokat (mivel ezek azok, amelyek meghatározzák a formátumot és a titkosítási kulcsokat). |

## <a name="end-user-experience"></a>Végfelhasználói élmény

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | [Ismerje meg a hozzájárulási élményt,](application-consent-experience.md) és konfigurálja az alkalmazás hozzájárulási parancsának egyes darabjait, hogy a végfelhasználók és a rendszergazdák elegendő információval rendelkezhessenek annak megállapításához, hogy megbíznak-e az alkalmazásban. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimalizálja, hogy a felhasználónak hány alkalommal kell megadnia a bejelentkezési hitelesítő adatokat az alkalmazás használata közben, ha az interaktív folyamatok előtt megkísérli a csendes hitelesítést (csendes tokenek beszerzése). |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ne használja a "prompt=consent" minden bejelentkezéshez. Csak akkor használja a prompt=consent-t, ha úgy döntött, hogy további engedélyekhez kell hozzájárulást kérnie (például ha módosította az alkalmazás szükséges engedélyeit). |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Adott esetben gazdagítsa alkalmazását felhasználói adatokkal. A [Microsoft Graph API](https://developer.microsoft.com/graph) használata egyszerű módja ennek. A [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) eszköz, amely segítséget nyújt az első lépésekhez. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Regisztrálja az alkalmazás által igényelt engedélyek teljes készletét, hogy a rendszergazdák könnyen beleegyezést adhassanak a bérlőjüknek. Futásidőben használja a [növekményes hozzájárulást,](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) hogy segítsen a felhasználóknak megérteni, hogy az alkalmazás miért kér olyan engedélyeket, amelyek az első indításkor a felhasználókat érintik vagy zavarják. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tiszta [egyszeri kijelentkezési élmény megvalósítása.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) Ez egy adatvédelmi és biztonsági követelmény, és jó felhasználói élményt nyújt. |

## <a name="testing"></a>Tesztelés

|   |   |
|---|---|
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tesztelje a [feltételes hozzáférési házirendeket,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) amelyek befolyásolhatják a felhasználók az on-use-t. |
| ![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tesztelje az alkalmazást az összes támogatni kívánt fiókkal (például munkahelyi vagy iskolai fiókokkal, személyes Microsoft-fiókokkal, gyermekfiókokkal és szuverén fiókokkal). |

## <a name="additional-resources"></a>További források

Részletes információk a 2.0-s verzióról:

* [Microsoft-identitásplatform (2.0-s verzió – áttekintés)](v2-overview.md)
* [Microsoft identity platform protokollok – hivatkozás](active-directory-v2-protocols.md)
* [Hozzáférési jogkivonatok referenciája](access-tokens.md)
* [Azonosító jogkivonatok referenciája](id-tokens.md)
* [Hitelesítési könyvtárak hivatkozása](reference-v2-libraries.md)
* [Engedélyek és hozzájárulás a Microsoft identitásplatformján](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
