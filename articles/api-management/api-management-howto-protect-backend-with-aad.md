---
title: API-k védelme az Azure Active Directory és az API Management az OAuth 2.0 használatával |} A Microsoft Docs
description: Tudnivalók a web API háttérmodul védelme az Azure Active Directory és az API Management.
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
ms.openlocfilehash: cfe2620801f743831f77fb76f344c156676966d3
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635067"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>API-k védelme az Azure Active Directory és az API Management az OAuth 2.0 használatával

Ez az útmutató bemutatja, hogyan konfigurálhatja az Azure API Management-példány védelmét egy API-t, az Azure Active Directoryval (Azure AD) az OAuth 2.0 protokoll használatával. 

## <a name="prerequisites"></a>Előfeltételek
A jelen cikkben ismertetett lépések követéséhez rendelkeznie:
* API Management-példány
* API-k közzétételét, amely az API Management-példány
* Az Azure AD-bérlő

## <a name="overview"></a>Áttekintés

A következő lépések rövid áttekintése:

1. (Háttérrendszer-alkalmazás) alkalmazás regisztrálása az Azure ad-ben, amely az API-t jelöli.
2. Az Azure ad-ben, amelyek egy ügyfélalkalmazás, amely az API-t kell regisztrálni a egy másik alkalmazás (ügyfél-alkalmazás).
3. Az Azure AD-ben engedélyeket, az ügyfél-alkalmazás a háttér-alkalmazást hívja.
4. A fejlesztői konzolt az OAuth 2.0-s felhasználói hitelesítés használatára konfigurálja.
5. Adja hozzá a **ellenőrzése jwt** házirend minden bejövő kérelem OAuth-jogkivonat érvényesítésére.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Alkalmazás regisztrálása az API-t képviselő Azure AD-ben

Az Azure AD-vel API-k védelme, az első lépéseként kell regisztrálni egy alkalmazást, amely az API-t jelöli az Azure AD-ben. 

1. Keresse meg az Azure AD-bérlőhöz, és keresse meg **alkalmazásregisztrációk**.

2. Válassza az **Új alkalmazás regisztrálása** elemet. 

3. Adja meg az alkalmazás nevét. (Ebben a példában a név `backend-app`.)  

4. Válasszon **webalkalmazás / API** , a **alkalmazástípus**. 

5. A **bejelentkezési URL-**, használhat `https://localhost` helyettesíti.

6. Kattintson a **Létrehozás** gombra.

Az alkalmazás létrehozásakor a jegyezze meg a **Alkalmazásazonosító**, egy későbbi lépésben való használatra. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Az Azure ad-ben, amelyek egy ügyfélalkalmazás egy másik alkalmazás regisztrálása

Minden ügyfélalkalmazás, amely meghívja az API-t kell regisztrálni egy alkalmazást, valamint az Azure AD-ben. Ebben a példában az ügyfélalkalmazásra a fejlesztői konzolt az API Management fejlesztői portálon. Íme egy másik alkalmazás regisztrálása az Azure ad-ben, amely a fejlesztői konzol jelöli.

1. Válassza az **Új alkalmazás regisztrálása** elemet. 

2. Adja meg az alkalmazás nevét. (Ebben a példában a név `client-app`.)

3. Válasszon **webalkalmazás / API** , a **alkalmazástípus**.  

4. A **bejelentkezési URL-**, használhat `https://localhost` helyőrző, vagy a bejelentkezési URL-címe az API Management-példány. (Ebben a példában az URL-cím van `https://contoso5.portal.azure-api.net/signin`.)

5. Kattintson a **Létrehozás** gombra.

Az alkalmazás létrehozásakor a jegyezze meg a **Alkalmazásazonosító**, egy későbbi lépésben való használatra. 

Hozza létre az alkalmazás használatát egy későbbi lépésben, az ügyfél titkos kulcs.

1. Válassza ki **beállítások** újra, és nyissa meg **kulcsok**.

2. A **jelszavak**, adjon meg egy **kulcs leírása**. Válassza ki, ha a kulcsot kell jár le, és válassza ki **mentése**.

Jegyezze fel a kulcs értékét. 

## <a name="grant-permissions-in-azure-ad"></a>Engedélyek megadása az Azure ad-ben

Most, hogy a regisztráció két alkalmazásokat, amelyek az API-t és a fejlesztői konzolt kell biztosítania az engedélyt, hogy az ügyfél-alkalmazásnak, hogy a háttér-alkalmazást hívja.  

1. Keresse meg a **alkalmazást az alkalmazásregisztrációk**. 

2. Válassza ki `client-app`, és nyissa meg **beállítások**.

3. Válassza ki **szükséges engedélyek** > **Hozzáadás**.

4. Válassza ki **API kiválasztása**, és keressen rá a `backend-app`.

5. A **delegált engedélyek**válassza `Access backend-app`. 

6. Válassza ki **kiválasztása**, majd válassza ki **kész**. 

> [!NOTE]
> Ha **Azure Active Directory** nem szerepel az engedélyek más alkalmazásoknak, jelölje be **Hozzáadás** hozzáadhatja a listából.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>A fejlesztői konzolt az OAuth 2.0-s felhasználói hitelesítés engedélyezése

Ezen a ponton az alkalmazások már létrehozott Azure AD-ben, és megfelelő engedélyekkel rendelkezik, az ügyfél-alkalmazás a háttér-alkalmazást hívja. 

Ebben a példában a fejlesztői konzolt az ügyfélalkalmazás. A következő lépések bemutatják, hogyan ahhoz, hogy a fejlesztői konzolt az OAuth 2.0 felhasználói hitelesítést. 

1. Az Azure-portálon keresse meg az API Management-példány.

2. Válassza ki **OAuth 2.0-s** > **hozzáadása**.

3. Adjon meg egy **megjelenítendő név** és **leírás**.

4. Az a **ügyfél-regisztrációs lap URL-címe**, adja meg például egy helyőrző értéket `http://localhost`. A **ügyfél-regisztrációs lap URL-címe** mutat, a lap, amelyen a felhasználók létrehozása és konfigurálása saját fiókok, amelyek támogatják azt OAuth 2.0-s szolgáltatók számára. Ebben a példában a felhasználók hozhatnak létre vagy nem a saját felhasználói konfigurálja, hogy egy helyőrző használhatja helyette.

5. A **engedélyezéstípusok**válassza **engedélyezési kód**.

6. Adja meg a **engedélyezési végpont URL-címe** és **jogkivonat-végpont URL-címe**. Az értékek lekérésére az **végpontok** oldal az Azure AD-bérlőben. Keresse meg a **alkalmazásregisztrációk** lapon újra, és válassza ki **végpontok**.

    >[!NOTE]
    > Használja a **v1** itt végpontok

7. Másolás a **OAuth 2.0 engedélyezési végpont**, és illessze be azt a **engedélyezési végpont URL-címe** szövegmezőben.

8. Másolás a **OAuth 2.0 jogkivonat-végpont**, és illessze be azt a **jogkivonat-végpont URL-címe** szövegmezőben. A jogkivonat-végpont beillesztésével, mellett adja hozzá a nevű body paraméter **erőforrás**. Ez a paraméter értékét, használja a **Alkalmazásazonosító** a háttéralkalmazás számára.

9. Ezután adja meg az ügyfél-hitelesítő adatok. Ezek a hitelesítő adatait az ügyfélalkalmazásban.

10. A **ügyfél-azonosító**, használja a **Alkalmazásazonosító** az ügyfél-alkalmazás.

11. A **titkos Ügyfélkód**, használja az ügyfél-alkalmazás korábban létrehozott kulcsot. 

12. Van a titkos ügyfélkulcsot követően azonnal a **redirect_url** engedélyezési kód engedélyezési típus. Jegyezze fel az URL-címet.

13. Kattintson a **Létrehozás** gombra.

14. Lépjen vissza a **beállítások** ügyfélalkalmazás-lapon.

15. Válassza ki **válasz URL-címek**, és illessze be a **redirect_url** első sorában. Ebben a példában lecserélte `https://localhost` első sorában az URL-címmel.  

Most, hogy konfigurálta az OAuth 2.0 engedélyezési kiszolgáló, a fejlesztői konzolt az Azure AD hozzáférési jogkivonatok szerezheti be. 

A következő lépés, hogy engedélyezze az OAuth 2.0 felhasználói hitelesítést az API-hoz. Ez lehetővé teszi a fejlesztői konzol, ismernie kell a felhasználó nevében hozzáférési jogkivonat beszerzése az API-hívások végrehajtása előtt.

1. Keresse meg az API Management-példány, és nyissa meg **API-k**.

2. Válassza ki a védeni kívánt API-t. Ebben a példában használja a `Echo API`.

3. Lépjen a **beállítások**.

4. Alatt **biztonsági**, válassza a **OAuth 2.0**, és válassza ki a korábban beállított OAuth 2.0-kiszolgálót. 

5. Kattintson a **Mentés** gombra.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Az API sikeresen meghívása a fejlesztői portálról

> [!NOTE]
> Ez a szakasz nem vonatkozik a **fogyasztás** szint, amely nem támogatja a fejlesztői portál.

Most, hogy az OAuth 2.0-s felhasználói hitelesítés engedélyezve van a `Echo API`, a fejlesztői konzol egy hozzáférési jogkivonatot a felhasználó nevében beszerzi az API meghívása előtt.

1. Bármely művelet alatt tallózással keresse meg a `Echo API` a fejlesztői portálra, és kattintson **kipróbálás**. Ekkor meg a fejlesztői konzol.

2. Jegyezze fel az új elem a **engedélyezési** szakaszban, az előzőekben adott hozzá az engedélyezési kiszolgáló megfelelő.

3. Válassza ki **engedélyezési kód** a hitelesítést a legördülő listából válassza ki, és megkezdésére jelentkezzen be az Azure AD-bérlővel. Ha már bejelentkezett a fiók, akkor előfordulhat, hogy nem kéri.

4. A sikeres bejelentkezést követően egy `Authorization` fejléc bekerül a kérést, az Azure ad hozzáférési jogkivonatot. A következő egy minta jogkivonatot (Base64-kódolású):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Válassza ki **küldése**, és sikeresen meghívhatja az API-t.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Az előzetes engedélyezéshez kérelmek JWT érvényesítési házirend konfigurálása

Ezen a ponton a felhasználó megpróbál egy hívás a fejlesztői konzol, a felhasználó a rendszer kér való bejelentkezéshez. A fejlesztői konzol egy hozzáférési jogkivonatot a felhasználó nevében olvassa be.

De mi történik, ha valaki meghívja az API-t egy token nélkül, vagy az érvénytelen jogkivonatot? Például, továbbra is meghívhatja az API-t még akkor is, ha törli a `Authorization` fejléc. A hiba oka, hogy az API Management nem ellenőrzi a hozzáférési jogkivonat ezen a ponton. Egyszerűen továbbítja a `Authorization` fejléc a háttérrendszeri API-hoz.

Használhatja a [ellenőrzése JWT](api-management-access-restriction-policies.md#ValidateJWT) házirend az előzetes engedélyezéshez érvényesítésével megjeleníthető az összes beérkező kérelem hozzáférési jogkivonatok az API Management szolgáltatásban a kérelmek. Ha a kérés nem rendelkezik érvényes token, az API Management blokkolja. Például hozzáadhatja a következő házirendet a `<inbound>` házirend szakasza a `Echo API`. A hozzáférési jogkivonat célközönség jogcím ellenőrzi, és hibaüzenetet ad vissza, ha a jogkivonat nem érvényes. Házirendek konfigurálásáról további információért lásd: [állítsa be, vagy szerkesztheti a szabályzatokat](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Hívja az API-alkalmazás létrehozása

Ebben az útmutatóban használt a fejlesztői konzolt az API Management szolgáltatásban, a mintaalkalmazás ügyfél hívja az `Echo API` OAuth 2.0 védi. Alkalmazás készítése és megvalósítása az OAuth 2.0 kapcsolatos további tudnivalókért lásd: [Azure Active Directory-Kódminták](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [OAuth2.0 és az Azure Active Directory](../active-directory/develop/authentication-scenarios.md).
* Tekintse meg a további [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) az API Management ismertetése.
* A háttérszolgáltatás biztonságos egyéb módjai, lásd: [kölcsönös tanúsítványhitelesítés](api-management-howto-mutual-certificates.md).

* [Az API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

* [Az első API kezelése](import-and-publish.md).
