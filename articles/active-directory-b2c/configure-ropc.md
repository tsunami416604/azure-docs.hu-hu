---
title: Az erőforrás tulajdonosa jelszó hitelesítő adatok folyamata konfigurálása az Azure Active Directory B2C |} Microsoft Docs
description: Ismerje meg, hogy az erőforrás tulajdonosa jelszó hitelesítő adatok folyamata konfigurálása az Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 073af4a57d55eb8b2f3608482159b57c7b408f3b
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102606"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Az erőforrás tulajdonosa jelszó hitelesítő adatok folyamata konfigurálása az Azure AD B2C

Az erőforrás tulajdonosa jelszó hitelesítő adatok (ROPC) folyamat egy OAuth szabványon alapuló hitelesítési folyamat, ahol az alkalmazás, más néven a függő entitás cseréje érvényes hitelesítő adatokat, például a felhasználói azonosítóját és jelszavát az azonosítója, hozzáférési jogkivonatot, és a frissítési jogkivonatot. 

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.

Az Azure Active Directory (Azure AD) B2C-ben a következő beállításokat támogatja:

- **Natív ügyfél**: hitelesítés során felhasználói beavatkozást történik, amikor a kód lefut egy felhasználó ügyféloldali eszközön. Egy natív operációs rendszerén, például Android, futó vagy futtatása böngészőben, például a JavaScriptek mobilalkalmazás is lehet.
- **Nyilvános ügyfél folyamatában**: csak felhasználói hitelesítő adatokat, az alkalmazások által összegyűjtött az API-hívásban. Az alkalmazás hitelesítő adatait a rendszer nem küldi.
- **Új jogcímeket adhatnak hozzá**: az azonosító token tartalma módosítható új jogcímeket adhatnak hozzá. 

A következő folyamatok nem támogatottak:

- **Kiszolgáló-kiszolgáló**: védelem identitásrendszere kell egy megbízható IP-címet a interakció részeként a hívó (a natív ügyfél) során. A kiszolgálóoldali API-hívás csak a kiszolgáló IP-cím szolgál. Ha egy dinamikus hitelesítések sikertelenek a küszöbérték elérése esetén, védelmi identitásrendszere azonosíthat ismétlődő IP-címnek, a támadó.
- **Bizalmas ügyfél folyamatában**: az alkalmazás ügyfél-azonosító érvényességét, de az alkalmazás titkos kulcs nincs érvényesítve.

##  <a name="create-a-resource-owner-policy"></a>Erőforrás tulajdonosa házirend létrehozása

1. Jelentkezzen be az Azure-portálon az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Váltson át az Azure AD B2C-bérlő, válassza ki a portál jobb felső sarkában a B2C-címtárban.
3. A **házirendek**, jelölje be **erőforrás tulajdonosa házirendek**.
4. Adjon meg egy nevet a házirend, például: *ROPC_Auth*, majd válassza ki **alkalmazási jogcímet**.
5. Válassza ki az alkalmazás jogcímét, amelyekre szüksége van az alkalmazás, például a *megjelenített név*, *E-mail cím*, és *identitásszolgáltató*.
6. Kattintson az **OK**, majd a **Létrehozás** gombra.

   Ekkor megjelenik egy végpontot, például az ebben a példában:

   `https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

1. Válassza ki a B2C beállítások **alkalmazások**, majd válassza ki **Hozzáadás**.
2. Írjon be egy nevet az alkalmazás, például *ROPC_Auth_app*.
3. Válassza ki **nem** a **Web App/Web API**, majd válassza ki **Igen** a **natív ügyfél**.
4. Minden más értéket hagyja, és válassza ki **létrehozása**.
5. Válassza ki az új alkalmazást, és jegyezze fel az Alkalmazásazonosítót későbbi használatra.

## <a name="test-the-policy"></a>A házirend tesztelése

A kedvenc API fejlesztési alkalmazás használja az API-hívás létrehozásához, és tekintse át a házirend debug válasz. Ehhez hasonló található információkat a következő táblázat a POST kérelem törzsében hívás össze:
- Cserélje le  *\<yourtenant.onmicrosoft.com >* a B2C-bérlő nevét.
- Cserélje le  *\<B2C_1A_ROPC_Auth >* az erőforrás tulajdonosa hitelesítő adatok jelszóházirend teljes nevét.
- Cserélje le  *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3 >* a regisztrációt a az alkalmazás azonosítójával.

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Kulcs | Érték |
| --- | ----- |
| felhasználónév | leadiocl@outlook.com |
| jelszó | Passxword1 |
| grant_type | jelszó |
| scope | openid \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| response_type | token id_token |

*Client_id* az érték, amely korábban feljegyzett, az alkalmazás azonosítóját. *Offline_access* nem kötelező, ha szeretne kapni egy frissítési jogkivonat. 

A tényleges POST-kérelmet a következőképpen néznek:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


A sikeres válasz offline hozzáférést a következőképpen néz ki:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>A frissítési jogkivonat beváltása

A POST híváson hasonló itt található információkat a következő táblázat a kérelem törzsében össze:

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Kulcs | Érték |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| erőforrás | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* és *erőforrás* azonosítóként alkalmazás korábban feljegyzett értékek *Refresh_token* lexikális eleme, amely a korábban említett hitelesítési hívás kapott.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Az előnyben részesített natív SDK-val megvalósítását vagy alkalmazás-alapú hitelesítés használata

Az Azure AD B2C-implementációjában OAuth 2.0 szabványoknak nyilvános ügyfél erőforrás tulajdonosa jelszó hitelesítő adatokat, és a legtöbb ügyfél SDK-k kompatibilisnek kell lennie. Általunk tesztelt folyamatot széles körben, AppAuth az iOS és Android rendszerhez AppAuth helyezésükön. A legfrissebb információkat lásd: [natív App SDK az OAuth 2.0 és az OpenID Connect megvalósítás modern ajánlott eljárások](https://appauth.io/).

Töltse le a Githubról, az Azure AD B2C való használatra beállított működő minták [Android](https://aka.ms/aadb2cappauthropc) és [IOS](https://aka.ms/aadb2ciosappauthropc).




