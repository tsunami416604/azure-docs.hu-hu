---
title: Api védelme az OAuth 2.0 használatával az AAD és az API Management segítségével
titleSuffix: Azure API Management
description: Ismerje meg, hogyan védheti meg a webes API-háttérrendszer az Azure Active Directory és az API Management használatával.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 300f44daeeea5e8a774575dabcb00686906bb5de
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804367"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>API-k védelme az OAuth 2.0 Azure Active Directoryval és API Managementtel történő használatával

Ez az útmutató bemutatja, hogyan konfigurálhatja az Azure API Management-példányt egy API védelmére az OAuth 2.0 protokoll használatával az Azure Active Directory (Azure AD) használatával. 

> [!NOTE]
> Ez a funkció az API Management **fejlesztői**, **standard** és **prémium** szintjein érhető el.

## <a name="prerequisites"></a>Előfeltételek
A cikkben leírt lépések végrehajtásához a következőkre van szüksége:
* API-felügyeleti példány
* Az API Management-példányt használó, közzététel alatt lévő API
* Egy Azure AD-bérlő

## <a name="overview"></a>Áttekintés

Íme egy gyors áttekintés a lépésekről:

1. Regisztráljon egy alkalmazást (háttér-alkalmazás) az Azure AD-ben az API-t.
2. Regisztráljon egy másik alkalmazást (ügyfél-alkalmazás) az Azure AD-ben, hogy képviselje az API-t meghívni igénylő ügyfélalkalmazást.
3. Az Azure AD-ben engedélyeket ad meg, hogy az ügyfélalkalmazás hívja meg a háttér-alkalmazás.
4. Konfigurálja úgy a fejlesztői konzolt, hogy az OAuth 2.0 felhasználói engedélyével hívja meg az API-t.
5. Adja hozzá az **validate-jwt** házirendet az OAuth-jogkivonat érvényesítéséhez minden bejövő kérelemhez.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Alkalmazás regisztrálása az Azure AD-ben az API-t ábrázoló

Az API-k védelme az Azure AD-vel, az első lépés az, hogy regisztráljon egy alkalmazást az Azure AD-ben, amely az API-t képviseli. 

1. Az alkalmazás regisztrálásához lépjen az [Azure Portalra.](https://portal.azure.com) Keresse meg és válassza **ki az APP-regisztrációkat.**

1. Válassza **az Új regisztráció lehetőséget.** 

1. Amikor megjelenik a **Jelentkezési lap regisztrálása,** adja meg a jelentkezési lap regisztrációs adatait: 
    - A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára, például a *háttéralkalmazás*számára. 
    - A **Támogatott fióktípusok** csoportban válasszon a forgatókönyvnek megfelelő beállítást. 

1. Hagyja üresen az **Átirányítás URI** szakaszt.

1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához. 

1. Az alkalmazás **áttekintése** lapon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és rögzítse későbbre.

1. Válassza **az API felfedése** lehetőséget, és állítsa be az **alkalmazásazonosító URI-ját** az alapértelmezett értékkel. Ezt az értéket későbbre rögzítse.

1. A **Hatókör hozzáadása** gombra kattintva jelenítse meg a **Hatókör hozzáadása** lapot. Ezután hozzon létre egy új hatókört, `Files.Read`amelyet az API támogat (például ). Végül a **hatókör hozzáadása** gombra kattintva hozza létre a hatókört. Ismételje meg ezt a lépést az API által támogatott összes hatókör hozzáadásához.

1. A hatókörök létrehozásakor jegyezze fel azokat egy későbbi lépésben való használatra. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Regisztráljon egy másik alkalmazást az Azure AD-ben egy ügyfélalkalmazás ábrázolásához

Minden ügyfélalkalmazás, amely meghívja az API-t kell regisztrálni, mint egy alkalmazás az Azure AD-ben is. Ebben a példában az ügyfélalkalmazás a fejlesztői konzol az API Management fejlesztői portálon. Az alábbiakban bemutatja, hogyan regisztrálhat egy másik alkalmazást az Azure AD-ben a fejlesztői konzol képviseletére.

1. Az alkalmazás regisztrálásához lépjen az [Azure Portalra.](https://portal.azure.com) Keresse meg és válassza **ki az APP-regisztrációkat.**

1. Válassza **az Új regisztráció lehetőséget.**

1. Amikor megjelenik a **Jelentkezési lap regisztrálása,** adja meg a jelentkezési lap regisztrációs adatait: 
    - A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára, például az *ügyfélalkalmazás számára.* 
    - A **Támogatott fióktípusok** szakaszban válassza **a Fiókok lehetőséget bármely szervezeti címtárban (Bármely Azure AD-címtár - Több-bérlő)**. 

1. Az **Átirányítás URI** szakaszában jelölje ki `Web` és írja be az URL-címet. `https://contoso5.portal.azure-api.net/signin`

1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához. 

1. Az alkalmazás **áttekintése** lapon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és rögzítse későbbre.

Most hozzon létre egy ügyféltitkos kulcsot az alkalmazás számára egy későbbi lépésben.

1. Az ügyfélalkalmazás laplistájában válassza a **Tanúsítványok & titkos kulcsok**lehetőséget, majd az Új **ügyféltitok**lehetőséget.

1. Az **Ügyféltitok hozzáadása**csoportban adja meg a **leírást.** Adja meg, hogy mikor járjon le a kulcs, és válassza a **Hozzáadás gombot.**

A titkos kulcs létrehozásakor vegye figyelembe a következő lépésben használható kulcsértéket. 

## <a name="grant-permissions-in-azure-ad"></a>Engedélyek megadása az Azure AD-ben

Most, hogy regisztrált két alkalmazást az API és a fejlesztői konzol képviseletére, engedélyeket kell adnia ahhoz, hogy az ügyfélalkalmazás meghívja a háttéralkalmazást.  

1. Az [Azure Portalon](https://portal.azure.com) adja meg az ügyfélalkalmazás engedélyeit. Keresse meg és válassza **ki az APP-regisztrációkat.**

1. Válassza ki az ügyfélalkalmazást. Ezután az alkalmazás laplistájában válassza az **API-engedélyek lehetőséget.**

1. Válassza **az Engedély hozzáadása**lehetőséget.

1. Az **API kiválasztása csoportban**válassza a **Saját API-k**lehetőséget, majd keresse meg és válassza ki a háttéralkalmazást.

1. A **Delegált engedélyek csoportban**válassza ki a háttéralkalmazás megfelelő engedélyeit, majd válassza **az Engedélyek hozzáadása**lehetőséget.

1. Ha szükséges, az **API-engedélyek** lapon válassza a **Rendszergazdai hozzájárulás megadása \<a bérlői név>,** hogy a címtár összes felhasználója nevében beleegyezést adjon. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>OAuth 2.0 felhasználói engedély engedélyezése a fejlesztői konzolon

Ezen a ponton az Azure AD-ben hozta létre az alkalmazásokat, és megfelelő engedélyeket adott ahhoz, hogy az ügyfélalkalmazás hívja meg a háttéralkalmazás. 

Ebben a példában a fejlesztői konzol az ügyfélalkalmazás. Az alábbi lépések bemutatják, hogyan engedélyezheti az OAuth 2.0 felhasználói engedélyezését a fejlesztői konzolon. 

1. Az Azure Portalon keresse meg az API Management-példányt.

1. Válassza **az OAuth 2.0** > **Hozzáadás lehetőséget.**

1. Adja meg **a megjelenítendő nevet** és **a leírást.**

1. Az **Ügyfél regisztrációs lapjának URL-címéhez**adja `http://localhost`meg a helyőrző értékét, például . Az **ügyfélregisztrációs lap URL-címe** egy olyan lapra mutat, amelyet a felhasználók saját fiókjuk létrehozásához és konfigurálásához használhatnak az ezt támogató OAuth 2.0-s szolgáltatók számára. Ebben a példában a felhasználók nem hoznak létre és konfigurálnak saját fiókokat, ezért ehelyett helyőrzőt használ.

1. Az **engedélyezési engedélyezési típusoknál**válassza az **Engedélyezési kód lehetőséget.**

1. Adja meg az **engedélyezési végpont URL-címét** és **a tokenvégpont URL-címét.** Ezeket az értékeket az Azure AD-bérlő **végpontok** lapjáról. Tallózással keresse meg ismét az **Alkalmazásregisztrációk** lapot, és válassza **a Végpontok**lehetőséget.


1. Másolja az **OAuth 2.0 engedélyezési végpontot,** és illessze be az **Engedélyezési végpont URL-címmezőjébe.** Válassza a **POST** lehetőséget az Engedélyezési kérelem metódus csoportban.

1. Másolja az **OAuth 2.0 tokenvégpontot,** és illessze be a **Token végpont URL-címmezőjébe.** 

    >[!IMPORTANT]
    > Használhatja a **v1** vagy **v2** végpontok. Azonban attól függően, hogy melyik verziót választja, az alábbi lépés eltérő lesz. V2-végpontok használatát javasoljuk. 

1. Ha **v1-es** végpontokat használ, adjon hozzá egy **nevű törzsparamétert.** Ennek a paraméternek az értékéhez használja a háttéralkalmazás **alkalmazásazonosítóját.** 

1. V2-végpontok használata esetén használja a háttéralkalmazáshoz az **Alapértelmezett hatókör** mezőben létrehozott hatókört. **v2** Ügyeljen arra is, hogy a [`accessTokenAcceptedVersion`](/azure/active-directory/develop/reference-app-manifest#accesstokenacceptedversion-attribute) tulajdonság `2` értékét az [alkalmazásjegyzékben](/azure/active-directory/develop/reference-app-manifest)állítsa be.

1. Ezután adja meg az ügyfél hitelesítő adatait. Ezek az ügyfélalkalmazás hitelesítő adatai.

1. **Ügyfélazonosító**esetén használja az ügyfélalkalmazás **alkalmazásazonosítóját.**

1. Az **ügyféltitkos kulcs**hoz használja a korábban létrehozott kulcsot. 

1. Közvetlenül az ügyféltitkos kulcsot követően az engedélyezési kód engedélyezési típusának **redirect_url.** Jegyezze fel ezt az URL-címet.

1. Kattintson a **Létrehozás** gombra.

1. Lépjen vissza az ügyfélalkalmazásba, és válassza a **Hitelesítés**lehetőséget.

1. Az **Irányított URI-k átirányítása csoportban**válassza ki a **webként**beillesztendő típust, illessze be a **redirect_url** az **Átirányítás URI**csoportba, majd mentse.

Most, hogy konfigurált egy OAuth 2.0 engedélyezési kiszolgálót, a fejlesztői konzol hozzáférési jogkivonatokat kaphat az Azure AD-től. 

A következő lépés az OAuth 2.0 felhasználói engedély engedélyezése az API-hoz. Ez lehetővé teszi, hogy a fejlesztői konzol tudja, hogy be kell szereznie egy hozzáférési jogkivonatot a felhasználó nevében, mielőtt hívásokat kezdeményezne az API-hoz.

1. Tallózással keresse meg az API Management-példányt, és nyissa meg az **API-kat.**

2. Válassza ki a védeni kívánt API-t. Használhatja például a `Echo API`ot.

3. Nyissa meg a **Beállítások lapot.**

4. A **Biztonság csoportban**válassza az **OAuth 2.0**lehetőséget, és válassza ki a korábban konfigurált OAuth 2.0-s kiszolgálót. 

5. Kattintson a **Mentés** gombra.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Sikeresen meghívja az API-t a fejlesztői portálról

> [!NOTE]
> Ez a szakasz nem vonatkozik a **felhasználási** szintre, amely nem támogatja a fejlesztői portált.

Most, hogy az OAuth 2.0 felhasználói engedélyezve van az API-n, a fejlesztői konzol kap egy hozzáférési jogkivonatot a felhasználó nevében, mielőtt meghívja az API-t.

1. Tallózzon az API alatti bármely műveletben a fejlesztői portálon, és válassza a **Kipróbálás**lehetőséget. Ezzel elérkezik a fejlesztői konzolhoz.

2. Jegyezz fel egy új elemet az **Engedélyezés** szakaszban, amely az imént hozzáadott engedélyezési kiszolgálónak felel meg.

3. Válassza ki az **engedélyezési kódot** az engedélyezési legördülő listából, és a rendszer kéri, hogy jelentkezzen be az Azure AD-bérlőbe. Ha már be van jelentkezve a fiókkal, előfordulhat, hogy a rendszer nem kéri.

4. Sikeres bejelentkezés után egy `Authorization` fejléc kerül a kérelembe, az Azure AD-ből származó hozzáférési jogkivonattal. A következő egy mintatoken (Base64 kódolva):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Válassza **a Küldés**lehetőséget, és az API-t sikeresen meghívhatja.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>JWT-érvényesítési házirend konfigurálása a kérelmek előzetes engedélyezéséhez

Ezen a ponton, amikor egy felhasználó megpróbál hívást kezdeményezni a fejlesztői konzolról, a rendszer kéri a bejelentkezést. A fejlesztői konzol egy hozzáférési jogkivonatot szerez be a felhasználó nevében, és tartalmazza a jogkivonatot az API-hoz benyújtott kérelemben.

Mi történik azonban, ha valaki jogkivonat vagy érvénytelen jogkivonat nélkül hívja meg az API-t? Például próbálja meg hívni az `Authorization` API-t a fejléc nélkül, a hívás továbbra is megy keresztül. Ennek az az oka, hogy az API Management ezen a ponton nem ellenőrzi a hozzáférési jogkivonatot. Egyszerűen átadja a `Authorization` fejlécet a háttér-API-nak.

A [JWT-szabályzat érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) használatával előzetesen engedélyezheti a kérelmeket az API Managementben, az egyes bejövő kérelmek hozzáférési jogkivonatainak ellenőrzésével. Ha egy kérelem nem rendelkezik érvényes jogkivonatkal, az API Management blokkolja azt. Például adja hozzá a `<inbound>` következő házirendet `Echo API`a házirend szakaszához. Ellenőrzi a közönség jogcímegy hozzáférési jogkivonatot, és hibaüzenetet ad vissza, ha a jogkivonat érvénytelen. A házirendek konfigurálásáról a [Házirendek beállítása és szerkesztése](set-edit-policies.md)című témakörben talál további információt.

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
> [!NOTE]
> Ez `openid-config` az URL-cím a v1-végpontnak felel meg. A v2 `openid-config`végponthoz `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`használja a használatát.

## <a name="build-an-application-to-call-the-api"></a>Az API-t hívó alkalmazás létrehozása

Ebben az útmutatóban a fejlesztői konzolt használta az API `Echo API` Management ben mintaügyfél-alkalmazásként az OAuth 2.0 által védett rendszer hívásához. Ha többet szeretne tudni arról, hogyan hozhat létre egy alkalmazást, és hogyan valósíthatja meg az OAuth 2.0-s rendszert, olvassa el az [Azure Active Directory kódmintáit.](../active-directory/develop/sample-v2-code.md)

## <a name="next-steps"></a>További lépések
* További információ az [Azure Active Directoryról és az OAuth2.0-ról.](../active-directory/develop/authentication-scenarios.md)
* Tekintse n le az API Managementről szóló további [videókat.](https://azure.microsoft.com/documentation/videos/index/?services=api-management)
* A háttérszolgáltatás védelmének egyéb módjairól a [Kölcsönös tanúsítvány hitelesítése](api-management-howto-mutual-certificates.md)című témakörben van.

* [Hozzon létre egy API Management szolgáltatáspéldányt.](get-started-create-service-instance.md)

* [Az első API kezelése.](import-and-publish.md)
