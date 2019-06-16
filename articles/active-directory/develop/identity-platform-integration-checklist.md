---
title: Integráció a Microsoft identity platform |} Az Azure
description: Ismerje ajánlott eljárásokról és a közös figyelmetlenség integrálása a Microsoft identity platform (2.0-s verzió).
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
ms.custom: aaddev
ms.openlocfilehash: 0acef783b4877b5b1787f142aec6cc3588293f83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111458"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>A Microsoft identity platform integrációs ellenőrzőlista

A Microsoft identity platform integrációs ellenőrzőlista végigvezeti Önt egy kiváló minőségű és biztonságos integrációs célja. Ajánlott eljárások kiemeli, és amikor integrálása a Microsoft identity platform általános figyelmetlenség Igen tekintse át a listában, hogy megmaradjanak a minőségének és biztonságának identitásplatformok integrálható az alkalmazás rendszeres időközönként. A feladatlista nem célja, hogy tekintse át a teljes alkalmazás. A feladatlista tartalmát, módosulhatnak, ahogy folyamatosan fejlesztjük a platform.

Ha Ön bevezető, tekintse meg a [dokumentáció](index.yml) hitelesítési alapok, a Microsoft identity platform alkalmazás-forgatókönyvekhez és egyéb megismeréséhez.

## <a name="testing-your-integration"></a>A-integráció tesztelése

A következő ellenőrzőlista segítségével győződjön meg arról, hogy az alkalmazás hatékonyan integrálva van a [Microsoft identitásplatformja](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Alapvető beállítások

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Elolvastam és megértettem a [Microsoft Platform házirendek](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Győződjön meg arról, hogy az alkalmazás betartja a feltételek, a felhasználók és a platform védelméhez lettek kialakítva. |

### <a name="ownership"></a>Tulajdonjog

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ellenőrizze, hogy naprakész állapotban, amellyel regisztrálja és kezeli az alkalmazásokat a fiókhoz társított információkat. |

### <a name="branding"></a>Védjegyzési

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Csatlakozhat a [védjegyzési útmutató alkalmazások](howto-add-branding-in-azure-ad-apps.md). |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Adjon meg egy közérthető nevet és embléma az alkalmazáshoz. Ez az információ jelenik meg az alkalmazás beleegyezést kérő üzenetet. Ellenőrizze, hogy a védjegy és embléma a vállalati/product képviselője, hogy a felhasználók megalapozott döntéseket hozhat. Győződjön meg arról, hogy nem használ megsértése bármely védjegye. |

### <a name="privacy"></a>Személyes adatok védelme

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az alkalmazás használati és az adatvédelmi nyilatkozat mutató hivatkozásokat tartalmaznak. |

### <a name="security"></a>Biztonság

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az átirányítási URI-k tulajdonjogának karbantartása, és tartsa naprakészen a DNS-rekordokat a számukra. Ne használjon helyettesítő karaktereket (*) az URI-k. A web apps ellenőrizze, hogy minden URI-k biztonságos és titkosított (például https-sémát). A nyilvános ügyfelek esetén használja platformspecifikus átirányítási URI-k ha van ilyen (főleg az iOS és Android). Ellenkező esetben használja a véletlenszerűségre azért, hogy ütközések vissza az alkalmazás hívása során nagy mennyiségű rendelkező átirányítási URI-k. Ha az alkalmazás az elkülönített webes ügynök használja, használhat https://login.microsoftonline.com/nativeclient. Tekintse át, és a tároló visszaigénylésének minden használaton kívüli vagy szükségtelen átirányítási URI-k rendszeres időközönként. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az alkalmazás regisztrálva van egy könyvtárban, kis méretűvé, és manuálisan monitorozása az alkalmazás regisztrációs tulajdonosok listája. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Nem engedélyezi az támogatása a [OAuth2 implicit folyamat megadása](v2-oauth2-implicit-grant-flow.md) , kivéve, ha explicit módon szükségesnek. Ismerje meg az érvényes forgatókönyv [Itt](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ne használjon [az erőforrást birtokló jelszavát hitelesítőadat-folyamat (ROPC)](v2-oauth-ropc.md), amely közvetlenül kezeli a felhasználói jelszavakat. Ez a folyamat nagyfokú megbízhatóság és a felhasználó kitettség igényel, és csak kell használni, amikor más, az biztonságosabb, a folyamatok nem használható. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Megvédheti és kezelheti az alkalmazás hitelesítő adatait. Használat [hitelesítő tanúsítvány](active-directory-certificate-credentials.md), nem jelszót (ügyfél titkos adatok). Jelszó hitelesítő adatokat kell használnia, ha nem állít be, manuálisan. Nem hitelesítő adatok tárolása vagy konfigurációverziót, és soha nem engedélyezi az emberek kell kezelnie. Ha lehetséges, használjon [felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) vagy [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) tárolására, és rendszeresen elforgatása a hitelesítő adatait. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Győződjön meg arról, hogy az alkalmazás a legalacsonyabb jogosultsági engedélyt kér. Csak az alkalmazása feltétlenül szükséges engedélyekkel, és csak akkor kérjen szüksége lesz rájuk. Megismerheti a különböző [típusú engedélyeket](v1-permissions-and-consent.md#types-of-permissions). Csak az Alkalmazásengedélyek; szükség esetén használja delegált engedélyeket használ, ahol csak lehetséges. A Microsoft Graph-engedélyek teljes listáját lásd: Ez [engedélyek referencia](https://docs.microsoft.com/graph/permissions-reference). |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha egy API-t a Microsoft identity platform biztonságáról, gondosan végiggondoljon az engedélyeket kell elérhetővé tennie. Fontolja meg, mi a megoldás a megfelelő részletességgel, és mely eke rendszergazdai jóváhagyásra van szükség. Ellenőrizze a bejövő jogkivonatot az várt engedélyek engedélyezési döntések meghozatala előtt. |

### <a name="implementation"></a>Megvalósítás

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Modern hitelesítés nélküli megoldások használata (OAuth 2.0-s [OpenID Connect](v2-protocols-oidc.md)) felhasználók biztonságos bejelentkezéshez. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Nem megvalósítani a protokollokat, saját magának, – használja [a Microsoft által támogatott hitelesítési tárak](reference-v2-libraries.md) (MSAL alkalmazáskiszolgálók közbenső). Ellenőrizze, hogy integrálhatók a hitelesítési kódtár legújabb verzióját használja-e. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ha az adott alkalmazáshoz szükséges adatok keresztül elérhető [Microsoft Graph](https://developer.microsoft.com/graph), ezeket az adatokat a Microsoft Graph-végpont, hanem az egyéni API-t használva az engedélyek kéréséhez. |

### <a name="end-user-experience"></a>Végfelhasználói élmény

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | [Ismerje meg a jóváhagyási felületen](application-consent-experience.md) és az alkalmazás jóváhagyási kérése darabjait konfigurálása, hogy a végfelhasználók és rendszergazdák rendelkezik elég információval a határozza meg, ha azokat megbízható-e az alkalmazást. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | A szám, ahányszor a felhasználó nem adja meg a bejelentkezési hitelesítő adatok használatával az alkalmazás csendes létrehozására tett minimalizálása interaktív folyamatok (csendes token beszerzéséhez) hitelesítést. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ne használja a "kérdés = jóváhagyás" minden bejelentkezéshez. Csak a parancssor használata = jóváhagyás, ha a szükséges további engedélyeket (például ha módosította az alkalmazás szükséges engedélyekkel) jóváhagyás kérése. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Ahol lehetséges, bővítheti az alkalmazás felhasználói adatokkal. Használja a [Microsoft Graph API](https://developer.microsoft.com/graph) ennek egyszerű módja. A [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) eszköz, amely segíthet az induláshoz. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Regisztrálja a teljes körű engedélyeket, amelyek az adott alkalmazáshoz szükséges, így a rendszergazdák egyszerűen hozzájárulási adhat a bérlőhöz. Használat [növekményes hozzájárulási](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) dolgozó felhasználók okokat futási időben az alkalmazás által kért engedélyeket, amelyek esetleg érintő vagy összekeverik felhasználókat, ha a kért első indításkor. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Alkalmazzon egy [egyszeri kijelentkezési használatát tiszta](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Adatvédelmi és biztonsági követelmény, és így az a jó felhasználói élményt. |

### <a name="testing"></a>Tesztelés

|   |   |
|---|---|
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | A teszt [feltételes hozzáférési szabályzatok](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , amely hatással lehet a felhasználók tudják használni az alkalmazást. |
| ![Jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) | Az összes lehetséges fiók (a példában, munkahelyi vagy iskolai fiókokhoz, személyes Microsoft-fiókok, a gyermekfiókoktól és szuverén fiókok) támogatását tervezi az alkalmazás teszteléséhez. |

## <a name="additional-resources"></a>További források

Részletes információk a 2.0-s verzióról:

* [A Microsoft identity platform (2.0-s verziójú áttekintése)](v2-overview.md)
* [A Microsoft identity platform protokollok referencia](active-directory-v2-protocols.md)
* [Hozzáférési jogkivonatok referenciája](access-tokens.md)
* [Azonosító jogkivonatok referenciája](id-tokens.md)
* [Hitelesítési tárak referencia](reference-v2-libraries.md)
* [Engedélyek és jóváhagyás az a Microsoft identity platform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
