---
title: Integráció a Microsoft Identity platformmal | Azure
description: Ismerje meg a Microsoft Identity platform (v 2.0) integrálásával kapcsolatos ajánlott eljárásokat és általános áttekintéseket.
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 33128cbece3b217778182b3831b02e2f3f654f3b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853216"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Microsoft Identity platform integrációs ellenőrzőlista

A Microsoft Identity platform Integration ellenőrzőlista célja, hogy kiváló minőségű és biztonságos integrációt biztosítson. Kiemeli az ajánlott eljárásokat és az általános áttekintéseket a Microsoft Identity platformmal való integráció során, ezért rendszeresen tekintse át a listát, és győződjön meg róla, hogy az alkalmazás az Identity platformmal való integrációjának minőségét és biztonságát tartja karban. Az ellenőrzőlista nem alkalmas a teljes alkalmazás áttekintésére. Az ellenőrzőlista tartalma változhat, ahogy javítunk a platformon.

Ha most ismerkedik az első lépésekkel, tekintse [](index.yml) meg a dokumentációt, amelyből megismerheti a hitelesítés alapjait, a Microsoft Identity platform alkalmazási forgatókönyveit és egyebeket.

## <a name="testing-your-integration"></a>Az integráció tesztelése

Az alábbi ellenőrzőlista használatával biztosíthatja, hogy az alkalmazás hatékonyan legyen integrálva a [Microsoft Identity platformmal](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Alapvető beállítások

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Olvassa el és Ismerje meg a [Microsoft Platform szabályzatait](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Győződjön meg arról, hogy az alkalmazás megfelel a felhasználók és a platform elleni védelemhez tervezett feltételeknek. |

### <a name="ownership"></a>Tulajdon

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Győződjön meg arról, hogy az alkalmazások regisztrálásához és kezeléséhez használt fiókhoz tartozó információk naprakészek. |

### <a name="branding"></a>Védjegyezés

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Tartsa be az [alkalmazások védjegyezési irányelveit](howto-add-branding-in-azure-ad-apps.md). |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Adjon meg egy értelmes nevet és emblémát az alkalmazáshoz. Ezek az információk az alkalmazás jóváhagyására vonatkozó kérdésben jelennek meg. Győződjön meg arról, hogy a név és az embléma megfelel a vállalatának vagy termékének, hogy a felhasználók tájékozott döntéseket hozhassanak. Győződjön meg arról, hogy nem sérti a védjegyeket. |

### <a name="privacy"></a>Személyes adatok védelme

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Adja meg az alkalmazás használati feltételeire és adatvédelmi nyilatkozatára mutató hivatkozásokat. |

### <a name="security"></a>Biztonság

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Őrizze meg az összes átirányítási URI tulajdonosát, és tartsa naprakészen a DNS-rekordokat. Ne használjon helyettesítő karaktereket (*) az URI-k között. Webalkalmazások esetén győződjön meg arról, hogy az összes URI biztonságos és titkosított (például https-sémák használatával). Nyilvános ügyfelek esetén használjon platform-specifikus átirányítási URI-ket, ha vannak ilyenek (főleg az iOS és az Android esetében). Ellenkező esetben használja a nagy mennyiségű véletlenszerű átirányítási URI-t, hogy elkerülje az ütközéseket, amikor visszahívja az alkalmazást. Ha az alkalmazást egy elkülönített webügynök használja, akkor használhatja https://login.microsoftonline.com/nativeclient a következőt:. Tekintse át és vágja fel az összes fel nem használt vagy szükségtelen átirányítási URI-t a szokásos módon. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az alkalmazás regisztrálva van egy címtárban, csökkentse és manuálisan figyelje az alkalmazás-regisztrációs tulajdonosok listáját. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha explicit módon nem szükséges, ne engedélyezze a [OAuth2 implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) támogatását. [Itt](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)tájékozódhat az érvényes forgatókönyvről. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ne használja az [erőforrás-tulajdonosi jelszó hitelesítő adatait (ROPC)](v2-oauth-ropc.md), amely közvetlenül kezeli a felhasználók jelszavát. Ennek a folyamatnak magas fokú megbízhatóságra és felhasználói expozícióra van szüksége, és csak akkor használható, ha más, biztonságosabb, a folyamatok nem használhatók. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az alkalmazás hitelesítő adatainak megvédése és kezelése. [Tanúsítvány hitelesítő adatainak](active-directory-certificate-credentials.md)használata, nem a jelszó hitelesítő adatai (az ügyfél titkos kulcsa). Ha jelszó-hitelesítő adatokat kell használnia, ne állítsa be manuálisan. Ne tárolja a hitelesítő adatokat a kódban vagy a konfigurációban, és soha ne engedélyezze őket emberek általi kezelésre. Ha lehetséges, használjon [felügyelt identitásokat az Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) -erőforrásokhoz vagy [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) a hitelesítő adatok tárolásához és rendszeres elforgatásához. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Győződjön meg arról, hogy az alkalmazás a legkevésbé megfelelő jogosultságokat kéri. Csak olyan engedélyeket kérjen, amelyeket az alkalmazásnak teljesen szüksége van, és csak akkor, amikor szüksége van rájuk. Ismerje meg a különböző [típusú engedélyeket](v1-permissions-and-consent.md#types-of-permissions). Csak szükség esetén használja az alkalmazás engedélyeit; Ha lehetséges, használjon delegált engedélyeket. Az Microsoft Graph engedélyek teljes listájáért tekintse meg ezt az [engedélyeket](https://docs.microsoft.com/graph/permissions-reference)ismertető témakört. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha a Microsoft Identity platform használatával biztosít API-t, alaposan gondolja át, hogy milyen engedélyekkel kell rendelkeznie. Gondolja át, hogy mi a megfelelő részletesség a megoldáshoz, és hogy a jogosultság (ok) rendszergazdai hozzájárulást igényel. Az engedélyezési döntések meghozatala előtt keresse meg a várt engedélyeket a bejövő jogkivonatokban. |

### <a name="implementation"></a>Megvalósítás

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Használjon modern hitelesítési megoldásokat (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) a felhasználók biztonságos bejelentkezéséhez. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ne hajtsa végre a protokollok használatát – használja a [Microsoft által támogatott hitelesítési kódtárakat](reference-v2-libraries.md) (MSAL, Server middleware). Győződjön meg arról, hogy a-ben integrált hitelesítési függvénytár legújabb verzióját használja. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az alkalmazás által igényelt adatai Microsoft Graphon [](https://developer.microsoft.com/graph)keresztül érhetők el, akkor az egyes API-k helyett az Microsoft Graph-végponton kell megadnia az ehhez az adatkezeléshez szükséges engedélyeket. |

### <a name="end-user-experience"></a>Végfelhasználói élmény

|   |   |
|---|---|
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ismerje meg [a hozzájárulási élményt](application-consent-experience.md) , és konfigurálja az alkalmazás beleegyezési kérésének részleteit, hogy a végfelhasználók és a rendszergazdák elegendő információval rendelkezzenek az alkalmazás megbízhatóságának megállapításához. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Csökkentse azt az időtartamot, ameddig a felhasználónak be kell írnia a bejelentkezési hitelesítő adatokat, miközben az alkalmazás használata közben csendes hitelesítést (csendes jogkivonat-beszerzést) kell megadnia az interaktív folyamatok előtt. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Minden bejelentkezéshez ne használja a "prompt = beleegyezés" kifejezést. Csak a prompt = beleegyezés használata, ha úgy döntött, hogy további engedélyek megadását kell kérnie (például ha módosította az alkalmazás szükséges engedélyeit). |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha szükséges, bővítse alkalmazását felhasználói adataival. Ezt egyszerűen elvégezheti a [Microsoft Graph API](https://developer.microsoft.com/graph) használatával. A [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) eszköz, amely segítséget nyújt az első lépésekhez. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Regisztrálja az alkalmazás által igényelt engedélyek teljes készletét, hogy a rendszergazdák könnyen megadhatják a hozzájárulásukat a Bérlőnek. Futtassa [](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) a növekményes beleegyezést a futtatáskor, hogy a felhasználók tisztában legyenek azzal, hogy az alkalmazás Miért kér olyan engedélyeket, amelyek az első indításkor érinthetik vagy megzavarják a felhasználókat. |
| ![jelölőnégyzetet](./media/active-directory-integration-checklist/checkbox-two.svg) | Hozzon létre [tiszta egyszeri kijelentkezési élményt](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Ez egy adatvédelmi és biztonsági követelmény, és jó felhasználói élményt tesz lehetővé. |

### <a name="testing"></a>Tesztelés

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
