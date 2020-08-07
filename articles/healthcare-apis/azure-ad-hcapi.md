---
title: Az Azure API FHIR-hez Azure Active Directory identitás-konfigurációja
description: Ismerje meg az Azure FHIR-kiszolgálók identitásának, hitelesítésének és engedélyezésének elveit.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: cdb73670996341e9219230bb277e087009266f32
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846020"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Az Azure API FHIR-hez Azure Active Directory identitás-konfigurációja

Az egészségügyi információkkal való munka során fontos, hogy az adat biztonságban legyen, és a jogosulatlan felhasználók vagy alkalmazások ne férhessenek hozzájuk. Az adatbiztonság biztosításához a FHIR-kiszolgálók a [OAuth 2,0](https://oauth.net/2/) -et használják. A [FHIR készült Azure API](https://azure.microsoft.com/services/azure-api-for-fhir/) a [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)használatával van védve, amely egy példa egy OAuth 2,0-es identitás-szolgáltatóra. Ez a cikk áttekintést nyújt a FHIR-kiszolgáló hitelesítéséről, valamint a jogkivonat beszerzéséhez szükséges lépésekről a FHIR-kiszolgálóhoz való hozzáféréshez. Habár ezek a lépések minden FHIR-kiszolgálóra és bármely identitás-szolgáltatóra érvényesek lesznek, az Azure API-t a FHIR-ként, a FHIR-kiszolgálóként és az Azure AD-t is megtekintjük ebben a cikkben.

## <a name="access-control-overview"></a>A hozzáférés-vezérlés áttekintése

Ahhoz, hogy egy ügyfélalkalmazás hozzáférhessen az Azure API-hoz a FHIR-hez, hozzáférési jogkivonatot kell bemutatnia. A hozzáférési jogkivonat a tulajdonságok (jogcímek) aláírt, [Base64](https://en.wikipedia.org/wiki/Base64) kódolású gyűjteménye, amely információkat közvetít az ügyfél identitásával, valamint az ügyfél számára biztosított szerepkörökkel és jogosultságokkal kapcsolatban.

A jogkivonatok beszerzésének számos módja van, de a FHIR készült Azure API nem gondoskodik arról, hogy a jogkivonat Hogyan szerezhető be, amíg a megfelelő jogcímekkel rendelkező, megfelelően aláírt jogkivonat. 

Ha például az [engedélyezési programkódot](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) használja, a FHIR-kiszolgálóhoz való hozzáférés az alábbi négy lépésből áll:

![FHIR-hitelesítés](media/azure-ad-hcapi/fhir-authorization.png)

1. Az ügyfél kérelmet küld az `/authorize` Azure ad-végpontnak. Az Azure AD átirányítja az ügyfelet egy bejelentkezési oldalra, ahol a felhasználó a megfelelő hitelesítő adatokkal (például felhasználónévvel és jelszóval vagy kétfaktoros hitelesítéssel) végzi a hitelesítést. Tekintse meg [az engedélyezési kód beszerzésének](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#request-an-authorization-code)részleteit. A sikeres hitelesítés után a rendszer egy *engedélyezési kódot* ad vissza az ügyfélnek. Az Azure AD csak azt engedélyezi, hogy ez az engedélyezési kód az ügyfélalkalmazás regisztrálásakor megadott regisztrált válasz URL-címére legyen visszaadva (lásd alább).
1. Az ügyfélalkalmazás az Azure AD végpontján egy *hozzáférési jogkivonat* engedélyezési kódját cseréli ki `/token` . Token kérelmezése esetén előfordulhat, hogy az ügyfélalkalmazás meg kell adnia egy ügyfél-titkot (az alkalmazások jelszava). Tekintse meg a [hozzáférési token beszerzésének](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#use-the-authorization-code-to-request-an-access-token)részleteit.
1. Az ügyfél egy kérést küld a FHIR készült Azure API-nak, például az `GET /Patient` összes beteg kereséséhez. A kérelem elkészítésekor a hozzáférési tokent egy HTTP-kérelem fejlécében tartalmazza, például `Authorization: Bearer eyJ0e...` `eyJ0e...` a a Base64 kódolású hozzáférési tokent jelképezi.
1. A FHIR készült Azure API ellenőrzi, hogy a jogkivonat tartalmazza-e a megfelelő jogcímeket (a jogkivonatban található tulajdonságokat). Ha minden befejeződik, a rendszer végrehajtja a kérést, és egy FHIR-csomagot ad vissza az ügyfélnek.

Fontos megjegyezni, hogy a FHIR készült Azure API nem része a felhasználói hitelesítő adatok érvényesítésének, és nem adja ki a tokent. A hitelesítés és a jogkivonat létrehozása az Azure AD-vel történik. A FHIR készült Azure API egyszerűen ellenőrzi, hogy a jogkivonat helyesen van-e aláírva (hiteles), és hogy megfelelő jogcímeket tartalmaz.

## <a name="structure-of-an-access-token"></a>Hozzáférési jogkivonat szerkezete

A FHIR alkalmazások fejlesztése gyakran hibakeresési hozzáférési problémákkal jár. Ha egy ügyfél nem fér hozzá a FHIR készült Azure API-hoz, érdemes megismerni a hozzáférési token szerkezetét, és azt, hogy miként lehet dekódolni a jogkivonat tartalmának (jogcímei) vizsgálatához. 

A FHIR-kiszolgálók általában egy [JSON web token](https://en.wikipedia.org/wiki/JSON_Web_Token) várnak (JWT, néha "lemond"). Három részből áll:

1. Egy fejléc, amely így néz ki:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. A hasznos adatok (a jogcímek), például:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Egy aláírás, amelyet a fejléc Base64 kódolású tartalmának összefűzésével és a hasznos adatok kiszámításával kell kiszámítani, valamint a fejlécben megadott algoritmus () alapján kell kiszámítani a titkosítási kivonatot `alg` . A kiszolgálók nyilvános kulcsokat szerezhetnek be az identitás-szolgáltatótól, és ellenőrizhetik, hogy a jogkivonatot egy adott identitás-szolgáltató bocsátotta-e ki, és hogy az nem lett-e illetéktelenül módosítva.

A teljes jogkivonat a három szegmens Base64 kódolású (valójában Base64 URL-cím) verzióját tartalmazza. A három szegmens össze van fűzve és el van választva egymástól `.` (pont).

Alább látható egy példa token:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

A jogkivonat dekódolása és ellenőrzése olyan eszközökkel végezhető, mint például a [https://jwt.ms](https://jwt.ms) . A jogkivonat dekódolásának eredménye:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Hozzáférési jogkivonat beszerzése

A fentiekben leírtak szerint számos módon szerezhet be tokent az Azure AD-ből. Ezeket részletesen ismertetik az [Azure ad fejlesztői dokumentációjában](https://docs.microsoft.com/azure/active-directory/develop/).

Az Azure AD két különböző verziójú OAuth 2,0-végpontot tartalmaz, amelyek a és a néven is ismertek `v1.0` `v2.0` . Mindkét verzió OAuth 2,0-végpont, a `v1.0` és a `v2.0` megjelölések pedig az Azure ad által a standard megvalósításával kapcsolatos különbségeket ismertetik. 

FHIR-kiszolgáló használatakor a `v1.0` vagy a `v2.0` végpontokat is használhatja. A választás az ügyfélalkalmazás által használt hitelesítési könyvtáraktól függően változhat.

Az Azure AD dokumentációjának megfelelő fejezetei a következők:

* `v1.0`végpont
    * [Engedélyezési kód folyamata](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code).
    * [Ügyfél-hitelesítő adatok folyamata](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow).
* `v2.0`végpont
    * [Engedélyezési kód folyamata](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).
    * [Ügyfél-hitelesítő adatok folyamata](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).

A token beszerzéséhez más változatok is tartoznak (például a flow nevében). A részletekért olvassa el az Azure AD dokumentációját. Ha a FHIR készült Azure API-t használja, az [Azure CLI](get-healthcare-apis-access-token-cli.md)-vel egy hozzáférési token (hibakeresési célú) beszerzésére is van néhány parancsikon.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan biztosíthatja az Azure API-hoz való hozzáférés biztosítását az Azure AD-vel való FHIR. Ha meg szeretné tudni, hogyan helyezheti üzembe az Azure API FHIR-példányát, folytassa az üzembe helyezési útmutatóval.

>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)