---
title: "Az Azure AD v2.0-végponttól vált |} Microsoft Docs"
description: "Az app model v2.0 nyilvános előzetes verzió protokollok végzett módosításokat leírását."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e6c5b891-0b5d-47dc-8184-5d0ef6a1a006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3d344529d1ac9891e5a961de630dea3c1e4caab4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="important-updates-to-the-v20-authentication-protocols"></a>Fontos frissítések a v2.0-hitelesítési protokollok
Figyelmet fejlesztők! A következő két hétben frissítjük néhány frissítések az lehet, hogy olyan módosításokat a próbaidőszak alatt írt alkalmazások számára megtörje, amely a v2.0-hitelesítési protokollok számára.  

## <a name="who-does-this-affect"></a>Aki befolyásolja ez?
A v2.0 használandó írt alkalmazások összevont hitelesítési végpontot

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

További információ a v2.0-végpontra található [Itt](active-directory-appmodel-v2-overview.md).

A v2.0-végpontra használatával kódolás közvetlenül a v2.0 protokoll az alkalmazás létrehozása, ha bármely az OpenID Connect vagy OAuth webes middlewares használatával, vagy más 3. fél könyvtárak segítségével végezhet hitelesítést, akkor kell készíteni a projektek teszteléséhez, és végezze el a szükséges módosításokat.

## <a name="who-doesnt-this-affect"></a>Ki nem ez hatással van?
Az éles Azure AD hitelesítési végpont, elleni írt alkalmazások

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Ez a protokoll köve van beállítva, és fog nem léptek fel a módosításokat.

Továbbá ha az alkalmazás **csak** használja az ADAL-könyvtár végezzen hitelesítést, nem kell bármit módosíthat.  ADAL rendelkezik az alkalmazás a módosításokat a védett.  

## <a name="what-are-the-changes"></a>Mik a módosításokat?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>A x5t érték eltávolítása a JWT fejlécek
A v2.0-végpontra használ JWT-jogkivonatokat széles körben, tartalmazó vonatkozó metaadatokat a jogkivonatot a paraméterek fejlécszakasza.  Ha dekódolni a fejléc az aktuális JWTs közül az egyik, találjuk hasonlót:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Ahol a "x5t" és a "kid" tulajdonság azonosítása a nyilvános kulcs ellenőrzése a jogkivonat aláírása, mert beolvasta az OpenID Connect metaadat-végpontjához használandó.

Itt hajtunk módosítása, hogy távolítsa el a "x5t" tulajdonságot.  Előfordulhat, hogy továbbra is használhatja ugyanazt a mechanizmust érvényesíthet jogkivonatokat, de csak a "kid" tulajdonság beolvasása a megfelelő nyilvános kulccsal, az OpenID Connect protokoll megadott szoftverlicenceknek. 

> [!IMPORTANT]
> **A feladat: Győződjön meg arról, hogy az alkalmazás nem létezik-e a x5t érték függ.**
> 
> 

### <a name="removing-profileinfo"></a>Profile_info eltávolítása
Korábban, a v2.0-végponttal rendelkezik lett visszaadni a base64-kódolású JSON-objektum nevű token válaszok `profile_info`.  Amikor olyan hozzáférési jogkivonatot kérnek a v2.0-végpontra vonatkozó kérelmet küld:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

A válasz a következő JSON-objektum jelenne meg:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

A `profile_info` érték található információ a felhasználó, aki regisztrált a alkalmazásba – a megjelenített név, Utónév, Vezetéknév, e-mail cím, azonosítója, és így tovább.  Elsősorban a `profile_info` token-gyorsítótárazási volt használva, és megjeleníti a célra.

Most megszüntetjük a `profile_info` érték –, de ne aggódjon, azt az adatokat továbbra is ad át a fejlesztőkig némileg eltérő helyen.  Ahelyett, hogy `profile_info`, a v2.0-végpontra most visszaállítja egy `id_token` minden token válaszként:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Előfordulhat, hogy dekódolni, és elemezni a id_token profile_info kapott adatokat beolvasni.  A id_token egy JSON webes jogkivonat (JWT), az OpenID Connect által megadott tartalom.  A kód minderre, legyen nagyon hasonló –, egyszerűen vissza kell fejteni a id_token a középső szegmens (szervezet), és base64 dekódolni a belül a JSON-objektum eléréséhez.

A következő két hétben kódaláírással kell az alkalmazásnak, hogy a felhasználói adatok lekérését vagy a `id_token` vagy `profile_info`; attól jelen.  Ily módon, ha módosítás történik, az alkalmazás zökkenőmentesen kezelni tud a átállás `profile_info` való `id_token` megszakítás nélkül.

> [!IMPORTANT]
> **A feladat: Győződjön meg arról, hogy az alkalmazás nem függ a megléte a `profile_info` érték.**
> 
> 

### <a name="removing-idtokenexpiresin"></a>Id_token_expires_in eltávolítása
Hasonló `profile_info`, is megszüntetjük a `id_token_expires_in` válaszok paraméter.  Korábban, a a v2.0-végpontra vonatkozó értéket visszaadnia `id_token_expires_in` minden egyes id_token válasz, például egy engedélyezés válaszul együtt:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Vagy a token válasz:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

A `id_token_expires_in` érték azt jelzi másodpercben a id_token érvényes marad.  Most, megszüntetjük a `id_token_expires_in` teljesen érték.  Ehelyett használhat az OpenID Connect szabvány `nbf` és `exp` jogcímeket kell vizsgálni egy id_token érvényességét.  Tekintse meg a [v2.0 jogkivonat referenciái](active-directory-v2-tokens.md) jogcímek olvashat.

> [!IMPORTANT]
> **A feladat: Győződjön meg arról, hogy az alkalmazás nem függ a megléte a `id_token_expires_in` érték.**
> 
> 

### <a name="changing-the-claims-returned-by-scopeopenid"></a>A jogcímek hatókör által visszaadott módosítása = openid
Ez a változás valójában a legjelentősebb – lesz, az hatással szinte minden a v2.0-végpontra alkalmazó alkalmazásban.  Számos alkalmazás kérelmeket küldeni a v2.0-végpontot a a `openid` hatókörét, például:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Ma, amikor a felhasználó engedélyezi a hozzájárulási a `openid` hatókör, az alkalmazás a felhasználó adatai rengeteg hasznos fogadása az eredményül kapott id_token.  Ezek a jogcímek tartalmazhatják a neve, elsődleges felhasználónév, e-mail címét, objektumazonosító: és több.

Ez a frissítés azt módosítani az adatokat, amelyek a `openid` hatókör számára biztosítja az alkalmazás eléréséhez, jobb comform az OpenID Connect meghatározásával.  A `openid` hatókör fog csak lehetővé teszi az alkalmazásnak a felhasználó bejelentkezni, és egy alkalmazás-specifikus azonosítót a felhasználó kap a `sub` a id_token a jogcímek.  A jogcímek, az csak egy id_token a `openid` nyújtott lesz nem tartalmaz személyazonosításra alkalmas adatok.  Példa id_token jogcímeket a következők:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Ha azt szeretné, személyazonosításra alkalmas adatokat (PII) beszerzése a felhasználóról az alkalmazásban, az alkalmazás további engedélyek kéréséhez a felhasználónak a kell.  Az OpenID Connect spec – vezetjük be két új hatókört támogatása a `email` és `profile` hatókörök – ami lehetővé teszi.

* A `email` hatóköre nagyon egyszerű – az alkalmazás eléréséhez a felhasználó elsődleges e-mail címéhez keresztül lehetővé teszi a `email` a id_token a jogcímek.  Vegye figyelembe, hogy a `email` jogcím nem mindig kerül be a id_tokens – csak akkor lesz része ha rendelkezésre áll a profil.
* A `profile` objektumazonosító hatókör számára biztosítja az alkalmazás eléréséhez a felhasználó – a neve, az elsődleges felhasználónév, minden más alapvető adatait, és így tovább.

Ez lehetővé teszi, hogy az alkalmazás minimális nyilvánosságra módon code – kérje meg a felhasználó csak a készlethez, hogy az alkalmazás a feladat elvégzéséhez szükséges információk.  Ha folytatja a az alkalmazás jelenleg fogadó felhasználói adatok teljes készletének első, a hitelesítési kérések bele kell foglalni a három hatóköröket:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Az alkalmazás megkezdődhet az küldése a `email` és `profile` hatókörök azonnal, és a v2.0-végpontra a két hatóköröket elfogadja és engedélyek kér a felhasználóktól, szükség esetén megkezdéséhez.  Azonban az értelmezési módosítása a `openid` hatókör nem lépnek érvénybe néhány hétig.

> [!IMPORTANT]
> **A feladat: vegye fel a `profile` és `email` hatóköröket, ha az alkalmazás csak a felhasználó adatait.**  Vegye figyelembe, hogy adal-t fog tartalmazni mindkét engedéllyel a kérelmek alapértelmezés szerint. 
> 
> 

### <a name="removing-the-issuer-trailing-slash"></a>A záró perjelet kibocsátó eltávolítása.
Korábban a kibocsátó érték, amely megjelenik a v2.0-végpontra a jogkivonatok volt

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Ha a guid volt a tenantId az Azure AD-bérlő a jogkivonatot kibocsátó.  A módosítások a kibocsátó érték válik.

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

mindkét jogkivonatokat, az OpenID Connect felderítési dokumentumban.

> [!IMPORTANT]
> **A feladat: Győződjön meg arról, hogy az alkalmazás és a záró perjelet anélkül kibocsátó értékét fogadja a kibocsátó érvényesítése során.**
> 
> 

## <a name="why-change"></a>Miért is megváltozik?
Ezek a változások bevezetéséről az elsődleges kifejlesztésének meg kell felelnie az OpenID Connect szabvány.  Mivel OpenID Connect megfelelő, Reméljük, a Microsoft identity szolgáltatásokkal és az egyéb identitás-szolgáltatások az iparág integrálásával közötti különbségek minimalizálása érdekében.  A fejlesztők a kedvenc nyílt forráskódú hitelesítési könyvtárat használja anélkül, hogy módosítják a könyvtárak Microsoft különbségek befogadásához szeretnénk.

## <a name="what-can-you-do"></a>Mire szolgál?
Mai megkezdheti a fent leírt módosításokat végzett.  Azonnal kell:

1. **Távolítsa el a függőségeket a a `x5t` fejléc paraméter.**
2. **Áttérés kezelésére `profile_info` való `id_token` token válaszokban.**
3. **Távolítsa el a függőségeket a a `id_token_expires_in` válasz paraméter.**
4. **Adja hozzá a `profile` és `email` hatóköröket az alkalmazáshoz, ha az alkalmazás alapszintű felhasználói információra van szüksége.**
5. **Fogadja el vagy záró perjelet anélkül jogkivonatokba kibocsátó értékeket.**

A [v2.0 protokoll dokumentációját](active-directory-v2-protocols.md) már frissítették a változásoknak, így használhatja referenciaként létrehozásában, frissítse a kódot.

Ha módosításait hatókörön további kérdése van, küldje el nyugodtan érheti el nekünk a Twitteren: @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Milyen gyakran történik a protokoll módosításait?
A hitelesítési protokollok működéséhez minden további megtörje vált nem várható.  Ezeket a módosításokat, azokat egy kiadási szándékosan azt vannak kötegelése, így nem kell végighaladnia az ilyen típusú frissítési folyamat újra bármikor elérhető.  Természetesen segítségével szolgáltatásokat adhat a v2.0 összevont hitelesítési szolgáltatás, amely kihasználhatja a rendszer a Folytatás, de ezeket a módosításokat kell adalékanyag és nem a meglévő kódot sortörés.

Végül szeretnénk dolgot próbálhatja ki a próbaidőszak alatt Köszönjük, hogy fel.  Az elemzések és a korai támogatók véleményeket hasznos információt eddigi, és Reméljük, megoszthatja vélemények és ötleteket fogja folytatni.

Örömmel kódolási!

A Microsoft Identity osztály

