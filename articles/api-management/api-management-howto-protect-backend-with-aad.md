---
title: Az API-k OAuth 2.0-s használata Azure Active Directory és az API Management védelme |} Microsoft Docs
description: Ismerje meg, hogyan védi meg a webes API háttéralkalmazás az Azure Active Directory és az API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 3caa3d2b8640c83f1001aeac3b0a5e9ada143183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Hogyan védi meg az API-k és Azure Active Directory és az API Management OAuth 2.0 használatával

Ez az útmutató bemutatja, hogyan konfigurálhatja az API Management (APIM) példány védelmét az API-k az OAuth 2.0 protokollt használja az Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Előfeltétel
Kövesse a cikkben, kell rendelkeznie:
* APIM példánya
* Az API-k közzétett APIM példányának használatával
* Az Azure AD-bérlő

## <a name="overview"></a>Áttekintés

Ez az útmutató bemutatja, hogyan védi meg az API-k APIM az OAuth 2.0-s verziójával. Ebben a cikkben az Azure AD szolgáltatásba az engedélyezési (OAuth kiszolgáló) használatos. Az alábbiakban van egy gyors áttekintést a lépéseket:

1. Az Azure AD határoz meg az API-alkalmazás (háttér-alkalmazás) regisztrálása
2. Egy másik alkalmazás (ügyfél-alkalmazás) regisztrálni kell az Azure AD-be egy ügyfél-alkalmazás, amely hívja az API-t kell meghatároznia
3. Az Azure AD-engedélyezze, hogy az ügyfél-alkalmazás hívni a háttér-alkalmazás
4. A fejlesztői konzolján OAuth 2.0 felhasználói engedélyezési használandó konfigurálása
5. Az OAuth jogkivonatot az összes bejövő kérés érvényesítéséhez érvényesítése jwt-házirend hozzáadása

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Alkalmazás regisztrálása az Azure AD-határoz meg az API-t

Az API-k és az Azure AD védelme érdekében az első lépés az alkalmazás regisztrálása az Azure ad-ben, amely jelöli az API-t. 

Navigáljon az Azure AD-bérlőn, majd navigáljon a **App regisztrációk**.

Válassza ki **új alkalmazás regisztrációja**. 

Adjon meg egy nevet az alkalmazás. Ebben a példában `backend-app` szolgál.  

Válasszon **Web app / API** , a **alkalmazástípus**. 

A **bejelentkezési URL-cím**, használhat `https://localhost` helyőrzőként.

Kattintson a **létrehozása**.

Az alkalmazás létrehozása után jegyezze fel a **Alkalmazásazonosító** egy későbbi lépésben használatra. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Egy másik alkalmazás regisztrálása az Azure AD-ügyfélalkalmazás képviselő

Minden ügyfél-alkalmazás, amelyet az API regisztrálva kell lennie, valamint az Azure AD-ben alkalmazásként. Ebben az útmutatóban a minta ügyfél alkalmazásként használjuk a APIM fejlesztői portálra a fejlesztői konzolján. 

Igazolnia kell egy másik alkalmazás regisztrálása az Azure AD-határoz meg a fejlesztői konzolján.

Kattintson a **új alkalmazás regisztrációja** újra. 

Adja meg az alkalmazás nevét, és válassza a **Web app / API** , a **alkalmazástípus**. Ebben a példában `client-app` szolgál.  

A **bejelentkezési URL-cím**, használhat `https://localhost` helyőrző vagy a bejelentkezési URL-CÍMÉT a APIM példányát használja. Ebben a példában `https://contoso5.portal.azure-api.net/signin` szolgál.

Kattintson a **létrehozása**.

Az alkalmazás létrehozása után jegyezze fel a **Alkalmazásazonosító** egy későbbi lépésben használatra. 

Most létre kell hozzon létre egy ügyfélkulcsot a az alkalmazás használatát egy későbbi lépésben.

Kattintson a **beállítások** újra, és navigáljon **kulcsok**.

A **jelszavak**, adjon meg egy **kulcs leírását**, válassza ki, ha a kulcsot kell jár le, és kattintson a **mentése**.

Jegyezze fel a kulcs értékét. 

## <a name="grant-permissions-in-aad"></a>Engedélyeket az aad-ben

Most azt regisztrált két alkalmazás felel meg az API-t (Ez azt jelenti, hogy háttér-alkalmazás) és a fejlesztői konzolján (Ez azt jelenti, hogy az ügyfél-alkalmazás), azt kell biztosítania engedélyt, hogy az ügyfél-alkalmazásnak, hogy a háttér-alkalmazást hívja.  

Navigáljon a **alkalmazás regisztrációk** újra. 

Kattintson a `client-app` , és navigáljon **beállítások**.

Kattintson a **szükséges engedélyek** , majd **Hozzáadás**.

Kattintson a **API kiválasztása** keresse meg a `backend-app`.

Ellenőrizze `Access backend-app` alatt **delegált engedélyekkel**. 

Kattintson a **válasszon** , majd **végzett**. 

> [!NOTE]
> Ha **Windows** **Azure Active Directory** van az engedélyek más alkalmazásoknak nincs felsorolva, kattintson a **Hozzáadás** , és adja hozzá a listából.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>A fejlesztői konzolján OAuth 2.0 felhasználó engedélyezése

Ezen a ponton azt hozott létre az alkalmazások az Azure ad-ben, és lehetővé teszi a háttér-alkalmazást hívja az ügyfél-alkalmazásnak megfelelő engedélyekkel rendelkeznek. 

Ebben az útmutatóban a fejlesztői konzolján használjuk, ha az ügyfél-alkalmazásként. Következő lépések azt ismertetik, hogyan OAuth 2.0 felhasználói engedélyezési a fejlesztői konzolján engedélyezése 

Nyissa meg a APIM példányát.

Kattintson a **OAuth 2.0** , majd **hozzáadása**.

Adjon meg egy **megjelenített név** és **leírás**.

Az ügyfél-regisztrációk a lap URL-címe, ** adja meg például a helyőrző értékét `http://localhost`.  A **ügyfél regisztrációs URL-címe** mutat, a felhasználók segítségével hozza létre és konfigurálja a saját felhasználói a felhasználói fiókok kezelését támogató OAuth 2.0-s szolgáltatók oldal. Ebben a példában a felhasználók létrehozása és nem a saját felhasználói konfigurálása, így helyőrzőjeként szolgál.

Ellenőrizze **engedélyezési kód** , a **engedélyezési biztosítani típusok**.

Ezt követően adja meg **engedélyezési végpont URL-címet** és **végponti URL-cím Token**.

Ezek az értékek lekérhetők a **végpontok** lap az Azure AD-bérlőben. A végpontok szeretne használni, keresse meg a **App regisztrációk** újra lapon, majd kattintson a **végpontok**.

Másolás a **OAuth 2.0 hitelesítési végpont** és illessze be azt a **engedélyezési végpont URL-címet** szövegmező.

Másolás a **OAuth 2.0 Token-végpont** és illessze be azt a **végponti URL-cím Token** szövegmező.

A jogkivonat végpontjához beillesztésével, mellett hozzáadása egy további válaszüzenettörzs-paramétert nevű **erőforrás** és az érték használható a **Alkalmazásazonosító** a háttér-alkalmazás.

Ezt követően adja meg az ügyfél hitelesítő adatait. Ezek a hitelesítő adatai az ügyfélalkalmazás.

A **ügyfél-azonosító**, használja a **Alkalmazásazonosító** az ügyfél-alkalmazás.

A **ügyfélkulcs**, korábban létrehozott kulcs használ az ügyfélalkalmazás. 

Azonnal követően a titkos ügyfélkulcs van a **redirect_url** engedélyezési kód támogatás típusa.

Jegyezze fel az URL-címet.

Kattintson a **létrehozása**.

Lépjen vissza a **beállítások** lap az ügyfél-alkalmazás.

Kattintson a **válasz URL-címek** , majd illessze be a **redirect_url** első sorában. Ebben a példában azt írni `https://localhost` a URL-címet az első sorban.  

Most azt az OAuth 2.0 hitelesítési kiszolgáló van konfigurálva, a fejlesztői konzolján kell tudni az Azure AD hozzáférési tokenek beszerzése érdekében. 

A következő lépés az API felületen az OAuth 2.0 felhasználói engedélyezése, a felhasználó nevében egy hozzáférési jogkivonat beszerzése az API hívása előtt kell, hogy a fejlesztői konzolján tudja azt.

Keresse meg a APIM példányát, írja be a **API-k**.

Kattintson az API-t, amelyet védeni kíván. A jelen példában használjuk a `Echo API`.

Ugrás a **beállítások**.

A **biztonsági**, válassza a **OAuth 2.0** jelölje ki a korábban konfigurált OAuth 2.0-kiszolgálót. 

Kattintson a **Mentés** gombra.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Sikeresen hívja az API-t a fejlesztői portálján

Most, hogy az OAuth 2.0 felhasználói hitelesítés engedélyezve van a `Echo API`, a fejlesztői konzolján egy jogkivonatot a felhasználó nevében beszerzi a API hívása előtt.

Keresse meg az összes műveletet a `Echo API` a fejlesztői portálján, majd kattintson a **kipróbálás**, amely be fogja hozni nekünk a fejlesztői konzolhoz.

Jegyezze fel az új elem a **engedélyezési** szakasz megfelelő a hitelesítési kiszolgáló, az előzőekben adott hozzá.

Válassza ki **engedélyezési kód** az engedélyt a legördülő listából válassza ki, és kéri jelentkezzen be az Azure AD-bérlő. Ha már be van jelentkezve a fiókot, előfordulhat, hogy nem kell kérni.

Sikeres bejelentkezés, miután egy `Authorization` fejléc nem kerülnek be a hozzáférési token kérelmet az Azure AD. 

Egy minta tokent tűnik, hogy az alábbi Base64-kódolású legyen.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Kattintson a **küldése** és meg kell sikeresen hívja az API-t.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Előre a kérések hitelesítése JWT érvényesítési házirend konfigurálása

Ezen a ponton amikor egy felhasználó megpróbál egy hívás a fejlesztői konzolról, a felhasználót a rendszer kéri a bejelentkezéshez és a fejlesztői konzolján fog szerezzen be egy hozzáférési jogkivonat a felhasználó nevében. Minden az elvárások szerint működik. Azonban mi történik, ha valaki hívja az API-t a token nélküli vagy lexikális eleme érvénytelen? Például megpróbálhatja a `Authorization` fejlécet, és megkeresi az API továbbra is képes. A az oka, mert APIM nem ellenőrzi a hozzáférési jogkivonat ezen a ponton. Adja át a `Auhtorization` a háttér-API fejlécben.

Használhatjuk a [érvényesítése JWT](api-management-access-restriction-policies.md#ValidateJWT) előre engedélyezésére APIM kérelmek érvényesítésével megjeleníthető az egyes bejövő kérelmek a hozzáférési jogkivonatok házirend. A kérelem nem rendelkezik érvényes tokent, ha API Management le van tiltva, és nem kerül át, mentén háttérkiszolgálóra. Azt is hozzáadhat a házirend számára, alá `Echo API`. 

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="next-steps"></a>További lépések
* Tekintse meg több [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) API-kezeléssel kapcsolatos.
* Egyéb módjai a háttérszolgáltatás biztonságos, lásd: [kölcsönös tanúsítványhitelesítés](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
