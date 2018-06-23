---
title: Az Azure Active Directory v2.0 végpont korlátai és korlátozásai |} Microsoft Docs
description: Korlátozások és az Azure AD v2.0-végpontra vonatkozó korlátozások listáját.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: d7328ba8403f2415705d049d1fc7947b52aeb5c1
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319559"
---
# <a name="should-i-use-the-v20-endpoint"></a>A v2.0-végpontra érdemes használni?
Alkalmazások, amelyekbe beépül az Azure Active Directory összeállításakor kell döntse el, hogy a v2.0-végpont és a hitelesítési protokollokat megfelel-e az igényeinek. Az Azure Active Directory eredeti végpont továbbra is teljes mértékben támogatja, és néhány tekintetben a v2.0-nál több szolgáltatás gazdag. Azonban a v2.0-végpontra [vezet be, jelentős előnyt](active-directory-v2-compare.md) a fejlesztők számára.

Az egyszerűsített ajánlott megoldás a fejlesztők számára ezen a ponton a időben történő itt található:

* Ha személyes Microsoft-fiókok támogatnia kell az alkalmazásban, használja a v2.0-végponttól. De előtt, ne feledje, hogy tudomásul veszi a korlátozások vonatkoznak, amelyek arról lesz szó ebben a cikkben.
* Ha az alkalmazásnak csak támogatja a Microsoft munkahelyi és iskolai fiókok, ne használjon a v2.0-végponttól. Ehelyett tekintse meg a [az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md).

Az idő múlásával a v2.0-végpontra nőhet elkerülése érdekében az itt felsorolt korlátozásokkal, így csak akkor használja a v2.0-végponttól. A cikk célja időközben segítségével meghatározhatja, hogy az Ön számára legmegfelelőbb-e a v2.0-végponttól. Ez a cikk a v2.0-végpontra aktuális állapotát jeleníti meg frissíteni folytatjuk. Térjen vissza a v2.0-képességek vonatkozó követelmények újra kiértékelje.

Ha egy meglévő Azure AD-alkalmazás nem használja a v2.0-végpontra, nincs szükség van kezd. A jövőben oly módon, hogy a meglévő Azure AD-alkalmazások használata a v2.0-végpontra lesz elérhető.

## <a name="restrictions-on-app-types"></a>Alkalmazástípusok korlátozásai
A következő típusú alkalmazásokat jelenleg nem támogatja a v2.0-végponttól. Támogatott alkalmazástípusok ismertetését lásd: [alkalmazástípusok az Azure Active Directory v2.0-végpontra vonatkozó](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>Önálló webes API-k
Használhatja a v2.0-végponttal való [létrehozása egy webes API védett OAuth 2.0](active-directory-v2-flows.md#web-apis). Azonban, hogy a webes API képes jogkivonatokat fogadni csak egy alkalmazás, amely rendelkezik a ugyanazon alkalmazás azonosítóját. Nem fér hozzá egy webes API-t egy ügyfél, amely rendelkezik egy másik alkalmazás azonosítóját. Az ügyfél nem fogja tudni kérheti, vagy a Web API engedélyek megszerzéséhez.

Hogyan hozhat létre egy webes API, amely egy ügyfelet, amelyben az alkalmazás azonosítója a jogkivonatokat fogad el, olvassa el a v2.0 végpont Web API minták a [bevezetés](active-directory-appmodel-v2-overview.md#getting-started) szakasz.

## <a name="restrictions-on-app-registrations"></a>Alkalmazás regisztrációk korlátozásai
Jelenleg minden alkalmazást, amely szeretné integrálni a v2.0-végponttal, létre kell hoznia egy alkalmazás regisztrálása az új [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Meglévő Azure AD vagy a Microsoft-fiók alkalmazások nem kompatibilisek a v2.0-végponttól. A portálon kívül az alkalmazásregisztrációs portálra regisztrált alkalmazások nem kompatibilisek a v2.0-végponttól. A jövőben tervezzük adjon meg egy meglévő alkalmazást használatára a v2.0-alkalmazásként. Jelenleg azonban nincs áttelepítési útvonal egy meglévő alkalmazás működéséhez a v2.0-végponttal.

Ezenkívül a létrehozott alkalmazás-regisztráció a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) rendelkezik a következő kikötésekkel:

* Csak kettő használható azonosítót.
* Egy alkalmazás regisztrációs személyes Microsoft-fiókkal rendelkező felhasználó által regisztrált tekinthetők meg és felügyeli csak egyetlen fejlesztői fiók létrehozása. Több fejlesztők között nem lehet megosztani. Ha meg szeretné osztani több fejlesztők többek között az alkalmazás regisztrációját, ha bejelentkezik a regisztrációs portálon az Azure AD-fiókot is létrehozhat az alkalmazás.
* Nincsenek az átirányítási URI-t, hogy a formátumának több korlátozásait. Átirányítási URI-kkal kapcsolatos további információkért lásd: a következő szakaszban.

## <a name="restrictions-on-redirect-uris"></a>Átirányítási URI-k korlátozásai
Az alkalmazásregisztrációs portálra regisztrált alkalmazások jelenleg korlátozott számú átirányítási URI-értékek korlátozni. Az átirányítási URI-JÁNAK webalkalmazások és szolgáltatások kell kezdődnie, a rendszer `https`, és az összes átirányítási URI-értékek közös egyetlen DNS-tartományt. Például egy webalkalmazást, hogy rendelkezik-e ezeket az átirányítási URI-azonosítók nem regisztrálható:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

A regisztrációs rendszer összehasonlítja a meglévő átirányítási URI-t az átirányítási URI-t hozzáadandó DNS-nevének teljes DNS-nevét. A DNS-név hozzáadására irányuló kérelem sikertelen lesz, ha a következő feltételek egyikének értéke igaz:  

* Az új átirányítási URI-JÁNAK teljes DNS-neve nem egyezik a meglévő átirányítási URI-t a DNS-nevét.
* Az új átirányítási URI-JÁNAK teljes DNS-neve nincs olyan altartomány, a meglévő átirányítási URI-t.

Például ha az alkalmazás az átirányítási URI-ja:

`https://login.contoso.com`

A következő módon adhatja hozzá:

`https://login.contoso.com/new`

Ebben az esetben a DNS-név pontosan egyezik. Esetleg a következőt teheti meg:

`https://new.login.contoso.com`

Ebben az esetben a login.contoso.com egyik DNS-altartományára hivatkozik. Ha egy alkalmazás, amely rendelkezik bejelentkezési-east.contoso.com és a bejelentkezési-west.contoso.com átirányítási URI-ként szeretne használni, hozzá kell adnia azokat átirányítási URI-azonosítók, az itt megadott sorrendben:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Az utóbbi két adhat hozzá, mert az első átirányítási URI-t, az altartományok contoso.com. Ez a korlátozás egy jövőbeli verzióban törlődni fog.

Vegye figyelembe azt is, akkor egy adott alkalmazáshoz csak 20 válasz URL-címek is.

Alkalmazás regisztrálása az alkalmazásregisztrációs portálra, lásd: [egy alkalmazás regisztrálása a v2.0-végponttal](active-directory-v2-app-registration.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Szalagtárak és SDK-k korlátozásai
Szalagtár támogatása a v2.0-végpontra jelenleg korlátozott. Ha egy éles alkalmazásban a v2.0-végpontra használni kívánt, akkor ezeket a beállításokat:

* Ha Ön által létrehozott webalkalmazás bejelentkezési és a token érvényességi végrehajtásához biztonságosan használhatja a Microsoft általánosan elérhető kiszolgálóoldali köztes. Ezek közé tartozik az OWIN Open ID Connect köztes az ASP.NET és a Node.js Passport beépülő modult. Használja a Microsoft köztes mintakódok, tekintse meg a [bevezetés](active-directory-appmodel-v2-overview.md#getting-started) szakasz.
* Ha egy asztali vagy hordozható alkalmazást fejleszt, Microsoft hitelesítési könyvtárak (MSAL) szereplő előzetes verzióban egyikét használhatja. Ezek a könyvtárak egy éles által támogatott előzetes szerepelnek, így biztonságosan is használhatja őket az üzemi környezetben működő alkalmazásokhoz. További tudnivalók az előzetes kiadásban, illetve a rendelkezésre álló tárak a [hitelesítési könyvtárak hivatkozás](active-directory-v2-libraries.md).
* Nem fedi le Microsoft szalagtárak platformokhoz így integrálhatja a v2.0-végponttal közvetlenül üzenetek küldése és fogadása protokoll az alkalmazás kódjában. A v2.0 OpenID Connectet és az OAuth protokollok [explicit módon szerepelnek](active-directory-v2-protocols.md) ilyen integrációs elvégzéséhez.
* Végezetül nyílt forráskódú ID Connect megnyitásához és az OAuth-tárak segítségével integrálható a v2.0-végponttól. A v2.0 protokoll számos nyílt forráskódú protokoll szalagtárak anélkül, hogy lényegesen módosul kompatibilisnek kell lennie. A rendelkezésre állási szalagtárak ilyen típusú függ a nyelvet és a platform. A [Open ID Connect](http://openid.net/connect/) és [OAuth 2.0](http://oauth.net/2/) webhelyek népszerű megvalósítások listának a karbantartására. További információkért lásd: [Azure Active Directory v2.0 és hitelesítési kódtárai](active-directory-v2-libraries.md), és nyílt forráskódú klienskódtárak és minták a v2.0-végponttal tesztelt listáját.

## <a name="restrictions-on-protocols"></a>Protokollok korlátozásai
A v2.0-végpontra nem támogatja a SAML-alapú vagy a WS-Federation; csak a támogatott Open ID Connect és az OAuth 2.0-s. Nem minden funkciók és képességek OAuth protokollok bekerültek a v2.0-végponttól. Ezen protokoll funkciók és képességek jelenleg *nem érhető el* a v2.0-végpontra a:

* Azonosító-jogkivonatokat, amelyeket a v2.0-végpontra nem tartalmaznak egy `email` jogcímek a felhasználó számára, akkor is, ha a felhasználó engedélye az e-mailek megtekintéséhez szerez be.
* Az OpenID Connect UserInfo végpont nem vonatkozik a v2.0-végponttól. Felhasználói profil összes adata, amely potenciálisan kapna a végponti azonban elérhető a Microsoft Graph `/me` végpont.
* A v2.0-végpontra kibocsátó szerepkör vagy csoport jogcímek azonosító-jogkivonatokat támogatja.
* A [OAuth 2.0 erőforrás tulajdonosa jelszó hitelesítő adatok megadása](https://tools.ietf.org/html/rfc6749#section-4.3) nem támogatja a v2.0-végponttól.

Emellett a v2.0-végpontra nem támogatja az SAML-alapú vagy a WS-Federation protokollok bármilyen.

Jobb megértése érdekében protokoll funkció támogatja a v2.0-végpontra a körét, olvassa végig a [OpenID Connectet és az OAuth 2.0 protokoll referenciája](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>A munkahelyi és iskolai fiókok korlátozások
Ha Windows-alkalmazások már használta az Active Directory Authentication Library (ADAL), előfordulhat, hogy elvégezte integrált Windows-hitelesítést, használja a Security Assertion Markup Language (SAML) helyességi feltétel grant előnyeit. Ez a támogatás a felhasználók összevont Azure AD bérlők csendes hitelesítheti a helyszíni Active Directory példánnyal hitelesítő adatok megadása nélkül. A SAML-alapú assertion támogatás a v2.0-végpontra a nem támogatott.
