---
title: Az API-k védelemmel való ellátása a OAuth 2,0-mel a Azure Active Directory és a API Management használatával | Microsoft Docs
description: Ismerje meg, hogyan védhető a webes API-k háttere Azure Active Directory és API Management használatával.
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
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 72cffea3e5d42210bffbdbeef94c475cc8bdebf4
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312100"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Az API-k védelemmel való ellátása a OAuth 2,0-mel a Azure Active Directory és API Management használatával

Ez az útmutató bemutatja, hogyan konfigurálhatja Azure API Management-példányát az API-k biztosításához a OAuth 2,0 protokoll és a Azure Active Directory (Azure AD) használatával. 

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:
* Egy API Management-példány
* A API Management példányt használó, közzétett API
* Azure AD-bérlő

## <a name="overview"></a>Áttekintés

Íme egy gyors áttekintés a következő lépésekről:

1. Regisztráljon egy alkalmazást (backend-app) az Azure AD-ben az API képviseletéhez.
2. Regisztráljon egy másik alkalmazást (ügyfélalkalmazás) az Azure AD-ben az API-t meghívó ügyfélalkalmazás megjelenítéséhez.
3. Az Azure AD-ben adja meg az engedélyeket, hogy az ügyfélalkalmazás meghívja a háttér-alkalmazást.
4. Konfigurálja a fejlesztői konzolt a OAuth 2,0 felhasználói hitelesítés használatára.
5. Adja hozzá a **validate-JWT** szabályzatot az OAuth-jogkivonat ellenőrzéséhez minden bejövő kérelemnél.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Alkalmazás regisztrálása az Azure AD-ben az API képviseletéhez

Az API-k Azure AD-vel való ellátásához az első lépés egy olyan alkalmazás regisztrálása az Azure AD-ben, amely az API-t képviseli. 

1. Navigáljon a [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapra. 

2. Válassza az **új regisztráció**lehetőséget. 

1. Amikor megjelenik az **Alkalmazás regisztrálása lap**, adja meg az alkalmazás regisztrációs adatait: 
    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `backend-app`). 
    - A **támogatott fiókok típusai** szakaszban válassza a **fiókok lehetőséget bármely szervezeti címtárban**. 

1. Hagyja üresen az **átirányítási URI** szakaszt most.

1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához. 

1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később.

Az alkalmazás létrehozásakor jegyezze fel az **alkalmazás azonosítóját**egy későbbi lépésben való használatra. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Egy másik alkalmazás regisztrálása az Azure AD-ben egy ügyfélalkalmazás képviseletéhez

Minden ügyfélalkalmazás, amely meghívja az API-t, alkalmazásként kell regisztrálni az Azure AD-ben is. Ebben a példában a minta ügyfélalkalmazás a fejlesztői konzol a API Management fejlesztői portálon. A következő témakörből megtudhatja, hogyan regisztrálhat egy másik alkalmazást az Azure AD-ben a fejlesztői konzol megjelenítéséhez.

1. Navigáljon a [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapra. 

1. Válassza az **új regisztráció**lehetőséget.

1. Amikor megjelenik az **Alkalmazás regisztrálása lap**, adja meg az alkalmazás regisztrációs adatait: 
    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `client-app`). 
    - A **támogatott fiókok típusai** szakaszban válassza a **fiókok lehetőséget bármely szervezeti címtárban**. 

1. Az **átirányítási URI** szakaszban válassza ki `Web` és adja meg az URL-címet`https://contoso5.portal.azure-api.net/signin`

1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához. 

1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később.

Most hozzon létre egy ügyfél-titkot ehhez az alkalmazáshoz egy későbbi lépésben való használatra.

1. Az ügyfélalkalmazás oldalain válassza a **tanúsítványok & titkok**lehetőséget, és válassza az **új ügyfél titka**lehetőséget.

2. Az **ügyfél titkos kulcsának hozzáadása**területen adjon meg egy **leírást**. Válassza ki a kulcs lejárati idejét, majd válassza a **Hozzáadás**lehetőséget.

Jegyezze fel a kulcs értékét. 

## <a name="grant-permissions-in-azure-ad"></a>Engedélyek megadása az Azure AD-ben

Most, hogy regisztrált két alkalmazást az API és a fejlesztői konzol képviseletére, engedélyeket kell megadnia ahhoz, hogy az ügyfél-alkalmazás meghívni lehessen a háttér-alkalmazást.  

1. Navigáljon **Alkalmazásregisztrációk**. 

2. Válassza `client-app`ki a elemet, majd az alkalmazás oldalain keresse meg az **API-engedélyeket**.

3. Válassza **az engedély hozzáadása**lehetőséget.

4. Az **API kiválasztása**területen keresse meg és válassza `backend-app`a lehetőséget.

5. A **delegált engedélyek**területen válassza ki a megfelelő engedélyeket `backend-app`a következőhöz:.

6. Válassza az **engedélyek hozzáadása** lehetőséget 

> [!NOTE]
> Ha **Azure Active Directory** nem szerepel az engedélyek más alkalmazásokhoz területen, a **Hozzáadás** gombra kattintva vegye fel azt a listáról.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>A OAuth 2,0 felhasználói hitelesítés engedélyezése a Fejlesztői konzolon

Ezen a ponton létrehozta az alkalmazásait az Azure AD-ben, és megfelelő engedélyeket kapott ahhoz, hogy az ügyfél-alkalmazás meghívja a háttér-alkalmazást. 

Ebben a példában a fejlesztői konzol az ügyfél-alkalmazás. A következő lépések azt ismertetik, hogyan engedélyezhető a OAuth 2,0 felhasználói hitelesítés a Fejlesztői konzolon. 

1. A Azure Portalban keresse meg a API Management-példányát.

2. Válassza a **OAuth 2,0** > **Hozzáadás**lehetőséget.

3. Adja **meg a megjelenítendő nevet** és a **leírást**.

4. Az **ügyfél-regisztrációs lap URL-címéhez**adja meg a helyőrző értékét `http://localhost`, például:. Az **ügyfél-regisztrációs oldal URL-címe** arra a lapra mutat, amellyel a felhasználók létrehozhatják és konfigurálhatják a saját fiókjaikat a OAuth 2,0-szolgáltatók számára, amelyek támogatják ezt. Ebben a példában a felhasználók nem hoznak létre és nem konfigurálja a saját fiókjaikat, ezért Ehelyett helyőrzőt használ.

5. Az **engedélyezési**engedélyek típusainál válassza az **engedélyezési kód**lehetőséget.

6. Az **engedélyezési végpont URL** -címének és a **jogkivonat-végpont URL-címének**megadása. Olvassa be ezeket az értékeket  az Azure ad-bérlő végpontok lapján. Nyissa meg ismét a **Alkalmazásregisztrációk** lapot, és válassza a végpontok lehetőséget.

7. Másolja az **OAuth 2,0 engedélyezési végpontot**, és illessze be az **engedélyezési végpont URL-címébe** .

8. Másolja a **OAuth 2,0 token**-végpontot, és illessze be a **jogkivonat-végpont URL-** szövegmezőbe. A jogkivonat-végpont beillesztése mellett adjon hozzá egy **erőforrás**nevű Body paramétert. A paraméter értékeként használja az **alkalmazás azonosítóját** a háttérbeli alkalmazáshoz.

9. Ezután határozza meg az ügyfél hitelesítő adatait. Az ügyfél-alkalmazás hitelesítő adatai.

10. Az **ügyfél**-azonosítóhoz használja a Client-app **alkalmazás azonosítóját** .

11. Az **ügyfél titkos**kulcsaként használja az ügyfélhez korábban létrehozott kulcsot. 

12. Az ügyfél titkos kulcsát közvetlenül követően az engedélyezési kód **redirect_url** adja meg. Jegyezze fel ezt az URL-címet.

13. Kattintson a **Létrehozás** gombra.

14. Térjen vissza az ügyfél-alkalmazás **Beállítások** lapjára.

15. Válassza a **Válasz URL-címek**lehetőséget, majd illessze be a **redirect_url** az első sorba. Ebben a példában az első sorban `https://localhost` szereplő URL-címet váltotta fel.  

Most, hogy konfigurált egy OAuth 2,0-es engedélyezési kiszolgálót, a fejlesztői konzol hozzáférési jogkivonatokat szerezhet be az Azure AD-ből. 

A következő lépés a OAuth 2,0 felhasználói engedélyezésének engedélyezése az API-hoz. Ez lehetővé teszi, hogy a fejlesztői konzol tudja, hogy a felhasználó nevében be kell szereznie egy hozzáférési jogkivonatot, mielőtt meghívja az API-t.

1. Keresse meg API Management-példányát, és nyissa meg az **API-kat**.

2. Válassza ki a védelemmel ellátni kívánt API-t. Ebben a példában a `Echo API`-t használja.

3. Lépjen a **beállítások**.

4. A **Biztonság**területen válassza a **OAuth 2,0**elemet, és válassza ki a korábban konfigurált OAuth 2,0-kiszolgálót. 

5. Kattintson a **Mentés** gombra.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Sikerült meghívni az API-t a fejlesztői portálról

> [!NOTE]
> Ez a szakasz nem vonatkozik a fejlesztői portált nem támogató **felhasználási** szintjére.

Most, hogy a OAuth 2,0 felhasználói hitelesítés engedélyezve van a `Echo API`on, a fejlesztői konzol a felhasználó nevében szerez be hozzáférési jogkivonatot az API meghívása előtt.

1. A fejlesztői portálon keresse meg `Echo API` a bármelyik műveletet, és válassza a **kipróbálás**lehetőséget. Ez a fejlesztői konzolra mutat.

2. Jegyezze fel, hogy az **engedélyezési** szakasz egy új eleme, amely az imént hozzáadott engedélyezési kiszolgálónak felel meg.

3. Válassza az engedélyezési **kód** lehetőséget az engedélyezés legördülő listából, és a rendszer felszólítja, hogy jelentkezzen be az Azure ad-bérlőbe. Ha már bejelentkezett a fiókkal, előfordulhat, hogy a rendszer nem kérdezi le.

4. A sikeres bejelentkezést követően a rendszer `Authorization` egy fejlécet ad hozzá a kérelemhez, amely az Azure ad hozzáférési jogkivonatával rendelkezik. A következő egy minta token (Base64 kódolású):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Kattintson a **Küldés**lehetőségre, és az API-t sikeresen meghívhatja.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>JWT-érvényesítési házirend konfigurálása a kérelmek előzetes engedélyezéséhez

Ezen a ponton, amikor egy felhasználó megpróbál hívást készíteni a fejlesztői konzolról, a rendszer felszólítja a felhasználót, hogy jelentkezzen be. A fejlesztői konzol hozzáférési jogkivonatot szerez be a felhasználó nevében.

De mi történik, ha valaki jogkivonat vagy érvénytelen token nélkül hívja meg az API-t? Ha például törli a `Authorization` fejlécet, akkor is meghívhatja az API-t. Ennek az az oka, hogy API Management ezen a ponton nem érvényesíti a hozzáférési jogkivonatot. Egyszerűen átadja a `Authorization` fejlécet a háttér-API-nak.

A [JWT](api-management-access-restriction-policies.md#ValidateJWT) szabályzat érvényesítésével engedélyezheti a kérelmeket a API Managementban, az egyes bejövő kérelmek hozzáférési jogkivonatának ellenőrzésével. Ha egy kérelem nem rendelkezik érvényes jogkivonattal, API Management blokkolja. Például a következő `<inbound>` szabályzatot adhatja hozzá a szabályzat szakaszához `Echo API`:. Ellenőrzi a célközönség jogcímet egy hozzáférési jogkivonatban, és hibaüzenetet ad vissza, ha a jogkivonat érvénytelen. A házirendek konfigurálásával kapcsolatos információkért lásd: [házirendek beállítása vagy szerkesztése](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Alkalmazás létrehozása az API meghívásához

Ebben az útmutatóban a fejlesztői konzolt használta a API Managementban, mint a OAuth 2,0 által `Echo API` védettként hívható minta ügyfélalkalmazás. Ha többet szeretne megtudni az alkalmazások létrehozásáról és a 2,0-es OAuth megvalósításáról, tekintse meg a [Azure Active Directory](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>További lépések
* További információ a [Azure Active Directory és a OAuth 2.0-s](../active-directory/develop/authentication-scenarios.md)verzióról.
* További [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) a API Managementról.
* A háttér-szolgáltatás biztonságossá tételének egyéb módjaival kapcsolatban lásd: [kölcsönös Tanúsítványos hitelesítés](api-management-howto-mutual-certificates.md).

* [Hozzon létre egy API Management Service-példányt](get-started-create-service-instance.md).

* [Az első API kezelése](import-and-publish.md).
