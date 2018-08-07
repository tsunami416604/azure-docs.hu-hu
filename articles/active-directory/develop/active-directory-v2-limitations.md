---
title: Az Azure Active Directory 2.0-s verziójú végpont korlátai és korlátozásai |} A Microsoft Docs
description: Korlátozások és megkötések az Azure AD v2.0-végpont esetében listáját.
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
ms.date: 07/12/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: aa931702975c2c6bdcc65853c3865dbeff570bf4
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578449"
---
# <a name="should-i-use-the-v20-endpoint"></a>A v2.0-végpont érdemes használni?

Ha hoz létre, amelyekbe beépül az Azure Active Directory (Azure AD-) alkalmazások, kell eldöntheti, megfelel-e a 2.0-s végpont és a hitelesítési protokollok az igényeinek. Az Azure AD eredeti végpont továbbra is teljes mértékben támogatott, és bizonyos szempontokból több szolgáltatás gazdag, mint a 2.0-s verzió. Azonban a v2.0-végpont [vezet be, jelentős előnyöket](azure-ad-endpoint-comparison.md) fejlesztők számára.

Íme egy egyszerűsített javaslatot a fejlesztők ezen a ponton az időben:

* Ha az alkalmazás támogatnia kell a személyes Microsoft-fiókok, használja a v2.0-végpontra. De előtt, fontos tisztában legyen azzal, hogy az ebben a cikkben tárgyalt korlátozások.
* Ha az alkalmazás csak a Microsoft munkahelyi és iskolai fiókok van szüksége, ne használja a v2.0-végpontra. Helyette tekintse meg a [az Azure AD fejlesztői útmutató](azure-ad-developers-guide.md).

A v2.0-végpont az itt felsorolt korlátozásokkal kiküszöböléséhez, így minden eddiginél csak kell használni a v2.0-végpont fejlődik. Addig is ez a cikk segítségével ellenőrizze, hogy a v2.0-végpont az Ön számára megfelelő. Ez a cikk a v2.0-végpont aktuális állapotát tükröző update továbbra is. Ellenőrizze, hogy vissza kiértékeli a v2.0-képességek követelményei.

Ha rendelkezik meglévő Azure AD-alkalmazás, amely nem használja a v2.0-végpont, esetén nem kell kezdenie a folyamatot. A jövőben biztosít olyan módon, hogy a meglévő Azure AD-alkalmazások használata a v2.0-végpontra.

## <a name="restrictions-on-app-types"></a>Alkalmazástípusok korlátozásai

A következő típusú alkalmazások jelenleg nem támogatottak a v2.0-végpont által. Támogatott alkalmazástípusok ismertetését lásd: [típusú alkalmazások esetében az Azure Active Directory v2.0-végpont](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>Önálló webes API-k

Használhatja a v2.0-végpont [hozhat létre a webes API-hoz biztosított az OAuth 2.0-val](active-directory-v2-flows.md#web-apis). Azonban, hogy a webes API képes jogkivonatokat fogadni csak egy alkalmazásból, amely rendelkezik az ugyanazon alkalmazás azonosítóját. Nem lehet hozzáférni egy webes API-ügyfélről, amely rendelkezik egy másik alkalmazás azonosítóját. Az ügyfél nem fog tudni kérje vagy szerezze meg a webes API-hoz való engedélyek.

Hogyan hozhat létre egy webes API-t, amely ugyanazon Alkalmazásazonosítóval rendelkező ügyfél származó jogkivonatokat, olvassa el a 2.0-s verziójú végpont webes API-t minták a [bevezetés](active-directory-appmodel-v2-overview.md#getting-started) szakaszban.

## <a name="restrictions-on-app-registrations"></a>Alkalmazásregisztrációk korlátozásai

Jelenleg minden alkalmazáshoz, amely szeretné integrálni a v2.0-végponttal, létre kell hoznia egy alkalmazásregisztráció az új [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Meglévő Azure ad-ben vagy a Microsoft-fiók alkalmazások nem kompatibilisek a v2.0-végpontra. Minden olyan portálon kívül az alkalmazásregisztrációs portálon regisztrált alkalmazások nem kompatibilisek a v2.0-végpontra. A jövőben tervezzük gondoskodhat a v2.0-alkalmazásként egy meglévő alkalmazás használatát. Jelenleg nincs áttelepítési útvonal egy meglévő alkalmazást a v2.0-végponttal működik.

Ezenkívül a létrehozott alkalmazásregisztrációk a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) az alábbi korlátozásokkal rendelkeznek:

* Az Alkalmazáskulcs csak két használható alkalmazás azonosítóját.
* Egy személyes Microsoft-fiókkal rendelkező felhasználó által regisztrált alkalmazásregisztráció tudja megtekinteni és felügyelt csak egyetlen developer-fiók használatával. Több fejlesztő között nem lehet megosztani. Ha meg szeretné osztani az alkalmazás regisztrációját, többek között a több fejlesztő, létrehozhat az alkalmazás a regisztrációs portál egy olyan Azure AD-fiókkal való bejelentkezés révén.
* Az átirányítási URI-t, hogy a formátum a több korlátozások vonatkoznak. Átirányítási URI-k kapcsolatos további információkért tekintse meg a következő szakaszt.

## <a name="restrictions-on-redirect-uris"></a>Átirányítási URI-k korlátozásai

Az alkalmazásregisztrációs portálon a regisztrált alkalmazások átirányítási URI-értékek korlátozott számú korlátozódnak. Az átirányítási URI-t a webalkalmazások és szolgáltatások kell kezdődnie, a rendszer `https`, és minden átirányítási URI-értékek egyetlen DNS-tartományba kell megosztani. Például nem lehet regisztrálni egy webalkalmazást, amely ezek az átirányítási URI-k valamelyikét:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

A regisztrációs rendszer összehasonlítja a meglévő átirányítási URI-t, a DNS-név az átirányítási URI-t ad hozzá a teljes DNS-nevét. A DNS-név hozzáadására irányuló kérelem sikertelen lesz, ha a következő feltételek egyikének értéke igaz:  

* Az új átirányítási URI-t teljes DNS-neve nem egyezik a meglévő átirányítási URI-t a DNS-nevét.
* Teljes DNS-neve az új átirányítási URI nincs részterületét képezi a meglévő átirányítási URI-t.

Például ha az alkalmazás az átirányítási URI-ja:

`https://login.contoso.com`

A következő módon adhatja hozzá:

`https://login.contoso.com/new`

Ebben az esetben a DNS-név pontosan egyezik. Esetleg a következőt teheti meg:

`https://new.login.contoso.com`

Ebben az esetben a login.contoso.com egyik DNS-altartományára hivatkozik. Ha szeretne egy alkalmazás, amely rendelkezik a login-east.contoso.com vagy a login-west.contoso.com átirányítási URI-k, hozzá kell adnia azokat átirányítási URI-k, az itt látható sorrendben:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Az utóbbi két adhat hozzá, mert az első átirányítási URI-t, altartományába tartoznak a contoso.com. Ez a korlátozás egy soron következő kiadásban törlődni fog.

Vegye figyelembe azt is, egy adott alkalmazásban csak 20 válasz URL-címmel rendelkezhet.

Alkalmazás regisztrálása az alkalmazásregisztrációs portálon az kezelésével kapcsolatos információkért lásd: [alkalmazás regisztrálása a v2.0-végponttal](quickstart-v2-register-an-app.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Függvénytárak és SDK-k korlátozásai

Klienskódtár-támogatásával a v2.0-végpont jelenleg korlátozott. Ha azt szeretné, használhatja a v2.0-végpontra egy éles alkalmazásban, ezen lehetőségek állnak rendelkezésére:

* Ha egy webalkalmazást hoz létre, a bejelentkezéshez és jogkivonat-ellenőrzéshez biztonságosan használhatja a Microsoft mindenki számára elérhető kiszolgálóoldali közbenső szoftver. Ezek közé tartozik az OWIN Open ID Connect közbenső szoftvert, az ASP.NET és a Node.js Passport beépülő modult. Használja a Microsoft közbenső kódmintákért tekintse a [bevezetés](active-directory-appmodel-v2-overview.md#getting-started) szakaszban.
* Ha egy asztali vagy mobil alkalmazást fejleszt, a Microsoft hitelesítési tárak (MSAL) előzetes egyikét használhatja. Ezek a kódtárak vannak egy éles által támogatott előzetes verzióban érhető el, így biztonságosan használhatja őket az éles környezetben. További tudnivalók az előzetes verzió és a rendelkezésre álló kódtárak a feltételek [hitelesítési tárak referencia](active-directory-v2-libraries.md).
* A platformon nem fedi le a Microsoft-kódtárak integrálható a v2.0-végpont által közvetlenül üzenetek küldése és fogadása protokoll az alkalmazás kódjában. A 2.0-s OpenID Connectet és az OAuth-protokollok [explicit módon vannak dokumentálva](active-directory-v2-protocols.md) ilyen az integráció végrehajtásához.
* Végül nyílt forráskódú ID Connect megnyitásához és az OAuth-kódtárak segítségével integrálhatók az a v2.0-végpontra. A 2.0-s protokoll jelentős módosítások nélkül számos nyílt forráskódú protokoll kódtár kompatibilisnek kell lennie. Az ilyen típusú kódtárak rendelkezésre állásának nyelvtől és platformtól függ. A [Open ID Connect](http://openid.net/connect/) és [OAuth 2.0](http://oauth.net/2/) webhelyek népszerű megvalósításokhoz listának a karbantartására. További információkért lásd: [Azure Active Directory v2.0 és hitelesítési kódtárai](active-directory-v2-libraries.md), és a nyílt forráskódú ügyféloldali függvénytárak és minták a v2.0-végponttal tesztelt listáját.

## <a name="restrictions-on-protocols"></a>Korlátozásokat protokollokkal

A v2.0-végpont nem támogatja a SAML vagy WS-Federation; csak a támogatott Open ID Connect és az OAuth 2.0. Nem minden funkciók és képességek OAuth protokollok bekerültek a v2.0-végpontra.

A következő protokoll funkciók és képességek jelenleg *nem érhető el* a v2.0-végpont:

* Jelenleg a `email` csak jogcímet ad vissza, ha egy nem kötelező jogcím van konfigurálva, és hatókör hatókör = e-mail lett megadva a kérelemben. Azonban ez a viselkedés változik, amint a v2.0-végpont további szabványoknak való megfelelés az Open ID Connect és OAuth2.0 frissül.
* Az OpenID Connect UserInfo végpont a v2.0-végpont nincs megvalósítva. Azonban a felhasználói profil összes adata kapott potenciálisan volna a végpont érhető el a Microsoft Graph `/me` végpont.
* A v2.0-végpont nem támogatja kiállító szerepkör vagy csoport jogcímek azonosító-jogkivonatokat.
* A [OAuth 2.0 erőforrás tulajdonosának jelszava hitelesítő adatok engedélyezés](https://tools.ietf.org/html/rfc6749#section-4.3) a v2.0-végpont nem támogatott.

Emellett a v2.0-végpont nem támogatja az SAML- vagy WS-Federation protokollt semmilyen formáját.

Jobb megértése érdekében a v2.0-végpont támogatott protokoll funkciók körét, olvassa el a [OpenID Connectet és az OAuth 2.0 protokoll referenciája](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Korlátozások a munkahelyi és iskolai fiókok esetében

Ha korábban már használta az Active Directory Authentication Library (ADAL) a Windows-alkalmazások, előfordulhat, hogy elvégezte Windows integrált hitelesítést, amely használja a Security Assertion Markup Language (SAML) helyességi feltétel grant veheti. Az engedélyhez a felhasználók Azure AD összevont bérlők is nélkül csendes hitelesítést a helyszíni Active Directory-példánynak a hitelesítő adatok megadása. Az SAML helyességi feltétel megadása a v2.0-végpont jelenleg nem támogatott.
