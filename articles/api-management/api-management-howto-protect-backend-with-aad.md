---
title: Az API-k védelméhez az Azure Active Directory és az API Management OAuth 2.0 használatával |} Microsoft Docs
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
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Az API-k védelméhez az Azure Active Directory és az API Management OAuth 2.0 használatával

Ez az útmutató bemutatja, hogyan konfigurálja az Azure API Management-védelemmel egy API-t, az OAuth 2.0 protokollt az Azure Active Directoryval (Azure AD). 

## <a name="prerequisites"></a>Előfeltételek
Kövesse a cikkben, kell rendelkeznie:
* Az API Management-példány
* Az API-k közzétett API Management példányát használó
* Az Azure AD-bérlő

## <a name="overview"></a>Áttekintés

Íme egy gyors áttekintést a lépéseket:

1. Egy alkalmazás (háttér-alkalmazás) regisztrálása az Azure AD-határoz meg az API-t.
2. Egy másik alkalmazás (ügyfél-alkalmazás) regisztrálása az Azure AD-be egy ügyfél-alkalmazás, amely hívja az API-t kell meghatároznia.
3. Az Azure AD engedélyt engedélyezése az ügyfél-alkalmazásnak, hogy a háttér-alkalmazást hívja.
4. A fejlesztői konzolján OAuth 2.0 felhasználói hitelesítés használatára konfigurálja.
5. Adja hozzá a **érvényesítése jwt** házirend az összes bejövő kérés OAuth jogkivonatot érvényesítéséhez.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Alkalmazás regisztrálása az Azure AD-határoz meg az API-t

Az API-k és az Azure AD védelme érdekében az első lépés az alkalmazás regisztrálása az Azure ad-ben, amely jelöli az API-t. 

1. Keresse meg az Azure AD-bérlőről, és tallózással keressen meg **App regisztrációk**.

2. Válassza az **Új alkalmazás regisztrálása** elemet. 

3. Adjon meg egy nevet az alkalmazás. (Például a név van `backend-app`.)  

4. Válasszon **Web app / API** , a **alkalmazástípus**. 

5. A **bejelentkezési URL-cím**, használhat `https://localhost` helyőrzőként.

6. Kattintson a **Létrehozás** gombra.

Az alkalmazás létrehozásakor, jegyezze fel a **Alkalmazásazonosító**, egy későbbi lépésben használatra. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Egy másik alkalmazás regisztrálása az Azure AD-ügyfélalkalmazás képviselő

Minden ügyfél-alkalmazás, amely hívja az API-t regisztrálva kell lennie, valamint az Azure AD-ben alkalmazásként. Ebben a példában a mintaalkalmazás ügyfél esetén a fejlesztői konzolján az API Management developer portálon. Íme egy másik alkalmazás regisztrálása az Azure AD-határoz meg a fejlesztői konzolján.

1. Válassza az **Új alkalmazás regisztrálása** elemet. 

2. Adjon meg egy nevet az alkalmazás. (Például a név van `client-app`.)

3. Válasszon **Web app / API** , a **alkalmazástípus**.  

4. A **bejelentkezési URL-cím**, használhat `https://localhost` helyőrző, vagy az API Management-példány URL-CÍMÉT használja a bejelentkezés. (Például az URL-címe: `https://contoso5.portal.azure-api.net/signin`.)

5. Kattintson a **Létrehozás** gombra.

Az alkalmazás létrehozásakor, jegyezze fel a **Alkalmazásazonosító**, egy későbbi lépésben használatra. 

Ezután hozzon létre egy ügyfélkulcsot, az alkalmazás egy későbbi lépésben használatra.

1. Válassza ki **beállítások** újra, és navigáljon a **kulcsok**.

2. A **jelszavak**, adjon meg egy **kulcs leírását**. Válassza ki a kulcsot kell lejár, és mikor kiválasztása **mentése**.

Jegyezze fel a kulcs értékét. 

## <a name="grant-permissions-in-azure-ad"></a>Az Azure AD-engedélyeket

Most, hogy regisztrálta az API-t és a fejlesztői konzolján képviselő két alkalmazás, kell biztosítania az engedélyt, hogy az ügyfél-alkalmazásnak, hogy a háttér-alkalmazást hívja.  

1. Keresse meg a **alkalmazás regisztrációk**. 

2. Válassza ki `client-app`, majd a **beállítások**.

3. Válassza ki **szükséges engedélyek** > **Hozzáadás**.

4. Válassza ki **API kiválasztása**, keresse meg a `backend-app`.

5. A **delegált engedélyek**, jelölje be `Access backend-app`. 

6. Válassza ki **válasszon**, majd válassza ki **végzett**. 

> [!NOTE]
> Ha **Azure Active Directory** nem szerepel az engedélyek más alkalmazásoknak, jelölje be **Hozzáadás** veheti fel a listából.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>A fejlesztői konzolján OAuth 2.0 felhasználó engedélyezése

Ezen a ponton hozott létre az alkalmazások az Azure AD, és lehetővé teszi a háttér-alkalmazást hívja az ügyfél-alkalmazásnak megfelelő engedélyekkel rendelkeznek. 

Ebben a példában a fejlesztői konzolján az ügyfélalkalmazás. A következő lépések végrehajtásával OAuth 2.0 felhasználói engedélyezése a fejlesztői konzolján. 

1. Keresse meg az API Management-példányt.

2. Válassza ki **OAuth 2.0** > **hozzáadása**.

3. Adjon meg egy **megjelenített név** és **leírás**.

4. Az a **ügyfél regisztrációs URL-címe**, adja meg például a helyőrző értékét `http://localhost`. A **ügyfél regisztrációs URL-címe** használó felhasználók létrehozása és konfigurálása a saját felhasználói OAuth 2.0-s szolgáltatók, amely támogatja ezt a lapra mutat. Ebben a példában a felhasználók létrehozása és nem a saját fiókok konfigurálása, hogy az egy helyőrző helyette használni.

5. A **engedélyezési biztosítani típusok**, jelölje be **engedélyezési kód**.

6. Adja meg a **engedélyezési végpont URL-címet** és **végponti URL-cím Token**. Ezek az értékek lekérését, a **végpontok** lap az Azure AD-bérlőben. Keresse meg a **App regisztrációk** újra lapon, és válassza ki **végpontok**.

7. Másolás a **OAuth 2.0 hitelesítési végpont**, és illessze be azt a **engedélyezési végpont URL-címet** szövegmezőben.

8. Másolás a **OAuth 2.0 Token-végpont**, és illessze be azt a **végponti URL-cím Token** szövegmezőben. A jogkivonat végpontjához beillesztésével, mellett nevű törzs paraméter hozzáadása **erőforrás**. Ez a paraméter értékét, használja a **Alkalmazásazonosító** a háttér-alkalmazás.

9. Ezt követően adja meg az ügyfél hitelesítő adatait. Ezek a hitelesítő adatai az ügyfélalkalmazás.

10. A **ügyfél-azonosító**, használja a **Alkalmazásazonosító** az ügyfél-alkalmazás.

11. A **ügyfélkulcs**, korábban létrehozott kulcs használ az ügyfélalkalmazás. 

12. Azonnal követően a titkos ügyfélkulcs van a **redirect_url** engedélyezési kód támogatás típusa. Jegyezze fel az URL-címet.

13. Kattintson a **Létrehozás** gombra.

14. Lépjen vissza a **beállítások** lap az ügyfél-alkalmazás.

15. Válassza ki **válasz URL-címek**, és illessze be a **redirect_url** első sorában. Ebben a példában cseréje `https://localhost` a URL-címet az első sorban.  

Most, hogy konfigurálta az OAuth 2.0 hitelesítési kiszolgáló, a fejlesztői konzolján hozzáférési jogkivonatokat kérhetnek az Azure AD. 

A következő lépéssel-OAuth 2.0 felhasználói engedélyezése az API-hoz. Ez lehetővé teszi, hogy a felhasználó nevében egy hozzáférési jogkivonat beszerzése az API-hívások végrehajtása előtt kell tudnia a fejlesztői konzolján.

1. Keresse meg az API Management-példányt, és navigáljon a **API-k**.

2. Válassza ki a védeni kívánt API. Ebben a példában, használja a `Echo API`.

3. Ugrás a **beállítások**.

4. A **biztonsági**, válassza a **OAuth 2.0**, és válassza ki a korábban megadott OAuth 2.0-kiszolgálót. 

5. Kattintson a **Mentés** gombra.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Sikeresen hívja az API-t a fejlesztői portálján

Most, hogy az OAuth 2.0 felhasználói hitelesítés engedélyezve van a `Echo API`, a fejlesztői konzolján egy jogkivonatot a felhasználó nevében beszerzi a API hívása előtt.

1. Tallózással keresse meg a műveleteket a a `Echo API` a fejlesztői portálon, majd válassza a **kipróbálás**. Ekkor meg a fejlesztői konzolhoz.

2. Jegyezze fel az új elem a **engedélyezési** szakaszban, a hitelesítési kiszolgáló, az előzőekben adott hozzá megfelelő.

3. Válassza ki **engedélyezési kód** engedélynek legördülő listából válassza ki, és felajánlja jelentkezzen be az Azure AD-bérlő. Ha már be van jelentkezve a fiókot, előfordulhat, hogy nem kell kérni.

4. Sikeres bejelentkezés, miután egy `Authorization` fejléc hozzáadódik a kérelmet, az Azure AD hozzáférési tokent. A következő egy minta jogkivonatot (a Base64 kódolású):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Válassza ki **küldése**, és sikeresen meghívhatja az API-t.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Előre a kérések hitelesítése JWT érvényesítési házirend konfigurálása

Ezen a ponton amikor egy felhasználó megpróbál egy hívás a fejlesztői konzolján, a felhasználótól a bejelentkezéshez. A fejlesztői konzolján egy jogkivonatot a felhasználó nevében beszerzi.

De mi történik, ha valaki hívja az API-t, vagy lexikális eleme érvénytelen jogkivonat nélkül? Például továbbra is meghívhatja az API-t még akkor is, ha törli a `Authorization` fejléc. A hiba oka, hogy az API Management nem ellenőrzi a hozzáférési jogkivonat ezen a ponton. Egyszerűen adja át a `Authorization` a háttér-API fejléc.

Használhatja a [érvényesítése JWT](api-management-access-restriction-policies.md#ValidateJWT) előre engedélyezésére az API Management kérelmek érvényesítésével megjeleníthető az egyes bejövő kérelmek a hozzáférési jogkivonatok házirend. Ha a kérelem nem rendelkezik érvényes tokent, az API Management blokkolja. Például a következő házirendet adhat hozzá a `<inbound>` házirend szakasza a `Echo API`. Ellenőrzi a célközönség jogcím egy hozzáférési jogkivonatot, és hibaüzenetet ad vissza, ha a jogkivonat nem érvényes. Házirendek konfigurálásával kapcsolatos további információkért lásd: [beállítása vagy szerkesztheti a szabályzatokat](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Az API hívása alkalmazás létrehozásához

Ebben az útmutatóban, szolgál a fejlesztői konzolján az API Management, a mintaalkalmazást ügyfél hívja a `Echo API` OAuth 2.0 védi. Alkalmazás létrehozásához, és az OAuth 2.0 megvalósításával kapcsolatos további információkért lásd: [Azure Active Directory-Kódminták](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>További lépések
* További információ [Azure Active Directory és OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* Tekintse meg több [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) API-kezeléssel kapcsolatos.
* Egyéb módjai biztonságos a háttér-szolgáltatás, lásd: [kölcsönös tanúsítványhitelesítés](api-management-howto-mutual-certificates.md).

* [Hozzon létre egy API-kezelés szolgáltatás példányt](get-started-create-service-instance.md).

* [Az első API kezelése](import-and-publish.md).
